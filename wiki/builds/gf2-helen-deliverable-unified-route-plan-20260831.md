---
type: build
title: Helen原作再現 成果物までの単一状態・KB活用計画 2026-08-31
status: active
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-08-31
last_reviewed: 2026-09-01
plan_status: draft-unapproved
approval_scope: unified-route-plan-revision-4
related:
  - "[[gf2-helen-repro-execution-audit-plan-20260830]]"
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
  - "[[gf2-helen-repro-v51-current]]"
  - "[[gf2-helen-cleanup-task-entry]]"
  - "[[gf2-helen-repro-v51-run]]"
  - "[[brainstorm-gf2-dusevnyj-bikini-to-helen]]"
tags: [gf2, helen, deliverable, state-machine, knowledge-base, model-routing]
revision: 4
---

# Helen原作再現 成果物までの単一状態・KB活用計画 — revision 4

## 承認状態と現在地点

> 2026-09-01 整理後の現在位置正本: [gf2-helen-repro-v51-current](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md>)。
> 整理結果: [gf2-helen-cleanup-task-entry](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-cleanup-task-entry.md>)。旧run / handoff / plan-repair / conversationは履歴へ降格し、`run-state.json` の `status_corrections_2026_09_01` を古い残作業欄より優先する。
> 原作再現の再開・承認記録: [Helen H0157専用の親メモ](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md>)。

**revision 4計画案・実装未承認。実装していない。** 最小機械監査の方針と、revision 3本文へ反映する
具体計画は2026-09-01に明示承認済み。本revision 4はその反映結果であり、schema、guard、hook、f154、
G10/S6/S8探索、Helen、f166、Blendの実装承認ではない。
revision 1は独立レビューでCritical 2・Major 5・Minor 1の差し戻し。revision 2ではCritical解消、
完成台帳の更新と並行処理の2点がMajorとして残った。revision 3はその修正を完了したが、
整理後currentへの拘束、旧版拒否、探索のH0157拘束、効果のない機能の候補昇格拒否が不足していた。
本revision 4で補う。旧計画の実行承認・対象範囲はまだ変更しない。

- 最終目的: HelenのH0157原作再現をBlender成果物として成立させる。
- 現在工程: 一本化計画のレビュー。成果物側はP0の棚卸し済み・P0B監査本体実装前。
- 今回の意味: KB、原作コード回収、効果試験、成果物比較を同じ証拠経路でつなぐ。
- 次の作業: 本revision 4を整理後の正本・実ファイルから独立レビューし、major finding 0件にする。モデル実ID配分・実行環境の設定差分・実装承認はその後に別提示する。
- 完成まで: 監査接続、入力再棚卸し、探索契約、抽出、因果審査、変更範囲承認、
  隔離実験、候補Blend、既存全要件の比較、ユーザー受入が残る。

## 高リスク成果物の6点ゲート

| 項目 | 内容 |
|---|---|
| 正解の所在 | 原作H0157動画・フレーム、原作コード、Unity prefab / renderer / material / RampSettingと実行条件 |
| 欠けうる入力 | H0157 scene join、Helen本人prefab、InternalLut、有効Volume、実行時髪処理、骨親情報、最終版f166 |
| 対象群 | 修復対象S6（顔の白飛び）・S8（髪の被覆）・G10（材質対応）と、既存品質台帳の4 family（対象群）を別軸で保持。S8のfamilyは契約時の直接証拠から単数または複数を決め、事前固定しない |
| 代表例 | 実G10ではなく、監査rev4 P3Aの**G10型・隔離合成fixture**。実G10 P3Bは参照鎖回収までblockedで、肯定経路PASSへ数えない。第1実探索はf154候補とG10/S6/S8 gapを比較して別承認で決める |
| 比較方法 | 場面・衣装・時刻・ポーズ・カメラ・ROI・画像処理条件を拘束し、原作／変更前／変更後を直接比較 |
| 停止条件 | 整理後currentまたは実ファイルのSHA変化、分母不明、参照辺不足、効果未確認、衣装・比較条件不一致、既存推定の未解消、未登録操作 |

## 目的と完成条件

監査は手段であり、監査PASSを成果物完成に数えない。今回は原計画の
**SSR0101・P1を候補とするH0157、300フレームの全身・胸の動き・色や陰影**を対象とする。
P1が比較動画の衣装と一致するかは要照合であり、一致を推定しない。
H0167は変種切替の検査入力に限る。他14アクション・他衣装への展開は今回の完成に含めない。

「S6/S8/G10の修復候補が揃う」と「H0157成果物が完成する」を分ける。
後者には以下の対応表を機械で満たすことを要求する。

| 原要件／対象 | 既存family | 完成に必要な証拠 |
|---|---|---|
| REQ2・REQ4、全身の形と構成 | mesh-static | 現行候補SHAで全構成・座標・ウェイト・表情を再検査し、欠けた部品・推定骨を一覧化して原作へ照合 |
| REQ1・REQ3・REQ7・REQ8、H0157の胸を含む動き | motion-h0157 | 300フレームの入力／出力対応、全身・胸の比較、原作動画との同期、対象限定の人間判断 |
| REQ2・REQ5・REQ10、衣装・色・陰影 | shading | G10参照鎖、S6を含む比較、既存f128等の残余欠陥、未承認近似の解消または別枝承認 |
| S8、D2アルファ髪の被覆 | 契約時にmesh-static / shadingの一方または両方 | メッシュ欠落・材質/透明処理の直接証拠を分け、根拠なしにshading単独へ固定しない |
| 切替規則の検査 | variant-switch-test | H0167の既知切替点による機械試験。完成アクション再現の承認ではないことを明示 |
| REQ9と承認済み成果物指定 | 上記全family | 同一の採用候補Blend SHAと来歴。別の旧Blendへの承認を流用しない |

原計画の全GATEと、現行quality-gateの全必須項目を候補SHAで再検査する。表の抜粋だけを検査集合にしない。
全要件・family・GATEの対照は `requirement-coverage.json` に保存する。
各行はrequirement ID、出典SHAと位置、family、test ID、candidate SHA、結果、gap ID、acceptance IDを持つ。
行欠落は `EA_REQUIREMENT_COVERAGE_MISSING`。今回の修復範囲外に残余欠陥を発見した場合は
修復を勝手に拡大せず、その欠陥を残して成果物completeを停止し、必要な変更範囲を提示する。

## 現物ベースラインと旧版拒否

2026-09-01、具体計画承認直前・直後の直接照合。KBと成果物rootはGit管理外なので、`git status`をcleanの証拠に使わない。

| 入力 | 現行SHA-256 |
|---|---|
| `gf2-helen-repro-v51-current.md` | `fd4cf11b97baaea3f955fe1ad778f508979ba8defc02fc67f89933a0f32532e1` |
| `gf2-helen-cleanup-task-entry.md` | `6a390e6d1ddf87f702550a4e4dbaa236813f714336ea45dd6765c1e1acec6d3a` |
| 本計画revision 3（改訂前） | `e1af011174cc63f37c1a85ed9db179414f6f761bfdfe8f6968f13a0dc36543c8` |
| 監査計画revision 4 | `c690d7be9986eca7f24930ffdeb45255a0f7e3b596fb0264879a2bab9b9fa7d5` |
| project `quality-gate.json` | `f7b29ca63f0d93d28f19f3fa34d54789c09493ad42ee26541b7f93ef191ffa96` |
| `06_repro-v51/run-state.json` | `b176b17bb1d1cb9c61573f8ab070fe67170ed57c69ed2fcc7f2e21e60839fc8e` |
| 現行Blend | `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` |
| P0 `writers.json` | `e09ca43104e6d163bf6b4b825d1e13be3e50084b7aaa7b3e4c826f6642302aee` |
| P0 `evidence-index.json` | `829a73ec998e37befb9656f280c5e51614d1771bc3a9587c5f60379787e3f0b9` |
| P0 `review-findings.json` | `3333d01abb9f5417992b8b44d3bf7d3f8590fb796d0712bff8fda6063c83ebaf` |
| P0 `bootstrap-status.json` | `c5be5ee4a7a2f53421c88479824791c7c4ec5813cf609c73b0255224f4ca1110` |

本計画自身のSHAを本文内へ埋めると自己参照で値が変わるため、revision 4の対象SHAは本文ではなく独立review receiptと外部登録簿へ固定する。上表は実行時の恒久値ではない。
開始直前に再測定し、差があれば自動追従せずdrift reportを作り、計画再照合と明示promoteまで停止する。
以前の `20260901-current-state-evidence.json` は現行run-state・rev4・rev3 SHAと不一致のため、過去スナップショットとしてだけ使う。
本revision 4のreview中にcurrentのLLM区画を実際の計画段階へ更新し、SHAが `919843…` から `fd4cf1…` へ変化した。自動追従せず、`20260901-unified-rev4-current-drift-reconcile.md` に差分・権限・非変更対象を記録して明示再基準化した。

現物の意味:
- 現行Blend SHA:
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- 旧plan PASS、f128は構造限定PASS、f152はFAIL。旧gate-resultsはBlend SHA無しでunbound。
- f166初回結果は走査後にスクリプトが変わりstale。7,424/7,726（96.1%）は全体MSLの既存抽出未一致率で、
  Helenに必要なコードの未回収率ではない。
- P0: 証拠48件、open finding 5件、writer候補54本。`.audit-bootstrap-20260830` の状態は `p0-complete-p0b-writer-review-required`、停止理由は `EA_WRITER_CLASSIFICATION_REVIEW_REQUIRED`。正規audit、guard、fixture、外部登録簿は未作成。
- 現行4 familyは全てuser_accepted=false。shadingはmode=approximation、approximation_approved=false。
  これを忠実版の完成済み初期入力と扱わない。
- P0Bは監査本体未導入であり、bootstrap上の次の肯定証拠はwriter 54本の独立分類。これを監査実装済みと読まない。
- 整理後currentは、LLM単独で実行可能な残りとして未解析コンテナ展開（f154候補）を1件記録。G10はrenderer→material対応不足でblocked。G10を第1実探索へ自動指定しない。

## 正本と旧規則の変更案

現在状態は事実・権限を上書きする上位規約ではない。ユーザー要件、承認、監査契約を検査した**結果**である。
状態と証拠が衝突したときは、状態を信じて続行せず拒否する。

以下は本revisionの実行承認を得た場合だけ有効になる。旧文書全文は書き換えず、対象節に後継参照を付ける。

| 旧規則 | 本計画で提案する扱い | 承認の境界 |
|---|---|---|
| rev4終了後にf166を別承認 | 本計画の実行承認はU0・U1・U2とU3の契約設計／独立reviewまで。第1search-contractの別承認後だけU4・U5へ進む。f166はその契約に必要性・範囲・SHAが明記された場合だけ実行 | 外部取得・GUI・attachは含まない。変更契約承認後だけU6 |
| 9.6のClaude必須・無断GPT代替禁止 | 探索設計／因果審査は登録済みハイエンド、抽出整理は登録済みLuna/Sonnet級 | 実行前にモデルID入り配分表を提示・承認。未登録モデルへ無断代替しない |
| rev4のG10後にS6/S8順を決定 | 読取解析は独立範囲で並行可能。出力変更順はG10の参照を確認後、依存グラフ順 | 実験・出力へ反映する順と対象群は変更契約承認時に固定 |
| 9.6のBlend変更別計画 | **因果審査後の変更範囲承認を残す**。同一ルート内の `change-contract.json` を提示 | U0〜U5承認はU6の実験／候補Blend書出し許可ではない |
| rev4欠陥state | 同じaudit/state.jsonのdefectsを拡張。全体phaseを別に手書きしない | 本計画の実行承認でstate schema差分を承認 |
| a10は2欄だけマージ | 維持。U7の受入登録はa10と別の限定トランザクションで行う | 受入記録のあるfamily・候補だけ更新 |

## 単一状態: 保存する値と導出する値

予定済み `06_repro-v51/audit/state.json` を使い、別のproject-stateは新設しない。

保存する進捗は次だけ:
- `shared_readiness`: `baseline_frozen / enforcement_ready / inventory_verified` の共有準備軸。
- `branches[branch_id].defects[defect_id].stage`: 欠陥別進捗。
- `entered_from / reason_id / evidence_refs`: blocked等からの復帰上限。
- `generation`、遷移履歴、承認記録への参照。遷移履歴はappend-only。

`current_phase`、`phase_purpose`、`allowed_actions`、全体完了は**読み取り時に導出**し、
書込APIで任意の文字列へ設定できない。枝ごとのdefect集合はmanifestのrequested_defectsと完全一致させる。
未知・欠落・重複は拒否する。共有準備が不合格なら欠陥側が進んでいても操作不可。

欠陥stageは
`scoped → search_scoped → candidate_traced → causal_reviewed → causal_tested →
artifact_candidate → artifact_measured → human_review → accepted`。
共有inventory_verifiedに加え、各search契約の分母・未回収数が一致して初めてsearch_scopedへ進む。
rev4のcoverage_verifiedの要件を弱めず、共有在庫＋欠陥探索範囲へ分解する。

終了・復帰は `entered_from` とreasonを区別する。
- 全範囲処理・陽性対照PASS・候補なしは `rejected`、`entered_from=search_scoped`。再開には、旧duplicate keyと異なる
  新 `search-contract`、追加または修正规則の直接根拠、独立reviewが必要で、`search_scoped` へだけ戻れる。
- 候補の反証による既存rev4の `rejected` は `entered_from=candidate_traced|causal_reviewed` とし、新candidate IDでだけ戻す。
- 原作入力欠損・対象コンテナ自体の展開不能は `blocked`。回収物のpath+SHAまたは展開可能化の肯定証拠で `entered_from` へ戻す。
- registry、hook、schema、証拠登録器の障害は欠陥stateを動かさない。`EA_REGISTRY_UNAVAILABLE`、
  `EA_HOOK_UNOBSERVED` 等で**技術的停止**し、同じ入力で機構の正常化を確認してから再試行する。

導出規則:
1. 正本・SHA・schema衝突は全体停止。
2. 共有準備が未完なら、その準備操作だけ許可する。
3. blocked欠陥に依存しない他欠陥の読取・解析は許可する。依存グラフと入出力を照合する。
4. G10がblockedでもS6解析済みなら「G10: blocked／S6: causal_reviewed」のように併記する。
   全体を最も進んだ一件に合わせない。
5. 各操作は `branch_id + defect_ids + action_id` に対して判定する。全体表示文を権限に使わない。
6. 全件acceptedでも、要件・4 family・既存gate・出力SHA・受入記録が不足なら全体completeはfalse。

legacy run-stateは過去履歴を維持する。現在判断では `status_corrections_2026_09_01` を古い
`agent_executable_remaining` 等より優先し、currentページの機械区画・実ファイルと直接照合する。
current_step / next_action / passed_gates / failed_gates / current_failure / 現行Blend参照だけを移行対象として一覧化する。U0で旧値のSHA保存と移行差分を作り、
U1導入時に現在欄を正本stateから生成する。history内の古いSHAや昔の方針は矛盾判定に含めない。
stateとlegacy現在欄を別々に手書きする操作は禁止する。

## 実際に機械で強制する範囲

任意のOS操作や自由文の意味を完全に制御できるとは主張しない。
保証するのは、**登録済み操作の実行許可・結果採用・正式な状態報告・完成登録**である。

`audit_guard.py` に `begin_operation / finish_operation / status / transition` を用意する。
各U工程は必ず登録済みaction_idでbeginを呼び、finish前の結果は工程完了証拠として採用できない。

新しいlockファイルや第二の正本は作らない。既存 `quality-gate.json.execution_audit.current_state_inputs` は
`schema_version / member_count / members[] / set_sha256` を持つ。各memberは `input_id / absolute_path / role / sha256` を必須とし、
current、cleanup、run-state、監査計画、本計画、quality-gate、Blend、P0の `writers.json` / `evidence-index.json` /
`review-findings.json` / `bootstrap-status.json` を**別々のmember**として列挙する。独立review完了後はreview receiptもmemberへ追加し、
実行入力は合計12memberとする。`P0 bootstrap`のような一語への圧縮、member欠落、余分なmember、重複IDを拒否する。
`audit/evidence-index.json` に取得時刻、取得コマンド、size、mtime、SHA、読んだJSON pointer／行位置を持たせる。
`begin_operation`、`finish_operation`、正式登録、plan/batch/completeのたびに実ファイルを再SHA化する。
不一致・欠損・別パス・読取不能なら許可証またはwriter tokenを出さず `EA_KB_SNAPSHOT_STALE`。
作業中に別エージェントが変更した場合も採用せず、変更前後SHAと差分を隔離記録する。
基準SHAの自動更新は禁止し、差分再読・独立review・明示promoteでだけ更新する。

監査機能自身の無効機能追加も止める。rev4で予定済みの外部登録簿
`tools/project_quality_gate_required_audits.json` に `approved_capabilities[]` を持たせ、各要素を
`capability_id / action_ids / cli_entrypoints / schema_fields / hook_branches / requirement_or_audit_refs /
positive_test_ids / mutation_test_ids` へ拘束する。schema項目、guard分岐、hook分岐、CLI、actionの全てが、
H0157要件または承認済み監査rev4の節と所期変異試験へ結び付かない場合は登録できない。
未登録capabilityのbegin、CLI、schema受入、hook分岐は既存 `EA_OPERATION_UNAUTHORIZED` で拒否する。
新しいcapabilityを足すには、計画差分・H0157への必要性・正常/単一変異試験・外部登録簿promoteの別承認を要求する。

| 登録操作 | 必要状態 | 必須出力 |
|---|---|---|
| U1.bootstrap | 承認・旧plan PASS・P0再照合 | 一時一式試験、復元可能な導入、新plan結果 |
| U2.inventory | enforcement_ready | 入力一覧、script SHA、走査開始終了、結果SHA |
| U3.search | inventory_verified | KB snapshot、探索契約、独立レビュー |
| U4.extract | 対象defectのsearch_scoped | 範囲内全件の成功／失敗／対象外台帳、原文・位置 |
| U5.review | candidate_traced | 因果仮説、反証、実行条件、試験設計 |
| U6.experiment | causal_reviewed＋変更契約承認 | 隔離実験出力、対照、測定値、回帰結果 |
| U6.candidate | causal_tested＋同じ変更契約 | begin/finish writer記録、採用候補SHA |
| U7.compare / accept | artifact_candidate以降 | 全要件・4 family比較、受入、限定登録結果 |

許可証はoperation ID、actor ID、plan SHA、current-state input-set SHA、branch、defect集合、発行時のstate generation、
依存read-set（共有準備、対象欠陥と依存欠陥、枝、承認、各入力の版とSHA）、script／入力SHA、
許可出力先、変更契約SHA、有効期間、単回使用nonceを拘束する。
finishとstate書込はlockで直列化する。別の独立操作が終わり全体generationだけ変わった場合は、
lock下でread-set・対象状態・入力不変・役割・権限・出力を再検査して採用できる。
依存read-setが変わった場合、同じ欠陥を別操作が進めた場合、期限切れ・nonce再使用は拒否する。
古いgenerationのまま書き戻さず、再検査結果と最新generationに基づいて新しい一件をappendする。
独立2件の両finish成功と、共有入力／依存欠陥変更時の拒否を別々に試験する。

### 実行入口・出力への接続

コード配置案は既存audit_guardとWiki側 `tools/helen_route_hook.py`（**未作成**）。
実際に実行する環境のPreToolUse／PostToolUse／Stopへ、プロジェクトパス限定adapterを接続する。
最初からCodexとClaude両方の導入完了を要求しない。未接続環境はその環境の操作を拒否し、
利用する時だけ設定差分承認と同じ実イベント試験を通す。レビュー役の読取は正式成果物操作と区別する。
設定変更の実パス・既存設定との差分・復元方法は、U1実行前の承認資料へ列挙する。
他プロジェクトの動作は変更しない。設定変更が承認されていない環境では未接続として停止する。

- 登録操作は検査済みコマンドと許可証で実行。曖昧なshell、動的生成コード、別ツール、GUI等を
  静的文字検索だけで安全と判定しない。入口が検査できない操作は自動実行対象外。
- 実ツール呼出しIDでbegin／終了イベントを結び付ける。
  `functions.exec` 等で包まれた呼出しを観測できるとは仮定せず、実イベントで試験する。
- 未接続、hook自体の異常、ID欠落ではenforcement_readyへ進めない。
  起動コードを読み込む前のSHA照合とbootstrap復元はrev4に従う。
- 自由文の全意味を保証しない。正式statusはguard生成JSON／HTMLとそのSHAだけ。
  Stop接続では現在generationに合う正式statusの添付を要求する。
  任意の会話文を原作一致・承認・進捗証拠として採用しない。
- 接続試験は登録コマンド許可、無許可コマンド拒否、別ツール入口、hook故障、
  古いstatus、圧縮／再開後のgeneration不一致を**実イベントで**確認する。
  単体fixtureだけでは実行強制済みと報告しない。

## KB・探索・候補・検証のデータ契約

新しい巨大全文コーパスは作らない。全量在庫は軽量な一覧、本文保存は探索契約に一致した候補だけ。

1. `knowledge-snapshot.json`
   - 最優先入口は整理後currentとcleanup。原計画REQ/OBS/GATE、監査計画、本計画、実ファイルを現行入力とし、run、handoff、9.6、旧current-state-evidenceは履歴として記録。
   - path、file SHA、主張ID／節・範囲、status、evidence_level、last_reviewed、used_for、
     原典／現物への参照を必須にする。legacyは一次資料再確認記録無しに確定根拠へ使わない。
   - ハイエンドモデルはsnapshotとそこから辿った原典を読む。wikiを読んだだけで原典確認済みとしない。
2. `search-contract.json`
   - `requirement_ids`、`family_ids`、`defect_or_gap_ids`、gapが未解決であるpath+SHA+JSON pointer／行位置、
     `decision_unlocked`、`claim_to_test`、`what_this_rules_out` を必須にする。
   - 対象ルート・形式・分母・処理数・未読数、シンボルと参照候補、対象外、陽性／陰性対照、
     否定可能範囲、追加形式へ広げる条件を固定する。
   - `duplicate_key = defect/gap + claim + input SHA集合 + search-rule SHA`。閉鎖済み同一keyは新規開始を拒否し既存記録を返す。
   - 独立review receiptが無い、実在IDへ拘束できない、無断拡張、分母・対照欠落は `EA_SEARCH_SCOPE_INVALID`。
     全範囲処理で候補なしは上記search由来 `rejected`、原作入力欠損・対象コンテナ展開不能は `blocked`。
     registry/hook/schema/証拠登録器の障害はstateを動かさず技術的停止。別state語を作らない。
3. `code-corpus.json / code-candidates.json`
   - 前者は抽出結果索引。入力集合の全件に成功／失敗／対象外と理由を付け、無記録を許さない。
   - 後者は候補の原文パス・位置・SHA、入力出力、依存辺、欠損辺、defect、review参照。
     欠損辺のある候補は発見済みとして保存できるがcandidate_tracedへの昇格は禁止。
4. `effect-hypotheses.json / change-contract.json / effect-tests.json`
   - 因果仮説と反証、単一変更または不可分な変更集合の定義、入力、試験対照、指標、期待方向、
     許容悪化限界、非対象回帰、戻し方を固定する。
   - 不可分な変更集合は独立因果審査で分割不能の理由を記録し、変更契約の対象として提示する。
   - 対象gapの直接解消または事前登録H0157指標の期待方向改善と、非対象回帰が限界内であることを実測する。
     効果差なし、逆効果、回帰、対照不一致は `EA_EFFECT_NOT_DEMONSTRATED` とし、candidate writer tokenを出さない。
5. `artifact-lineage.json / requirement-coverage.json`
   - 変更候補、実験、入力、writer、候補Blend、原作比較画像、全要件、受入を同じSHA鎖へ接続する。

各出力のreview receipt（検証担当の実行記録）には
`run_id / actor_id / model_id / role / input_refs(path+SHA) / output_refs(path+SHA) /
producer_actor_ids / operation_id` を必須にする。
actor_idはオーケストレータの実タスク／実行IDから解決し、モデルが書いた別名だけでは別人と認めない。
モデル名が同じ別タスクは独立可能だが、入力は正本と生データに限定し、制作側の成功説明を判定根拠にしない。
運用者によるOS上の記録改竄まで耐タンパ保証するものではない。

禁止兼任: 探索契約作成／その検証、候補整理・候補実装／因果審査、
実験・候補制作／独立原作比較、受入記録の自己捏造。
欠落ID、同一runの別名、同一actor、入力SHA違い、古いreviewの流用を拒否する。
モデル能力の上下は機械判定しない。承認済み `model-routing.json` の具体model ID／role／代替順と照合する。
未登録モデルしか使えなければその役割だけ技術的停止し、入力が独立した許可済み作業は継続する。

Lunaへ渡せるのは、固定済みroot／format／ruleについての列挙、件数、SHA、JSON pointer、既知署名の転記、
単一変異試験の再実行だけ。原因確定、gapとの因果、欠損値推定、候補採用、原作一致、完成宣言は渡さない。
Luna出力も同じcurrent-state input-set SHAに拘束し、別SHAの結果は採用しない。

## 忠実版・実験・近似版の分離

- 初期枝 `baseline-observed` は既存Blendの観測記録であり、忠実版合格とはしない。
  quality-gateのknown_gaps、未承認近似、推定骨、欠けた材質・動きの対応をgap IDで保持する。
- `faithful-h0157` は基準Blendを親参照として使えるが、そのgapを全て継承し、影響を消すか
  対象外を直接証明するまでcomplete不可。人間が見た目を許容しただけで推定を原作由来へ変換しない。
- 近似版を作る場合は別のbranch ID、出力先、mode、比較、承認を要求する。今回は自動作成しない。
- 許可証、実験、writer、review、受入にbranch IDとmodeを拘束する。
  近似枝acceptedや旧基準の承認を忠実枝へ流用しない。

### 効果試験前に許す隔離実験

`causal_reviewed` と**変更契約の明示承認**が揃った場合だけ、実験writerに
`purpose=experiment` のbegin/finishを発行する。
experiment ID、親Blend SHA、入力・変更SHA、枝、専用出力先を固定する。
親Blend・正規出力はread-only扱い。実験出力をrepresentative outputやacceptedへ登録する操作は拒否する。

実験用Blendまたはメモリ内一時変更から、対照画像と測定差を作る。
効果予測、非対象の回帰、独立比較を通してcausal_testedへ進む。
合格した実験Blendをcandidate専用経路でバイト一致コピーし、元の実験記録とSHAを再照合する。
再生成する場合は別の候補として同じ試験を再実行し、再現性を仮定しない。
これで「効果確認前には効果測定用Blendも作れない」循環を除く。

## 実装順と承認関所

- **U0 現行入口と入力拘束**: 本計画実行承認＋計画独立レビュー合格後、整理後current、cleanup、
  run-state correction、実ファイル、P0 bootstrapを再取得する。revision 4で承認されたinput-setと1件でも違えば
  `EA_KB_SNAPSHOT_STALE` で停止し、drift reportを作る。旧4枚を現在正本へ戻さない。新stateやguardの存在をU0開始条件にしない。
- **U1 監査接続**: U0後の同一性を開始条件にrev4 bootstrapを実行。
  単一state、登録操作、役割台帳、実イベント接続、非破壊マージ、復元、writer54本の独立分類を試す。
  設定実パス・model ID配分表の承認が不足なら、その導入の前で停止する。
- **U2 第1探索候補の比較**: 最新currentが示す未解析コンテナ展開（f154候補）とG10/S6/S8 gapについて、
  入力実在、解禁する判断、既存重複、H0157への因果候補を読み取りだけで比較する。G10は正常fixtureであり、
  旧順序から第1実探索へ自動昇格させない。f166の修理・全量走査も、選ばれた契約が必要とする場合だけ対象にする。
- **U3 探索設計**: ハイエンドがKBと原典から第1 `search-contract` 1件を作り、別役割が直接照合。
  requirement/family/gap拘束、分母、両対照、duplicate key、rejected/blocked閉鎖を固定し、ユーザーの別承認を待つ。
- **U4 抽出整理**: 承認された1契約の登録形式だけを機械抽出しLuna/Sonnet級が候補整理。
  追加形式は探索契約条件に一致するローカル範囲だけ。無条件のDLL/Lua/MonoScript全深掘りはしない。
- **U5 因果審査**: 別ハイエンドが候補の実行条件、反証、原作差への経路を審査。
  ここで具体的変更契約（対象、予測される見え方、実験、回帰、戻し方）を提示し承認待ち。
- **U6 実験・候補**: 変更契約承認後、隔離実験と検証済みcandidate生成を行う。
  変更契約から外れる変更を要したら再承認。承認対象群を横展開しない。
- **U7 成果物照合と受入**: 同じcandidate SHAで全要件・4 family・全既存GATEを再測定。
  LLMが差と残余gapを先に列挙し、代表1〜4件の許容判断をユーザーへ求める。
  H0157全身の300フレーム検査は代表画像の数で代替しない。
  accepted / blocked / contested / rejectedを実態に合わせて保持する。

監査から抽出へ戻るためだけの確認は繰り返さない。一方、候補の変更内容が未確定な時点で
Blend変更・見た目の許容まで一括承認したとは記録しない。
上記以外でも新しい構造、対象変更、GUI、外部連携、強制DL、attach、不可逆操作は承認を求める。

## U7の正式受入登録

`a10_quality_gate.py`の2欄以外を変えない規則は維持する。受入登録はguardの別APIで行う。
各familyへcandidate SHA、比較記録、gapの処理、承認者、時刻、実カード／会話の承認根拠、
対象群・入力条件・比較項目を束縛する。source_compared等は証拠から計算し、自由なtrue代入を受け入れない。
variant-switch-testの承認は検査範囲のみで、H0167完成を意味しない。

manifest更新は受入対象familyの限定キー（representative_output、comparison_evidence、source_compared、
user_accepted、batch_safe、approved_by、approved_at、approval_evidence、accepted_gaps）だけ。
known_gaps、原作資料、歴史、他family、未知キーは保持する。
mode変更や必須試験削除は受入操作の対象外。approximation_approvedは近似枝別承認がある場合だけ別登録する。

### 証拠に基づく完成台帳更新（受入とは別の限定操作）

受入操作の後に `U7.reconcile_completion` を実行する。a10の許可範囲を拡大するのではなく、
以下のキーだけを、同一候補SHAの直接証拠から更新する。証拠を生成せず既存欄をtrueにする操作は禁止。

- `families[*].missing_inputs`: 元のrequired input IDと結合できる回収物、実SHA、参照鎖と独立確認が
  揃った入力だけを現行欠損集合から除く。旧欠損と回収根拠はgap履歴へ保持する。
- `families[*].known_gaps`: 現行の未解消gap集合を投影する。解消済みgapは直接比較・試験・独立reviewを
  根拠に除外し、元の文言と解消記録は追記専用履歴へ残す。上記「保持」は歴史保持を意味し、
  解消済みを永遠に現行欠陥として残す意味ではない。
- `families[*].accepted_gaps`: **残っているが許容された**gapだけに限定する。
  解消済みと許容済みを混ぜず、忠実版の必須入力不足・推定を許容だけで消さない。
- `families[*].mode`: branch/modeに対応して更新。faithfulへの変更は全継承近似・推定の解消または
  不適用の直接証明、必要入力回収、当該候補の独立比較が揃う場合だけ。gapを消すためのmode変更は禁止。
- `verifier.method / source_read_directly / output_read_directly / report_path / evidence / findings_addressed`:
  今回の候補を直接比較した独立actor receiptと報告から更新する。旧監査記録は保存し、旧reportを使い回さない。
- `batch.completed / result_count / audit_report / final_audit_passed`: 承認済みrequested_countとrequested_familiesを
  変えず、来歴・全GATE・要件・4familyを満たす成果物台帳から集計する。現在requested_count=1であり、
  H0157候補1件を数え、4familyや試験画像を4件以上の成果物と誤算入しない。

解消・mode移行・集計に必要な証拠が無ければ該当欄を変更せずcompleteを停止する。
原作入力の所在、旧履歴、他family、未知キー、absence_claimsの要求、必須検査定義は変えない。
absence_claimsで指摘が残る場合は、その不在主張台帳の根拠訂正を別の登録操作で行い、検査自体を外さない。
元JSONの回復用コピーとキー別差分を保存し、許可キー以外のdeep comparison一致を必須にする。
下記journal方式を受入／完成台帳更新の両方へ適用する。

正常fixtureでは、未完了・欠損・未承認近似の初期台帳から、回収・gap解消・受入・集計を順に適用し、
**既存共有gateのcompleteまでPASSする肯定経路**を試験する。固定辞書でPASS台帳を直接作って代用しない。
欠損1件、未承認gap、近似modeのまま忠実完了、旧verifier、誤ったresult_count、途中更新をそれぞれ拒否する。

state、manifest、証拠索引の複数ファイルを「単純なrename一つでatomic」と主張しない。
更新前SHA・回復用コピー・書込予定差分をjournalへ保存し、lock下で更新する。
pending journalがある間は全採用／completeを拒否する。途中失敗は全旧SHAへ復元し、
再照合PASS後に再開する。journalはトランザクション証拠であり第2の進捗正本ではない。
completeは全ての導出条件と共有品質ゲートが同じ世代・同じ候補SHAでPASSした場合だけ許す。

## 必須の否定試験

既存rev4の試験に加え、各項目を1つだけ壊したfixtureと正常対照を用意する。

| ID | 必ず検出する欠陥 |
|---|---|
| EA_OPERATION_UNAUTHORIZED | begin無し結果、別action、期限切れ・再使用・依存read-set変更の許可証 |
| EA_HOOK_UNOBSERVED | 実ツール／Stop接続未観測、包まれた呼出しの未捕捉、hook異常 |
| EA_PHASE_DERIVATION_MISMATCH | 全体phase手書き、G10 blockedを別欠陥の進捗で隠す、legacy履歴と現行の混同 |
| EA_REQUIREMENT_COVERAGE_MISSING | 3欠陥acceptedだけで4 family・全要件未完を素通し |
| EA_EXPERIMENT_PROMOTION_DENIED | 未検証実験出力の正式採用、承認前実験、親Blend改変 |
| EA_REVIEW_ROLE_COLLISION | 同一actor／別名run、入力SHA違い、候補制作側の自己比較 |
| EA_BRANCH_MISMATCH | 近似枝の証拠・承認を忠実枝へ流用、既存gapの消去 |
| EA_KB_SNAPSHOT_STALE | 整理後current・cleanup・run-state correction・計画・manifest・Blend・P0 bootstrapのSHA変更、旧4枚の正本化、begin後のread-set変更、自動SHA追従 |
| EA_SEARCH_SCOPE_INVALID | H0157の実在REQ/family/gap参照欠落、分母・対照・未処理件数の欠落、閉鎖済みduplicate key、未登録形式の無断追加 |
| EA_EFFECT_NOT_DEMONSTRATED | 効果なし、逆効果、非対象回帰、対照条件不一致 |
| EA_ARTIFACT_LINEAGE_BROKEN | candidate／画像／writerのSHA相違、別候補への承認流用 |
| EA_ACCEPTANCE_TRANSACTION_PENDING | 受入更新途中のcomplete、限定外キー変更、復元不一致 |

IDを返すだけでは合格ではない。正常対照PASS、単一変異の所期FAIL、復元後PASSを記録する。
実イベント接続、OS/Blender試験、人間判断は別々に報告し、単体試験で代替しない。

旧版拒否は少なくとも、(1) currentを1バイト変更、(2) 旧handoffを正本指定、(3) begin後にrun-state変更、
(4) review無しで基準SHAを自動更新、を1件ずつ変異させる。探索閉鎖は、unreadableをno-hit/rejectedへ誤分類する変異を
`blocked`維持で拒否する。効果試験は変更を無効化して差0にする変異を `EA_EFFECT_NOT_DEMONSTRATED` で拒否する。

## 完成・未完成と保証の限界

- faithful completeは全要件と4 familyの証拠、既存全ゲート、未解決重大指摘なし、
  対象候補への明示受入が揃った場合だけ。
- 入力不足や不明な実行条件はblocked。コードの量やモデルの高評価で上書きしない。
- 内部数値と見た目の原作一致は別。モデルの判断品質や任意自由文の意味全体を機械が保証するとは言わない。
- この文書・HTMLは設計資料。運用の正式statusではなく、まだどの接続も実装・実機試験していない。

## 使わなかったもの・落とした情報

1. **全工程の無条件な一括実行承認**は採用しない。
   監査→ローカル回収→因果審査は継続できるが、具体的変更が決まった地点で一度戻る。
   無停止の速さを失う代わりに、未確定の見た目変更を無断適用しない。
   変更契約の内容とリスクが確定し承認されれば、その範囲は継続できる。
2. **任意操作・自由文を全部停止できるという表現**を削る。
   正式status／結果採用は機械で制限するが、任意の文章が常に正しい保証はしない。
   この限界は文章上の工夫だけでは戻せず、実行環境が提供する強制境界の実試験が必要。
3. **7,424件の無条件深掘り**はしない。
   原文データは捨てず、契約一致候補へ解析を限定する。見落としリスクは対照と別レビューで検出し、
   契約に従い追加形式・範囲を広げられる。
4. **第二の全体phase**は保存しない。
   欠陥別状態と共有準備から現在地を導出するため、一覧の見え方は複数状態併記になる。
   自由な一語への集約は戻さない。
5. **G10を第1実探索として自動開始する順序**と、**f166全量修理を無条件に先行する順序**は採用しない。
   手元ではG10が正常fixtureとして残り、最初の実探索はf154とG10/S6/S8の比較後に別承認となる。
   すぐ探索を始める速さを失うが、整理後currentが示す候補を無視した旧順序と、H0157に効かない全量作業を防ぐ。
成果物の部品や原作入力自体は今回削除していない。Blendの見た目は変更なし。

## 根拠・再開の入口（実パス）

KB-root:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

成果物root:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz`

- 本計画: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md
- 整理後current: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md
- 整理タスク結果: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-cleanup-task-entry.md
- 承認済み具体計画: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-h0157-mechanical-audit-concrete-integration-plan.md
- 監査rev4: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md
- 技術9.6: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md
- 実行記録: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md
- 引継ぎ: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md
- pipeline: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-character-repro-pipeline.md
- 原REQ: /Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md
- 品質台帳: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
- brainstorm親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md
- revision 3独立レビュー（本revision 4には流用しない）: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260831-unified-route-plan-independent-review.md
- レビュー対象旧版: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260831-unified-route-plan-revision-1.md

## 矛盾・未確定

- revision 3の独立再レビューは未解消Critical/Majorなしだったが、対象SHAと仕様が本revision 4で変わったため流用しない。
  revision 4の新SHAを固定し、整理後current・実ファイルを直接読む独立reviewを新規実施する。
- モデル実ID配分表、hook設定差分、第1search-contract、U5以後の具体変更契約は未承認。
- 実機のhook到達性、作業遮断、Blender見た目は未試験。
- 現行旧計画の承認境界は、本計画が実行承認されるまで維持する。
