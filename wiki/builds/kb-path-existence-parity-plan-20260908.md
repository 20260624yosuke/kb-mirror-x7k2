---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-08
---

# パスの実在照合を3ハーネスで揃える計画（2026-09-08）

作業ディレクトリ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

**これは計画書。実装はしていない。** 武田さんの承認「揃え方の計画を作る」（2026-09-08）による。
親メモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`

## 1. なぜ作るか（実際に起きた1件）

`wiki/analyses/brainstorm/gf2-helen-fingerfix/_index.md` の本文 `## 再開の入口（実パス）` の
3行目が、実在しないファイル（ファイル名 `f173-finger-flip-fix.json`）を指している。
同じ `logs/` に実在するのは `f173-gate-replay-test.json` と `f173-transfer-claim.json`。

このメモは **opencode の検査を通り、Claude の検査で落ちる**。実測（2026-09-08）:

```
python3 .opencode/scripts/muse_brainstorm_check.py check --parent wiki/analyses/brainstorm/gf2-helen-fingerfix/_index.md
結果: muse 関所: PASS

python3 ~/.claude/skills/brainstorm/brainstorm_guard.py audit-handoff --memo wiki/analyses/brainstorm/gf2-helen-fingerfix/_index.md
結果: H3 _index.md: パスが実在しません: …/logs/f173-finger-flip-fix.json
```

**同じ規則を、見る場所とタイミングが違う3つの実装で持っているのが原因。**

## 2. 現状（実ファイルを読んだ結果・2026-09-08）

| ハーネス | frontmatter の `entry_paths` | 本文の実パス | 走るタイミング | 実装 |
|---|---|---|---|---|
| Claude | 実在照合する（H6） | **実在照合する（H3）** | 会話を閉じるとき | `~/.claude/skills/brainstorm/brainstorm_guard.py` の `_check_reachability` / `_entry_paths` |
| Codex | しない | しない | — | `~/.codex/skills/brainstorm/scripts/codex_adapter.py` は `kb_guard` で `prose_guard.py` と `deliverable_path_guard.py guard-stop` だけを呼ぶ |
| opencode | 実在照合する | **しない** | 承認カードを出す直前 | `.opencode/scripts/muse_brainstorm_check.py` の `check_parent` |

補足の実測:

- `tools/deliverable_path_guard.py` に実在照合は無い（`exists` の呼び出しが 0 件）。
  あれは「応答本文にパスが**出ているか**」を見る検査で、「そのパスが**在るか**」は見ていない。
- `.opencode/plugins/skill-gate.js` が呼ぶ検査は `muse_brainstorm_check.py` と
  `display_check.py` の 2 本だけ。保管庫側の検査は 1 本も呼んでいない。
- Codex の Stop では `deliverable_path_guard.py guard-stop` だけが走る。
  `brainstorm_guard.py audit-handoff` に相当する呼び出しは無い。

## 3. 目的と完成条件

### 目的

**同じメモを、どのハーネスに読ませても同じ合否になる。** 武田さんの意図
「Claude での使用感を、kb フォルダを通したエージェントで再現する」の、パス到達性の部分。

### 最低限の完成条件

1. 上の `gf2-helen-fingerfix` のメモを、**Claude・Codex・opencode の3つとも FAIL にできる**。
2. 3つとも同じ理由（本文の実パスが実在しない）を返す。
3. 直したメモは 3つとも PASS になる。
4. 壊し試験（本文だけ死んでいる／frontmatter だけ死んでいる／両方生きている）を
   3ハーネス分 = 9 通り書き、すべて捕まえる。
5. 既存の検査の合否を1件も変えない（実在するメモ群で回帰を確認する）。

### 今回やらないこと

- コードが動くかを見る関所（層3）。これは別の判断。
- `gf2-helen-fingerfix` のメモの1行そのものの修正。担当外の案件で、
  正しい行き先を私は判断できない。**別に承認を取る。**
- 承認語・カード形式・本文量など、パス到達性以外の分岐。

## 4. 採る方法：同じ1本を呼ばせる

**規則を3か所に書き直させない。** 2026-08-31 に「Codex に Claude と合わせろと言って書き直させる」を
実行して週の 25% を成果ゼロで消費した記録があり、同じ規則を別々に書かせる限り再発する
（親メモ `## 捨てた案と理由`）。

置き場は既に用意されている。`tools/harness_compat/` は
「各ハーネスは、コピーせずここを呼ぶ」ための場所として 2026-09-05 に作られている
（`common.py` の冒頭コメント）。ただし既存の 3 ファイルは **I/O を持たない純関数**なので、
実在照合（ファイルを触る）を同じファイルに混ぜない。

### 作るもの（1本）

`tools/reachability_check.py`（新規・KB 側・ハーネス中立）

- 入力: `--memo <親メモの絶対パス>`
- 中身: `brainstorm_guard.py` の H1〜H4 と H6 を**移設**する
  （`_extract_paths` / `_resolve_path` / `_path_candidates` / `_check_reachability` / `_entry_paths`）。
- 出力: FAIL 行を日本語で 1 行ずつ。終了コード 0 = PASS、1 = FAIL、2 = 使い方誤り。
- `--selftest` を持たせ、壊し試験をこのファイル自身に入れる。

### 各ハーネスの繋ぎ（それぞれ数行）

| ハーネス | 変える場所 | 変え方 |
|---|---|---|
| Claude | `~/.claude/skills/brainstorm/brainstorm_guard.py` | `_check_reachability` / `_entry_paths` の中身を `tools/reachability_check.py` の呼び出しに置き換える。H5・H7 は現状のまま残す |
| Codex | `~/.codex/skills/brainstorm/scripts/codex_adapter.py` | 既にある `kb_guard` に 1 行足す（`kb_guard(["reachability_check.py", "--memo", <親メモ>], data)`）。呼び出しの器は 2026-09-07 に実機確認済み |
| opencode | `.opencode/scripts/muse_brainstorm_check.py` の `check_parent` | frontmatter の照合を残したまま、末尾で `tools/reachability_check.py` を呼び、その FAIL を連結して返す |

**移設であって、書き直しではない。** Claude の H3 は既に実データで働いている
（上の実測で `gf2-helen-fingerfix` を捕まえた）ので、そこを正本にする。

## 5. 手を付ける順番と、衝突の避け方

**`.opencode/` は別の会話が 2026-09-08 00:45 に控えを取って着手している**
（`.opencode/_restore/20260908-pre-opencode-parity/` は現行と同一＝まだ変更していない状態）。
2026-09-07 に同じ状況で 2 回、書き換えの衝突が起きている。

1. **`tools/reachability_check.py` を作る**（誰とも衝突しない。新規ファイル）。
   `--selftest` を通し、既存メモ群への回帰を確認する。
2. **Claude 側を差し替える**（`~/.claude/` は他の会話が触っていない）。
   差し替え後、`gf2-helen-fingerfix` が今までどおり FAIL することを確認する。
3. **Codex 側に 1 行足す**（`kb_guard` の器は実機確認済み）。
4. **opencode 側は最後**。着手中の会話が終わったことを確認してから触る。
   確認方法: `.opencode/scripts/muse_brainstorm_check.py` の更新時刻が
   2026-09-08 00:45 より新しくなっているか、武田さんに「あちらは終わったか」を聞く。

**1〜3 だけでも 2/3 が揃う。** 4 を待つ間に価値が出ない設計にはしない。

## 6. 壊し試験（9通り）

3ハーネス × 3パターン。すべてコピーの上で行い、本番のメモには触らない。

| # | パターン | 期待 |
|---|---|---|
| 1 | 本文の実パスが1件死んでいる | FAIL（理由: 本文のパスが実在しない） |
| 2 | frontmatter の `entry_paths` が1件死んでいる | FAIL（理由: entry_paths が実在しない） |
| 3 | どちらも生きている | PASS |

加えて、既存の穴を塞いだことの確認として:

- `done-when` ブロックの中のパスは**照合しない**（着手前は実在しないのが正しい。
  2026-09-07 に実際に起きた誤検知）。移設先でもこの除外が効くこと。
- コードフェンスの中のパスは照合対象のまま。
- `glob` や `<穴埋め>` を含むパスは丸ごと捨てる（切り詰めて別のパスに化けさせない）。

## 7. 失うもの

| 選ぶ道 | 失うもの |
|---|---|
| この計画どおり移設する | Claude の `brainstorm_guard.py` が外部ファイルに依存する。`tools/` が読めない環境では H1〜H4・H6 が働かなくなる（**素通りに倒す**設計にすること。止めない） |
| 移設せず、opencode だけに同じ検査を書き足す | 3つ目の実装が増える。次に規則が変わると、また 3 か所を直すことになる |
| 何もしない | 同じメモがハーネスによって通ったり止まったりする状態が続く。武田さんが原因不明の停止に当たり続ける |

## 8. 実装への申し送り

### 絶対にやってはいけないこと

- **`.opencode/` を、着手中の会話の終了を確認せずに触る。** 2026-09-07 に 2 回起きている。
- **`gf2-helen-fingerfix` の本文の 1 行を、推測で書き換える。** 正しい行き先が未確定。別承認。
- **検査基盤の異常で仕事を止める。** ファイルが無い・タイムアウト・起動失敗は**素通り**に倒す
  （`skill-gate.js` と `muse_brainstorm_check.py` の既存方針に合わせる）。
- 承認語・カード形式・本文量など、この計画の対象外の分岐に手を伸ばす。

### 捨てた案とその理由

- **各ハーネスに同じ規則を書き足す** … 2026-08-31 に実行して成果ゼロで週の 25% を消費した。
  同じ規則を別々に書かせる限り再発する。
- **`tools/harness_compat/common.py` に足す** … あそこは I/O を持たない純関数の場所。
  ファイルを触る検査を混ぜると、既存の 3 ファイルの前提が壊れる。

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/reachability_check.py
run: python3 "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/reachability_check.py" --selftest ==> PASS
run: python3 "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/reachability_check.py" --memo "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-fingerfix/_index.md" ==> 実在しません
```

### 終わったら次に取る承認

完成条件を満たしたら止まる決まりなので、次に聞くことを先に書いておく。

1. **`gf2-helen-fingerfix` の死んだ1行を、どう直すか。**（消す／`f173-gate-replay-test.json` に差し替える／
   `f173-transfer-claim.json` に差し替える／武田さんが指定する）
2. **opencode 側（手順4）に進んでよいか。** 着手中の会話の状況を確認したうえで。
3. **層3（コードが動くかを見る関所）を作るか。** 今回の対象外。別の判断。

## 9. 関連ファイル（実パス）

- 親メモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`
- 説明ページ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260908-opencode-environment-and-bugs.html`
- Claude の実装: `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`
- Codex の実装: `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py`
- opencode の実装: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/muse_brainstorm_check.py`
- opencode の関所: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/plugins/skill-gate.js`
- 共有部品の置き場: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/harness_compat/common.py`
- 分岐を数える監査: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/harness_parity_check.py`
