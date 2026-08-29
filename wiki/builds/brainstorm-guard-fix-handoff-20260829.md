---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-29
sources: []
---

# brainstorm_guard.py 修正の引き継ぎ（2026-08-29）

**この文書だけで作業できるように書いてある。** 前の会話の文脈は不要。
実行する人（LLM）は、まず下の「関連ファイル」を全部開いてから着手すること。

## 関連ファイル（すべて実パス）

| 役割 | 実パス |
|---|---|
| 修正対象 | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` |
| 規則本体（スキルの説明） | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` |
| 動作記録（発火の実績が残る） | `/Users/takedayousuke/.claude/skills/brainstorm/guard.log` |
| フック登録 | `/Users/takedayousuke/.claude/settings.json` |
| 仕様の正本 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md` |
| この修正が出た議論 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-brainstorm-skill-portability.md` |
| 実際に動いているメモ（壊すと実害が出る） | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` |

KB ルートの絶対パスは
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`。
**半角スペースが2つ入っている。これが課題1の原因そのものなので、扱いに注意すること。**

## このスクリプトが何をしているか（前提知識）

`/brainstorm` は「考えを詰めて計画を作るところまで」を担当するスキルで、実装はしない。
`brainstorm_guard.py` はその規則を機械で担保する監査で、Claude Code のフックから呼ばれる。

| サブコマンド | 起動点 | 働き |
|---|---|---|
| `inject-light` | UserPromptSubmit | メモの要点を毎ターン context へ戻す |
| `inject-full` | SessionStart | 圧縮後などにメモを厚く戻す |
| `guard-write --lockdown` | PreToolUse | ブレスト中、成果物への書き込みを拒否する |
| `guard-write --unread` | PreToolUse | `ready` のメモを読まずに実装しようとしたら拒否する |
| `guard-stop` / `guard-stop-handoff` | Stop | 承認カード無し・到達不能な引き継ぎのまま会話を閉じさせない |
| `check` / `bypass` / `audit-handoff` | 手動 | 確認・一時解除・到達性監査 |

設計上の大原則: **どんな失敗でも「素通り（作業を止めない）」に倒す。**
監査が壊れて作業不能になる事故を作らないこと。

---

## 課題1: 封鎖側のパス抽出が、半角スペースを含むパスで壊れる

### 症状（2026-08-29 09:30 に実際に発生）

KB 内にメモを作ろうとして、KB ルートへ `cd` してから `cat` のヒアドキュメントで
`wiki/analyses/` の中へ書き込む Bash を実行したところ、封鎖フックが拒否した。
`guard.log` に残った行は、`guard-write` `lockdown DENY` `tool=Bash` に続けて
`path=` にパスの先頭断片（KB ルートの1つ目の半角スペースまで）だけが記録されている。

**この断片はパスとして存在しないもので、実在しない。**
書こうとしていたのは `wiki/analyses/` の中（本来は許可される場所）だった。
同じ内容を Write ツールで書いたら通った（`lockdown pass tool=Write targets=1`）。

### 原因（コードを読んで確認済み）

`brainstorm_guard.py` の `_candidate_paths()`（350行付近）。Bash の分岐で

```python
for tok in re.split(r"[\s;|&()]+", cmd):
```

と、**コマンド文字列を空白で切っている**。KB のパスには半角スペースがあるため、
1つ目のスペースで切れた断片（存在しない文字列）がパスとして扱われ、許可判定に失敗して
拒否側へ倒れる。

### 既に正しく直っている手本が、同じファイルの中にある

引き継ぎ到達性の監査側は 2026-08-29 09:16 に修正済みで、`_path_candidates()`（640行付近）が
**貪欲一致＋末尾から1語ずつ削って実在を試す**方式になっている。`_resolve_path()`（700行付近）が
その候補を長い順に `exists()` で試す。**課題1はこの方式を封鎖側にも持ち込めば解ける。**
2つの実装を将来また食い違わせないため、共通化できるなら共通化すること。

### 満たすこと

1. 半角スペースを含む KB 内のパスを Bash で書こうとしたとき、**許可される場所なら通ること**。
2. 引用符で囲まれた（`"..."` / `'...'`）パスを1つのまとまりとして扱うこと。
3. **拒否側へ倒れるより素通りを選ぶ**という既存の原則を壊さないこと。断片が実在しないときは、
   拒否の根拠にしないこと。
4. 本来止めるべき書き込み（成果物フォルダへの Bash 書き込み）は、これまでどおり止まること。

### やってはいけないこと

- **封鎖そのものを弱めて解決したことにしない。** 「Bash は全部通す」は禁止。
- `_path_candidates()` / `_resolve_path()` / `audit-handoff` の既存の挙動を壊さない
  （こちらは 2026-08-29 に自己試験で合格している）。
- 既存の自動試験を消して通す、は禁止。

---

## 課題2: メモを階層フォルダへ置けるようにする（武田さん承認済み・2026-08-29）

### なぜ

現行は「1テーマ1枚・`wiki/analyses/` に平置き」。長期のテーマで1枚が肥大化しており、
`brainstorm-gf2-dusevnyj-bikini-to-helen.md` は **1240行**ある。
武田さんの指示は「Obsidian で階層を分けられるのだから、1つを肥大化させる必要はない。
各セッションに1枚。大元は計画や引き継ぎの資料で、関連ファイルが全部紐づいている状態にしたい」。

### 承認された形

```
wiki/analyses/brainstorm/<プロジェクト>/
    _index.md                  ← 親1枚。計画と引き継ぎの正本。子と関連ファイルを実パスで束ねる
    20260829-<セッションの主題>.md   ← 子。セッションごとに1枚
```

**機械が読むのは親。** 親の frontmatter に `brainstorm_status` / `scope` /
`entry_paths` / `background_paths` を置き、子は親から実パスで辿れるようにする。

### いま壊れる場所（コードを読んで確認済み）

`brainstorm_guard.py` の 40〜41行と 183行:

```python
MEMO_DIR = KB_ROOT / "wiki" / "analyses"
MEMO_GLOB = "brainstorm-*.md"
...
for p in sorted(MEMO_DIR.glob(MEMO_GLOB)):
```

**非再帰の glob なので、サブフォルダへ移したメモは一切見つからない。**
この状態でメモだけ移動すると、再注入・封鎖・未読ブロックが**無言で全部効かなくなる**。
移動と修正は必ずセットで行うこと。

### 満たすこと

1. サブフォルダに置かれたメモを見つけられること（再帰探索）。
2. **親と子を二重に数えないこと。** 同じテーマの親と子が両方 `active` として注入されると、
   context が二重になる。機械が読むのは親1枚と決めること（子は親から辿る）。
3. 既存の平置き3枚が、移行前も移行後も壊れないこと。
4. 移行作業（既存メモの移動とリンク張り直し）まで含めて完了させること。移動対象は次の3枚。
   - `wiki/analyses/brainstorm-brainstorm-skill-design.md`（`done`）
   - `wiki/analyses/brainstorm-brainstorm-skill-portability.md`（`active`）
   - `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md`（`active`・**進行中の案件。最優先で壊さない**）
5. `SKILL.md` と `wiki/builds/brainstorm-skill.md` の記述を、実際の構造に合わせて更新すること。

### やってはいけないこと

- **`brainstorm-gf2-dusevnyj-bikini-to-helen.md` の本文を要約・圧縮・分割しない。**
  武田さん本人の言葉と実測値が入っており、失うと復元できない。移動とリンク追加のみ。
- 移行の途中で「メモが見つからない」状態を残したまま終わらせない。

---

## 検証（両方の課題に共通）

1. `python3 "/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py" audit-handoff --selftest`
   が PASS すること。
2. 既存の自動試験（封鎖・未読・解除・再注入・停止）を全部通すこと。
   2026-08-28 時点で22件合格の記録がある。
3. 課題1は**再現試験を足すこと**。半角スペースを含むパスへの Bash 書き込みで、
   許可される場所なら通り、成果物フォルダなら止まること。
4. 実機での確認は `guard.log` に本番の行が出るかで見る。
   **武田さんに画面で確認させる設計にしないこと。**

## 完了の報告に含めること

- `guard.log` の実際の行（合格・拒否の両方）
- 移行後のメモの実パス一覧
- 直せなかったもの・未検証のものがあれば、その事実

## 関連リンク

- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm-brainstorm-skill-portability.md`
