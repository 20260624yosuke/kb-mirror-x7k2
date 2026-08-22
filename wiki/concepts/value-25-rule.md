---
type: concept
name: バリュー値25以上の法則
aliases: [バリュー値25, 明度差25, バリュー値の測り方]
tags: [value, shadow, color, tool, hizurume]
sources: [coloso-hizurume-ch09-light-shadow-color]
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-06-01
---

# バリュー値 25 以上の法則

## 定義

[[hizurume]] の陰影明度の指標。光と影を白黒(バリュー値)にしたとき、**明度差を 25 以上開ければ絵が崩れない**。

## 構成要素 / 主要な論点

- 陰影の色に「答え」はないが指標は存在する → 迷ったら明度差 25 以上。
- **彩度 0(saturation 0)は不可**: 明度依存になり色の重みを省く。**レイヤープロパティ or レイヤーカラーの「バリュー値(グレースケール)」** で測る。
- 測定をショートカット/初期設定に登録すると一瞬で確認できる。
- 影の色相は **光の色相と被らないようずらす**(やった方がよい)。
- 例外: 明るい絵は 25 ほど開かないことが多い。

## 関連

- [[me-do-tai-hi]] — 明度対比の一般概念(本概念はその数値指標)
- [[tone-prediction-practice]] — chan「白黒予測 → Ctrl+Y 検証」と同系
- [[meian-hikaku-rensa]] — 明度差の視線誘導効果
- [[shadow-design]] — 影の設計の講座横断ハブ(明度規律として統合)

## 出典

- [[coloso-hizurume-ch09-light-shadow-color]]
