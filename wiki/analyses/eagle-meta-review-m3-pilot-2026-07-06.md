---
type: analysis
title: Eagle M3 meta-review 実装 + 絵柄4棚パイロット dry-run
created: 2026-07-06
prompted_by: 引き継ぎ資料の第2期キュー M3 を進め、dry-run確認ページができたら承認待ちで止まる
sources:
  - codex-handoff-eagle-clip-operations
  - eagle-meta-tags-design
  - eagle-clip-tag-runbook
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-06
---

# Eagle M3 meta-review 実装 + 絵柄4棚パイロット dry-run

## 問い

M3 の `meta-review` を `tools/eagle_meta_tags.py` に実装し、監修済み `meta_dict.json` を使って
絵柄4棚(ひづるめ・モ誰・MX2J・米山舞)の dry-run 確認ページを生成する。

## 回答(要約)

`tools/eagle_meta_tags.py` に `meta-review` サブコマンドを追加した。監修済み辞書の
`adopt=true` / `author_rules_pruned` / `keyword_rules` / `keyword_mode` を読む実装で、
Eagle への書き込みは行わず HTML/JSON の確認ページだけを出す。

今回の絵柄4棚パイロットでは、`MX2J` 390枚、`モ誰` 163枚、`米山舞` 31枚の新規候補を検出し、
100枚チャンクの確認ページを生成した。`ひづるめ` は新規候補0枚で、確認ページは生成されなかった。

## 詳細

### 実装

- `tools/eagle_meta_tags.py`
  - `meta-review` サブコマンドを追加
  - `meta_dict.json` の `adopt=true` 棚のみ対象
  - author 判定は `author_rules_pruned`
  - keyword 判定は `name + annotation` の部分一致(大文字小文字無視)
  - `keyword_mode=with-work-context` 用に、既存フォルダの重なりから対応作品コンテキストを推定する処理を追加
  - 出力先は既存 review と同じ `tools/eagle_sort_data/clip_full/`、画像コピー方式も既存 `tag-review` と同型

### 棚別結果

- `MX2J`: 新規候補390枚、既存メンバー339枚
- `モ誰`: 新規候補163枚、既存メンバー611枚
- `米山舞`: 新規候補31枚、既存メンバー76枚
- `ひづるめ`: 新規候補0枚、既存メンバー189枚

### 確認ページ

- `tools/eagle_sort_data/clip_full/metareview-絵柄-MX2J_01.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-MX2J_02.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-MX2J_03.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-MX2J_04.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-モ誰_01.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-モ誰_02.html`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-米山舞_01.html`

対応する JSON:

- `tools/eagle_sort_data/clip_full/metareview-絵柄-MX2J_01.json` 〜 `_04.json`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-モ誰_01.json` 〜 `_02.json`
- `tools/eagle_sort_data/clip_full/metareview-絵柄-米山舞_01.json`

## 実際に確認できたこと

- `python3 -m py_compile tools/eagle_meta_tags.py` 成功
- `python3 tools/eagle_meta_tags.py meta-review --help` で CLI が表示されることを確認
- M3 dry-run コマンドを実行し、上記 HTML/JSON が実際に生成されたことを確認

## 未完了 / 承認待ち

- まだ Eagle へは1件も書き戻していない
- 次の停止点は、武田さんが dry-run 確認ページを見て承認すること
- 承認後にのみ runbook ④〜⑥(事前ログ保存 → `item_add_tags` → 検証 → 記録)へ進む
