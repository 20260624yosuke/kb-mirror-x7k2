---
type: analysis
title: H0157探索・変更の有効性を強制する最小機械監査案
status: active
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-09-01
last_reviewed: 2026-09-01
related:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-cleanup-task-entry.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md
---

# H0157探索・変更の有効性を強制する最小機械監査案

## ユーザー要求

> 承認しない。選択肢1の案を、心がけではなく、機械的監査で動作する仕組みにしてください。
> 懸念点は、このプロジェクトの目標であるhelen原作再現に有効ではない機能をつけることです。
> 有効ではない機能をつけることを禁止します。

前回の「探索票を残す」という運用上の心がけは不承認。再起動後も不承認を維持し、Helen・f166・Blend・品質台帳の実装承認へ変換しない。

## 結論

新しい汎用チケットシステム、別状態ファイル、GUI、ダッシュボード、LLM採点器は作らない。未承認の一本化計画revision 3が既に予定する `audit_guard.py`、`state.json`、`search-contract.json`、`change-contract.json`、`effect-tests.json`、`requirement-coverage.json` に、次の既存エラーで拒否する条件を追加する案とする。

- `EA_SEARCH_SCOPE_INVALID`: H0157の現行要件・family・未解決gapへ拘束されない探索、重複探索、根拠のない範囲拡張を拒否。
- `EA_EFFECT_NOT_DEMONSTRATED`: 対象gapの解消または対象指標の期待方向の改善を実測できない変更、逆効果、非対象回帰、対照不一致を候補昇格から拒否。
- `EA_REQUIREMENT_COVERAGE_MISSING`: S6/S8/G10だけを直して全身・300フレーム・色陰影・衣装・同一候補SHAの全要件を満たした扱いにすることを拒否。
- `EA_OPERATION_UNAUTHORIZED` / `EA_EXPERIMENT_PROMOTION_DENIED`: 契約前の実装、隔離実験の正式成果物化、許可外writerを拒否。
- `EA_KB_SNAPSHOT_STALE`: 整理後の現在位置、run-state、計画、quality-gate、Blend、P0 bootstrapの現行SHAと不一致の旧環境を拒否。基準SHAへ自動追従しない。

監査は自由文の意味を正しいと推測しない。実在ID、JSON pointer、path+SHA、状態遷移、独立review receipt、対照付き効果試験だけを機械判定する。

## 0. 整理後の現在位置を毎回再取得するゲート

2026-09-01の別エージェント整理により、現在位置の正本は `gf2-helen-repro-v51-current.md` になり、旧run/handoff/plan-repair/conversationは履歴へ降格した。`run-state.json` には `status_corrections_2026_09_01` が追加され、古い残作業欄より優先される。以前の `20260901-current-state-evidence.json` は現行run-state/rev4/rev3 SHAと不一致なので、現在状態として再利用しない。

`audit_guard.py` は、既存予定の `quality-gate.json.execution_audit` と `audit/evidence-index.json` に固定したcurrent、cleanup、run-state、rev4、rev3、quality-gate、Blend、P0 bootstrapのpath+SHAを、begin/finish/正式登録のたびに実測する。差があれば `EA_KB_SNAPSHOT_STALE` で停止し、基準を自動更新しない。別エージェントの変更は差分記録を作り、再読・独立review・明示promoteを経て初めて新基準にできる。

## 1. 探索開始ゲート

`U3.search` の `begin_operation` は、`search-contract.json` に次が揃わなければ許可証を出さない。

1. `requirement_ids`: 原要件REQ1〜REQ10のうち、この探索が前進させる実在ID。
2. `family_ids`: 現行quality-gateの `mesh-static` / `motion-h0157` / `shading` / `variant-switch-test` の実在ID。
3. `defect_or_gap_ids`: 現行state、quality-gate、f152、監査契約に実在する未解決ID。自由な新名称は禁止。
4. `baseline_refs`: そのgapが未解決である直接証拠のpath+SHA+JSON pointerまたは行位置。
5. `decision_unlocked`: 成功時に許される状態遷移または次の具体判断。`役に立つかもしれない`は禁止。
6. `claim_to_test` と `what_this_rules_out`: 当たりの場合と外れの場合の両方で、何が確定するか。
7. 対象ルート・形式・分母・未処理数・対象外・陽性対照・陰性対照・追加範囲へ広げる条件。
8. `duplicate_key = defect/gap + claim + input SHA集合 + search-rule SHA`。閉鎖済みの同一keyがあれば新規探索を拒否し、既存票を返す。

IDの実在、SHA、状態、重複、対照、分母はguardが検査する。因果として妥当かは、探索契約を作ったactorとは別のreview actorが原要件・現行gap・一次資料を直接読み、receiptを作る。receiptが無い探索は `search_scoped` へ進めない。

## 2. 外れた探索の機械的閉鎖

- 範囲内を全件処理し、陽性対照が通り、候補が無い: 正式stateは `rejected`。`scope-covered-no-hit` という別状態は作らない。
- 入力欠損、展開失敗、登録パス不明: 正式stateは `blocked`。未発見へ読み替えない。
- 候補があるが参照辺が欠ける: 証拠は保存できるが `candidate_traced` へ進めない。
- `rejected` / `blocked` には、入力SHA、検索規則SHA、分母、未読、コードの直接観測、否定できた範囲、残る未知、再開条件を必須にする。

これにより、外れたコードが何だったかを記録せず別の枝へ移る操作を、次状態への遷移時に拒否する。

## 3. Helenへ有効でない機能の候補昇格を拒否

探索結果だけではコード変更も候補Blend書出しも許可しない。

1. `candidate_traced` と独立 `causal_reviewed` が両方成立。
2. ユーザーが、対象、予測される見え方、対照、指標、期待方向、許容悪化限界、戻し方を持つ `change-contract.json` を明示承認。
3. `purpose=experiment` の隔離出力だけを作る。親Blendと正規出力は変更しない。
4. `effect-tests.json` で、対象gapの直接解消または対象指標の期待方向改善を測定し、非対象の回帰が限界内であることを確認。
5. 効果差なし、逆効果、回帰、対照不一致なら `EA_EFFECT_NOT_DEMONSTRATED`。実験を `rejected` にし、candidate writer tokenを発行しない。
6. 合格しても、同じ変更契約・入力・branch・出力SHAに限定してcandidateへ進める。別の便利機能へ拡張しない。

「Helenに有効」と機械が意味理解で採点するのではない。現行の未解決gapを直接閉じるか、H0157の対象指標を事前に決めた方向へ実測で動かした場合だけ、正式成果物への昇格を許す。

## 4. G10の正常fixture（第1実探索の自動指定ではない）

G10は監査がH0157へ正しく拘束できることを確かめる正常fixtureにする。整理後の現在位置は、LLM単独で実行可能な残りとして未解析コンテナの展開（f154候補）を1件記録し、G10はrenderer→material対応不足でblockedとしている。したがって、第1実探索は旧順序から自動継承せず、f154とG10/S6/S8 gapの因果接続を比較した別契約・別承認で決める。

- requirement: REQ2、REQ5、REQ10。
- family: `shading`。
- gap: Material不在により、どのH0157 renderer/submeshがどのmaterial/RampSettingを使うか決まらない。
- chain: `H0157 prefab → renderer → submesh → material → RampSetting`。
- success: 本人・衣装・部位・実行条件に拘束したpath+SHAの全辺が揃い、G10の欠損辺を閉じられる。
- reject: 調査範囲を全件処理したが、候補が別キャラ・別衣装・未使用設定だけだった。
- blocked: 登録済み原作bundle 2本が読めず、参照鎖を確認できない。
- この探索の成功は、S6/S8、全身、300フレーム、最終受入を自動合格にしない。

## 5. 必須の正常対照・単一変異試験

| ID | 正常対照 | 1項目だけ壊す | 期待する拒否 |
|---|---|---|---|
| U01 | G10票が実在REQ/family/gapへ接続 | gap参照を削除 | `EA_SEARCH_SCOPE_INVALID` |
| U02 | 入力SHA・分母・処理数・未読数が一致 | 分母を改変 | `EA_SEARCH_SCOPE_INVALID` |
| U03 | 陽性・陰性対照が両方ある | 陰性対照を削除 | `EA_SEARCH_SCOPE_INVALID` |
| U04 | 閉鎖票と異なるkey | 同一duplicate_keyで再発行 | 新規探索拒否、既存票を返す |
| U05 | 全件no-hitを`rejected`で閉鎖 | unreadableをno-hitへ変更 | `blocked`維持／遷移拒否 |
| U06 | 期待方向・悪化限界・回帰条件がある | 期待方向を削除 | `EA_EFFECT_NOT_DEMONSTRATED` |
| U07 | 対象指標改善・回帰なし | 変更を無効化して効果差0 | `EA_EFFECT_NOT_DEMONSTRATED` |
| U08 | 同一branch・入力・出力SHA | finish後に出力またはbranch変更 | `EA_ARTIFACT_LINEAGE_BROKEN` / `EA_BRANCH_MISMATCH` |

各試験は正常PASS、単一変異の所期FAIL、復元後PASSを必要とする。エラーIDを返しただけ、単体fixtureだけ、LLMの成功報告だけでは運用合格にしない。

## 6. 追加しないもの

- 新しい汎用チケット管理機能、別の全体phase、別state、第二の正本。
- ダッシュボード、検索UI、LLMによる有効性スコア、自動モデル選択。
- 全7,424MSL本文、全DLL/Lua/MonoScriptの無条件深掘り。
- 14アクションを分母・進捗・対象総数にする欄。
- `scope-covered-no-hit` など、既存stateと重複する状態語。
- Helenのgapを閉じず、対象指標も改善しないが「将来便利そう」という理由だけのコード・Blender機能。

追加機能を持たないことで失うのは、汎用調査基盤としての拡張性と一覧UI。Helen H0157を成立させる今回の目的には不要なので採らない。必要になった場合も、実害と直接要求を別途示すまで追加しない。

## 7. 承認境界

これは修正版設計案。一本化計画revision 3の実行、schema変更、guard実装、フック設定、f166再走査、原作bundle探索、Blend変更の承認ではない。承認された場合も、まずこの差分を一本化計画へ反映し、独立レビューと具体的model ID・設定差分の承認を通す。

## 根拠（実パス）

- /Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-cleanup-task-entry.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-current-state-evidence.json（過去スナップショット。現行状態には使わない）
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
