---
type: build
title: H0157 decision 0003 — Q0-CRと差分再審査フローの昇格後の現在地
status: active
confidence: high
evidence_level: source-backed
created: 2026-09-11
last_reviewed: 2026-09-11
capsule_id: H0157-ACTIVE-20260911-R3
decision_id: H0157-DECISION-0003
---

# H0157 decision 0003

## 決めたこと

1. **現在地の正本はR3カプセルとする。** R1（H0157-ACTIVE-20260910-R1）とstageのR2は
   `superseded` として保持し、現行説として参照しない。R1の3ファイルは
   `wiki/builds/h0157-active/superseded/` へ原本のまま残す。
2. **監査基盤の積み残しは無い。** Q0、Q0-CR、差分再審査フローの3つが本番反映済みで、
   それぞれ独立読み返しがPASSしている。次の欠落は `H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT`。
3. **審査方針を確定した。**
   - scanner が候補として拾わない変更にも、独立レビューの判定を要求する。
   - rename はバイトが同一のときだけ既存判定を継承し、旧pathと明示承認を残す。
   - baseline 更新は batch を許可する（件数は差分報告で必ず表示する）。
   - 目録は Python ソースを持ちうる全ファイルへ広げ、機械生成物と一時ファイルだけ明示除外する。
4. **独立reviewは通常運転では機械が行う。** ただし実装担当とは別プロセスまたは別エージェントで、
   実装側のPASS表示を信用せず実バイト・SHA・diff・receiptを直接検査する。
   意味判断・見た目判断・ユーザー受入は自動承認しない。
5. **P0許可は分離したまま。** registryの `p0_authorization` は `authorized: false`。
   Q0・Q0-CR・rebaseline の技術PASSをP0許可へ流用しない。

## 変えていないこと

- 親Blend、run-state、quality-gate.json、writers.json、raw、appは不変。
- Helenの見た目・抽出・Blend候補作成は未着手。

## この決定が証明しないこと

- 原作一致、ユーザー受入れ、運用開始可能。
- 将来の差分審査が正しく行われること（そのつど独立reviewが要る）。
