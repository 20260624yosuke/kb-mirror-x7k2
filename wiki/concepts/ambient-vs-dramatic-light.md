---
type: concept
name: アンビエント vs ドラマチック光(Nekojira)
aliases: [ambient-vs-dramatic-light, ambient-light, dramatic-light, lighting-strategy]
tags: [lighting, ambient, dramatic, atmosphere, nekojira-stylebook]
sources: [coloso-nekojira-ch22-lighting-mood, coloso-nekojira-ch17-instructor-workflow]
---

# アンビエント vs ドラマチック光(Nekojira)

## 定義

[[nekojira]] の **ch22(ライティングと雰囲気の演出)** で確立される、ライティングの **二項軸**。すべての光は **アンビエント(常に存在する環境光)** と **ドラマチック(人工的な強い直接光)** の組み合わせで表現される。

## 構成要素 / 主要な論点

| タイプ | 特徴 | 用途 |
|---|---|---|
| **アンビエント** | あらゆる方向、明確な光原なし、薄くぼやけた影、細部が見える | キャラクターの **ベースカラーやテクスチャを見せたい** 時 |
| **ドラマチック** | 強い直接光、強い影、細部が大きな形に融合、露出オーバー | **光と雰囲気を強調** したい時 |

### アンビエントの基本原則

1. **白色は反射率が高く環境色を拾いやすい**
2. **暗い色は光を吸収** = 反射少なめ
3. **影や中間調で基本色が覆われやすい** → 環境色が強い場合は影が環境色一色

### ドラマチックの基本原則

- **メインライト 1 つ + 補助ライト + リフレクタボード**(色付き)
- **露出オーバー** = カメラ的な光感
- 明部の細部が **消える** → 形に集中

### 両者の重ね方

- 「**環境校は常に存在する柔らかな光**」「**劇的な証明は環境にとって変わらず上に重ねられる**」(ch22_05 19:30)
- → アンビエント(下層) + ドラマチック(上層)の **多層モデル**

### マルチプライによる影付け規則

- **30°-40% の明度差**(影 = 主光の 70% 程度)
- これは「**さらなる変化を加えるための余裕**」を確保するため

## 関連

- [[nekojira-rendering-workflow-5-stages]] — 段階 3(光と雰囲気)の核
- [[atmospheric-perspective]] — ye_jji の空気遠近、関連
- [[shadow-area-via-occlusion-and-reflection]] — ye_jji 影観、関連
- [[grey-plus-local-high-saturation]] — Nekojira 色味哲学、強アンビエントで活用
- [[shadow-edge-high-saturation]] — SSS による影の縁の赤み、ドラマチック光の応用
- `secondary-light-overlay` — 2 次光をベースに重ねる色相モデル

## 解釈の揺れ / 異論

- ye_jji 講座は **「光の 3 種類」**([[hikari-no-3-shurui]]、broken link)で類似テーマを扱うが、Nekojira の **二項軸** とは粒度が異なる
- 「**強い光がない場合(曇り空)は緑の環境でも肌に緑が影響しない**」(ch22_05 19:13) は **光の強度と反射量の比例関係** を示す重要な原則 → これは [[saido-no-3-points]](SSS)の前提でもある

## 出典

- [[coloso-nekojira-ch22-lighting-mood]] — 起点、5 環境実演で確立
- [[coloso-nekojira-ch17-instructor-workflow]] — 別キャラ実演で先取り
