---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-03
parent: ../_index.md
---

# 2026-09-03 承認基準の置換え（旧固定値→新基準）

武田さんの指示「新基準へ置き換えて」により作成。旧ファイルは凍結のまま書き換えない。
以後の承認引用は本書の新値を用いる。本書の確定は承認カードで行う。

## 置換え表

| input_id | 旧固定値 | 新値 | 理由 |
|---|---|---|---|
| `kb-current` | `5bb60fb5...` | `ec641b37cc3270f2f8c57fbe58d2a90edd61e1063eeddd2ec3d7d37b69f7ec70` | 09-03残タスク追記（承認済み） |
| `project-quality-gate`（全体） | `f7b29ca6...` | `479f8a1daea14ff3e83597298140555d89488fd223ae2830c2a7b0d8a0f49141` | U1正規導入（承認済み） |
| `project-quality-gate`（投影） | `ef9cc55f...` | `c7ba2586db872df0b977401529e82ba7269acd78d39b2c0dfd9d6fd8209ed1c3` | 上に伴う変化 |
| 集合SHA | `d69690be...` | `e39ff76c4712bb8115b9a045b75409b8a5257c3a3c8a6f6a104004745e7342f0` | 上2件に伴う変化 |
| 他10件 | 旧固定値のまま | 変化なし | 2026-09-03再照合で一致 |
| 環境3ファイル | 入口作成時のまま | 変化なし | 同上 |

## 不変の固定値

- 一本化revision 4：`04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a`
- 具体計画：`cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f`
- 最終review受領書：`83ecf2868e835bd3cd6466d19c42be58a3ad85533a0f4a4bff4d58f03a41b7ea`
- 計画承認受領書：`637db144b2f67a2778b2228914f4251f9895b5f1939ac598f8675988466a150f`

## 検証

- 2026-09-03に全文11件・品質台帳全体・環境3ファイルの現物SHAを再照合し、
  本書の新値と一致した（未知の書き換わりなし）。
- 実測JSONは `sessions/20260903-u0-remeasurement.json`（実測集合SHA `e39ff76c...`）。

## 証拠の境界

本書は基準値の置換え記録であり、実行承認・実装承認そのものではない。
旧承認資料・入口書類の本文は凍結のまま残す。
