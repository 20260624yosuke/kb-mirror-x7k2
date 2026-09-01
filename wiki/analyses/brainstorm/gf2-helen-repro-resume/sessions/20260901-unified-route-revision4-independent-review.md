---
type: analysis
title: 一本化計画revision 4 独立レビュー
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-01
parent: ../_index.md
review_actor_id: /root/unified_rev4_independent_review
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
