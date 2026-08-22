---
type: concept
name: 3 トーン単純化(Nekojira)
aliases: [three-tone-simplification, 3-tone-rule, simplified-rendering]
tags: [rendering, simplification, tone, nekojira-stylebook]
sources: [coloso-nekojira-ch21-color-value, coloso-nekojira-ch24-final-finishing]
---

# 3 トーン単純化(Nekojira)

## 定義

[[nekojira]] の **ch21(色彩明度)** で確立される **レンダリング単純化原則**。「**通常 3 つ以上の式張は使いません。白色に入色暗いテーマを組み合わせるだけ**」(ch21 8:38)という方針。**ベース色 + 影 + ハイライト(またはバウンス光)** の 3 階層で完了することで、[[over-rendering-trap]] を回避する。

## 構成要素 / 主要な論点

### 3 階層

| 階層 | 役割 |
|---|---|
| **1. ベースカラー** | 物体の基本色(最も明るい色) |
| **2. 影** | マルチプライ 30-40% 暗くしたトーン |
| **3. ハイライト(またはバウンス光)** | 明部のフラットな白 or 影内の反射光 |

### 補完手段(階層数を増やさず情報量を増やす)

- **微差調整(2-3%)** で多階調の錯覚([[micro-tone-variation]])
- **エッジコントロール 4 段階** で形と構造([[edge-4-levels]])
- **影の縁に高彩度色** で SSS([[shadow-edge-high-saturation]])

### 「ハイライトを使わない」スタイル

- 「**必要な場合を除きハイライトは使いません。私のスタイルはフラットで光原を一点とします**」(ch21_02 0:32)
- → 実質 **2 階層**(ベース + 影)で完了するケースも多い

## 関連

- [[nekojira-rendering-workflow-5-stages]] — 段階 2(トーンの値)の核
- [[micro-tone-variation]] — 補完手段
- [[edge-4-levels]] — 補完手段
- [[grey-plus-local-high-saturation]] — Nekojira 色味哲学の数値的実装
- [[over-rendering-trap]] — 違反すると陥る罠

## 出典

- [[coloso-nekojira-ch21-color-value]] — 起点、明示的な単純化原則
- [[coloso-nekojira-ch24-final-finishing]] — 最終キャラで実装
