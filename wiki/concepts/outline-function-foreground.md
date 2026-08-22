---
type: concept
name: アウトライン機能で手前だけ完結感
aliases: [outline-function-foreground, 輪郭線機能, 手前アウトライン]
tags: [outline, line-art, foreground, clip-studio, background]
sources: [coloso-ye-jji-ch20-background]
---

# アウトライン機能で手前だけ完結感

## 定義

[[ye-jji]] が ch20 で示す **背景描写の完成度差設計テクニック**。CLIP STUDIO の **アウトライン(輪郭線)機能** を **手前のヤシの木だけ** にかけ、後方には線を入れない。これにより **前後でクオリティの差** を意図的に作り、近景の完結感と遠景の省略感を両立させる。

## 構成要素

### 使い方

- 手前のヤシの木だけ **レイヤーを分離** + アウトライン機能で輪郭線を追加
- 後方のヤシ・植物には線なし
- 線が入るだけで **陰影の段階が少ない状態でも仕上がり感** が出る

### 効果

- 近景 = 完結感(線あり)
- 中景 = シルエット程度(線なし、階調少)
- 遠景 = 大気色シルエット(線なし、最大省略)

### 関連:[[line-as-shadow-deformation]] との接続

- 線 = **デフォルメ強度の表現** として読める
- 近景に線を入れる = デフォルメ強度高い
- 遠景に線を入れない = デフォルメ強度低い(より省略的)
- = 線をデフォルメ度の連続体として運用する [[line-as-shadow-deformation]] の応用

## 関連

- [[distance-information-gradient]] — 距離による情報量段階化の一手段
- [[clip-studio-tools]] — CSP 機能
- [[line-as-shadow-deformation]] — 線をデフォルメ度として運用する哲学

## 出典

- [[coloso-ye-jji-ch20-background]]
