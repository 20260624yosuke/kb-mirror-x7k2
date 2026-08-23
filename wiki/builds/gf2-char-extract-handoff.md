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

## 1. 目的と現在地（2026-08-23 時点）

- **目的**: 光の再現と独立に、ドルフロ2原作データから複数キャラの形（メッシュ＋骨＋テクスチャ）をBlenderへ抽出する量産導線を作る。光・輪郭線・RampMap・アニメは全て deferred
- **経緯**: ヘレンの光再現が停止中（[[gf2-helen-lighting-diagnosis-summary-20260823]]）のため、武田さんが並列抽出を提案 → hold で方針v2・計画v2.1が承認（2026-08-23）
- **現在位置**: **Step 0 実行中**（在庫台帳）

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
| `scripts/00b_needle_bundle_scan.py` | 全local bundle 展開レベル走査（pass1=ベース名、pass2=族のvariant） | **実行中** |
| （次）`scripts/00c_classify_assets.py` | ヒットbundleのオブジェクト級パース → 依存種別ごとの在庫分類 | 未着手 |
| （次）陽性対照（Helen期待値表）・陰性対照 | `ledger/inventory-controls.json` | 未着手 |

- 対象範囲の機械的定義: `(SSR|SR|UR)\d{2,}` バリアント行を持つベース族のみ（113族・敵NPC除外）。この絞り込みは台帳 `needle-bundle-scan.json` の `scoping_rule` に記録される
- Python 方針: UnityPy/lz4 系は `/opt/anaconda3/bin/python3` 3.11.7 固定（lz4 なし環境では欠損断定をせず停止）。stdlib のみの新規スクリプトは python3 3.14

## 4. 既知の限界・未決

- ベース名のバイト部分一致のため親族偽ヒットがあり得る（Nagant⊂Mosinnagant 等）。族帰属の確定は 00c のオブジェクト級パースで行う
- ModelConfigData の protobuf スキーマは未解読（文字列フィールドの収集のみ）。行↔依存の正式な対応表が必要になればスキーマ解読へ拡張する
- ヘレン自身の衣装材質は原作に欠損（既知）→ 台帳では partial が正しい期待値

## 5. 触ったファイル（作業単位で追記）

- 新規: `gf2-char-extract/` 全体（quality-gate.json・scripts/common_ce.py・00a・00b・ledger/model-config-strings.json・model-name-candidates.json）
- 変更なし（read-only 遵守）: `gf2-helen-starlit-waltz/`、ゲームデータ一式

## 6. 変更履歴

- **2026-08-23**: 作成。計画v2.1承認を受け実装開始。品質ゲート plan PASS。00a完走・00b実行中。
