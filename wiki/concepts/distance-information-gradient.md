---
type: concept
name: 距離による情報量の段階化
aliases: [distance-information-gradient, 情報量グラデーション]
tags: [composition, distance, density, background]
sources: [coloso-ye-jji-ch16-highlight, coloso-ye-jji-ch20-background]
---

# 距離による情報量の段階化

## 定義

物体の **距離に応じて描写の階調数・ディテール量を段階的に変える** ルール。手前は多階調・高ディテール、遠ざかるほど階調数が減り、最遠景はシルエット 1 階調のみになる。

## 構成要素 / 主要な論点

| 距離 | 階調 | ディテール |
|---|---|---|
| 手前(主題部近傍) | 多階調 + アウトライン | キャストシャドウまで描く |
| 中距離 | 1〜2 階調 | シルエット程度 |
| 遠景 | 1 階調 | 大気色に溶けたシルエットのみ |

- アウトライン機能を **手前だけ** にかけ、後方では使わない → 完結感の差を意図的に作る([[outline-function-foreground]])
- 細かい波・葉などの **省略判断** をこの原則で下す

## 関連

- [[atmospheric-perspective]] — 距離による色の変化
- [[mitsudo-management]] — 全体としての密度設計
- [[shi-sen-yu-dou]] — 視線を主題部に集める

## 出典追加 (2026-05-17): ch16 の中間トーン工程での起点

- **ch16(明部の描写)**: 雲とヤシで「近 = 多階調 / 中 = 少階調 / 遠 = シルエットのみ」を **中間トーン工程で既に実装**。ch20 で全面体系化される前の **実装プロトタイプ** が ch16 に存在することが確認できる

## 出典

- [[coloso-ye-jji-ch16-highlight]] — 中間トーン工程での起点
- [[coloso-ye-jji-ch20-background]] — 背景章での体系化
