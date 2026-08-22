---
type: concept
name: 光ベクトルは X/Y/Z 3 軸必須
aliases: [light vector 3 axis explicit]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-01
sources: [coloso-chan-02-sec17-realistic-rendering-practice]
tags: [chan-02, light, volume, definition]
---

# 光ベクトルは X/Y/Z 3 軸必須

## 定義

[[chan]] が sec17 で示す独自指摘。光の方向を聞くと多くは「右上・左上」と 2 次元でしか答えないが、**前後方向(Z 軸)も含めた X/Y/Z 3 軸で指定**しないと立体の明度計算は不完全になる。

## 関連

- [[grid-line-method-for-volume]] — 3 軸光を前提とした明度配点
- [[hikari-no-3-shurui]] — ye_jji の光の 3 種類(別観点)
- [[chan]]

## 出典

- [[coloso-chan-02-sec17-realistic-rendering-practice]] — 光ベクトル 3 軸の宣言
