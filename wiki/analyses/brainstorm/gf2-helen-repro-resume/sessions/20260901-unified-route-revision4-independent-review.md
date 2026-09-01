---
type: analysis
title: 一本化計画revision 4 独立レビュー
status: complete
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-01
parent: ../_index.md
review_actor_id: /root/unified_rev4_independent_review
review_result: pass
---

# 一本化計画revision 4 独立レビュー

## 対象1回目

- 対象SHA-256: `62c3094d56bfbccda3d27a2d554b3ec624691c19e1ab479131787628c9bc674e`
- 結果: Critical 0 / Major 5 / Minor 1。実行承認へ出せない。

## 指摘と最小修正

1. **Major: 監査実装自身の無効機能禁止が未強制**
   - effect gateはU6のHelen候補だけに効き、U1の余分なCLI/schema/hook機能を止めない。
   - 修正: 既存外部登録簿の `approved_capabilities[]` へaction/CLI/schema/hookとH0157要件・監査rev4節・正常/変異試験を拘束。未登録は `EA_OPERATION_UNAUTHORIZED`。
2. **Major: blockedの実G10を正常fixtureと呼ぶ衝突**
   - 修正: 正常対照は監査rev4 P3AのG10型・隔離合成fixture。実G10 P3Bは参照鎖回収までblockedで肯定経路に数えない。
3. **Major: U1〜U5同一承認とU3別承認の矛盾**
   - 修正: 本計画実行承認はU0/U1/U2/U3契約設計・独立reviewまで。第1search-contract承認後にU4/U5、change-contract承認後にU6。
4. **Major: search no-hit rejected、blocked、技術的停止の復帰規則不足**
   - 修正: search由来rejectedはentered_from=search_scoped、新契約・新keyで再開。原作入力欠損・対象コンテナ展開不能だけblocked。registry/hook/schema/証拠登録器障害はstate不変の技術的停止。各肯定経路を追加。
5. **Major: 機械拘束入力集合が一意でない**
   - 修正: current_state_inputsは12memberを絶対パス・役割・SHAで個別列挙。P0 4件を圧縮しない。欠落・余分・重複・各SHA変更を単一変異試験。
6. **Minor: S8 family分類の揺れ**
   - 修正: S8は契約時の直接証拠でmesh-static/shadingの単数または複数familyを決め、事前固定しない。

## 再レビュー状態

上記修正を一本化revision 4と承認済み具体計画へ反映後、新SHAを同じ独立review actorへ渡して再レビューする。major finding 0件になるまで合格へ変えない。

### 2回目の中間結果

- 対象SHA: `31749d38faff573b86ed4ff5b30324701c9119af2034834a97386d5b66acaabc`
- 1回目のMajor 5 / Minor 1は解消確認。
- 新規Major 1: `current_state_inputs` を保存するquality-gate自身をfull SHA memberにすると自己参照で安定値を作れない。
- 修正: quality-gate memberは `/execution_audit/current_state_inputs` を除外した固定canonical projection SHA。set SHAは自分自身を除くinput_id順membersだけをhash。2回生成同値・自己欄改変・非自己欄改変・除外pointer変更の試験を追加。

### 3回目の結果

- 対象一本化revision 4 SHA: `0254be0ce640e12fc419231720060cdb30dd42af2f1114cfcb81702e738aa7eb`
- 対象具体計画 SHA: `cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f`
- 結果: Critical 0 / Major 0 / Minor 0。自己参照Majorを解消し、既存監査rev4との衝突なし。
- ただし結果をcurrentへ反映するとcurrent SHAが変わるため、変更後current `5bb60f…` を読む最終再reviewを別に行い、その結果を最終receiptとする。

## 最終review receipt

- 一本化revision 4 SHA-256: `04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a`
- current SHA-256: `5bb60fb5fab92d7fa8c8d310b4318f6121ef67df8aadca9b932d7b61f56ad87e`
- 具体計画 SHA-256: `cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f`
- 結果: **Critical 0 / Major 0 / Minor 0**。
- cleanup `6a390e6d…`、監査rev4 `c690d7be…`、quality-gate `f7b29ca6…`、run-state `b176b17b…`、Blend `04ef8b79…`、P0四入力 `e09ca431… / 829a73ec… / 3333d01… / c5be5ee4…` を直接再読し一致。
- currentは0/0/0を表示しつつ `draft-unapproved`・実装未承認を維持。一本化計画はcurrent `5bb60f…` をdrift記録付きで再基準化しており、意味衝突なし。
- 残る修正なし。このreceipt自身の確定SHAを、実装時の `current_state_inputs` 12番目memberへ登録する。

このreceipt記入後に一本化計画やcurrentを自己更新しない。本文を変えず、review終端の肯定証拠は本receiptで保持する。
