---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-03
parent: ../_index.md
---

# 2026-09-03 U0再測定 drift report（U2に向けた新基準）

旧承認資料の固定版ではU2へ進めないため、12入力を測り直した。算出式は本番の `audit_guard.py` と同一
（`canonical_bytes` / `manifest_projection_sha` / `calculate_set_sha`）。
測定は読み取り限定。正規の台帳・共有設定・Blend本体は無変更。

## 新旧対照

| input_id | 旧固定値 | 実測値 | 判定 |
|---|---|---|---|
| `kb-current` | `5bb60fb5...` | `ec641b37...` | 変化（09-03残タスク追記・承認済み） |
| `project-quality-gate`（全体） | `f7b29ca6...` | `479f8a1d...` | 変化（U1正規導入・承認済み） |
| `project-quality-gate`（投影） | `ef9cc55f...` | `c7ba2586...` | 上に伴う変化 |
| 他10件 | 旧固定値と一致 | 一致 | 不変 |
| 集合SHA | `d69690be...` | `e39ff76c...` | 上2件に伴う変化 |
| 環境3ファイル | 入口作成時と一致 | 一致 | 衝突なし |

## 判定：PASS-WITH-KNOWN-DRIFT

変化は2件とも承認済みの経緯で説明できる。未知の書き換わりはない。
以後は旧固定値を使わず、本報告と `20260903-u0-remeasurement.json`（実測集合SHA `e39ff76c...`）を
U2の入力基準にする。

## 次の一手

U2（f154候補とG10/S6/S8の未解決点の読み取り比較）の実行承認をカードで取る。
因果審査の担当ID（`claude-opus-5`）をこの会話が名乗れるかは、U2開始時に記録して確認する。

## 証拠の境界

本報告はU0再測定の結果であり、U2の実行承認・原作一致・Blend完成の証拠ではない。
