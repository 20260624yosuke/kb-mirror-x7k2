---
type: build
title: Helen 原作再現 実行保証・機械監査計画 2026-08-30
status: active
confidence: medium
evidence_level: user-stated+source-backed
created: 2026-08-30
last_reviewed: 2026-08-30
plan_status: approved-for-implementation
approved_at: 2026-08-30
approval_scope: execution-audit-plan-revision-4
related:
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
  - "[[brainstorm-gf2-dusevnyj-bikini-to-helen]]"
tags: [gf2, helen, audit, quality-gate, execution-plan]
revision: 4
---

# Helen 原作再現 実行保証・機械監査計画 — 2026-08-30

## 再開の入口（実パス）

> 2026-09-01 原作再現の再開記録: [Helen H0157専用の親メモ](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md>)。
> [現状と成果物までの問題点](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260831-helen-repro-project-current-state.html>)。武田さんの「新規親・孤立禁止」の選択により相互リンクを追加。
> 本追記は入口の追加のみで、既存計画の仕様・承認範囲・水着化の進捗を変更しない。

- brainstorm親メモ `[[brainstorm-gf2-dusevnyj-bikini-to-helen]]`:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
- 作業ディレクトリ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

> [!important] 承認状態
> **revision 4 実行承認済み・未実装。** これは既存の技術計画を置き換えない。既存計画が「何を調べ、何を
> Blenderへ実装するか」を担当し、本計画は「どの証拠が揃えばLLMが次へ進み、完了と言ってよいか」
> を担当する。2026-08-30のカードで「revision 4を実行承認」＋確認「はい」を取得した。
> このbrainstorm中には実装せず、新しい通常作業へ渡す。

> [!important] 独立レビュー反映
> 2026-08-30のGPT-5.6・推論強度mediumによる読み取り専用レビューで、Critical 2件、Major 5件、
> Minor 1件が見つかった。revision 2は、任意監査の迂回、品質台帳の破壊的上書き、既存writerの迂回、
> G10既存FAILの自己証明化、状態・証拠契約不足、既存section 9.6との衝突、共有ゲートの回帰試験不足を
> 修正した。再レビューで5件解消・3件部分解消となり、revision 3は残った対象照合、guard信頼起点、
> phase別条件、writer再走査、状態の単一正本を具体化した。revision 3の最終確認で残った初回導入の循環、
> writer分類結果の改変余地、project IDと対象defectの正本不足をrevision 4で具体化した。レビュー正本は
> `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260830-helen-repro-execution-audit-plan-independent-review.md`。

## 目的と完成条件

目的は、**コードから Helen の原作再現を Blender 成果物として成立させること**である。
監査ハーネス自体を成果として数えず、未回収入力・因果未確認・古い結果があるのに候補Blendを書き出す、
または完成と報告する経路を機械的に止める。

最低限の完成条件は次のとおり。

1. S6・S8・G10が共通スキーマの別々の契約になり、正解、現在値、対象範囲、候補因果、反証、
   変異試験（わざと壊して検査が落ちるか確かめる試験）、非対象、状態を機械判定できる。
2. 小さい監査状態ファイルを正本とし、既存run-stateとの矛盾、古い証拠、入力・writer・Blendの
   SHA-256（ファイル内容を識別する値）不一致をFAILにする。
3. 監査必須対象は、`quality-gate.json` 内の任意欄ではなく、Wiki側の外部登録簿から判定する。
   `execution_audit` が欠落・削除されても監査固有IDを付けてFAILにする。
4. S6・S8・G10に関係する既存の全writerと正式採用経路を列挙し、候補Blend書出し前と既存品質ゲートの
   `complete` 判定の両方へ同じ監査を接続する。
5. 監査だけを1項目ずつ壊すfixture（小さい試験用入力）で検出力を証明する。現行G10の `blocked` と
   既存completeのFAILは、ハーネス検出力の合格には数えない。
6. `a10_quality_gate.py` を実行しても、`execution_audit` 以外の現行品質台帳を削除・変更しない。
7. 未解決の現行G10を正しく `blocked` に止め、根拠のない候補書出しと完成報告を拒否できる。
8. 監査が通っても、原作一致・実機確認・運用可能を自動では主張しない。

## 高リスク成果物の6点ゲート

| 項目 | 本計画で固定する内容 |
|---|---|
| 正解の所在 | 原作H0157の比較フレーム、回収コード、Unityのprefab・renderer・material参照、原作実機の見た目 |
| 欠けうる入力 | InternalLut、実行時の髪処理、Helen本人のprefab・material・RampSetting参照、`f166`の最終再実行結果 |
| 性質の違う対象群 | S6＝顔の白飛び、S8＝髪の被覆・形、G10＝renderer・材質・RampSetting対応 |
| 代表例 | **G10**。Ramp値の転写ではなく、権威ある割当の参照鎖不足を扱う |
| 原作比較方法 | `f152`・`f128`・`gate-results.json`、参照IDの直接追跡、候補Blendと原作フレームの比較 |
| 停止条件 | SHA・対象範囲・参照鎖の不足、未解決指摘、古い結果、未登録writer、別Blend、監査未接続 |

## 作るファイルと既存系への接続

新設先は `06_repro-v51/audit/` とする。ただし、置くだけのフォルダは禁止する。

```text
06_repro-v51/
├── audit/
│   ├── contract.schema.json
│   ├── S6.json
│   ├── S8.json
│   ├── G10.json
│   ├── state.json
│   ├── review-findings.json
│   ├── writers.json
│   ├── evidence-index.json
│   └── fixtures/
│       └── enforcement/
└── scripts/
    └── audit_guard.py
```

- Wiki側へ監査必須登録簿を新規作成する。ファイル名は `project_quality_gate_required_audits.json`、
  配置先はWikiの `tools` ディレクトリとする（**実装時に作成する予定物で、現時点では未作成**）。
  登録簿にはmanifest実パス、project ID、
  audit root、schema versionを登録する。`project_quality_gate.py` はこの外部登録簿を先に読み、対象manifestで
  `execution_audit` が欠ければ `EA_REQUIRED_MISSING` としてFAILにする。任意欄を削除しても必須判定は消えない。
- 必須対象の照合は次で固定する。
  1. manifestは `Path.resolve()` 後の絶対パスで比較する。
  2. 登録パスが一致すれば対象。project IDも一致しなければ `EA_PROJECT_ID_MISMATCH` でFAILする。
  3. 登録project IDと一致するmanifestが別パスにあれば、コピーによる迂回として
     `EA_CANONICAL_PATH_MISMATCH` でFAILする。対象外へ戻さない。
  4. 外部登録簿が無い・読めない・schema不正なら、対象判定を省略せず全manifestを
     `EA_REGISTRY_UNAVAILABLE` でFAILする。
- project IDは `quality-gate.json` のトップレベル `project_id` とし、この案件は
  `gf2-helen-starlit-waltz` で固定する。既存の人間向け `project` 文字列をIDとして流用しない。
- 対象defectの正本は `quality-gate.json` の `execution_audit.requested_defects` とし、初期値は
  `["S6", "S8", "G10"]`。`plan` は3契約全部、`batch` と `complete` は配列内の全defectへphase条件を
  要求する。空配列、未知ID、重複、3契約との過不足は `EA_DEFECT_SCOPE_INVALID` でFAILする。
- 外部登録簿は `audit_guard.py`、`contract.schema.json`、writer走査器の承認済みSHA-256も持つ。
  `project_quality_gate.py` がproject側コードをimport・実行する**前に自分で**現物SHAを照合し、不一致なら
  `EA_GUARD_SHA_MISMATCH` でFAILする。guardにguard自身の正当性を判定させない。
- guardまたはschemaを更新する場合、候補SHAで全共有ゲート試験を通した後だけ、明示コマンド
  `project_quality_gate.py promote-audit-sha --registry <path> --project-id <id>` で登録簿を更新する。
  promoteは変更前登録簿の回復用コピー、候補ファイルの実測SHA、試験結果パスを必須にし、自動昇格しない。
- P0および `plan`・`batch`・`complete`・正式登録のたびに、`save_as_mainfile` と同等のBlender保存APIを
  全スクリプトから機械走査し、`writers.json` へ
  S6・S8・G10・対象外のいずれかと根拠を記録する。少なくとも既知の `f109`、`f121`、`f153`、`f162`
  を含むが、この4本だけで全件とはみなさない。
- 検出集合は `save_as_mainfile`、`save_mainfile`、`bpy.data.libraries.write`、既存Blendのcopy/move、
  Blender subprocess内の保存式、生成されるinner scriptとする。未分類、台帳に無い新規writer、writer SHA不一致が
  1件でもあれば `EA_WRITER_UNREGISTERED` でFAILする。走査器自身のSHAも外部登録簿で固定する。
- P0で生成した `writers.json` は独立レビュー後のSHAを外部登録簿へ固定する。以後の再走査で新規writer、
  削除、分類変更、根拠変更、SHA変更が出た場合は自動採用せず `EA_WRITER_CLASSIFICATION_CHANGED` で停止する。
  更新は差分の独立レビューと共有ゲート試験の後、明示promote操作でだけ承認する。
- 正式writerは書出し前に `audit_guard.begin_candidate_write(defect_id, writer_path, input_paths,
  requested_output_path)` を呼ぶ。guard自身がwriterと入力を実ファイルからSHA化し、許可された一時出力パスと
  tokenを返す。書出し後に `finish_candidate_write(token, output_path)` が出力SHAを記録して初めて採用可能にする。
- 正式採用とは、候補を `quality-gate.json` のrepresentative outputまたは監査stateへ登録する操作をいう。
  begin/finish記録のない出力、許可パス外の出力、書出し後に変わった出力は登録を拒否する。
- `scripts/a10_quality_gate.py` は既存JSONを固定辞書で全面再生成してはならない。現行JSONを読み、
  `execution_audit` だけを検証付きでマージする。変更前SHAと回復用コピーを保存し、既存内容を深い比較で照合する。
- Wiki側の `tools/project_quality_gate.py` は対象manifestについて `plan`・`batch`・`complete` の全phaseで
  `audit_guard` を呼ぶ。`complete` では契約、指摘、証拠SHA、writer、原作資料、Blendの版拘束まで照合する。
- OSやBlenderによる任意のファイル作成そのものは物理的に禁止できない。代わりに、未登録成果物は
  SHA不一致で採用・完了報告できないようにする。

### 非破壊マージの完成条件

1. a10実行前の `quality-gate.json` を
   `audit/backups/quality-gate.<実行前SHA256>.json` へ保存し、既存バックアップは上書きしない。
2. 実行前後のJSONから新設を許可した `project_id` と `execution_audit` だけを除き、キー、値、配列順、
   未知キーを含めて深い比較で一致する。
3. 公式2Dアート、原作動画、監査履歴、`absence_claims`、既存gatesが同数・同内容で残る。
4. 同じ入力で2回実行して2回目の内容が変わらない（冪等）。1項目でも違えば復元して停止する。

## 実装順

### P0 現在地を凍結する

Blendを変更せず、現行Blend SHA、S6・S8・G10、`f166`、run-state、既存ゲート、原作資料、検査script、
全writerの版と結果を記録する。証拠は `current`（必要SHAが現物と一致）、`stale`（拘束先が変更済み）、
`unbound`（必要SHAを元記録が持たない）に分類する。SHAを持たずパスだけが同じ証拠は `unbound` とし、
`complete` には使わない。現状の `gate-results.json` はBlend SHAがなく、旧f166結果はscript更新後なので、
そのまま現行証拠へ格上げしない。食い違いは推測で直さず、初期指摘として台帳へ入れる。

### P0B 初回bootstrap（初めて監査を導入する手順）

初回だけは新監査がまだ無いため、新 `--phase plan` を開始条件にしない。開始証拠は、ユーザーが明示承認した
本計画revision 4、現行旧版 `project_quality_gate.py --phase plan` のPASS、P0の入力・既存ファイルSHAとする。

1. audit一式、共有ゲート変更、外部登録簿、a10変更、writer変更を正規パスではなく一時パスへ生成する。
2. 一時パス上で全fixture、非対象互換、a10非破壊、writer分類の独立レビューを通す。
3. 導入前の共有ゲート、登録簿、quality-gate、a10、全writerを内容SHA付きで回復用コピーへ保存する。
4. 試験済み一式を単一の導入操作で正規パスへ置換する。個別に途中導入しない。
5. 置換直後に新 `--phase plan` を実行する。PASS前は候補writer実行、Blend変更、正式登録を禁止する。
6. 新planがFAILした場合、共有ゲート、登録簿、quality-gate、a10、全writerを導入前SHAへすべて復元し、
   部分導入を残さない。復元後に旧planが元どおりPASSすることまで確認する。

2回目以降はbootstrapを使わず、通常の外部登録簿、承認SHA、promote手順を通す。

### P1 契約と監査器を作る

共通スキーマ、3契約、監査状態、指摘台帳、writer台帳、証拠索引、`audit_guard.py` を作る。

| 状態 | 進入に必要な肯定証拠 | 失効・戻り条件 | 次に許す状態 |
|---|---|---|---|
| `scoped` | defect ID、正解資料、測定対象、非対象 | 正解資料または対象範囲の変更 | `coverage_verified` / `blocked` |
| `coverage_verified` | 全入力一覧とSHA、未回収数、対象範囲の分母 | 入力・探索script・分母の変更 | `candidate_traced` / `blocked` |
| `candidate_traced` | 候補コードの絶対パスと実測SHA、原作差までの参照鎖 | 候補・入力・参照鎖の変更 | `causal_tested` / `contested` / `blocked` |
| `causal_tested` | 陽性・陰性対照、反証、変異試験、監査固有ID | 検査器・fixture・候補SHAの変更 | `artifact_candidate` / `rejected` |
| `artifact_candidate` | begin/finish token、writer・入力・出力SHA、許可出力パス | 出力またはwriterの変更 | `artifact_measured` / `rejected` |
| `artifact_measured` | 別実装の数値、原作比較対象、同一Blend拘束 | 比較器・原作資料・Blendの変更 | `human_review` / `contested` |
| `human_review` | LLMが列挙した差と代表1〜4件、人間の明示判断 | 対象群・入力条件・比較項目の変更 | `accepted` / `rejected` |
| `accepted` | 承認範囲、対象SHA、承認記録 | いずれかの拘束SHAまたは範囲変更 | 対応する直前状態へ戻す |
| `blocked` | 不足入力または停止条件のID、`entered_from` | 不足を埋める直接証拠＋独立再確認 | `entered_from` を上限に証拠から再判定 |
| `contested` | 相反する直接証拠2件以上、`entered_from` | 独立確認者が根拠を比較し決着記録 | 根拠が許す状態 / `blocked` |
| `rejected` | 反証成立、変異未検出、許容不可、`entered_from` | 新しい候補IDと新しい反証一式 | `candidate_traced` |

状態値の唯一の正本は `state.json` とする。S6・S8・G10契約には期待状態を重複保存せず、遷移に必要な
不変条件だけを持たせる。状態更新は `audit_guard.py` だけが行い、一時ファイルへ全体を書き、fsync後に
atomic rename（途中状態を見せない置換）する。`blocked`・`contested`・`rejected` は `entered_from`、
`reason_id`、`evidence_sha` を必須とし、復帰先は `entered_from` を上限に肯定証拠から再判定する。
findingはID、defect、artifact SHA、severity、status、opened evidence、closed evidence、独立確認者を必須にし、
`closed evidence` を欠いた手書きのstatus変更では閉じない。鮮度はTTL（経過時間）でなく、原則として
入力・writer・検査器・fixture・原作資料・Blendの全SHA一致で判断する。

### P2 二重接続を実装する

候補writerの書出し前と、既存品質ゲートの完了判定へ接続する。専用ランナーへの一本化は行わず、
既存スクリプトの独立実行は残す。ただし `writers.json` の対象writerすべてにbegin/finishを接続し、
正式成果への登録操作でも同じ記録を必須にする。登録簿・manifest・guardのいずれかが欠落、import失敗、
異常終了した場合はfail-openせず監査固有IDで停止する。

共有品質ゲートの既存試験を維持し、非対象manifest互換、対象manifestのaudit欠落、guard import失敗、
guard異常終了、plan/batch/complete各phase、監査固有エラーID、外部登録簿欠落を回帰試験へ追加する。

### phase別の要求

| phase | 合格に必要な条件 | この案件での意味 |
|---|---|---|
| `plan` | 外部登録簿と承認SHA一致、schema、3契約、writer全分類、state整合、open Critical findingなし。defectは `scoped` / `blocked` でよい | 監査を実装・修正する入口。入力不足を隠さなければ進める |
| `batch` | `requested_defects` 全件が `artifact_measured` 以上、全SHA一致、open Critical/Majorなし、未解決blockedなし | 本案件では量産でなく、3欠陥を揃えて人間レビューへ渡す許可として使う |
| `complete` | `requested_defects` 全件が `accepted`、明示的人間判断、独立比較、出力SHA、begin/finish記録、全finding closure | 原作再現の完成報告を許す最終関所 |

phase条件に満たない正常な停止も終了コードだけでまとめず、状態固有IDを機械可読JSONへ出す。
`plan` がG10 `blocked` を理由に落ちる設計、`batch` がblockedを素通しする設計のどちらも禁止する。

### P3A 監査専用fixtureで検出力を証明する

G10実データとは分けた一時fixtureで、他の品質ゲートはPASSに固定し、監査だけを1項目ずつ変える。

1. 正常対照はPASSする。
2. `execution_audit` 欠落は `EA_REQUIRED_MISSING` でFAILする。
3. writer hook削除、古い証拠、別Blend、open finding、guard import失敗は、それぞれ別の監査固有IDでFAILする。
4. 仮writerはbegin前でも物理ファイルを作れるが、その出力の正式登録は拒否される。
   begin/finish後の一致する一時出力だけ正式登録できる。
5. a10の非破壊マージ試験と共有品質ゲートの非対象互換試験がPASSする。

既存の別理由によるFAIL、単に例外で落ちた結果、監査を呼ばずに得たG10 FAILは合格に数えない。

### P3B G10で実環境のfail-closedを確認する

これはG10の見た目修正試験ではなく、**執行力の試験**である。現状はRampの4帯の数値転写が
最大誤差0.00039（許容0.001）でも、Helen本人の
`renderer → submesh → material → RampSetting` の権威ある参照鎖がない。

したがって最初の正しい結果は `scoped` または `blocked` であり、次を実演できれば実環境の停止確認は成功とする。

1. 根拠のないG10候補書出しを拒否する。
2. 現行プロジェクトの `complete` 判定をFAILにする。
3. 不足している参照鎖と再開点を短い状態報告へ出す。

実際のG10候補writerができるまでは、P3Aの仮writerで接続器を試しても「実G10 writerへ完全接続済み」
とは報告しない。G10の `blocked` はP3Aの検出力証明を代替しない。

### P4 S6・S8を初期化する

S6・S8も同じ深さの契約として初期状態を記録する。この工程ではBlend変更、候補制作、`f166`再実行をしない。

### P5 短い状態報告を生成する

正本から「現在段階、通った関所、停止理由、次の1件」だけを生成する。長い引き継ぎ文を現在状態の
代わりにしない。

## してはいけないこと

- 既存のHelen原作再現計画を書き換える。
- Blend、抽出コード、`f166`結果、原作入力をこの計画の実装中に変更する。
- 未回収の参照対応を他キャラや既定の白黒Rampから推定し、忠実版として扱う。
- S6・S8・G10の合格線を通りやすく下げる。
- ハーネス完成を、Helen原作再現またはG10解決の完成と言い換える。
- 原作再現以外へ一般化するための汎用CI、常駐reviewer agent、専用ランナーを先に作る。
- `execution_audit` が無い場合に従来処理へ黙って戻る。
- 外部登録簿または承認SHAが読めないとき、対象外扱いへ戻る。
- project側guardにguard自身の正当性を自己申告させる。
- 初回bootstrapで一時試験を省き、正規パスへ個別ファイルを順番に置く。
- `writers.json` の分類結果を独立レビューとpromoteなしに変更する。
- 現行 `a10_quality_gate.py` の全面上書きを、非破壊化と復元試験より先に実行する。
- G10の既存FAILまたは共有completeの既存FAILを、監査ハーネスの検出力証明に数える。

## 既存計画 section 9.6 との優先関係

既存正本
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md`
のsection 9.6は、次のように扱う。

- 9.6-1「新しい状態ファイルやスキーマを作らずrun-stateを修復」は、後日のユーザー選択
  **「小さい監査状態ファイルへ分離し、run-state矛盾は停止する」**が状態管理部分だけをsupersede
  （後の決定で置き換え）する。run-stateは消さず、監査stateとの不一致検出に使う。
- 9.6-2〜4のf166最小修理、1回の全量再走査、S6・S8・G10候補絞込みは技術タスクとして残る。
  本監査計画の実装中には実行せず、P3A・P3B完了後に別承認を取って既存技術計画へ戻る。
- したがって、監査実装がf166修理を不要にしたことにはならず、旧f166結果はP0で `stale` として止める。

## 機械が決めないこと

- 見た目の差が原作用途として許容できるか。
- 権威ある入力が回収できないとき、忠実版を止めたままにするか、失う見え方を示した近似版へ分けるか。
- G10が通った後にS6・S8のどちらを技術実装へ進めるか。

## 捨てた案と、選択で失うもの

- 既存計画の全面改訂: 技術上の未完了点とLLMの進行欠陥を一冊にまとめる単純さを失う。代わりに、
  既存の技術判断を壊さず監査だけを交換できる。
- 専用ランナー一本化: 一つの入口だけ見れば済む単純さを失う。代わりに、既存スクリプトの独立性を残す。
- S6先行: 見た目が分かりやすい欠陥から早く直し始める速さを失う。代わりに、入力不足を正しく止める
  G10で、監査の執行力を先に証明する。
- 3契約を1 JSONへ統合: 一覧性を失う。代わりに、1件の更新で別欠陥の状態が動く事故を避ける。

## 実装時の変更対象

- 新規: `06_repro-v51/audit/` の契約3件、schema、state、findings、writers、evidence index、fixture、
  `06_repro-v51/scripts/audit_guard.py`
- 変更: `06_repro-v51/scripts/a10_quality_gate.py`
- 新規予定: Wikiの `tools` ディレクトリに `project_quality_gate_required_audits.json`
  （実装時に作成。現時点では未作成）
- 変更: Wikiの `tools/project_quality_gate.py` と `tools/tests/test_project_quality_gate.py`
- 新規: writer保存API走査器（SHAを外部登録簿で固定）
- 変更: P0で列挙したS6・S8・G10の既存writerすべて。対象外判定には根拠を必須にする
- 将来変更: 実在するG10候補writer。作成前は「実G10 writer接続待ち」と明記する
- 読み取りのみ: 現行Blend、`f128`、`f152`、`f166`、`gate-results.json`、run-state、原作入力

## 実装後に取る次の承認

本計画の実装、監査fixtureの検出力証明、G10実環境の停止確認が完了しても、自動的に原作再現の技術作業へ
進まない。次は、既存section 9.6に残るf166最小修理・再走査とG10参照鎖回収を実行してよいかを別に承認し、
その後S6・S8の展開順を決める。

## 実装記録 2026-08-30

- 状態: `in-progress`。P0実測済み、P0B step 2のwriter分類独立確認前で停止。
- 開始証拠: 現行旧版 `tools/project_quality_gate.py check <manifest> --phase plan` はPASS。
- P0結果: 現行ファイルと原作比較入力のSHA、初期指摘5件、writer候補54本を一時領域
  `gf2-helen-starlit-waltz/.audit-bootstrap-20260830/` に凍結。
- 現行Blend SHA-256: `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
  P0作業前後で一致し、Blend本体は変更していない。
- writer走査は2回とも同一で、`writers.json` SHA-256は
  `e09ca43104e6d163bf6b4b825d1e13be3e50084b7aaa7b3e4c826f6642302aee`。
- 自動分類案は S6=9、S8=3、G10=36、対象外=6。独立確認の肯定証拠がないため、
  承認済み分類へ格上げせず、正規パスへのbootstrap導入も実行していない。
- 視覚報告: `wiki/_attachments/project-hub-index/20260830-helen-repro-p0-freeze.html`。
- 正規の `06_repro-v51/audit/`、共有ゲート、`quality-gate.json`、`a10_quality_gate.py`、
  既存writerは未変更。

## 使わなかったもの・落とした情報

なし。P0ではBlend、原作入力、既存検査結果を捨てず、現物SHAを一時台帳へ追加した。
正規パスのwriterや品質ゲートは、独立確認なしに一部だけ導入しないため未変更。
