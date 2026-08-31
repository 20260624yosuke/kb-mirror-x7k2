---
type: build
title: Helen原作再現 成果物までの単一状態・KB活用計画 2026-08-31
status: active
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-08-31
last_reviewed: 2026-08-31
plan_status: draft-unapproved
approval_scope: unified-route-plan-revision-3
related:
  - "[[gf2-helen-repro-execution-audit-plan-20260830]]"
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
  - "[[gf2-helen-repro-v51-run]]"
  - "[[brainstorm-gf2-dusevnyj-bikini-to-helen]]"
tags: [gf2, helen, deliverable, state-machine, knowledge-base, model-routing]
revision: 3
---

# Helen原作再現 成果物までの単一状態・KB活用計画 — revision 3

## 承認状態と現在地点

**計画案・未承認。実装していない。** 承認済みなのは一本化を計画化する方針だけ。
revision 1は独立レビューでCritical 2・Major 5・Minor 1の差し戻し。revision 2ではCritical解消、
完成台帳の更新と並行処理の2点がMajorとして残った。本revision 3はその修正案であり、
旧計画の承認・対象範囲をまだ変更しない。レビュー記録と旧版は末尾の実パスから辿れる。

- 最終目的: HelenのH0157原作再現をBlender成果物として成立させる。
- 現在工程: 一本化計画のレビュー。成果物側はP0の棚卸し済み・P0B監査本体実装前。
- 今回の意味: KB、原作コード回収、効果試験、成果物比較を同じ証拠経路でつなぐ。
- 次の作業: モデル実ID配分・実行環境の設定差分を確定し、実行承認を得る。実装は未開始。
- 完成まで: 監査接続、入力再棚卸し、探索契約、抽出、因果審査、変更範囲承認、
  隔離実験、候補Blend、既存全要件の比較、ユーザー受入が残る。

## 高リスク成果物の6点ゲート

| 項目 | 内容 |
|---|---|
| 正解の所在 | 原作H0157動画・フレーム、原作コード、Unity prefab / renderer / material / RampSettingと実行条件 |
| 欠けうる入力 | H0157 scene join、Helen本人prefab、InternalLut、有効Volume、実行時髪処理、骨親情報、最終版f166 |
| 対象群 | 修復対象S6（顔の白飛び）・S8（髪の被覆）・G10（材質対応）と、既存品質台帳の4 family（対象群）を別軸で保持 |
| 代表例 | G10の参照鎖。G10の成功・承認をS6/S8や全身・動きへ流用しない |
| 比較方法 | 場面・衣装・時刻・ポーズ・カメラ・ROI・画像処理条件を拘束し、原作／変更前／変更後を直接比較 |
| 停止条件 | 分母不明、根拠SHA不一致、参照辺不足、効果未確認、衣装・比較条件不一致、既存推定の未解消、未登録操作 |

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
| REQ2・REQ5・REQ10、衣装・色・陰影 | shading | G10参照鎖、S6/S8を含む比較、既存f128等の残余欠陥、未承認近似の解消または別枝承認 |
| 切替規則の検査 | variant-switch-test | H0167の既知切替点による機械試験。完成アクション再現の承認ではないことを明示 |
| REQ9と承認済み成果物指定 | 上記全family | 同一の採用候補Blend SHAと来歴。別の旧Blendへの承認を流用しない |

原計画の全GATEと、現行quality-gateの全必須項目を候補SHAで再検査する。表の抜粋だけを検査集合にしない。
全要件・family・GATEの対照は `requirement-coverage.json` に保存する。
各行はrequirement ID、出典SHAと位置、family、test ID、candidate SHA、結果、gap ID、acceptance IDを持つ。
行欠落は `EA_REQUIREMENT_COVERAGE_MISSING`。今回の修復範囲外に残余欠陥を発見した場合は
修復を勝手に拡大せず、その欠陥を残して成果物completeを停止し、必要な変更範囲を提示する。

## 現物ベースライン

2026-08-31の直接照合:
- 現行Blend SHA:
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- 旧plan PASS、f128は構造限定PASS、f152はFAIL。旧gate-resultsはBlend SHA無しでunbound。
- f166初回結果は走査後にスクリプトが変わりstale。7,424/7,726（96.1%）は全体MSLの既存抽出未一致率で、
  Helenに必要なコードの未回収率ではない。
- P0: 証拠48件、open finding 5件、writer候補54本。正規audit、guard、fixture、外部登録簿は未作成。
- 現行4 familyは全てuser_accepted=false。shadingはmode=approximation、approximation_approved=false。
  これを忠実版の完成済み初期入力と扱わない。
- 旧記録の「P0B step 2で停止」は現物と不一致。正しくは「P0完了・P0B本体実装前」。

## 正本と旧規則の変更案

現在状態は事実・権限を上書きする上位規約ではない。ユーザー要件、承認、監査契約を検査した**結果**である。
状態と証拠が衝突したときは、状態を信じて続行せず拒否する。

以下は本revisionの実行承認を得た場合だけ有効になる。旧文書全文は書き換えず、対象節に後継参照を付ける。

| 旧規則 | 本計画で提案する扱い | 承認の境界 |
|---|---|---|
| rev4終了後にf166を別承認 | U1監査とU2〜U5のローカル読取・抽出・解析を同じ承認範囲で継続 | 本計画の実行承認で置換。外部取得・GUI・attachは含まない |
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

導出規則:
1. 正本・SHA・schema衝突は全体停止。
2. 共有準備が未完なら、その準備操作だけ許可する。
3. blocked欠陥に依存しない他欠陥の読取・解析は許可する。依存グラフと入出力を照合する。
4. G10がblockedでもS6解析済みなら「G10: blocked／S6: causal_reviewed」のように併記する。
   全体を最も進んだ一件に合わせない。
5. 各操作は `branch_id + defect_ids + action_id` に対して判定する。全体表示文を権限に使わない。
6. 全件acceptedでも、要件・4 family・既存gate・出力SHA・受入記録が不足なら全体completeはfalse。

legacy run-stateは過去履歴を維持し、current_step / next_action / passed_gates / failed_gates / current_failure /
現行Blend参照だけを移行対象として一覧化する。U0で旧値のSHA保存と移行差分を作り、
U1導入時に現在欄を正本stateから生成する。history内の古いSHAや昔の方針は矛盾判定に含めない。
stateとlegacy現在欄を別々に手書きする操作は禁止する。

## 実際に機械で強制する範囲

任意のOS操作や自由文の意味を完全に制御できるとは主張しない。
保証するのは、**登録済み操作の実行許可・結果採用・正式な状態報告・完成登録**である。

`audit_guard.py` に `begin_operation / finish_operation / status / transition` を用意する。
各U工程は必ず登録済みaction_idでbeginを呼び、finish前の結果は工程完了証拠として採用できない。

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

許可証はoperation ID、actor ID、plan SHA、branch、defect集合、発行時のstate generation、
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
   - 原計画REQ/OBS/GATE、run、handoff、監査計画、本計画、9.6、再現pipelineの採用節を記録。
   - path、file SHA、主張ID／節・範囲、status、evidence_level、last_reviewed、used_for、
     原典／現物への参照を必須にする。legacyは一次資料再確認記録無しに確定根拠へ使わない。
   - ハイエンドモデルはsnapshotとそこから辿った原典を読む。wikiを読んだだけで原典確認済みとしない。
2. `search-contract.json`
   - 対象ルート・形式・分母、シンボルと参照候補、対象外、陽性／陰性対照、
     否定可能範囲、追加形式へ広げる条件を固定する。
3. `code-corpus.json / code-candidates.json`
   - 前者は抽出結果索引。入力集合の全件に成功／失敗／対象外と理由を付け、無記録を許さない。
   - 後者は候補の原文パス・位置・SHA、入力出力、依存辺、欠損辺、defect、review参照。
     欠損辺のある候補は発見済みとして保存できるがcandidate_tracedへの昇格は禁止。
4. `effect-hypotheses.json / change-contract.json / effect-tests.json`
   - 因果仮説と反証、単一変更または不可分な変更集合の定義、入力、試験対照、指標、期待方向、
     許容悪化限界、非対象回帰、戻し方を固定する。
   - 不可分な変更集合は独立因果審査で分割不能の理由を記録し、変更契約の対象として提示する。
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

- **U0 記録訂正**: 本計画実行承認＋計画独立レビュー合格後、既存の記録と正本入口だけ非破壊訂正。
  P0・Blend SHAを再確認し、旧plan PASSを取得。新stateやguardの存在をU0開始条件にしない。
- **U1 監査接続**: U0後の同一性を開始条件にrev4 bootstrapを実行。
  単一state、登録操作、役割台帳、実イベント接続、非破壊マージ、復元、writer54本の独立分類を試す。
  設定実パス・model ID配分表の承認が不足なら、その導入の前で停止する。
- **U2 在庫修理**: f166を最小修理して1回全量走査。旧結果保持、NUL正規化、post差分、
  vertex控除、LZ4失敗を理由付き再集計。非決定性が観測された場合だけ2回目。
- **U3 探索設計**: ハイエンドがKBと原典からS6/S8/G10別契約を作り、別役割が直接照合。
- **U4 抽出整理**: 登録形式を機械抽出しLuna/Sonnet級が候補整理。
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
| EA_KB_SNAPSHOT_STALE | 採用KB／原典のSHA・status変更、根拠不足のlegacy採用 |
| EA_SEARCH_SCOPE_INVALID | 分母・対照・未処理件数の欠落、未登録形式の無断追加 |
| EA_EFFECT_NOT_DEMONSTRATED | 効果なし、逆効果、非対象回帰、対照条件不一致 |
| EA_ARTIFACT_LINEAGE_BROKEN | candidate／画像／writerのSHA相違、別候補への承認流用 |
| EA_ACCEPTANCE_TRANSACTION_PENDING | 受入更新途中のcomplete、限定外キー変更、復元不一致 |

IDを返すだけでは合格ではない。正常対照PASS、単一変異の所期FAIL、復元後PASSを記録する。
実イベント接続、OS/Blender試験、人間判断は別々に報告し、単体試験で代替しない。

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
成果物の部品や原作入力自体は今回削除していない。Blendの見た目は変更なし。

## 根拠・再開の入口（実パス）

KB-root:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

成果物root:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz`

- 本計画: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md
- 監査rev4: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md
- 技術9.6: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md
- 実行記録: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md
- 引継ぎ: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md
- pipeline: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-character-repro-pipeline.md
- 原REQ: /Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md
- 品質台帳: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
- brainstorm親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md
- 独立レビュー: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260831-unified-route-plan-independent-review.md
- レビュー対象旧版: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260831-unified-route-plan-revision-1.md

## 矛盾・未確定

- revision 3の独立再レビューは完了し、未解消Critical/Majorなし（計画上のみ）。
  検証対象SHAは `3860e540e929c340e773106acc2f3b2e6b899f6a606013b88c9a35f7a0bb83c4`。
  検証後の変更はこのレビュー状態と「次の作業」の記録更新だけで、仕様の変更ではない。
  モデル実ID配分表、hook設定差分、U5以後の具体変更契約は未承認。
- 実機のhook到達性、作業遮断、Blender見た目は未試験。
- 現行旧計画の承認境界は、本計画が実行承認されるまで維持する。
