---
type: source
title: ヘレン「星夜のワルツ」3D資料 成果物と検証記録
authors: [Codex]
date: 2026-07-15
source_path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/README-ja.md; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/source-manifest.md; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/import-report.json; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/bone-report.json; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/render-manifest.json; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/blend-verification.json; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/eagle-selection.md; /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/helen-shape-analysis.md
ingested: 2026-07-15
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-15
tags: [gf2, helen, starlit-waltz, blender, mmd, renders, verification]
---

# ヘレン「星夜のワルツ」3D資料 成果物と検証記録

## 要約

ヘレン「星夜のワルツ」の公式配布 PMX を Blender へ導入し、**中立版 + 観察ポーズ版 + 20 枚の
レンダー + 検証レポート群**として保存した時点の成果物記録。Blender 画面は日本語化済みで、
全ビューポートがマテリアルプレビューで開くよう保存されている。

## ソース内の主要主張

- 配布元は `https://www.aplaybox.com/details/model/mS5BtSoHrE7l`。
- 元アーカイブは RAR 5、SHA-256 は
  `b0a75f1a9e5ee3babc26b213c372c0fcbfa3666c443fcbe00bfec82cfb9ed366`。
- Blender は `4.5.11 LTS`、MMD Tools は `4.5.13`。
- PMX は `GirlsFrontline HelenSSR0101.pmx`、物理演算なしで読み込み済み。
- 骨数は `432`。観察ポーズで変わる骨は `右ひじ / 右腕 / 左ひじ / 左腕` の 4 本だけ。
- 2048x2048 PNG を `neutral` と `observation-30deg` の 2 状態 × 10 方向、合計 20 枚出力済み。
- 中立版 `helen-neutral.blend` の SHA-256 は再撮影前後で一致し、観察ポーズ版の再生成でも
  20 枚だけが更新される。
- Eagle へ手動登録する推奨 8 枚は選定済みだが、**実際の Eagle 登録完了だけ未確認**。

## 抽出されたエンティティ

- `Blender 4.5.11 LTS`
- `MMD Tools 4.5.13`
- `GirlsFrontline HelenSSR0101.pmx`
- `helen-neutral.blend`
- `helen-observation-30deg.blend`
- `helen-reference-capture.py`
- `Eagle`

## 抽出された概念

- マテリアルプレビュー固定
- 中立版を壊さない再撮影
- 観察ポーズの差分最小化
- 角度別 20 枚レンダー
- 作画資料としての推奨 8 枚選定

## 不確実・要確認

- `spa` 参照は PMX 内の既知の欠落扱いとして残るが、主要材質は正常表示されている。
- Eagle への手動登録と再表示確認は、成果物側の記述では未完了。

## 関連リンク

- [[gf2-helen-starlit-waltz-mmd-quickstart-investigation-2026-07-15]]
- [[gf2-helen-starlit-waltz-3d-materialization-plan-2026-07-15]]
- [[gf2-helen-starlit-waltz-3d-reference-build]]
