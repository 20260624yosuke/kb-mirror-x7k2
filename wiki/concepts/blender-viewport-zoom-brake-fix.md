---
type: concept
title: Blenderビューポートズームの急ブレーキと対処法
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-07-26
---

# Blenderビューポートズームの急ブレーキと対処法

## 現在の統合見解

Blenderの3Dビューポートでオブジェクトへズームインしていくと、途中から急に動きが重くなり、
それ以上近づけなくなることがある。これは故障ではなく、Blenderのズームが「視点の中心点
(旋回の軸)までの距離」に比例して1回の移動量を決める仕組みのため。中心点に近づくほど
1回のスクロールでの移動量が対数的に小さくなり、ブレーキがかかったように感じる。

## 根拠

2026-07-26、`gf2-helen-starlit-waltz` プロジェクトの
`rest-room-v2.2/blends/helen-ssr0102-body-17.blend` を開き、アクションエディターで
`BED-ACT_H0158_HelenSSR0101_Bedroom_01_Idle__ssr0102__SRC` を展開してビュー操作していた際に発生。
以下の対処法を提示し、**方法2(プリファレンス変更)で武田さんが実機解決を確認済み**。

- 方法1: 対象オブジェクトを選択した状態でテンキー「.」(View Selected)を押し、視点の中心点を
  対象に置き直してズーム基準距離をリセットする。
- 方法2(**今回の実効打**): `Edit(編集)` → `Preferences(プリファレンス)` → `Navigation(操作)`
  タブ → `Zoom(ズーム)` 項目の `Zoom to Mouse Position(マウス位置にズーム)` をオンにする。
  視点の中心がマウス位置に寄るようになり、ブレーキが緩和される。
- 方法3: Nパネルの `View` タブにある `Clip Start(クリップ開始距離)` を小さくする
  (近距離での表示消失・カクつき対策、今回は未検証)。

## 矛盾・未確定

方法1・方法3は提示のみで実機検証していない。今回のケースでは方法2のみで解決した。

## 変遷

なし(初版)。

## 関連リンク

- [[gf2-helen-starlit-waltz-3d-reference-build]] — 発生時に操作していたBlenderプロジェクト
