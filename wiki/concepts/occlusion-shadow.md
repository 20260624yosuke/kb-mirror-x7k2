---
type: concept
name: オクルージョンシャドウ(Occlusion Shadow)
aliases: [occlusion-shadow, ambient-occlusion, AO, シャドウドッグ]
tags: [shadow, occlusion, lighting, fundamentals]
sources: [coloso-nekojira-ch14-wrinkle-practice, coloso-nekojira-ch18-lineart-occlusion, coloso-nekojira-ch24-final-finishing]
---

# オクルージョンシャドウ(Occlusion Shadow)

## 定義

**2 つの物体が近接して光が入りにくい狭い隙間ができる場合に生じる最も暗い部分**(ch18 6:15)。3D レンダリングでは Ambient Occlusion(AO)として知られる。[[nekojira]] は本概念を **線画段階で先取りして「シャドウドッグ」と呼び**(ch14)、ch18 でレンダリング技法として正式化、ch24 で最終キャラに実装する。

## 構成要素 / 主要な論点

### 発生箇所

| 例 | 説明 |
|---|---|
| 指と指の間 | 近接した 2 つの円柱の隙間 |
| 胸の谷間 | 2 つの曲面の接近部 |
| 髪の重なり | 髪束の重なり目 |
| スカートの折り返し | 布の近接した折り目 |
| ボールと床の接地点 | 球と平面の接点近傍 |

### 線画での表現

- **点 + 三角形 + 直線** のコンビ
- **線を完全につなげず、隙間を残す**(柔らかさ表現)
- 「**全てを先で書くのではなく観客の想像力のための余白を残す**」(ch18 9:48)

### レンダリングでの表現

- **影の中で最も濃いトーン** を割り当てる
- ye_jji の **暗部はオクルージョン + 反射光で描く** 原則と統合
- マルチプライ + キャストシャドウより **濃い色**

### Nekojira の「シャドウドッグ」と「オクルージョンシャドウ」

- ch14 で「**シャドウドッグ**(オクルージョンシャドウ)」と命名 → ch18 で **オクルージョンシャドウ** に統一
- 本講座では Nekojira の **線画哲学の核** となる

## 関連

- [[lineart-3-principles-nekojira]] — ch18 の 3 原則の影の核
- [[shadow-area-via-occlusion-and-reflection]] — ye_jji の同じ概念、ch14 PDF 起点で「暗部はオクルージョン + 反射光で描く」
- [[occlusion-shadow-as-mid-contrast]] — ye_jji 概念、線/塗りの統一視
- [[gap-as-line-soft-edge]] — 隙間+点で柔らかさ表現
- [[line-as-shadow-deformation]] — ye_jji 概念、線画 = 影のデフォルメ

## 解釈の揺れ / 異論

- ye_jji と Nekojira は **同じ物理現象を異なる用語** で呼ぶ:
  - ye_jji: 「**暗部はオクルージョン + 反射光で描く**」(c14 PDF p4)
  - Nekojira: 「**オクルージョンシャドウ**」「**シャドウドッグ**」
- 両者とも **ミッドトーンやキャストシャドウより重要な暗部の核** として扱う
- Nekojira は **線画段階での先取り** を強調する点が独自

## 出典

- [[coloso-nekojira-ch14-wrinkle-practice]] — 「シャドウドッグ」として先取り言及
- [[coloso-nekojira-ch18-lineart-occlusion]] — 正式に概念化、線画 3 原則の核
- [[coloso-nekojira-ch24-final-finishing]] — 最終キャラのオクルージョン点として実装
