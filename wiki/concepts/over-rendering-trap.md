---
type: concept
name: 過剰レンダリングの罠(Nekojira)
aliases: [over-rendering-trap, brain-fog-rendering, painterly-trap]
tags: [rendering, anti-pattern, simplification, nekojira-stylebook]
sources: [coloso-nekojira-ch21-color-value, coloso-nekojira-ch24-final-finishing]
---

# 過剰レンダリングの罠(Nekojira)

## 定義

[[nekojira]] の **ch21(色彩明度)** で警告される **アンチパターン**。**敷張(色調)変化を多重に重ねるとフラット感が失われ、線画の情報が消える** という現象。講師自身の初心者時代の失敗として開示される。

## 構成要素 / 主要な論点

### 症状

- **線画の情報が見えにくくなる**
- **色張の情報が目立ちすぎて細部が混乱**
- **フラット保持の場合と比べて線画の輪郭が消える**
- **形のデザインが情報過多で読みづらく**

### 講師の初心者時代の例(ch21)

- **金属棒** に多段階のトーン変化を入れた結果 → リアルだが線画消失
- 「**社実的にしようとすればするほど、フラットに保った場合と比べて線画の情報が目立たなくなる**」(ch21 7:25)

### 解決策(Nekojira スタイル)

1. **3 つ以下のトーン** で完了(白 + 入色 + 暗いテーマ)
2. **微差調整**(2-3%)で情報量
3. **エッジコントロール** で形と構造
4. **形のデザイン** で細部の錯覚

### 線画と組み合わせのスタイル衝突

- 「**車実的なレンダリングの場合、通常シルエットの端に手や体の周りに明確な黒線はない**」(ch21 7:00)
- → 線画 + リアル質感は **スタイル衝突**
- 線画スタイルを選ぶなら **フラットレンダリングに統一** すべき

### 「ブライ(?)に似た絵画」

- 講師の用語(transcript ノイズ、おそらく「**ペインタリー(painterly)**」の誤聞)
- レイヤーを重ねていく **リアル方向**
- これを目指すならエッジコントロールより **多階調レンダリング**

## 関連

- [[three-tone-simplification]] — 3 トーンでの単純化
- [[micro-tone-variation]] — 微差調整で情報追加
- [[edge-4-levels]] — エッジで形を表現(代替手段)
- [[grey-plus-local-high-saturation]] — Nekojira 色味哲学(回避策)
- [[line-as-shadow-deformation]] — ye_jji 概念、線画フラットの正当化

## 解釈の揺れ / 異論

- 講師は「**両方を完璧にバランスよく扱える素晴らしいアーティストもいる**」と認めつつ「**自分のスタイル選択**」を強調(ch21 7:24)
- ye_jji の **「整える」**([[totoneru-definition]])や **「軽く 1 周触る」時間管理原則** は同じ罠への対処法だが、Nekojira の方が **失敗の症状を明示的に開示** する

## 出典

- [[coloso-nekojira-ch21-color-value]] — 起点、講師の初心者時代として開示
- [[coloso-nekojira-ch24-final-finishing]] — 仕上げ時の「**目の細部とコントラストが他と合わない**」例で再強調
