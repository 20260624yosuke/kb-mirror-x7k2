---
type: build
title: Codex brainstorm 曖昧な終了・再開点の機械監査修理計画
status: active
confidence: medium
evidence_level: source-backed+user-stated+inferred
created: 2026-08-31
last_reviewed: 2026-08-31
revision: 3
plan_status: draft-unapproved
review_status: pending-independent-review
---

# Codex brainstorm 終了・再開点監査の修理計画 — revision 3

## 0. この計画の状態

計画案・未承認。今回の会話ではコード・設定を変更しない。初期コードが既に存在することと、この計画の承認・修理完了は別。
ユーザーの最新依頼は「ここまでの経緯を踏まえた詳細計画」「実装前のサブエージェントレビュー」「GPT-5.6・medium限定」「結論を誘導しない」「仕様と実装の不一致を重点確認」。
利用可能なGPT-5.6系の実IDとしてgpt-5.6-sol、reasoning effort=mediumを指定する。レビュー結果を受ける前に承認カードへ進まない。

revision 1の独立判定はCritical 0 / Major 5 / Minor 1。revision 2はCritical 0 / Major 1 / Minor 1。
本revision 3は旧基準移行の最終通過証拠と、限定品質ゲートの検査実体を明確化した修正案。再照合前の合格を主張しない。
レビュー記録:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/sessions/20260831-resume-audit-independent-review.md

### 実機品質の6点

正解はユーザー要件・承認済み本計画・実フックイベント。欠けうる入力は実カードID、原ユーザー入力、親の検査済み基準、実Stopの再入記録。
性質の異なる対象群は終了契約・書込分類・起動とカード遷移。代表例は通常終了、旧計画への回答、親未選択、別セッション再開、引用内比較記号。
比較は同じ入力と対象SHAを固定した修正前／修正後の応答・状態・実ログの直接照合。停止条件は根拠欠損、ゲート不合格、実イベント未観測、対象外変更の必要性。

親メモ（本人の言葉・状態・承認履歴）:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md

実測・途中変更・経緯:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/sessions/20260831-concrete-resume-audit-repair.md

## 1. 目的と今回やらないこと

目的は、brainstormの返答が「次に何をするのか分からない」まま戻る経路を、スキル共通の機械検査で拒否すること。
現在作業を前工程へ戻しても、その先で保留した仕事・理由・復帰先を消さない。
注意書きや「今後気を付ける」だけでは解決に数えない。

対象はCodex版brainstormの既存監査とその試験。Helen専用の対策にしない。次は対象外:

- HelenのBlend、54本のwriter分類、P0B監査本体、モデル配分・設定の実装。
- Claude/Kimi/opencode版の改変、全体設定の再生成、カードUI自体の変更。
- 全ての自然言語の意味・正しさを自動判定する仕組み。自由文の意味の完全保証はしない。
- シェル全体の新しい安全実行基盤。今回の書込誤認を直すために任意Python等を一括許可しない。
- このbrainstorm会話内での実装、無回答を承認にすること、bypassによる実装境界の迂回。

## 2. ここまでの経緯と証拠の強さ

| 出来事 | 確認できたこと | 未完・区別すること |
|---|---|---|
| Helenの監査作業から戻った | 現行一本化計画rev3はP0棚卸し済み・P0B本体実装前 | 54本の判定をユーザーの宿題にしない |
| ユーザーがbrainstorm本体の修理を指定 | 終了監査に具体的な次操作の契約がなかった | 既存カード検査だけでは今回の条件を満たさない |
| 初期修理 | resume_contract.py追加、codex_adapter.py変更 | 後述の初期変更SHAを基線として保持 |
| 一時的な試験 | 既存11件・追加23ケースが合格との実行記録 | 23ケースの恒久ファイルはまだ無い |
| 実イベント | 起動復旧、メモ追記、カードIDと回答、実Stop正常通過 | 実Stopの不備拒否・再入拒否は未確認 |
| 今回の続行 | 既存11件を再実行して合格、CLI単独で検査合格 | コード追加はしていない |
| 修理中の制約 | 計画限定が注入され、残実装へ進めない | 「限定解除でこの会話の実装へ戻る」案内は撤回 |
| 読取の誤拒否 | jqの引用内比較記号で入力ログを書込先として拒否 | Python実行名が原因とは断定しない |
| 今回のカード回答 | HTML説明要求、その後詳細計画とレビューの依頼 | 計画承認ではない |

基線SHA（現在の初期変更を消して開始しない）:

- /Users/takedayousuke/.codex/skills/brainstorm/scripts/resume_contract.py
  — 385bfbab3c70e134f9aa40130d74fbed0ea0c70af7a445d50cc4c20ed75a7f75
- /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
  — ba4aeaed7b4318d97c61980ff7a1cf91ee1aea8fced254fb2809d8621465d74d

実イベント: /Users/takedayousuke/.codex/skills/brainstorm/scripts/card-events.jsonl
書込判定のログ: /Users/takedayousuke/.codex/skills/brainstorm/scripts/guard.log
過去の合成試験・制作者の成功報告を、そのまま実機での拒否成功の証拠にしない。

## 3. 変更対象と維持する境界

| 実ファイル | 変更する責務 | 維持するもの |
|---|---|---|
| /Users/takedayousuke/.codex/skills/brainstorm/scripts/resume_contract.py | checkpointの型・必須値・根拠・復帰辺・終了状態・表示契約 | 読取専用CLI。nextの操作は実行しない |
| /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py | 実カード・実状態とcheckpointの接続、全Stopの検査 | 同ターン記録、二問カード、実ID束縛、brainstorm外での非介入 |
| /Users/takedayousuke/.codex/skills/brainstorm/scripts/brainstorm_guard.py | 引用データの書込誤認、Codex引継ぎ監査の再入無条件通過 | 保護対象への実書込拒否、未読readyメモ、全Stopの到達性検査 |
| /Users/takedayousuke/.codex/skills/brainstorm/tests/test_codex_adapter.py | 実状態・Stop・引用誤認の回帰試験 | 既存11ケース |
| /Users/takedayousuke/.codex/skills/brainstorm/tests/ | 追加23ケース等を恒久保存する新テストを実装時に追加 | 現時点で新テストファイルは未作成 |
| /Users/takedayousuke/.codex/skills/brainstorm/SKILL.md | 実コードと一致するcheckpoint手順・保証範囲 | 明示起動、計画限定、承認と中断の区別 |
| /Users/takedayousuke/.codex/skills/brainstorm/quality-gate.json | 今回の根拠・対象群・証拠を非破壊で追記 | 旧対象群・旧batch・旧承認を保持。旧未受入を合格にしない |
| /Users/takedayousuke/.codex/skills/brainstorm/scripts/state/ | 親別の検査済み基準、ロック、今回範囲の品質ゲート検査用写し | 既存セッション状態を一括書換えしない。新ディレクトリは作らない |

追加予定の検査実体は /Users/takedayousuke/.codex/skills/brainstorm/scripts/repair_quality_gate.py（実装時に作成・現時点未作成）。責務は8章の限定版生成と正本照合だけ。

hooks.jsonとconfig.tomlは今回変更しない。登録・信頼の不足で実イベントが取れない場合は検証未完として報告し、未依頼の再設定へ進まない。

## 4. 機械的に検査する契約

### 4.1 現在地・先の停止地点・復帰先

親メモには現在有効なbrainstorm-checkpointを厳密に1個保存する。
timeline内の実在ノードをcurrent.nodeとparked[].nodeから参照する。ID重複、型不正、存在しない参照を拒否。
戻り作業中はcurrentとparkedを分け、return_toを保留ノードと完全一致させる。
前回検査済みcheckpointにあった保留ノードを消す場合は、そのノードに対応する解除根拠が必要。
同じファイルが存在するだけでは根拠にせず、参照ファイル内容のSHA-256一致を検査する。
SHA一致は内容の同一性の証明であり、内容の正しさの証明ではない。

親別の比較基準は既存scripts/state内にparent-<親のresolve済み絶対パスSHA>.jsonとして保存する（実装時に作成）。
中身はschema_version、parent_path、generation、検査済みcheckpoint全文、checkpoint_sha、検査元session/turnのハッシュ。
フックだけが更新する。対象親のパス一致を必須にし、他親から復元しない。別セッションのSessionStart/親選択確定後にも同じ基準を読む。
同名.lockで排他し、読込時generationとの一致を確認して一時ファイル・atomic replaceで更新する。
競合なら自動マージせず再読込を要求し、古いセッションが保留を消して保存することを拒否する。
checkpoint・カード・メモ記録・引継ぎ到達性の全検査が通った場合だけ基準を進める。途中検査だけの成功で上書きしない。
基準欠損を空の保留一覧とは扱わない。新規親は実Addイベントで新規作成を確認できた場合だけ初期基準を作る。
旧親の移行は、同じ親の保存済みlast_checkpoint全文と、同じsession/turnの実checkpoint_verifiedのハッシュ一致に加え、
その検証に対応する最終stop_passまで一意に証明できる基準だけから復元する。checkpoint_verified単独は採用しない。
親との対応は保存状態target_memoと同turnのmemo_patch_verifiedのpath_hashで照合する。
checkpoint_verifiedからstop_passまでに別checkpoint検証・stop_block・対象親変更がある場合は採用しない。
stop_pass後に保存全文が別checkpointで上書きされ、当該通過時の全文を一意に復元できない場合も採用しない。
復元後、現在メモへの無根拠削除がないことを検査して初期化する。一致資料が無い／複数候補で一意に決まらない場合は移行停止とする。
移行停止時に現在メモを無条件で初期基準にしない。実際に最終通過した記録を復元する作業を次操作として示す。
親の移動・改名も自動で「新規」にせず、旧基準と対応する移行の証拠が必要。

### 4.2 次の操作と終端

nextにowner、action、実在する絶対target、done_when、availabilityを必須化する。
空欄・既知の曖昧語のみの値、型不正を拒否する。曖昧語の全表現を列挙できるとは言わない。
assistantかつrunnableで終了しようとしたら拒否。exit.kindと実カード状態が矛盾しても拒否する。
単に自由記述のownerをuserへ変えるだけで監査を通せないよう、needs_userは現在の実カードへの束縛を必須化する。

回答前に固定するdecision_bindingと、回答後に変わるnextを分離する。decision_bindingはcard_kind、
実callハッシュ、セッション/turn、対象親、question/optionハッシュ、対象文書または質問のSHA、判断範囲を持つ。
next全体を回答対象の固定ハッシュに含めない。正当な回答後の引継ぎ操作への更新を失効理由にしない。
計画承認カードは独立した計画文書のSHAへ束縛する。通常質問はカードの質問・選択肢と対象親のIDへ束縛し、毎回独立文書を増やさない。
実PreToolUseで保存し、PostToolUseは回答の反映前に同じ束縛を再検査する。不一致ならapproved/stoppedへ入らず、
stale_decisionとして回答を不採用にし、現在の対象でカードを再発行する。Stopでも既に受理したbindingの整合を確認する。
計画SHAが承認後に変わった場合も承認を流用しない。計画とは別の親メモのマーカー追記は計画のSHAを変えない。
pendingカードが存在するだけで、人間判断が本当に必要だという意味の真実性までは証明できない。この限界を表示する。

| 入力 | 反映前の必要条件 | 状態・次操作 |
|---|---|---|
| 計画承認＋確認はい | 現在の実call/親/計画SHA/選択肢の一致 | approvedと受理bindingを保存。nextは引継ぎへ変更可 |
| 中断＋確認はい | 現在の実call/親/判断範囲の一致 | stopped。保留ノードは削除しない |
| 通常回答・自由記述 | 実call/親/質問の一致 | awaiting_card。新依頼は内容を記録して作業へ。計画承認にしない |
| 空・部分・確認拒否 | 現在callとの対応のみ確認 | 承認・中断しない。同じ判断対象の待機を維持 |
| 独立した承認・中断発話 | 既存の許可語のみの最新原ユーザー入力、同一セッションに一意な有効pending binding、対象SHA一致 | そのpending判断だけ受理。対象不明・失効なら状態を上げない |

### 4.2A 起動段階の限定契約

dialog_stageをtheme_wait / parent_select / discussion / plan_decisionに分け、実起動・候補探索・実回答の証拠からだけ遷移する。
theme_waitはテーマが無い明示起動だけ。固定案内を返せるが、一般作業の欠落をこの段階に逃がさない。
parent_selectではcheckpoint未作成を許す代わり、候補のresolve済みパスとSHA、質問/選択肢、実call、session/turnをセッションに固定する。
PreToolUseで候補が実在すること、PostToolUseで選択候補が候補一覧にあり版が一致することを検査して親を確定する。
新規候補は親作成前であることを明示した選択肢に束縛し、承認後のAddイベントと対応させる。空回答は親未確定のまま。
Stopはこの限定契約と現在の実カードを検査し、通常checkpointを要求しない。discussion以後に戻して流用しない。
親確定後は当該親の記録・基準を読み、discussionに移る。この段階の通常質問には独立した計画文書を要求しない。

### 4.3 全ての終了経路

実Stopの初回・再入、無回答、approved、stopped、technical_stoppedの各経路で同じ検査を通す。
stop_hook_activeだけで無条件通過しない。未回答・部分回答・確認拒否を承認扱いしない。
Codex版guard-stop-handoffにも残る再入無条件returnを対象に含め、checkpoint正常でも到達性不正なら初回・再入ともblockする。
到達性検査の対象の選び方は維持するが、再入フラグを検査省略の条件にしない。Claude版は変更しない。
親メモがまだ無い起動直後・テーマ待ちの場合は、実状態から識別した起動段階専用の待機案内に限定する。
親メモを必要としない本物の起動待機を、通常作業のcheckpoint欠落と混同しない。

検査失敗は構造化したエラーコードを返し、同じ失敗でも再入監査を省略しない。
本検査が故障した際、例外を握りつぶして今回のStopを許可しない。可能な場合はtechnical_stoppedを保存し、
Stop応答にはblockを返す。状態保存不能も検査不能として区別して返す。
フック自体が呼ばれない場合まではこのプログラムだけで保証できないため、実イベント確認を完成条件に含める。

### 4.4 ユーザーが読める表示

summaryは現在作業、先の保留、理由、復帰条件、次の担当・操作・対象・終了条件を同じcheckpointから生成。
回答本文またはHTMLに必要行が無ければ終了を拒否する。HTMLコメント・script・style・head内は本文とみなさない。
任意CSSによる視覚的な隠蔽を現在の文字抽出だけで保証できない。HTML成果物では表示検査を別に行い、
文字列検査を「ユーザー画面に見えた」と言い換えない。
現在地と先の停止地点の区別を図でも維持し、Helen固有名を監査プログラムへハードコードしない。

## 5. 読取の誤拒否修正

現状はWRITE_HINTが引用を解釈せずコマンド全文の大なり記号を拾い、_candidate_pathsが入力ファイルも書込先にする。
修正は、書込ヒントの検出をシェルの引用・演算子と整合させる範囲に限定する。

- 単引用内の比較記号など、実行されない文字を出力リダイレクトとみなさない。
- 本物の出力リダイレクト、tee等の既存書込コマンド、連結された別の書込を維持して検出する。
- 二重引用内のコマンド置換、引用を閉じて始まる演算子は、単なる文字と扱わない。
- 対応できない複合構文では、今回の新しい「読取として許可する」判定を適用しない。既存の検査へ戻す。
- Python、jq、スキルフォルダ、セッション全体を無条件で許可しない。bypassを試験成功の条件にしない。
- 空白を含むパス、既存のheredoc本文と出力先の分離の回帰も確認する。

## 6. 実装順序と、各段階の合格条件

### R0 — 現状を固定（通常タスクの最初の操作）

親メモ・本計画・修理記録を読む。対象ファイルのSHAと差分を採取し、ユーザーの既存変更を区別する。
既存quality-gate.jsonの内容とSHAを保存し、8章の今回対象を非破壊で記録する。既存判定器の--phase planを通すまでコード実装へ進まない。
この時点では新しい限定版検査器を未実装なので依存しない。R2で検査器を実装したら、今回範囲の限定版planも再検査する。
既存11テストを再実行。新しいテスト用ディレクトリはmktempで作り、liveの承認・セッション状態を書き換えない。
合格: 対象と基線が一致、または差分を説明できる。説明不能な変更があればその箇所の修正を止める。

### R1 — 失敗を先に恒久テストへ固定

下記T01〜T13を保存し、現コードで期待通り拒否／通過しない部分を実測する。
前回23ケースは結果だけを転載せず、恒久テストとして再実行できる形にする。
既存のカード無し拒否試験にも正常checkpointと他の前提を用意し、単なるblockの有無でなく期待した拒否コード・到達した検査を確認する。
テスト追加は通常タスクで実施。この計画会話でコードとして保存しない。

### R2 — 契約と書込誤認を修正

4章・5章の責務ごとに小さく修正し、変更ごとに該当テストと既存試験を回す。
新フィールドを加える場合はversionを更新し、旧checkpointを黙って補完しない。
旧親メモは診断を出して更新箇所を示す。別案件のメモを一括書換えしない。
起動中・承認待ちの状態を改修の都合でapprovedやinactiveへ変えない。

### R3 — 文書同期と読取CLI

SKILL.mdへ必須項目・実行方法・拒否時の修正手順・保証しないことを反映。
CLI単独とHTML本文を渡す検査の両方が正常に動くことを確認する。
機械監査が未実装の仕様を、SKILLの文言だけで担保済みとは書かない。

### R4 — 実イベントによる終端試験

まず隔離された合成入力で正常／拒否／再入／復元の試験を完了する。
次に専用の試験親メモを使い、実際の会話で必須項目の欠落によるStop拒否、再入拒否、復元後の正常通過を採取する。
実イベントは実セッション・実turn・実カード呼出しの記録を使い、疑似IDを本番IDとして記録しない。
実案件の親メモを故意に壊して試験しない。新しい試験タスクの作成が必要ならユーザーの明示依頼を得る。
フック不発・保存不能・利用上限の場合は、確認できた最後のイベントと次の具体操作を記録して未検証を残す。

### R5 — 結果固定・戻り先の保持

試験ID・対象SHA・結果・実イベント参照を修理記録に保存。HTMLを同じ現在地に更新し、本文の監査を通す。
機械化した指摘の節、引継ぎ到達性も検査する。HelenへはB（一本化計画の具体化）、A（P0B前）、C（原作比較）を残す。
この修理の検証結果をHelenの完成・実装承認へ流用しない。

## 7. 必須試験表

| ID | 入力・故障 | 合格条件 |
|---|---|---|
| T01 | 必須項目欠落・型不正・空白・曖昧語のみ | CLI不合格、実Stopはblock。例外で素通りしない |
| T02 | 正しいcheckpointと実在する根拠 | CLI合格。前後で入力ファイルSHA不変 |
| T03 | currentとparkedの混同・復帰辺欠落・停止点削除、別セッション再開後の削除 | 親別基準から削除拒否。欠損を空一覧にしない。根拠付き遷移だけ通す |
| T04 | 根拠SHA不一致・対象不在 | 拒否。古い証拠で新しい成果を許可しない |
| T05 | assistant/runnable、実カード無しneeds_user、カード後の計画改変、正当な承認後next更新 | 旧版への回答はapprovedへ入る前に拒否。正当なnext更新は通す |
| T06 | 空回答・部分回答・確認いいえ・自由記述の新依頼 | 承認しない。新依頼の内容は保存し、説明要求を承認に変えない |
| T07 | 本文の必要行欠落・HTMLコメントだけ | 拒否。本文に行があれば通す。描画保証とは区別 |
| T08 | checkpoint不正、またはcheckpoint正常・引継ぎ不正のそれぞれで初回／再入／復元 | 両検査とも初回・再入で拒否、原因を修正後だけ通過。拒否理由も一致 |
| T09 | 検査例外・状態保存失敗・テーマ待ち・親候補選択・候補変化・空回答・正常確定 | 親選択カードが循環せず、実回答でのみ親確定。通常Stop欠落は素通りしない |
| T10 | 引用内比較記号のjq、単独resume CLI | 読取として通過。入力ファイルSHA不変 |
| T11 | T10に保護先への実書込を連結、コマンド置換、空白入りパス | 書込を拒否。引用除外による新しい素通りがない |
| T12 | 非brainstorm、別親、同親の新セッション、同親の並行更新、旧基準移行、圧縮後再注入 | 別親混入なし。同親基準を復元。競合拒否。checkpoint_verified後stop_blockは移行拒否。一意な全文とstop_passまで対応した場合だけ移行可 |
| T13 | 品質ゲート写しの元SHA不一致・family改変・対象群脱落・追加・件数不正 | repair_quality_gate.pyが公式判定器の前に拒否。正しい写しは同じphaseで公式判定へ進む |

既存試験入口:
`PYTHONDONTWRITEBYTECODE=1 python3 -m unittest discover -s /Users/takedayousuke/.codex/skills/brainstorm/tests -v`

引継ぎ監査入口:
`python3 /Users/takedayousuke/.codex/skills/brainstorm/scripts/brainstorm_guard.py audit-handoff`

コマンド文字列の比較だけでなく、終了コード・block応答・保存状態・入力SHA・実ログを判定根拠とする。

## 8. 完成条件・停止条件・復旧

### 既存品質ゲートの非破壊運用

実装時に既存quality-gate.jsonへground_truthの今回計画を追加し、familiesへresume-contract-repair / write-classifier-repairを追加する。
旧default-mode-probe / production-brainstormと旧batch/承認/証拠は保持し、今回の合格へ上書きしない。
終了契約群はT01〜T09/T12、書込分類群はT10/T11を対象にする。代表入力・出力・比較証拠を実際の保存ファイルへ結ぶ。
今回の検査用写しを既存scripts/state/quality-resume-repair-view.jsonに生成する（実装時に作成）。
全familyを保持したままrequested_familiesを上記2群だけに限定し、今回のbatch・verifierだけを今回の証拠から設定する。
source_manifest_path / source_manifest_sha256 / scope_idsを添え、元の全family定義と今回2群が機械一致することを検査する。
この一致検査は既存project_quality_gate.pyには無いため、新しいrepair_quality_gate.pyが担当する。
同スクリプトのbuild-viewは--source（元ゲート）/ --run-evidence（今回batch・verifierの実証記録）/ --output（限定版）の明示パスを受け取る。
元の全familyを複写し、2つのscope_idsとrequested_count=2を固定する。familyの承認や証拠を生成時に改変しない。
checkは--source / --view / --phaseを受け取り、元絶対パス・元SHA・全family定義・対象群完全一致・件数を検査し、
不一致なら非0で終了する。一致後だけ既存project_quality_gate.py checkへ同phaseを渡し、その失敗も非0で返す。
run-evidenceはbatch/verifierの必要情報だけを受け取り、familyや対象群の上書きを拒否する。user_accepted等は元ゲートの実証記録をそのまま使う。
元ゲートを限定版へ置換しない。限定版生成でuser_accepted等を自動でtrueにしない。実ユーザー受入の証拠がない限りcompleteは不合格のまま。
限定版にはrepair_quality_gate.py checkを--phase plan（R2以降）と--phase complete（R5）で実行し、正本照合と公式判定を両方通す。
元ゲートも同じ2フェーズで検査し、旧未受入のためcompleteが不合格ならその事実を併記する。
今回の修理範囲の合格と、旧機能を含むスキル全体の運用開始は別。合成試験だけなら「修理実装・自動試験済み」に留める。
品質ゲート自体の判定器は変更しない。量産・一括配布を追加する場合は別範囲としてbatch検査と承認が必要。

検査器の実パス:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/project_quality_gate.py

完成条件はR0〜R5の証拠が揃い、必須試験に未解消の失敗がなく、実Stop拒否／再入／復元後通過が揃うこと。
加えて今回範囲の品質ゲートcompleteを満たすこと。旧機能の未受入を残す場合はスキル全体の運用可能とは報告しない。
「コードを書いた」「一時的な試験が通った」「カードが見えた」だけで修理完了・運用開始とはしない。
実イベント未確認は実装済み・自動試験済みと分けて残す。

停止条件: 対象外の設定変更・別環境改変が必要、計画未承認、判断対象SHAが変わる、実イベントが採れない、
現在の依頼と次の操作が衝突する、利用上限等で続行不能。いずれも具体対象・担当・再開操作を残す。
復旧は変更したファイルの差分単位で行い、変更前基線へ戻す。無関係なユーザー編集を戻さない。
既存の初期変更を「元々ないもの」として削除せず、どの版へ戻したか記録する。

## 9. 独立レビューと承認

レビュアーにはこの計画、ユーザーの依頼、現行SKILL、実コード・試験・実ログを渡す。
結論・期待する合否・予想する指摘は渡さない。仕様と実装の不一致、目的に対する不足、過剰な変更、
実証方法を独立に判断してもらう。レビューは読取専用。制作側の要約だけで判定しない。
Critical/Majorがあれば正本を改訂して再照合する。未解消を残して実行承認を求めない。
レビューが合格でもユーザー承認・実装完了の代わりにはしない。

### 終わったら次に取る承認

この計画の実行範囲をユーザーへ確認する。承認後もこの会話では実装せず、通常タスクへ渡す。
新しいタスクは明示依頼がある場合だけ作成する。実装終了時は検証結果と未検証を提示し、運用受入を分ける。

## 使わなかったもの・落とした情報

既存コード・旧承認履歴・Helen成果物の削除なし。「この会話の限定解除で実装へ戻る」案内は撤回。
そのため、この会話の承認だけでコード修正は始まらない。通常タスクへ本計画と親メモを渡して再開する。
Python等の一括許可は採用しない。書込の保護を維持し、読取の誤認だけを試験で切り分ける。
