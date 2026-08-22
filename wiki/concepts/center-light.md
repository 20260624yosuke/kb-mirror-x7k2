---
type: concept
name: センターライト(Center Light)
aliases: [center-light, センターライト, 最明部]
tags: [light, volume, ye-jji, chiaroscuro]
sources: [coloso-ye-jji-ch04-volume, coloso-ye-jji-ch05-texture-basic]
---

# センターライト(Center Light)

## 定義

**入射角と面の傾きが 90° を成す地点**(c04 PDF p3)。つまり、光線が面に対して垂直に当たる位置で、ランバート余弦則の **明度のピーク(100%)**。物体表面の **固有色(local color)が最も鮮明に出る最明部**。鏡面反射である [[highlight-position|ハイライト]] とは **明確に区別される**。

## 構成要素 / 主要な論点

- **物理的位置**: 光源から物体表面へ垂線を引いたときの接触点
- **明度値**: c04 PDF p2 の角度→明度% 表で 90° = 100%
- **ハイライトではない**: センターライトは「最も光が当たる地点」、ハイライトは「鏡面反射が起こる地点」。両者は **しばしば近くにあるが一致しない**(→ [[highlight-position]])
- **固有色の現出地点**: 物体本来の色(明度・彩度・色相)が最も忠実に見える位置。物体の色を判定するときの基準点

## ye_jji 流の扱い方

- ch04 で陰影 7 要素のうち **明部側 1 要素** として位置付け
- ch05 で「**ハイライトの位置はセンターライトではない**」と明確に対比して提示。多くの学習者がハイライトをセンターライトの位置に置いてしまうミスを指摘
- ch16(明部回)では中間トーン描写の基準点として扱われる
- センターライトを描き始めるとき、**過剰に白くしない** ことが暗黙の前提(ハイライトと混同しない)

## 関連

- [[ryou-kan]] — 親概念
- [[mid-tone]] — センターライトから始まる遷移帯
- [[highlight-position]] — 別概念(鏡面反射)、混同注意
- [[lambert-cosine-law]] — 明度ピークの定義根拠
- [[saido-no-3-points]] — 高彩度ピークの 1 つはセンターライト近傍ではなく明暗境界に来る原理

## 解釈の揺れ / 異論

- 古典絵画では「light area」「direct light」と呼ばれることが多く、「center light」という用語自体は ye_jji 流の整理に近い
- 3DCG では Lambert ピーク = N·L = 1.0 の点で、これに鏡面反射(Blinn-Phong, GGX 等)が加算される

## 出典

- [[coloso-ye-jji-ch04-volume]] — 陰影 7 要素の明部要素として定義
- `raw/_coloso/01_coloso_ye_jji/c04_要約ノート.pdf` p3(「入射角と 90 度を成す地点」)
- [[coloso-ye-jji-ch05-texture-basic]] — ハイライトとの違いを明示
