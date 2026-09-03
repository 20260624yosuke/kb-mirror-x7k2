---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-03
parent: ../_index.md
---

# 2026-09-03 U2実在inventory報告（読み取り限定・因果判断なし）

U2実行承認（inventory先行）の範囲で、f154候補とG10/S6/S8の未解決点を読み取りだけで列挙した。
因果の採否・候補採用・探索順は決めていない。正規の台帳・共有設定・Blend本体は無変更。

## 一覧

- f154走査の出力 `ledger/h0157-gff-container-scan-v1.json` は実在（SHA `6b50c6d8...`、9,684バイト、2026-08-25生成、承認#78）。
  新版台帳9,033件とローカル9,033件の一致、現行マニフェストへのscene root・prefab root・Helen材質トークンの不検出、
  旧版へのscene root実在を記録している。解釈は因果審査へ委ねる。
- f154スクリプトは実在（SHA `e8d68afa...`）。
- f154が読んだcache側 `.d` はこの機械に存在しない（ゲームコンテナパス自体が無い）。再実行はここでは不可。
  app側 `.d` は存在するが版・容量が異なる。
- G10は不合格のままblocked（silkstockのramp割当て不能）。
- S6・S8は不合格だが、欠け検知としての正常動作（実装の誤りではない）。S7合格、S9参考値帯内。
- P3A合成fixtureは実在（SHA `2380a2e2...`）。正常対照専用で、実G10 P3Bのblockedは動かない。
- 重複：陽性対照の一致は独立証拠ではない。ledgerの複製はsessions・stageに無い。

## 判定：COMPLETE-WITH-INPUT-CAVEAT

一覧化は完了。注意点はcache側 `.d` の不在（再実行不可）の1件。

## 次の一手

因果審査は別承認が必要。開始時に担当ID（承認資料は `claude-opus-5` を指定）を記録して確認し、
名乗れなければ無断代替せず技術的停止する。

## 証拠の境界

本報告はU2の一覧化の結果であり、因果審査・探索契約・原作一致・Blend完成の証拠ではない。
