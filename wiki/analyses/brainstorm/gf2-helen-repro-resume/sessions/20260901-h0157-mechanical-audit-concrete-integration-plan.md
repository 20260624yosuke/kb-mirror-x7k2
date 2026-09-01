---
type: analysis
title: H0157 最小機械監査を一本化計画へ反映する具体計画
status: active
confidence: medium
evidence_level: user-stated+source-backed+inferred
last_reviewed: 2026-09-01
plan_status: approved-for-unified-plan-revision
parent: ../_index.md
---

# H0157 最小機械監査を一本化計画へ反映する具体計画

## 状態

- 2026-09-01、最小機械監査の方針と、この具体計画を明示承認済み。確認回答も「はい、この選択でよい」。
- 承認範囲は、未承認の一本化計画revision 3本文の改訂と独立reviewまで。schema、guard、hook、Helen、f166、Blendの実装は未承認。
- 旧版の記憶を使わず、2026-09-01の整理タスク後の現在位置ページ、実ファイル、SHAを再取得して作成した。

## 高リスク成果物として先に固定する6点

1. **正解の所在**: 原作bundle・原作動画/2Dアート・H0157実データ。承認と優先順位は武田さんの明示発言。現在位置の入口は `gf2-helen-repro-v51-current.md`、実測状態は実ファイルと `run-state.json`。
2. **欠けうる入力**: 原作照明、階調表、衣装材質、renderer→material対応、D2アルファ髪、post grade。欠損時は忠実再現を `blocked` とし、推定で埋めない。
3. **性質の違う対象群**: shading（G10/S6）、mesh-static、motion、variant/dress。S8はD2アルファ髪の欠けがメッシュ構成と材質/透明処理の両方に関わり得るため、契約時の直接証拠で単数または複数familyを決める。H0157の受入れを他14アクションへ流用しない。
4. **代表例**: 実装を始める場合もH0157一件だけ。正常対照は実G10ではなく監査rev4 P3AのG10型・隔離合成fixture。実G10 P3Bは参照鎖回収までblockedであり、最初の実探索や肯定経路を自動決定しない。
5. **原作比較方法**: 入力・候補Blend・比較画像のSHAを固定し、原作資料と直接比較する。内部数値PASSを原作一致へ言い換えない。
6. **停止条件**: 現行正本のSHA変化、入力欠損、未承認の範囲拡張、H0157 gapへの拘束欠落、効果未実証、回帰、別branch、独立review欠落のいずれかで停止。

## 今回の再照合で変わった前提

### 最新の入口

- 現在位置の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md`
- 整理タスク: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-cleanup-task-entry.md`
- 整理タスクで、旧run/handoff/plan-repair/conversationの4枚は現在位置の正本から降格。`run-state.json` には既存欄を変えず、`status_corrections_2026_09_01` が追加された。
- 現在位置ページは、LLM単独で実行可能な残りとして未解析コンテナの展開（f154候補）を1件記録している。一方、G10はrenderer→material対応不足でblocked。したがって、旧案どおりG10探索を自動的に最初の実作業にしない。

### 2026-09-01 15時台の入力スナップショット

| 入力 | SHA-256 |
|---|---|
| `gf2-helen-repro-v51-current.md`（本計画用の方針追記後） | `919843e207d8d4043ecc1f73585bb6cdc7745dc3c35b4e768500dc26fde547df` |
| `gf2-helen-cleanup-task-entry.md` | `6a390e6d1ddf87f702550a4e4dbaa236813f714336ea45dd6765c1e1acec6d3a` |
| 一本化計画revision 3 | `e1af011174cc63f37c1a85ed9db179414f6f761bfdfe8f6968f13a0dc36543c8` |
| 監査計画revision 4 | `c690d7be9986eca7f24930ffdeb45255a0f7e3b596fb0264879a2bab9b9fa7d5` |
| project `quality-gate.json` | `f7b29ca63f0d93d28f19f3fa34d54789c09493ad42ee26541b7f93ef191ffa96` |
| `06_repro-v51/run-state.json` | `b176b17bb1d1cb9c61573f8ab070fe67170ed57c69ed2fcc7f2e21e60839fc8e` |
| 現行 `helen-h0157-repro.blend` | `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` |
| P0 bootstrap `writers.json` | `e09ca43104e6d163bf6b4b825d1e13be3e50084b7aaa7b3e4c826f6642302aee` |
| P0 bootstrap `evidence-index.json` | `829a73ec998e37befb9656f280c5e51614d1771bc3a9587c5f60379787e3f0b9` |
| P0 bootstrap `review-findings.json` | `3333d01abb9f5417992b8b44d3bf7d3f8590fb796d0712bff8fda6063c83ebaf` |
| P0 bootstrap `bootstrap-status.json` | `c5be5ee4a7a2f53421c88479824791c7c4ec5813cf609c73b0255224f4ca1110` |

P0 bootstrapの現物は `gf2-helen-starlit-waltz/.audit-bootstrap-20260830/`。状態は `p0-complete-p0b-writer-review-required`、writer 54本、evidence 48件、finding 5件、P0B停止理由は `EA_WRITER_CLASSIFICATION_REVIEW_REQUIRED`。正規 `06_repro-v51/audit/`、`scripts/audit_guard.py`、Wiki側必須監査登録簿、`helen_route_hook.py` は未作成である。

これは実行時の恒久値ではない。次の計画改訂や実装を始める直前に再取得し、差があれば本表を無言で上書きせず、drift report（何が変わったかの差分記録）を作って計画を再照合する。`20260901-current-state-evidence.json` はこの表のcurrent/run-state/rev4/rev3 SHAと一致しないため、現在状態ではなく過去スナップショットとしてだけ使う。

### 承認後に実際に検出したdrift

一本化revision 4のreview中、currentのLLM区画を「revision 4独立review中・実装未承認」へ合わせたため、current SHAが本表の `919843…` から `fd4cf1…` へ変化した。本表は承認時点の証拠として残し、自動上書きしない。差分・変更権限・非変更対象は `20260901-unified-rev4-current-drift-reconcile.md` へ記録し、一本化revision 4側の基準を明示更新した。これは旧版拒否が計画作成中にも作動すべき実例である。

## 正本の優先順位と衝突時の扱い

1. **スコープ・承認・優先順位**: 武田さんの最新の明示発言。
2. **再開入口と現在判断の要約**: `gf2-helen-repro-v51-current.md`。機械生成区画は手書きしない。
3. **実在・内容・成果物版**: ディスク上の実ファイルと実測SHA。
4. **legacy状態**: `run-state.json`。`status_corrections_2026_09_01` を古い欄より優先し、既存欄の残存だけで現行判断を覆さない。
5. **旧計画・run・handoff**: 履歴と根拠の所在。現在位置の正本として使わない。

上位同士が食い違う場合は推測で順位を補わず、`EA_KB_SNAPSHOT_STALE` で停止する。古い入力から新しい計画を自動再生成しない。

## 一本化revision 3へ入れる最小差分

### A. 新しい管理システムを作らず、既存予定物へ固定

追加ファイルは作らない。既存予定の次だけを使う。

- `quality-gate.json.execution_audit`: `current_state_inputs` を `member_count / members[] / set_sha256` とし、current、cleanup、run-state、監査計画、一本化計画、quality-gate、Blend、P0の4ファイル、独立review receiptの合計12memberを絶対パス・役割・SHAつきで個別列挙する。group名への圧縮、欠落、余分、重複を拒否する。
- `audit/evidence-index.json`: 同じ入力の取得時刻、取得コマンド、size、mtime、SHA、読み取ったJSON pointer/行位置を持つ。
- `audit_guard.py`: `begin_operation`、`finish_operation`、正式登録、`plan|batch|complete` の各入口で実ファイルを再SHA化する。
- `audit/state.json`: 承認・gap・branchの状態だけを持ち、現在位置ページやlegacy run-stateを複製しない。
- rev4予定の外部登録簿: `approved_capabilities[]` に各action・CLI・schema項目・hook分岐をH0157要件または監査rev4節と正常/変異試験へ拘束する。未登録機能は既存 `EA_OPERATION_UNAUTHORIZED`。

### B. 旧版環境を使わせない機械関所

1. `begin_operation` 前に `current_state_inputs` の全SHAを再測定する。
2. 1件でも不一致、欠損、別パス、読取不能なら許可証を出さず `EA_KB_SNAPSHOT_STALE`。
3. 一致して開始した後も、`finish_operation` と正式登録の直前にread-set（その作業が読んだ入力集合）を再測定する。
4. 作業中に別エージェントが変更した場合は成果物を採用せず、変更前後SHAと差分だけを隔離記録する。
5. 基準更新は自動で行わない。最新current/run-state/実ファイルを読み直し、計画差分の独立reviewとユーザー承認を得た明示promoteだけで更新する。

### C. 探索開始をH0157へ拘束

既存 `search-contract.json` に以下を必須化する。

- `requirement_ids`、`family_ids`、`defect_or_gap_ids`
- gapが未解決であるpath+SHA+JSON pointer/行位置
- `decision_unlocked`（成功時に何の判断を解禁するか）
- root、形式、分母、処理数、未読数、対象外、陽性/陰性対照
- `duplicate_key = defect/gap + claim + input SHA + search-rule SHA`
- 独立review receipt

不足・閉鎖済み重複・無断範囲拡張は既存 `EA_SEARCH_SCOPE_INVALID`。範囲内全件no-hitは `rejected(entered_from=search_scoped)` とし、新契約・新duplicate key・独立reviewでだけ再開する。原作入力欠損・対象コンテナ展開不能は `blocked` とし、回収SHAでentered_fromへ戻す。registry/hook/schema/証拠登録器の障害はstateを動かさず技術的停止にする。

### D. 効果のない機能を候補へ上げない

既存 `change-contract.json` と `effect-tests.json` に、対象gap、予測される見え方、指標、期待方向、許容悪化限界、対照、戻し方を必須化する。`purpose=experiment` の隔離出力だけで試し、gapの直接解消またはH0157指標の期待方向改善と非対象回帰なしを実測できた場合だけcandidate writer tokenを出す。効果差なし・逆効果・回帰・対照不一致は既存 `EA_EFFECT_NOT_DEMONSTRATED`。

### E. 最初の実探索を旧案から自動継承しない

- rev4 P3AのG10型・隔離合成fixtureを正常対照として残す。実G10 P3Bはblockedのまま分離する。
- 実際の第1 `search-contract` は、最新currentが示すf154未解析コンテナとG10/S6/S8 gapの因果接続を、入力実在・解禁判断・重複状態で比較してから1件に決める。
- この比較は計画作成であり、コンテナ展開・コード探索・Blend変更を実行しない。
- 選択した第1契約は別の承認対象にする。

## Lunaの利用範囲

Lunaへ渡せるのは、固定されたroot/format/ruleについての列挙、件数、SHA、JSON pointer、既知署名の転記、単一変異試験の再実行。原因確定、gapとの因果、欠損値推定、候補採用、原作一致、完成宣言は主担当と独立reviewが行う。Luna出力も同じ入力スナップショットに拘束し、別SHAの結果は採用しない。

## 必須の正常対照・単一変異試験

既存revision 3のエラーIDを使い、新しい汎用判定器を増やさない。

| ID | 正常 | 1項目だけ壊す | 所期結果 |
|---|---|---|---|
| C01 | current/run-state/計画/manifest/Blendが登録SHA一致 | currentページを1バイト変更 | `EA_KB_SNAPSHOT_STALE` |
| C02 | 整理後の降格バナーとstatus correctionを読む | 旧handoffを正本指定 | `EA_KB_SNAPSHOT_STALE` |
| C03 | begin時とfinish時のread-set一致 | begin後にrun-state変更 | 採用拒否、drift記録 |
| C04 | 明示promoteに独立reviewと承認あり | 自動で新SHAへ追従 | `EA_KB_SNAPSHOT_STALE` |
| C04b | current_state_inputsが12member完全一致 | P0 4件をgroup 1件へ圧縮 | `EA_KB_SNAPSHOT_STALE` |
| C04c | 登録済み最小capability | 未登録CLI/schema/hook/actionを追加 | `EA_OPERATION_UNAUTHORIZED` |
| C05 | 実在REQ/family/gapへ接続 | gap参照を削除 | `EA_SEARCH_SCOPE_INVALID` |
| C06 | 分母・未読数・両対照あり | 陰性対照を削除 | `EA_SEARCH_SCOPE_INVALID` |
| C07 | unreadableはblocked | unreadableをno-hit/rejected化 | 状態遷移拒否 |
| C08 | 対象指標改善・回帰なし | 変更を無効化して効果差0 | `EA_EFFECT_NOT_DEMONSTRATED` |
| C09 | 同一branch・入出力SHA | finish後に候補SHA変更 | `EA_ARTIFACT_LINEAGE_BROKEN` |

各試験は正常PASS→単一変異の所期FAIL→復元後PASSを同じfixtureで記録する。unit testの成功を実hook接続・Blender実機・原作一致へ読み替えない。
別の肯定経路として、no-hit rejectedから新契約で再開、blockedから回収SHAで復帰、技術的停止からstate不変で機構復旧、P3A合成fixture PASSと実G10 P3B blocked分離を試験する。

## 計画改訂の順序（実装ではない）

1. 一本化revision 3の冒頭に、現在位置の正本と整理タスクを最優先入力として追加し、旧4枚を履歴へ降格。
2. `EA_KB_SNAPSHOT_STALE` の対象へcurrent、cleanup、run-state correction、manifest、Blend、計画SHAを追加。
3. U0/U1へ `current_state_inputs` と二重SHA照合（begin/finish）を追記。
4. U3のsearch契約へH0157拘束・重複拒否・rejected/blocked正規化を追記。
5. U4/U5のchange/effect契約へ候補昇格拒否を追記。
6. 実G10を「最初の実探索」から外し、P3AのG10型・隔離合成fixtureだけを正常対照とする。f154対G10/S6/S8の第1契約比較を計画上の停止点へ置く。
7. 上記C01〜C09の否定試験と、Lunaの許可/禁止範囲を追記。
8. 改訂後の計画を、正本・実ファイルを直接読む別actorが監査する。major finding 0件になるまで実行承認へ出さない。

## 今回やらないこと

- 一本化revision 3本文、監査revision 4本文、quality-gate、run-state既存欄、Helen/f166/Blendの変更。
- f154展開、G10/S6/S8コード探索、候補Blend作成、他14アクションへの展開。
- 新しいチケット管理、別state、ダッシュボード、検索UI、LLM有効性スコア、自動モデル選択。

## 受入条件

- 武田さんがこの具体計画を承認。
- 承認直前に上表の全入力を再SHA化し、本表との差分が無い。差があれば承認を流用せず再照合。
- 承認後に行うのは一本化計画本文の改訂と独立reviewまで。schema/guard/Helen実装は別の明示承認。

## 使わなかったもの・落とした情報

- **捨てたもの**: G10を第1実探索として自動開始する旧順序、汎用調査基盤、別state、UI、LLM採点器。
- **手元でどう変わるか**: この段階では3D成果物・画像・操作は変わらない。計画上、最新currentが示すf154候補を無視してG10へ直行できなくなる。
- **戻せるか**: 文書案なので戻せる。ただし最新正本を無視する旧順序へ戻す場合は、なぜ現行currentより優先するかの新しいユーザー判断が必要。
