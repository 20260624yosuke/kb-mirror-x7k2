---
type: concept
name: 影の縁に高彩度を隠す(Nekojira)
aliases: [shadow-edge-high-saturation, hidden-saturation-shadow, sss-edge-warm, sss-shadow-edge-warm]
tags: [shadow, saturation, sss, terminator, nekojira-stylebook]
sources: [coloso-nekojira-ch17-instructor-workflow, coloso-nekojira-ch22-lighting-mood, coloso-nekojira-ch24-final-finishing]
---

# 影の縁に高彩度を隠す(Nekojira)

## 定義

[[nekojira]] の **最も特徴的な彩色技法**(ch17 で初出、ch22/24 で深掘り)。影の **縁(明暗境界、ターミネーター)** に **サイドの高い色(主に赤・オレンジ)** を隠すことで、**SSS(サブサーフェススキャタリング)効果** + **光の存在感** + **色変化の豊かさ** を同時に実現する。

## 構成要素 / 主要な論点

### 原理

- 「**強い光が皮膚を投化して散乱し、影の縁に赤みを作り出す**」(ch17_02 11:57)
- これは **物理的に正しい SSS の単純化**
- **影の中ではなく、影と光の境界部** に高彩度を置く

### 適用箇所(ch24 から)

| 部位 | 隠す色 |
|---|---|
| 鼻の影の縁 | 高サイド赤 |
| 鎖骨周辺 | 高サイド赤・オレンジ |
| 肌の影の縁 | 高彩度の影色(SSS) |
| 胸の谷間影縁 | 高サイド赤 |
| ベルトの暗い縁 | 高サイド青 |
| 髪の毛の影の縁 | 鮮やかな青 |

### ターミネーターの認識

- 「**シャツの影の端は実はターミネーターというかっこいい名前**」(ch17_02 13:34)
- 「**ターミネーター = 影の境界**」 → ここで **明るさが少し戻りつつサイドが高くなる**
- 物理的には **光が形態に対して接線的になる帯**

### 「赤色 → 寒色」のグラデーション

- 「**徐々に赤色に近づくに連れて赤みを帯びていきますが、色層は完触系のまま**」(ch17_02 13:02)
- これは **温色を寒色のグラデーションに隠す** 反直感的な手法

### Nekojira スタイルの色味哲学との関係

- [[grey-plus-local-high-saturation]] の **局所高彩度** の最も典型的な実装箇所
- **大部分はグレー基調、影の縁だけ高彩度** = グレーとの対比で映える

## 関連

- [[grey-plus-local-high-saturation]] — Nekojira 色味哲学、本技法の親
- [[saido-no-3-points]] — ye_jji の高彩度 3 ポイント(SSS / 明暗境界 / 同系色光)で機構が一致
- 旧 slug `sss-shadow-edge-warm` — 同じ概念のサブセット(本概念へ吸収済み。frontmatter の aliases 参照)
- [[ambient-vs-dramatic-light]] — ドラマチック光下で SSS が特に映える
- [[nekojira-rendering-workflow-5-stages]] — 段階 4(反射と素材)の核

## 解釈の揺れ / 異論

- ye_jji の [[saido-no-3-points]] と **物理機構は同一** だが:
  - ye_jji: **3 つの場所**(SSS / 明暗境界 / 同系色光)を整理して紹介
  - Nekojira: **影の縁 1 つに集中** して反復実装
- → Nekojira は **絞り込んだ反復** で習得しやすく、ye_jji は **体系化** で全体像
- Photoshop / Procreate での実装は **影レイヤーの透明度ロック + 高彩度色を縁に塗る** → 簡単な再現性

## 出典

- [[coloso-nekojira-ch17-instructor-workflow]] — 起点、別キャラの肌で初実装 + 命名(ターミネーター)
- [[coloso-nekojira-ch22-lighting-mood]] — ライティングと雰囲気で複数環境に適用
- [[coloso-nekojira-ch24-final-finishing]] — 最終キャラの仕上げで集大成的に実装
