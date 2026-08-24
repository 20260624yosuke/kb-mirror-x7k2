---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
sources:
  - gf2-helen-repro-v51-handoff
---

# 引き継ぎ資料 — GF2 CHAR EXTRACT（他キャラ原作抽出・並列）

> [!info] 正本の所在
> このページがセッション横断の引き継ぎ正本（[[gf2-helen-repro-v51-handoff]] の wiki 移行と同じ運用）。
> プロジェクト側 `run-state.json` の `handoff_file` がこのページを指す。
> **段階追記型**： 各Step完了・検証器合否が出た時点で追記する（死亡セッションの完了主張を正本にしない教訓）。

## 0. まず読むもの

| # | 何 | どこ |
|---|---|---|
| 1 | **このファイル** | wiki `wiki/builds/gf2-char-extract-handoff.md` |
| 2 | 承認済み計画書 v2.1 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/PLAN-CHAR-EXTRACT-2026-08-23.md` |
| 3 | 機械可読現在位置 | `gf2-char-extract/run-state.json` |
| 4 | 品質ゲート | `gf2-char-extract/quality-gate.json`（plan PASS 済み） |

## 1. 目的と現在地（2026-08-24 時点）

- **目的**: 光の再現と独立に、ドルフロ2原作データから複数キャラの形（メッシュ＋骨＋テクスチャ）をBlenderへ抽出する量産導線を作る。光・輪郭線・RampMap・アニメは全て deferred
- **経緯**: ヘレンの光再現が停止中（[[gf2-helen-lighting-diagnosis-summary-20260823]]）のため、武田さんが並列抽出を提案 → hold で方針v2・計画v2.1が承認（2026-08-23）
- **現在位置**: **Step 0 完了（対照試験 ALL PASS）**。次は Step 1（抽出ドライバ `10_extract_char.py`）

### Step 0 の主な実測結果（2026-08-24）

- 対象: レアリティ行を持つ族 113族（敵NPC除外）。うち **complete_shape=69族**
- 走査: local bundle 18,568本 → UnityFS 13,536本を展開レベル走査、ヒット5,321ファイルをオブジェクト級でパース（75,115オブジェクト・エラー0）
- 陽性対照(Helen): complete_shape・face有り・dorm有り・mesh138/tex126/anim698 — 既知事実と一致。衣装材質の少なさ(mat=8 vs Sabrina45)も既知の欠損と整合
- 陰性対照: 実在しない名前は0ヒット。**両対照とも ALL PASS**（`ledger/inventory-controls.json`）
- 成果物: `ledger/char-inventory.json`・`reports/inventory-summary.md`・`ledger/needle-bundle-scan.json`
- 既知の限界: AFS2/CRI コンテナ5,032本は生バイト走査のみ（中身未走査）。RX_/Summon系の設定行は本体assetを別名で共有している可能性（partial/no_assets の主因）

## 2. 承認履歴

| 日付 | カード | 結果 |
|---|---|---|
| 2026-08-23 | 方針①「光と独立に他キャラの形抽出を並列で始める」 | 詳細検討のうえ再提出を指示 |
| 2026-08-23 | 方針v2「在庫台帳→ドライバ→パイロット2体→バッチ」 | **承認** |
| 2026-08-23 | 計画② | 「機械検証スクリプト必須・抜けチェック」を指示 → サブエージェント監査（major 6/minor 7）→ v2 へ反映 |
| 2026-08-23 | 計画v2 | 「引き継ぎ資料を作れ」を指示 → v2.1 へ反映 |
| 2026-08-23 | 計画v2.1 | **承認（実装開始）** |

## 3. 実装メモ（スクリプト構成）

| スクリプト | 役割 | 状態 |
|---|---|---|
| `scripts/common_ce.py` | パス定数・インタプリタ/lz4 関所 | 完了 |
| `scripts/00a_extract_model_names.py` | ModelConfigData.bytes の汎用 protobuf ウォーク → 文字列宇宙・バリアント名 | 完了（walk_completed=true） |
| `scripts/00b_needle_bundle_scan.py` | 全local bundle 展開レベル走査（needle全449名×トークン集合積・単一パス） | 完了（pipeline_valid=true） |
| `scripts/00c_classify_assets.py` | ヒットbundleの UnityPy オブジェクト級パース → 族ごと分類（最長一致帰属） | 完了（75,115 objs・エラー0） |
| `scripts/00d_controls_summary.py` | 陽性対照(Helen期待値表)・陰性対照・サマリ生成 | **完了（ALL PASS）** |
| （次）`scripts/10_extract_char.py` | 抽出ドライバ（Step 1） | 未着手 |
| （次）`scripts/20_diff_char_blend.py` | blend対原作の機械突合（Step 1・計画書の比較項目テーブルどおり） | 未着手 |

- 対象範囲の機械的定義: `(SSR|SR|UR)\d{2,}` バリアント行を持つベース族のみ（113族・敵NPC除外）
- Python 方針: UnityPy/lz4 系は `/opt/anaconda3/bin/python3` 3.11.7 固定＋`.python-deps`（新プロジェクトに複製済み）。stdlib のみの新規スクリプトは python3 3.14
- 実装上の教訓（再発防止）: ①macOS の multiprocessing は spawn — worker 用グローバルは import 時に構築する ②asset名は `c_<Char>_slg_...` の `_` 区切りで現れるため、トークン照合は `_` 分割部分も集合に入れる（この抜けで Helen のヒットが12→92ファイルに変わった実測あり）

## 4. 既知の限界・未決

- ベース名のバイト部分一致のため親族偽ヒットがあり得る（Nagant⊂Mosinnagant 等）。族帰属の確定は 00c のオブジェクト級パースで行う
- ModelConfigData の protobuf スキーマは未解読（文字列フィールドの収集のみ）。行↔依存の正式な対応表が必要になればスキーマ解読へ拡張する
- ヘレン自身の衣装材質は原作に欠損（既知）→ 台帳では partial が正しい期待値

## 5. 触ったファイル（作業単位で追記）

- 新規: `gf2-char-extract/` 全体（quality-gate.json・run-state.json・scripts/common_ce.py・00a〜00d・`_inspect_00b.py`(開発用)・ledger/{model-config-strings,model-name-candidates,needle-bundle-scan,needle-variant-hits,char-inventory,char-inventory-objects,inventory-controls}.json・reports/inventory-summary.md・logs-00b/00c-run.log(実行ログ)）
- 新規（wiki）: このページ
- 変更なし（read-only 遵守）: `gf2-helen-starlit-waltz/`、ゲームデータ一式

## 6. 変更履歴

- **2026-08-24**: Step 0 完了。00b を needle全集合×トークン(`_`分割含む)方式へ作り直し（Helen 12→92ファイルの改善）。00c 完走（75,115 objs）、00d 対照試験 ALL PASS。サマリ生成。
- **2026-08-23**: 作成。計画v2.1承認を受け実装開始。品質ゲート plan PASS。00a完走・00b実行中。
