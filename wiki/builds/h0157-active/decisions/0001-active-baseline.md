---
type: build
title: H0157 active baseline decision 0001
status: active
confidence: high
evidence_level: source-backed+user-stated
created: 2026-09-10
last_reviewed: 2026-09-10
capsule_id: H0157-ACTIVE-20260910-R1
decision_id: H0157-DECISION-0001
---

# H0157 active baseline decision 0001

## 決定

`wiki/builds/h0157-active/`を、H0157の次回作業で最初に読む小さな現行入口とする。
現在識別できる親BlendはSHA `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
次の欠落は `H0157-GAP-Q0-CURRENT-STATE-SNAPSHOT` の1件だけを採用する。

この決定は、親Blend、quality-gate、run-state、抽出物を変更せず、Q0実装を許可しない。

## 根拠

- `EV-H0157-003`: 親Blendの現物path・size・full SHA。
- `EV-H0157-014`: production quality-gateのplan検査がSHA不一致でFAIL。
- `EV-H0157-015`: 12入力中、唯一の現物不一致がproject-run-state。
- `EV-H0157-008` / `EV-H0157-012`: 現行入口と計画がQ0を先頭に置き、旧f166を全域抽出器として採用していない。
- `EV-H0157-011` / `EV-H0157-013`: 復旧のみ許可され、実装は未許可。

## 採用の代償

- 何を捨てたか: 旧計画、旧handoff、Muse由来判断、F12、Time Machine、特定bundle探索、`f166`再実行を次回の入口にすること。
- 手元でどう変わるか: HelenのBlendは1バイトも変わらない。次の担当が最初に読む範囲と、最初に扱う欠落がQ0の12入力SHA照合へ絞られる。見た目の改善はまだ起きない。
- 戻せるか: 既存ファイルは削除・移動・変更していない。このカプセルを`superseded`にし、旧資料を再読すれば戻せる。append-onlyの`log.md`は履歴として残す。

## 再評価条件

- 親Blend、quality-gate、run-state、現行入口のいずれかのSHAが変わった。
- Q0が独立review、user approval、production plan PASSまで完了した。
- Q0より前に扱うべき別の直接証拠が新たに現れた。

## 言っていないこと

- H0157の見た目が原作と一致したとは言っていない。
- 抽出が完了したとは言っていない。
- Q0以降を実装してよいとは言っていない。
