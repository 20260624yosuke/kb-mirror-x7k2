---
type: concept
name: カラーレイヤーで情報の入り抜き
aliases: [カラーレイヤー, バリュー値保存, 情報の入り抜き]
tags: [layer, clip-studio, value, gaze-guidance, hizurume]
sources: [coloso-hizurume-ch05-environment-setup]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-01
---

# カラーレイヤーで情報の入り抜き

## 定義

CSP の **カラー(合成モード)レイヤー** を、バリュー値(明度)を保ったまま彩度だけ変える道具として使う [[hizurume]] の最重要レイヤー運用。

## 構成要素 / 主要な論点

- カラーレイヤーは **白黒(バリュー値)を保存したまま彩度を変える** → 明度構造を壊さず色を操作。
- カラーで彩度を抜く / 足すことで **情報の入り抜き**([[sub-shisen-yudou]])が容易(キャラを目立たせ、白黒部分は沈める)。
- 表面下散乱(明暗境界に高彩度)もカラーレイヤーで実装([[sss-and-surface-scattering]])。
- 本人のレイヤー使用順の筆頭(カラー → 乗算 → オーバーレイ…)。

## 関連

- [[value-25-rule]] — バリュー値の概念
- [[sub-shisen-yudou]] — 情報を抜く操作
- [[sss-and-surface-scattering]] — 明暗境界の高彩度
- [[layer-effects-by-intensity]] — ye_jji のレイヤー効果強度論と接続

## 出典

- [[coloso-hizurume-ch05-environment-setup]]
