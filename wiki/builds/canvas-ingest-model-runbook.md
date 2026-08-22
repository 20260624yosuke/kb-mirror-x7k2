---
type: build
title: Canvas ingest 実行ランブック(廉価モデル向け・お手本付き)
created: 2026-07-07
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-09
related:
  - "[[canvas-ingest-eagle-feedback-guide]]"
  - "[[art-canvas-ingest-design]]"
  - "[[llm-cheap-model-execution-workflow]]"
---

# Canvas ingest 実行ランブック(廉価モデル向け・お手本付き)

raw/_MY_ART の Canvas を1個ずつ取り込み、Eagle へ `実戦_使用済み` タグを返す作業の
**機械的な実行手順書**。優先順位・目的の説明は [[canvas-ingest-eagle-feedback-guide]]、
設計の正本は [[art-canvas-ingest-design]]。本書は「そのとおり打てば動く」ことに特化する。
[[llm-cheap-model-execution-workflow]] の成功例として、廉価モデルには機械実行だけを渡し、
バッチ後ゲートをハイエンド級へ戻す前提で使う。

## 役割分担(2026-07-07 武田さん決定)

- **実行**: 廉価〜ミドル級モデル(Sonnet / Haiku / Codex 等)。本書の手順を逸脱しない
- **抜き取り確認(ゲート)**: その時点のハイエンド推論モデル(現状 Opus 4.8 か GPT-5.5。
  Fable が定額利用可になれば Fable)。頻度は**毎バッチ後に短い確認**を基本
- 停止条件に当たったら**作業を止めてハイエンド級(または武田さん)へ報告**。自己判断で回避しない

## 絶対規則

1. **直列実行のみ。canvas_ingest の並行実行は禁止**。
   事故例: 2026-07-06 に3個並行実行 → 台帳(`wiki/canvas-registry.json`)が
   「全体読込→追記→全体書込」方式のため後書きが先書きを消し、2エントリが消失
   (2026-07-07 に修復)。1個終えて台帳エントリ数の増加を確認してから次へ
2. raw/ は読み取り専用。Canvas・添付画像の変更・削除は一切しない
3. Eagle への書き込みは `item_add_tags` の**タグ追加のみ**。タグは `実戦_使用済み` 1種類
4. タグは sidecar の `status == "confirmed"`(sha256一致)のみに付与。
   **candidate(画素類似どまり)には絶対に付与しない**
5. 書き込み前に必ず事前ログ JSON を保存し、書き込み後に item_get で検証する
6. `*__light.canvas` は取り込まない(正本の軽量複製)

## 事前確認

```bash
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
# Eagle アプリが起動しているか(タグ書き込みに必須。落ちていたら武田さんに起動を依頼):
node .claude/skills/eagle/scripts/eagle-api-cli.js call item_get --json '{"limit": 1}'
# 台帳の現在のエントリ数を控える:
python3 -c "import json; print(len(json.load(open('wiki/canvas-registry.json'))['entries']))"
```

## 手順(1 Canvas あたり)

`EAGLE_LIB` は常に
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library`

### 1. dry-run

```bash
python3 tools/canvas_ingest.py --taskb2 \
  --canvas "raw/_MY_ART/<年月>/<Canvas名>.canvas" \
  --eagle-library "$EAGLE_LIB" \
  --dry-run
```

期待される出力(お手本・2026-07-07 実行実績):

```
mode: phase1-taskB2
canvas_id: 2ba3554a0a63          ← 新規Canvasではdry-runと本実行でIDが変わる(正常)
canvas_id_resolution: new
markdown: …/wiki/sources/art-canvas-<id>.md
json: …/wiki/sources/art-canvas-<id>.usage.json
note: 44
related-to: 29
review_candidates: 8
dry_run: no files written
```

- **注意**: `canvas_id_resolution: new` の場合、dry-run に表示される canvas_id は仮。
  **本実行の出力に表示される canvas_id が正**(以後のファイル名はそちら)
- **停止条件**: エラーで終了する / `canvas_id_resolution` が `new` でも `path_match` でも
  ない値になり意図と合わない / 件数が明らかに異常(画像だらけの Canvas で note も
  related-to も 0 等)

### 2. 本実行

```bash
python3 tools/canvas_ingest.py --taskb2 \
  --canvas "raw/_MY_ART/<年月>/<Canvas名>.canvas" \
  --eagle-library "$EAGLE_LIB" \
  --update-index --update-log
```

再取り込み(既に art-canvas ページがある Canvas)の場合のみ、次を追加:
`--canvas-id <既存id> --previous-sidecar "wiki/sources/art-canvas-<既存id>.usage.json" --overwrite`

- **停止条件**: 整合性検査エラーで停止する / 出力に MD と sidecar の両方が出ない

### 3. 実行後の確認(次の Canvas に進む前に必須)

```bash
python3 - << 'PY'
import json
reg = json.load(open('wiki/canvas-registry.json'))
print('registry entries:', len(reg['entries']))   # ← 手順前より 1 増えていること(再取り込み時は同数)
d = json.load(open('wiki/sources/art-canvas-<id>.usage.json'))
conf = {m['item_id'] for a in d['assets'].values() for m in (a.get('eagle_matches') or []) if m['status']=='confirmed'}
cand = {m['item_id'] for a in d['assets'].values() for m in (a.get('eagle_matches') or []) if m['status']=='candidate'}
print('confirmed:', len(conf), '/ candidate:', len(cand))
PY
```

- **停止条件**: registry が増えていない(新規なのに) / sidecar が読めない

### 4. Eagle タグ付与(バッチ内の全 Canvas の ingest が終わってからまとめて)

1. バッチ内全 sidecar から `confirmed` の item_id を収集し重複排除する
2. **既にタグが付いている item を除外**する
   (`item_get --json '{"tags":["実戦_使用済み"],"limit":500}'` で現状を取得して差し引く)
3. 事前ログを `tools/eagle_sort_data/clip_full/tag_writeback_log_YYYYMMDD_<バッチ名>.json` に
   保存(書式は `tag_writeback_log_20260707_canvas_p2b1.json` を踏襲:
   `planned_at / operation / policy_note / tag / per_canvas_confirmed / batches[].item_ids / status`)
4. タグ付与:

```bash
node .claude/skills/eagle/scripts/eagle-api-cli.js call item_add_tags \
  --json '{"ids": ["ITEM_ID1", "ITEM_ID2"], "tags": ["実戦_使用済み"]}'
```

(Claude 系で Eagle MCP が使える場合は `item_add_tags` MCP ツールでも同じ)

5. 検証: 同じ ids で `item_get` し、全件に `実戦_使用済み` が付いたことを確認。
   事前ログの `status` を `executed` にし `executed_at` と検証結果を追記
- **停止条件**: 要求件数と付与済み件数が一致しない

### 5. log.md への記録

`## [YYYY-MM-DD] query | <バッチ名> 実戦_使用済みタグ付与` のエントリを追記し、
Canvas 名・canvas_id・confirmed 件数・付与件数・除外件数・writeback ログのファイル名・
触ったファイル全部を列挙する(お手本: log.md の
「[2026-07-07] query | 優先2バッチ1 3件の実戦_使用済みタグ付与」)。

## バッチ後のゲート(ハイエンド級の抜き取り確認手順)

1. **タグ照合**: Eagle の `実戦_使用済み` 全件を取得し、全 writeback ログの和集合と突合。
   ログ外の野良付与ゼロ・ログ済みでタグ無しはアイテム削除以外ゼロであること
2. **candidate 誤付与ゼロ**: 各 sidecar の candidate item_id にタグが無いこと
3. **MD/sidecar 整合**: 全 file/text node が source MD に掲載されていること
4. **note 抜き取り**: relation を数件サンプルし、evidence_span が Canvas 原文と一致・
   polarity/modality が規則どおり(明示否定=negative、「かも」=tentative、「?」=question)
5. 合否を log.md に `query | <バッチ名> 抜き取り確認(ゲート判定)` で記録。
   不合格なら次バッチへ進まず武田さんへ報告
(お手本: log.md「[2026-07-07] query | C1 優先1 抜き取り確認(ゲート判定・合格)」)

## 既知の挙動メモ

- Canvas は制作中の生きた資料なので、ingest 後に編集されることがある。再実行すると
  新規 relation が active 追加・消えた分は retracted になる(正常。churn ではない)
- 同一画像が Eagle に複数登録されている場合、`eagle_matches[]` に全件入る(全部 confirmed で正常)
- タグ済みアイテムが後日 Eagle から消えることがある(武田さんの重複整理)。
  ゲートのタグ照合で「ログ済みだが item_get 返却ゼロ」はアイテム削除であり異常ではない
- **Eagle インデックス不整合(2026-08-22 発見)**: まれに、ディスクには
  `images/<ID>.info/` が存在し `deleted: null`(削除ではない)なのに、グローバル
  `metadata.json` に ID が無く API(`item_get`/`item_add_tags`)でも取得できない
  アイテムがある。ingest 側は sha256 照合で confirmed を出せるため、タグ付与のときだけ
  「Item not found」で失敗する。削除と混同しないこと。対応は Eagle 再起動での
  インデックス回復を確認してからの再付与

## 進捗(2026-08-22 更新)

- 優先1(2026_07 水着系3個): 完了+ゲート合格
- 優先2: 完了(バッチ1+残り。`2026_05_30_アスナxアイドル衣装` のタグは backfill 済み)
- 優先3(無題系): **完了(2026-08-22)**。2026-07-12 バッチで 2026_06 分を処理済み、
  本日 2026_07 無題26〜57+アスナ(32枚)と 2026_08 全5枚を取り込み、ゲート合格。
  これで `raw/_MY_ART` の全 Canvas(142枚・うち `*__light.canvas` は規則#6で除外)の
  ingest が完了。今後は新規 Canvas を作った都度の単件 ingest に戻る
- **未解決**: Eagle インデックス不整合4件(MRDESBLGT7NWK / MOI4VOAXSI5PG /
  MQRDOJ4P2HU2O / MRPQJDC4VCZQ3)が未タグ。Eagle 再起動後に再照合・再付与すること

## 関連リンク

- [[canvas-ingest-eagle-feedback-guide]](目的・優先順位)
- [[art-canvas-ingest-design]](設計正本 v2.3)
- [[llm-cheap-model-execution-workflow]](廉価LLMへの渡し方の正本)
- `skills/canvas-ingest/SKILL.md`(スキル入口)
