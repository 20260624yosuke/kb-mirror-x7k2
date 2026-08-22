---
type: build
title: Eグル旧重複アイテムの非破壊統合バッチ
created: 2026-07-07
sources:
  - x-eagle-duplicate-existing-item-discard-fix-2026-07-01
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-09
---

# Eグル旧重複アイテムの非破壊統合バッチ

## 目的

X→Eagle 保存拡張の現行挙動では、同じ画像を再保存しても新規アイテムを増やさず、既存アイテムへ
メモを非破壊追記し、フォルダ追加プラグイン経由でフォルダも足せる。
ただし、この仕組みが入る前にライブラリへ残っていた旧重複は分裂したままだったため、
**既存ライブラリ側を一回きりで現行ルールへそろえる**のが目的。

## 対象と前提

- 対象ライブラリ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library`
- 判定基準: **sha256 完全一致のみ**
- 対象件数(事前 dry-run):
  - 完全一致グループ 66 組
  - ゴミ箱移動対象の余分アイテム 69 件
- 残す側の選び方: `btime asc, mtime asc, item_id asc`
- 統合方針:
  - フォルダ: 和集合を残す側へ追加
  - タグ: 和集合を残す側へ追加
  - スター: 最大値を残す側へ反映
  - メモ: X由来の本文を上、その他は日付順で並べ、対処しづらい情報は
    `## 統合元メタデータ` として残す

## 実装

- 本体: `tools/eagle_dedup_merge.py`
- 既定動作: dry-run
- 実行前チェック:
  - Eagle起動確認 `/api/application/info`
  - フォルダ追加プラグイン確認 `GET http://127.0.0.1:48637/health`
- 書き込み順:
  1. `/api/item/update` で tags / annotation / star を更新
  2. `POST http://127.0.0.1:48637/add-to-folders` で folder を追加
  3. `/api/item/info` 再読込で反映確認後に `/api/item/moveToTrash`
- 監査ログ:
  - dry-run HTML/JSONL
  - execute JSONL

## 実行結果(2026-07-07)

- パイロット 3 組を先に実行し、武田さんが Eagle 実機で確認済み
- その後、残りを本処理
- 途中で **フォルダ和集合が空のグループ** でプラグインが
  `folders must not be empty` を返して停止
- `tools/eagle_dedup_merge.py` を修正し、空フォルダ群は no-op として正常処理へ変更
- 現状態から dry-run を切り直して再開

最終結果:

- 統合完了グループ: 66
- ゴミ箱移動完了: 66 回(`move-to-trash` の実行回数)
- 最終再スキャン: `groups=0`, `extras=0`

## 確認済みの事実

- `python3 -m py_compile tools/eagle_dedup_merge.py` 通過
- 3本の実行ログ合計で `move-to-trash = 66`
- 最終 dry-run で完全一致グループ 0 件
- ゴミ箱移動は Eagle のゴミ箱止まりで、空にする操作は未実施

## 主なログ

- パイロット実行:
  `tools/logs/eagle-dedup-merge-20260706-171005-execute.jsonl`
- 本処理途中(43組目で空フォルダ群に遭遇):
  `tools/logs/eagle-dedup-merge-20260706-171745-execute.jsonl`
- 修正後の最終実行:
  `tools/logs/eagle-dedup-merge-20260706-171903-execute.jsonl`
- 最終 0 件確認 dry-run:
  `tools/logs/eagle-dedup-merge-20260706-171948-dryrun.html`
  `tools/logs/eagle-dedup-merge-20260706-171948-dryrun.jsonl`

> [!note]
> ログ名の日付が `20260706` なのは、スクリプトのタイムスタンプが UTC 基準のため。
> 作業日自体は 2026-07-07(JST)。

## 位置づけ

- [[x-eagle-free-save-pilot]] は **今後の新規重複を増やさない入口**
- このページのバッチは **既に溜まっていた旧重複を後処理で片付ける一回きりの統合作業**
- 廉価LLMへ実行を渡す場合の終端ゲートは [[llm-cheap-model-execution-workflow]]。
  このバッチでは `groups=0 / extras=0` の最終再スキャンまで通って初めて完了扱いにする。

## 関連リンク

- [[x-eagle-free-save-pilot]]
- [[x-eagle-duplicate-existing-item-discard-fix-2026-07-01]]
- [[llm-cheap-model-execution-workflow]]
