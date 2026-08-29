---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-29
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
  - /Users/takedayousuke/.claude
  - /Users/takedayousuke/.agents
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-guard-fix-handoff-20260829.md
background_paths:
  - /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-brainstorm-skill-design.md
  - /Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py
sources: []
---

# brainstorm スキルを他 LLM へ移植する指示書（2026-08-29）

設計の正本は `wiki/builds/brainstorm-skill.md`（[[brainstorm-skill]]）。
Claude 側の設計経緯は `wiki/analyses/brainstorm-brainstorm-skill-design.md`
（[[brainstorm-brainstorm-skill-design]]、状態 done）。このメモは **移植（他 LLM で同じ使用感を出す）**
だけを扱う。

## 武田さんの考え

- `/brainstorm` を作った。**この KB フォルダを開いた LLM 共通で使用感を同じにしたい**という意図
  だったが、まだ Claude 以外で使えるか確認できていない。
- スキルの実装作業は実際のその LLM にさせるので、**どういう指示を送ればいいかを考えてほしい**。
- （2026-08-28 の既存方針）指示書だけ提出し、その環境でどう実現するかはその LLM に考えさせる方が
  解像度が高い。

## 実測で分かったこと（2026-08-29・すべてファイルを読んで確認）

### 1. 指示書は既にあるが、1日ぶん古い

`wiki/builds/brainstorm-skill.md` に「## Codex / Kimi へ渡す指示書」節が既にある（2026-08-28 版）。
ただし **翌 8-29 に足した引き継ぎ到達性の監査（H1〜H7 / `guard-stop-handoff` / `entry_paths`・
`background_paths` の frontmatter / `## 再開の入口（実パス）` 節）が入っていない。**
今そのまま渡すと、他 LLM 側は 8-28 時点の使用感になり、Claude と揃わない。

### 2. 環境ごとの土台（実ファイルで確認）

| 環境 | 設定ファイル | フックの実績 | 未確認 |
|---|---|---|---|
| Codex | `/Users/takedayousuke/.codex/hooks.json` | Claude と**同じイベント名スキーマ**。SessionStart / UserPromptSubmit / Stop / PreCompact / PostCompact / SubagentStart / SubagentStop を context-harness が登録済み | **PreToolUse の登録実例が無い**。対応可否そのものが未確認 |
| Kimi Code | `/Users/takedayousuke/.kimi-code/config.toml` | `[[hooks]] event = "Stop"` が実在（旧 hold の `hold_guard_kimi.py` が**今も登録されたまま**。hold は休止済みなので残骸） | PreToolUse 相当・UserPromptSubmit 相当の有無 |
| opencode | `/Users/takedayousuke/.config/opencode/opencode.jsonc` | フック設定は無い（中身は `permission: "allow"` のみ）。**プラグイン方式**で `@opencode-ai/plugin` 1.18.21 が導入済み | プラグインで書き込み拒否・毎ターン注入ができるか |

→ **最大の未確認点は「成果物封鎖（PreToolUse で書き込みを拒否）」が Codex / Kimi / opencode で
成立するか。** Stop（会話を閉じさせない）は Kimi で実績があり、Codex も同名イベントを持つ。

### 3. 置き場所が Claude 専用になっている

- 監査スクリプトの実体は `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`。
  旧 hold は共通置き場 `/Users/takedayousuke/.agents/skills/hold/` を使っていた（`llm-wiki` も同じ）。
- `brainstorm_guard.py` の `audit-handoff` は `~/.claude/settings.json` の存在をハードコードで
  確認している（フック登録の検査）。他環境ではこの検査が必ず落ちる。

### 4. 移植できる部分／できない部分

- **移植できる（Python 3 だけで動く）**: メモの探索、`brainstorm_status` / `scope` の読み取り、
  H1〜H7 の到達性監査、`check` サブコマンド。
- **移植できない**: フック用サブコマンド（`inject-*` / `guard-write` / `guard-stop*`）の**入出力**。
  Claude のフック JSON（`transcript_path`、`hookSpecificOutput.permissionDecision`）前提のため、
  環境ごとに薄い変換層（アダプタ）が要る。判定ロジック自体は共有できる。

### 5. Claude 側で今日出た誤検知（実測）

このメモを Bash のヒアドキュメントで作ろうとしたところ、封鎖フックが KB ルートの
1つ目の半角スペースまでの断片（存在しない文字列）を「止めた対象」として拒否した。
**KB ルートのパスに半角スペースがあるため、コマンド文字列からのパス抽出が途中で切れている。**
Write ツールでは通った。
移植時に同じ誤検知を持ち込まないよう、指示書に明記する必要がある。

### 6. 武田さんの意図（2026-08-29 カード回答）

> 俺の意図としては、llm ごとに適応したスキルであって良くて、claude は codex のフォルダを見る必要
> ないし、codex は claude のフォルダを見る必要はないよね？ ここの認識が違う？
> 要は各 llm に claude でこういうスキルを実装したから、似た感じのを作ってって言いたい。
> 書いてて思った。修正するときとかにバッティングするのか？

→ 認識は合っている。フックの入出力形式が環境ごとに違うため、本体を共有しても変換層が要るだけで、
独立させたほうが素直。**バッティングするのはスクリプトではなくメモ側**で、実在する衝突は2つ。

1. **書式のズレ** — `brainstorm_status` / `scope` / `entry_paths` の書き方が環境間でズレると、
   Claude が書いたメモを Codex の判定が読めず、止まるべきところで止まらない（逆もある）。
   使用感が揃わない原因は主にここ。
2. **同時起動での上書き** — 同じテーマのメモを2つの LLM で同時に開くと後勝ちで消える
   （既知の同時編集事故と同型。小さな追記・書く直前に読み直す、で回避）。

「修正が3回」は、書式さえ固定されていれば実害が小さい。判定の中身が違ってもメモは読める。

## 決まったこと

- **スキル本体は LLM ごとに独立**（2026-08-29 武田さん承認）。Claude / Codex / Kimi / opencode が
  互いのフォルダを参照しない。共通本体の一元化は行わない。
- **指示書は条件だけを渡し、実現方法はその環境の LLM に考えさせる**（8-28 方針の踏襲・同日承認）。
- **メモは階層で持つ**（同日承認）。`wiki/analyses/brainstorm/<プロジェクト>/_index.md` が親
  （計画と引き継ぎの正本）、その下にセッションごとの子。機械が読むのは親1枚。
- **メモの書式（機械が読む frontmatter と節の見出し）だけは指示書で固定する**（同日承認）。
  判定の仕組み・フックの組み方は各 LLM に任せる。
- **封鎖側のパス抽出の誤検知は放置しない**（同日・武田さん指示）。他 LLM 向けには注意として
  指示書に明記し、Claude 側の修正は**別エージェントへ引き継ぐ**。
  引き継ぎ資料: `wiki/builds/brainstorm-guard-fix-handoff-20260829.md`。

### 中断とその後（2026-08-29）

PC 再起動で推論が止まった。中断地点は「メモを階層フォルダへ移すと機械側が壊れないか」の確認中。
確認結果は下の 9 のとおりで、**壊れる**。よって階層化は引き継ぎ資料の課題2に入れた。

### 9. 階層化はスクリプト修正とセットでしかできない（2026-08-29 実測）

`brainstorm_guard.py` の 40〜41行・183行が
`MEMO_DIR.glob("brainstorm-*.md")`（`MEMO_DIR = KB_ROOT/wiki/analyses`）で、**非再帰**。
サブフォルダへ移したメモは一切見つからない。メモだけ先に移すと、再注入・封鎖・未読ブロックが
**無言で全部効かなくなる**。移動と修正は必ずセットで行う。

### 7. メモの粒度（2026-08-29 武田さんの指摘）

> メモって、その1枚に全てのプロジェクトのメモを入れるつもりなの？ なら禁止させたいな。
> Obsidian で管理してるから、階層を分けられるはずだから、1つを肥大化させる必要性はない。
> 俺の認識では各セッションに対して1枚って感じだった。
> 大元は計画や引き継ぎの資料であって、関連ファイルが全部紐づいてる状態。
> だから、コンテキストの粒度に制限されずに、プロジェクトを進められる環境を作りたい。

事実確認（同日）: 全プロジェクト1枚ではなく **1テーマ1枚**。現存3枚。ただし
`brainstorm-gf2-dusevnyj-bikini-to-helen.md` は **1240行**まで育っており、肥大化の指摘は当たる。
階層は切っておらず `wiki/analyses/` に平置き。

「1テーマ1枚・日付なし」にした理由は **実装側が探せるようにするため** の1点のみ。
親になる1枚が子を実パスで束ねれば、セッションごとに分けても探せなくなる問題は起きない。

### 8. 封鎖側のパス抽出は未修正（2026-08-29 実測）

`brainstorm_guard.py` の最終更新は 09:16、誤検知の発生は 09:30 で、**その間にファイルは
変わっていない**。09:16 の修正が入ったのは引き継ぎ監査側の `_path_candidates`（640行・貪欲一致）で、
今日誤発火した封鎖側 `_candidate_paths`（364行）は **`re.split(r"[\s;|&()]+", cmd)` のまま**で、
半角スペースを含むパスを途中で切る。同じ症状の別個所が残っている。

## まだ決まってないこと

- Kimi の `config.toml` に残っている休止済み hold の Stop フックの撤去を、誰にいつやらせるか
  （指示書には「要撤去」と書いた）。
- 3環境のうちどれから着手するか。
- 封鎖ができない環境（opencode が該当する可能性）で、代わりに何で担保するか。

## 捨てた案と理由

（まだ無し）

## 直した記録

- 2026-08-29 `wiki/builds/brainstorm-skill.md` の `[[hold]]` 4か所。対応する wiki ページが存在せず
  切れたリンクだった。実体の場所（`~/.claude/skills/hold/`）を書いた素のテキストへ変更。
- 2026-08-29 同ページの `[[plan-gate-skill]]` `[[llm-state-transition-gate]]`
  `[[llm-project-quality-gate]]` に、フォルダを含む実パスを併記（渡した先から開けるように）。
- 2026-08-29 このメモと引き継ぎ資料で、壊れたパス断片をそのまま引用していた箇所を言い換え。
  監査が「実在しないパス」として毎回 FAIL を出すため。内容は同じ。
- 2026-08-29 `entry_paths` から `SKILL.md` を `background_paths` へ移動。必読ではなく背景資料で、
  中身の監査対象にすると相対パス表記で毎回 FAIL になるため。
- 上記の後、`audit-handoff` は PASS。

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-brainstorm-skill-portability.md`
- 仕様の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
- 規則本体: `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`
- 監査スクリプト: `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`

## 実装への申し送り

作業は2系統に分かれる。**互いに独立していて、順序の縛りは無い。**

### 系統A: 他 LLM（Codex / Kimi / opencode）へスキルを組ませる

- 渡すもの: `wiki/builds/brainstorm-skill.md` の
  「## Codex / Kimi へ渡す指示書」節 **と** 「### 2026-08-29 追記（渡す前に必ず足す条件）」節。
  **8-28 版だけを単独で渡さないこと**（追記の条件が抜ける）。
- 完成条件: その環境で `/brainstorm` 相当が動き、①会話を勝手に閉じない ②メモを毎ターン書き足す
  ③実装しない（成果物への書き込みが機械で止まる）④矛盾は1問テストで自律修正 ⑤圧縮対策の再注入
  が成立していること。
- **やってはいけないこと**: 環境間で本体を共有しようとしないこと（武田さん承認済みで独立が方針）。
  文言ルールだけで担保して「できた」と報告しないこと。
- 揃えるのはメモの書式だけ。判定の実装は各環境に任せる。

### 系統B: Claude 側の `brainstorm_guard.py` の修正

- 正本の依頼書: `wiki/builds/brainstorm-guard-fix-handoff-20260829.md`。
  **この1枚だけで作業できるように書いてあるので、実装エージェントにはこれを渡す。**
- 課題1: 封鎖側 `_candidate_paths()` の空白分割を、監査側 `_path_candidates()` と同じ
  貪欲一致方式へ直す。
- 課題2: メモの階層化（再帰探索＋既存3枚の移行）。**移動と修正は必ずセット。**
- **やってはいけないこと**: `brainstorm-gf2-dusevnyj-bikini-to-helen.md` の本文を要約・分割しない
  （武田さん本人の言葉と実測値が入っており復元できない）。封鎖を弱めて解決したことにしない。

### 捨てた案と理由（実装側が蒸し返さないため）

- 共通本体を `~/.agents/skills/brainstorm/` に一元化する案 → 却下。フックの入出力が環境ごとに
  違うため、共有しても変換層が要るだけで、独立させたほうが素直（武田さん判断）。
- アダプタの入出力仕様までこちらで決めて渡す案 → 却下。その環境の作法はその LLM のほうが詳しい。

## 関連リンク

- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-guard-fix-handoff-20260829]] — `wiki/builds/brainstorm-guard-fix-handoff-20260829.md`
- [[brainstorm-brainstorm-skill-design]] — `wiki/analyses/brainstorm-brainstorm-skill-design.md`
