---
type: concept
name: Nekojira 5 段階レンダリング・ワークフロー
aliases: [nekojira-rendering-workflow-5-stages, nekojira-workflow]
tags: [workflow, rendering, color, nekojira-stylebook]
sources: [coloso-nekojira-ch17-instructor-workflow, coloso-nekojira-ch23-color-sketch, coloso-nekojira-ch24-final-finishing]
---

# Nekojira 5 段階レンダリング・ワークフロー

## 定義

[[nekojira]] の **ch17(講師の作業工程に関する簡単な説明)** で確立される、彩色レンダリングを **5 段階に分解** したフレームワーク。「**難しい作業を段階に分け、1 度に 1 つの問題解決に集中**」する CPU 節約戦略の体系化。Section 05(ch18 以降)各章は、本ワークフローのいずれかの段階を深掘りする目次的構造になっている。

## 構成要素 / 主要な論点

### 0 段階(下準備)

- **3D モデルでライティング構成を決定**(肌の暗化・髪のベース色)
- **パーツ別レイヤー分け**(顔/髪/服/小物)
- **ベースカラー = 最も明るい色を 95%程度** に設定(マルチプライ前提)

### 1. 影の形(ch18 / ch20)

- **マルチプライ + ハードブラシ** で大胆に
- コントラスト(明暗・密度・大中小)
- **7:3 / 8:2 比率** で均等回避
- **影の形が決まったら線画レイヤーをオフにして形・構造が分かるか確認**

### 2. トーン(塔)の値(ch21)

- **暗めの色から始めて徐々に明るく**(調整余地確保)
- 微差(2-3%)で微細な変化
- **エッジ 4 段階**(シャープ/ファーム/ソフト/ロスト)で形と構造を表現
- **3 トーン**(ベース + 影 + ハイライト)で完了

### 3. 光と雰囲気(ch22)

- **セカンダリーライト** を加えて影に詳細
- **アンビエント vs ドラマチック** の選択
- **露出オーバー**(100% 白)で光感を演出
- **マルチプライ 30-40% の明度差**

### 4. 反射と素材(ch24 / ch25)

- **目・髪・服素材** の表現
- **影の縁に高彩度の色**(SSS)
- **上向き面に青を隠す**(天空光)
- **線画の色を変える**(高彩度赤や青)

## 関連

- [[grey-plus-local-high-saturation]] — Nekojira 色味哲学(各段階で適用)
- `base-color-brightest-rule` — 0 段階の核
- [[shadow-shape-design-4-principles]] — 1 段階(ch20)
- [[edge-4-levels]] — 2 段階(ch21)の核
- [[ambient-vs-dramatic-light]] — 3 段階(ch22)の核
- [[shadow-edge-high-saturation]] — 4 段階(ch24)の核
- [[shadow-area-via-occlusion-and-reflection]] — ye_jji の同等概念(c14 PDF 起点 6 工程)

## 解釈の揺れ / 異論

### ye_jji 色塗り 6 工程 との比較

| 段階 | ye_jji 6 工程 | Nekojira 5 段階 |
|---|---|---|
| 0 | (下準備、ベース) | 3D + ベース + パーツ分け |
| 1 | 工程 1: 影領域設定 | 影の形 |
| 2 | 工程 2: 中間トーン | トーンの値 |
| 3 | 工程 3: シャドウ詳細 | (光と雰囲気で扱う) |
| 4 | 工程 4: 髪・目 | 反射と素材 |
| 5 | 工程 5: テクスチャ | 反射と素材 |
| 6 | 工程 6: 背景 + 仕上げ | (4 段階で完結、別工程) |

- ye_jji は **被写体ベース**(各パーツ実装)、Nekojira は **要素ベース**(影/光/素材)で分割
- 順序は両者一致(形 → 値 → 光 → 素材)

## 出典

- [[coloso-nekojira-ch17-instructor-workflow]] — 起点、別キャラ実演で 5 段階を凝縮
- [[coloso-nekojira-ch23-color-sketch]] — 段階 0/1 の最終キャラ実装
- [[coloso-nekojira-ch24-final-finishing]] — 段階 2/3/4 の最終キャラ実装
