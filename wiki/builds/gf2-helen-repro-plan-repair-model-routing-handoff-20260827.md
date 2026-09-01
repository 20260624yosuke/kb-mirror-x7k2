---
type: build
title: ヘレン完全原作再現 状態修復・未回収コード解析・モデル配分 引き継ぎ 2026-08-27
status: active
confidence: high
evidence_level: source-backed+user-stated
created: 2026-08-27
last_reviewed: 2026-08-29
sources:
  - gf2-helen-repro-v51-handoff
  - gf2-helen-repro-v51-run
  - gf2-repro-and-swimsuit-conversation-handoff-20260827
related:
  - "[[gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827]]"
  - "[[gf2-char-extract-handoff]]"
tags: [gf2, helen, handoff, state-repair, code-inventory, model-routing]
revision: 2
---

# ヘレン完全原作再現 状態修復・未回収コード解析・モデル配分 引き継ぎ — 2026-08-27

> [!warning] 2026-09-01 追記 — 「現在位置」の正本は別ページです
> 本ページは **状態修復・未回収コード解析・モデル配分の計画・提案の記録** として有効ですが、
> 「いまどこにいるか（現在位置・再開入口）」の正本は `wiki/builds/gf2-helen-repro-v51-current.md` です。
> 本文中の「再開するための入口」「正本」の表現は、計画・提案についてのものと読んでください。
> 本追記は冒頭へのバナー追加のみで、計画の仕様・承認範囲・本文は変更していません
> （別セッション承認済み計画の「既存の Helen 原作再現計画を書き換えるな」に触れない範囲）。
> 経緯: 2026-08-31 の武田さんの裁定に基づく整理タスク（`wiki/builds/gf2-helen-cleanup-task-entry.md`）。

## このページの役割

> 2026-09-01 原作再現の再開記録: [Helen H0157専用の親メモ](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md>)。
> [現状と成果物までの問題点](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260831-helen-repro-project-current-state.html>)。武田さんの「新規親・孤立禁止」の選択により相互リンクを追加。
> 本追記は入口の追加のみで、既存計画の仕様・承認範囲・水着化の進捗を変更しない。

このページは、別セッションが会話履歴なしで次の順序を守って再開するための入口である。

1. 承認済みの目的・完成条件を保護する。
2. 現行成果物、検査結果、古い状態表示を分離する。
3. `f166`コード資産棚卸しの未完了点を確定する。
4. 未回収コードをS6・S8・G10へ接続する作業を、Claude主解析と独立検証へ限定する。

## 関連ファイルの実パス（2026-08-30 追記）

本文の `[[slug]]` は Obsidian の記法で、新しいセッションの LLM は解決できない。実体は次のとおり。
作業ディレクトリは
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`。

- `[[gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827]]` → `wiki/builds/gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827.md`
- `[[gf2-char-extract-handoff]]` → `wiki/builds/gf2-char-extract-handoff.md`
- `[[gf2-repro-and-swimsuit-conversation-handoff-20260827]]` → `wiki/builds/gf2-repro-and-swimsuit-conversation-handoff-20260827.md`
- `[[gf2-helen-repro-v51-handoff]]` → `wiki/builds/gf2-helen-repro-v51-handoff.md`
- `[[gf2-helen-repro-v51-run]]` → `wiki/builds/gf2-helen-repro-v51-run.md`

このページを入口として渡された場合の戻り先（水着案件の brainstorm メモ・正本）:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

承認済み計画を全面的に作り直すページではない。「計画が崩れている」という曖昧な説明で済ませず、
どの記録が何と食い違い、何を修復してから解析へ進むかを固定する。

> [!warning] 承認状態
> このページに書く状態修復、モデル配分、未回収コード解析は、再開用の`proposed / not-approved`
> （提案済み・未承認）仕様である。Blend変更、`f166`再実行、強制DL、状態JSONの新設・変更は未承認。
>
> **2026-08-29追記:** 下の旧section 4〜7を発展させた過大な計画は、ユーザーが承認せず、独立レビューでも
> `全面差し戻し`となった。現行の提案・再開地点はsection 9を正本とする。旧節は経緯を失わないため残すが、
> 実行指示として採用しない。

## 1. 目的・完成条件と現在状態の正本

### 1.1 要件・完成条件の正本

次の2ファイルを要件と工程規約の正本として保持する。

1. `/Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md`
2. `/Users/takedayousuke/.claude/plans/mellow-questing-elephant-implementation-instructions-v2.md`

古い状態値や過去時点の事実が混在することを理由に、計画全体を破棄しない。目的・完成条件・禁止事項と、
その後に変わった実行状態を分離する。

### 1.2 現在状態の採用順位

現在状態は次の順で確認する。

1. 実ファイルの存在・内容・SHA-256
2. 現行成果物SHAへ拘束された個別ゲートJSON
3. `run-state.json`の現行成果物節
4. Wiki後半の最新履歴
5. Wiki冒頭の要約、古いゲート集計、古い`next_action`

古い要約や履歴は削除しない。現行ではない値を`superseded`（後の版で置き換え済み）、過去工程だけの
合格を`historical`、限られた検査範囲だけの合格を`scoped-pass`として区別する。

## 2. 2026-08-27に固定した現在事実

### 2.1 現行成果物

- プロジェクト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51`
- 成果物:
  `blends/helen-h0157-repro.blend`
- 実測SHA-256:
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`

[[gf2-helen-repro-v51-handoff]]冒頭の`6be9540d…`、古い`14 PASS / 1 FAIL`、古い事実件数は、
現行成果物の完成状態として採用しない。同ページ後半と`run-state.json`の現行成果物節は
`04ef8b79…`を認識しており、冒頭要約との不一致がある。

### 2.2 現行ゲート

| ゲート | 現行SHAとの関係 | 結果 | 意味 |
|---|---|---|---|
| `logs/f128-audit.json` | `04ef8b79…`に拘束 | `pass=true` | 構造監査の限定合格。原作の見た目一致ではない |
| `logs/f152-visual-gates.json` | 同じ`04ef8b79…`に拘束 | `pass=false` | 見た目比較門は未合格 |
| 汎用品質ゲート`plan` | 現行計画条件 | PASS | 計画段階のみ |
| 汎用品質ゲート`batch` | 量産・展開条件 | FAIL | 後続展開禁止 |
| 汎用品質ゲート`complete` | 完成条件 | FAIL | 完成・運用可能とは報告禁止 |

`f152`の現行内訳:

- S6: 顔の白飛び率39.334%。合格条件は1%以下。
- S7: 手は現行測定でPASS。
- S8: 髪の被覆率0.557。合格条件は0.67以上。
- S9: 広い参照帯域に入る限定PASS。材質忠実性全体の合格ではない。

工程Fのユーザー受入れと最終監査は未完了。工程Gへ進むことは禁止状態である。古い
`logs/gate-results.json`の`14 PASS / 1 FAIL`は2026-08-22の旧成果物に対する集計で、現行完成率ではない。

### 2.3 現在の既知課題

- S6: 顔の白飛び、輝度分配、`InternalLut`、ポスト処理
- S8: 生え際・髪被覆、実行時髪物理、白飛びとの複合
- G10: 材質とRampの権威ある対応
- FaceSDF
- 直接光
- scene/prefab側の未回収入力
- 実行時物理入力
- `f153`スペキュラ候補はポスト処理解決まで凍結
- `f162`ではACES伝達だけでS6はほぼ改善せず、`k=0.4`は顔を通す一方で全身を暗くしすぎた
- `f165`では髪rest規約を原因から除外し、実行時物理入力が残った

## 3. `f166`コード資産棚卸しの正確な停止点

### 3.1 初回全量走査で確認した値

- 18,568 bundleを走査
- 固有MSL文書7,726種
- 既存抽出と一致しない文書7,424種
  - fragment 5,553種
  - vertex 1,855種
  - kernel 63種
- 平文PE DLL 33本、33本ともメタデータ読取可能
- Lua 1,031本
  - Lua 5.3ヘッダ確認1,030本
  - 主チャンク定数抽出成功1,030本
- MonoScript 14,534件
- distinct assemblies 62
- DLLとの突合は`possible_partial`

### 3.2 完了扱いできない理由

- `logs/f166-code-inventory.json`とstatusは2026-08-26 22:42頃の結果。
- `scripts/f166_code_inventory.py`は22:49頃にさらに変更された。
- 最終変更は、既存post MSL末尾のNUL以降へ混入したメタデータを正規化する修正。
- 最終修正版による全量再実行、整合確認、独立監査は未実施。
- LZ4エラー4件が残る。
- vertex既存抽出の控除が不足している。
- post MSLは109件不一致が残る。
- 固定出力名が再実行時に旧結果を上書きする。
- 結果へスクリプトSHAと入力スナップショットが拘束されていない。

したがって`status=done`は初回プロセスが終了したことだけを意味する。`f166完成`、`監査合格`、
`ローカルコード解析完了`へ読み替えない。2回目の子セッションはモデル非対応エラーで終了しており、
完成根拠にしない。

## 4. 旧案 — 状態修復を先に行う実装順（2026-08-29 superseded）

> [!warning] 旧案
> この節を拡張した計画は、状態投影・台帳・版拘束・独立checkerを大規模化しすぎたため全面差し戻しとなった。
> 現行案はsection 9.6を使う。

次セッションは、未回収コード解析やBlend修正へ直行しない。

1. **読み取り専用スナップショット**
   - 現行Blend、承認済み計画、`run-state.json`、Wiki、個別ゲートJSON、`f166`スクリプトと結果の
     SHA・更新時刻を採取する。
2. **主張台帳**
   - 各文書の成果物SHA、事実件数、ゲート合否、次作業、工程状態を1行ずつ照合する。
   - `active / superseded / historical / scoped-pass / incomplete`へ分類する。
3. **現行状態の単一投影**
   - 既存ファイルを機械正本にするか、小さな現行状態ファイルを新設するかを設計案として提示する。
   - 新しいJSONスキーマやファイルは構造変更なので、実装前にユーザー承認を取る。
4. **版拘束**
   - 各ゲート結果へ成果物SHA、入力群SHA、検査スクリプトSHA、開始・終了時刻、実行状態を必須化する。
   - いずれかが異なる旧結果は現行合格へ数えない。
5. **ゲート統合**
   - `f128`合格と`f152`不合格を1つの完成率へ潰さない。
   - project固有ゲートと汎用品質ゲートを同じrun manifestへ列挙し、欠けたゲートはFAILにする。
6. **`f166`確定**
   - 最終版スクリプトで全量再走査する。
   - 別実装の読み取り監査を行う。
   - 時刻以外のpayloadで決定性を比較する。
   - LZ4エラー4件と各不一致を説明可能な分類へ分ける。
   - 成果物Blendが実行前後で不変であることを確認する。
7. **Wiki同期**
   - 冒頭の現行値、現在位置、残課題、次作業を機械正本から同期する。
   - 履歴は消さず、旧値を`superseded`として残す。
8. **修復完了判定**
   - 同一成果物SHAの状態表示が機械正本・Wiki・ゲートmanifestで一致した場合だけ
     「状態修復済み」とする。
   - 状態修復済みは、原作再現完成を意味しない。

## 5. 旧案 — 未回収コード解析のモデル配分（2026-08-29 superseded）

> [!warning] 旧案
> Luna→Solを前提にした全面棚卸し型の配分は現行では採用しない。ユーザーの要件はClaudeを主解析役にすること、
> Claudeが使えない場合はGPTへ黙って代替しないことである。現行の役割分担はsection 9.6を使う。

### 5.1 配分の正本

次のユーザー提示ファイルを、前回回答の正本として使う。

`/Users/takedayousuke/llm-uploads/20260827-220708-未回収コード解析のモデル配分.md`

7月作成の一般的な廉価LLMワークフローを、この回答と取り違えない。

### 5.2 Lunaへ割り当てる作業

- DBから発言・実行結果を転記する。
- 7,424種のMSLを、事前に固定した署名・単語・参照元規則で分類する。
- 既存検査を再実行する。
- 件数、SHA、時刻、パスを台帳化する。
- DLL・Luaの文字列と型一覧を抽出する。
- Solが決めた否定試験を機械実行する。
- 確定済み結果をWikiへ転記する。

Lunaには、品質欠陥へ効くコードの最終判断、scene/prefab値の推定、Blender変換規則の設計、
相反する根拠の決着、原作一致・完成の宣言をさせない。

### 5.3 Solへ割り当てる作業

- 肌色、顔、髪、指、質感、ポスト処理、物理へ接続するコード候補を選別する。
- vertex/computeコードをBlenderへ移す際の座標系・入力・実行順を設計する。
- 検査器が品質欠陥を本当に止めるか設計する。
- ログ・コード・原作フレームが相反する場合に、根拠の採用可否を決める。
- Lunaの抽出規則と否定試験を設計する。
- 原作忠実性の最終監査を担当する。

Solにも、存在しない入力値の補完、自分の推論の`OBS`昇格、原作フレームを見ただけの原因確定、
自分の成功報告だけによる合格判定をさせない。

### 5.4 LunaからSolへ渡す証拠束

各証拠束は最低限、次を持つ。

- `task_id`
- 調べる品質欠陥
- 入力パスとSHA
- 抽出元bundle・DLL・Lua・MSLの識別子
- stage、関数、参照元、入力、出力、関連語
- 一致件数と全体件数
- 陽性対照・陰性対照
- `extractable / unreadable / ambiguous / missing`の区別
- 再現コマンド
- Lunaによる品質結論は空欄

### 5.5 SolからLunaへ戻す作業票

- 対象候補
- 品質欠陥へ接続しうる因果経路
- その経路を否定できる試験
- 必要な入力
- 期待する観測値
- `pass / fail / blocked`条件
- Blenderや成果物を変更してよいか
- 結論をWikiへ固定できる証拠レベル

### 5.6 効率化の停止条件

- Lunaの分類規則に判断語が混ざる。
- Solへ全7,424文書を無選別で渡す。
- 証拠束にSHAまたは再現コマンドが無い。
- Solが否定試験なしで採用を決める。
- Lunaの再実行結果がSolの前提と食い違う。
- scene/prefab入力不足をモデル能力で埋めようとする。

最も効率がよい流れは、`Lunaで証拠を縮小 → Solで因果と否定試験を設計 → Lunaで再実行・台帳化 →
Solで原作忠実性を最終監査`である。

## 6. 旧案 — 高リスク品質ゲート（2026-08-29 superseded）

> [!warning] 旧案
> 正解の所在と推測禁止は維持するが、対象群を広げたこの節の実行順は採用しない。現行の対象群、比較方法、
> 停止条件はsection 9.6〜9.7を使う。

### 正解の所在

原作ゲームのコード・データ、原作実機フレーム、現行成果物の直接比較。内部ゲート単独では正解にならない。

### 欠けうる入力

scene/prefab、FaceSDF、InternalLutの実行時入力、直接光、髪物理、材質とRampの権威対応。

### 性質の違う対象群

顔・肌、髪、生え際、手・指、材質・Ramp、照明・ポスト処理、実行時物理。

### 代表例

現行差が機械検出済みのS6、S8、G10を先に扱い、S7とS9を対照として保持する。

### 原作比較方法

コード・入力・処理順を一次データで結び、同一条件の原作フレームと候補を比較する。LLMが差を先に
1〜4件へ絞り、ユーザーへ求めるのは差の許容判断だけとする。

### 停止条件

- `f166`が最終版で再走査・監査されるまで、コード資産の網羅を主張しない。
- 欠けた入力を推定で埋める場合は忠実版と分離し、`approximation`として別承認を取る。
- `batch`失敗中は量産・展開しない。
- `complete`失敗中は完成・運用可能と報告しない。

## 7. 旧・次セッションへ貼る再開プロンプト（2026-08-29 superseded）

> [!warning] 使用禁止
> このプロンプトは休止中の`hold`とPlan Modeを要求するため、現在の再開指示として使わない。
> 2026-08-29時点の再開入口はsection 9.9を使う。

```text
/hold

次の現行引き継ぎ資料を最初から最後まで読んでください。
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md

Plan Modeのまま、ファイル変更・f166再実行・Blend変更・強制DLは行わないでください。
会話履歴や古いWiki要約ではなく、引き継ぎ資料が指定する正本と現行実ファイルを再照合してください。

最初に、
1. 承認済みの目的
2. 現行成果物SHA
3. 現行ゲートの合否
4. f166の正確な未完了点
5. 状態修復を先に行う理由
6. LunaとSolの役割境界
を短く説明してください。

その説明のあと、理解した前提だけを承認対象にするカードを出してください。
私が前提を承認するまで、状態修復計画や実装を承認済みとして扱わないでください。
```

## 8. このページ作成時に変更していないもの

- `blends/helen-h0157-repro.blend`
- `run-state.json`
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json`
- `scripts/f166_code_inventory.py`
- `logs/f166-code-inventory.json`
- その他のプロジェクトJSON、ゲート、検査スクリプト

このページは会話再開用のWiki記録だけであり、状態修復や解析を実行した証拠ではない。

## 使わなかったもの・落とした情報

- **Dusevnyj P3水着案件の詳細をこのページから分離した。**
  手元でどう変わるか: このページだけでは水着の部品選定やハーネス計画を再開できず、ヘレン完全原作再現の
  状態修復とコード解析へ集中する。戻せるか: [[gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827]]を読めば戻せる。
- **未回収入力を推測で補う案を採用しなかった。**
  手元でどう変わるか: scene/prefab等が無い項目は忠実版のまま未完了として残る。戻せるか: 推定版を
  `approximation`として分離する計画を別承認すれば試せる。
- **強制DLを次作業として固定しなかった。**
  手元でどう変わるか: まず状態修復とローカルコード棚卸しを確定し、未調査領域を飛ばさない。
  戻せるか: ローカル側の調査境界確定後、別のリスク評価と承認で再提案できる。

## 関連リンク

- [[gf2-helen-repro-v51-handoff]]
- [[gf2-helen-repro-v51-run]]
- [[gf2-repro-and-swimsuit-conversation-handoff-20260827]]
- [[gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827]]

## 9. 2026-08-29 — 利用上限中断からの再開、ユーザー不承認、独立レビュー、縮小計画

### 9.1 この節の位置づけ

この節は、2026-08-29にCodexで発生した計画作成・不承認・独立レビュー・利用上限中断・再開の経緯を、
次セッションが会話履歴なしで復元するための現行記録である。section 1〜3の目的・実測事実・`f166`停止点は
維持し、section 4〜7の旧案だけを`superseded`とする。

**現在の段階は計画レビューであり、実行ではない。** この一連の作業では、Blend変更、`run-state.json`変更、
`f166`再実行、強制ダウンロード、解析用基盤の新設を行っていない。

### 9.2 会話の時系列

1. ユーザーは、このページの絶対パスを指定し、中断したタスクの続きを再開するよう依頼した。
2. 最初の再開案に対し、ユーザーは「計画が省かれすぎ」と指摘した。また、`hold`スキルが壊れているため
   無視すること、ここまでの経緯を踏まえない計画を禁止することを明示した。
3. その後に提示された詳細計画は、状態管理・全コード棚卸し・全件解析・二重走査・巨大な検査器まで含む
   規模になった。ユーザーはこれを承認せず、計画を作成したメインエージェントの誘導を避ける指示で、
   サブエージェントによる独立レビューを要求した。
4. メインエージェントはサブエージェント`01a045a7-2303-7270-bcc5-e6a3f0cfc924`へ、既存計画の結論を前提に
   せず、正本・現行実ファイル・ゲート・`f166`を自分で読み直し、最終成果物の品質とClaudeの有限トークンを
   優先してレビューするよう依頼した。レビュー指示には、全量基盤を正当化する方向へ誘導しないこと、
   過剰実装を削ること、原作差へ接続できない解析を退けることを含めた。
5. 独立レビューは完了し、判定は`全面差し戻し`だった。レビュー完了後、利用上限に到達して会話が中断した。
6. 利用上限後の再開時に、メインエージェントは本ページ、承認済み計画2本、現行Blend、`run-state.json`、
   `f128`、`f152`、`f166`スクリプトと旧結果を再照合した。その結果を基に、全面棚卸しではなく
   S6・S8・G10へ直結する縮小計画を提示した。ユーザーの承認はまだない。

### 9.3 独立レビューへ渡した評価基準

レビュー役には次を要求した。

- この案件の目的を「ローカルコードをすべて整理すること」ではなく、H0157成果物を原作の見た目へ近づけ、
  工程Fのユーザー判断へ進めることとして復元する。
- 制作側の成功報告や既存計画の用語を根拠にせず、正本・入力・出力・検査結果を直接読む。
- 全件解析、巨大台帳、新スキーマ、二重全量走査が、本当にS6・S8・G10の改善へ必要かを個別に判定する。
- Claudeの有限トークンを、関連性が低い全件投入で消費しない。
- 候補コードから原作フレームの差、実行時条件、Blendで必要な変更へ至る証拠経路が無ければ不合格にする。
- 指摘を`critical / major / minor`へ分け、削除すべき作業、追加すべき証拠、正しい実行順、合格条件、
  停止条件を提示する。

### 9.4 独立レビューの結論

判定は**全面差し戻し**。主な理由は次のとおり。

#### Critical

1. **全コード棚卸し基盤が目的化していた。** DLL全メソッド、MonoScript 14,534件の完全結合、全シェーダ
   変種、二重全量走査、巨大スキーマは、S6・S8・G10の改善開始を遅らせる。
2. **コード候補を原作差へ接続する合格経路が弱い。** `classification-ready`等の内部状態を細かく定義する一方、
   候補コード、原作フレーム、実行条件、Blend差分を直接結ぶ証拠が中心になっていなかった。
3. **「全ローカルコード」の範囲が無期限に拡大する。** compiled shader、TextAsset、MonoBehaviour、Mach-Oを、
   S6・S8・G10との接続証拠なしに追加していた。

#### Major

1. `f166`に今すぐ必要なのは、両方向NUL正規化、LZ4失敗の記録、スクリプトSHA・入力manifest、旧結果を
   上書きしない出力名である。最初から同じ全量走査を2回行う必要はない。
2. 分散した状態を直すために新しい状態台帳を増やすと、正本がさらに分裂する。
3. DLL・Lua・MonoScriptの全件完了を待たず、品質欠陥に関連するMSL候補が得られた時点でClaude解析を始めるべき。
4. `/Users/takedayousuke/llm-uploads/20260828-083505-サブエージェント起動時にプロバイダ側エラー-Upstream-request-f.md`
   は`Upstream request failed, restart`の1行だけで、過去の作業内容を復元する根拠にはできない。

### 9.5 旧計画から削除・保留するもの

#### 今回削除する

- 巨大なコードドメイン台帳と新しい状態スキーマ。
- DLL 33本すべての無条件なメソッド解析。
- MonoScript 14,534件すべての無条件な完全結合。
- 全シェーダ変種の無条件な詳細解析。
- 最初から行う2回の全量走査。
- 関連性を証明する前のcompiled shader、TextAsset、MonoBehaviour、Mach-O追加走査。
- 全ドメインを再実装した独立checker。

#### 実害が出た場合だけ行う

- 同一入力で結果が変動した場合の2回目の`f166`全量走査。
- S6・S8・G10候補が既存抽出に存在しないことを確認した後の追加形式走査。
- 関連候補が見つかったDLL・Lua・MonoScriptだけの深掘り。

### 9.6 現行の縮小計画（未承認）

#### 1. 既存状態を最小修復

- 新しい状態ファイルやスキーマを作らない。
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json`
  の既存項目だけを対象とし、現行Blend SHA、`f128=scoped-pass`、`f152=fail`、`f166=incomplete`、
  次作業を一致させる案とする。
- 旧SHA・旧ゲート集計は削除せず履歴として残す。
- 工程Fのユーザー合格が無いため、工程Gへ進めない。

#### 2. `f166`の最小修理

- 既存抽出側と再抽出側の両方でMSL末尾NULを正規化する。
- LZ4失敗4件の入力パス、圧縮形式、失敗理由を保存する。
- 結果へスクリプトSHA、入力一覧とSHA、開始・終了時刻を拘束する。
- 旧結果を上書きしない新しい結果名を使う。
- 全MSLの巨大コーパスは作らず、S6・S8・G10候補だけ本文・SHA・bundle・Shader・pass・stageを保存する。

#### 3. 1回だけ全量再走査

- 修理後の`f166`を1回実行する。
- 実行前後でスクリプト・入力・現行Blend SHAが変化していないことを確認する。
- NUL差分、post不一致109件、vertex控除不足、LZ4失敗4件を再集計する。
- 非決定性が観測された場合だけ2回目を行う。

#### 4. S6・S8・G10候補を機械的に絞る

- S6: `InternalLut`、`_Lut_Params`、`_FilmWhiteClip`、`ColorLookup`、`Tonemapping`、`ACES`、
  exposure、bloom、clamp、saturate、face、skin、direct light。
- S8: hair、alpha、clip、cutoff、`GFHairShadow`、`GFCharHairTransE`、vertex、kernel、physics、
  gravity、wind、hair shadow、coverage。
- G10: `_UseRampMap`、`_RampMap`、`_BaseMap`、`_BumpMap`、`_RMOTex`、RampSetting、Material、
  Renderer、submesh、silkstock。
- `_FilmWhiteClip`、`_Lut_Params`、`_RampMap`を陽性対照にし、無関係なUI・OSSを陰性対照にする。

#### 5. Claudeを主解析役にする

- S6・S8・G10ごとに、コード本文、SHA、来歴、入出力、原作差との候補接続、反証試験、必要入力を渡す。
- Claudeは`confirmed / candidate / blocked`を分ける。
- 3対象は独立性がある範囲で並列解析できる。
- DLL・Lua・MonoScript全件の完了を待たない。
- Claudeが利用できない場合は、GPTへ黙って代替せず`技術的停止`とする。

#### 6. 別のClaude検証役が直接比較

- 制作側の説明を根拠にせず、原作アセットまたは原作フレーム、候補コード、実行条件、現行Blend出力を読む。
- 原作フレームとBlend比較の、動画・時刻・ポーズ・カメラ・衣装・ROI・画像処理条件を照合する。
- 対応を証明できない画像は`visual-reference-only`とし、因果修正の合格証拠に使わない。
- 結論は「接続できる／証拠不足／反証／入力不足」に分ける。

#### 7. Blend変更は別計画

- 原作差との接続証拠、変更対象と影響範囲、戻し方が揃った候補だけを別計画へ送る。
- この計画ではBlendを変更せず、工程Gにも進まない。

### 9.7 合格条件と停止条件

合格条件:

- 現行Blend SHAとゲート状態が既存正本間で一致する。
- `f166`結果に記録したスクリプトSHAが実ファイルと一致する。
- 候補MSLからbundle・Shader・passへ逆引きできる。
- ClaudeがS6・S8・G10ごとに候補、根拠、反証方法、不足入力を返す。
- 原作とBlendの比較時刻・条件が対応する。
- 内部検査の合格を原作一致と書かない。

停止条件:

- `f166`の不一致またはLZ4失敗を説明できない。
- 実行中に入力またはスクリプトが変化する。
- Claudeが利用できない。
- 候補を原作差へ接続する証拠が無い。
- 欠けた入力を推測で補う必要がある。
- Blend変更または工程Gが必要になる。

### 9.8 2026-08-29に再照合した現行事実

- 現行Blend SHA-256: `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- `logs/f128-audit.json`: 同SHAに拘束され、`pass=true`。構造の限定合格。
- `logs/f152-visual-gates.json`: 同SHAに拘束され、`pass=false`。
- `scripts/f166_code_inventory.py`: 更新時刻2026-08-26 22:49:42、SHA-256
  `5ec2fbfd4efbe4622774845bfa5a9105b48835e56a06f9337c3ed2bcea431c27`。
- `logs/f166-code-inventory.json`: 更新時刻2026-08-26 22:42:48、SHA-256
  `842c0312487199098b2e78d6318a1cf272aa329bc8d31438edc0a4f01731ea2d`。
- したがって旧`f166`結果は現行スクリプトより古く、現行結果ではない。
- 汎用品質ゲートの実ファイルは`06_repro-v51/quality-gate.json`ではなく、
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json`
  に存在する。前者は存在しない。旧section 8の「`quality-gate.json`を変更していない」は、ファイル名だけを
  記したもので所在が曖昧だったため、この絶対パスを現行の所在とする。

### 9.9 再開時に必ず辿る実パス

#### Wiki側

- Wikiルート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
- この引き継ぎ正本:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md`
- 実行記録:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md`
- 既存HANDOFF:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md`

#### 承認済み要件・工程規約

- `/Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md`
- `/Users/takedayousuke/.claude/plans/mellow-questing-elephant-implementation-instructions-v2.md`

#### 成果物プロジェクト

- プロジェクト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51`
- 現行Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend`
- 実行状態:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json`
- 汎用品質ゲート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json`
- `f128`:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f128-audit.json`
- `f152`:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f152-visual-gates.json`
- `f166`スクリプト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f166_code_inventory.py`
- `f166`旧結果:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f166-code-inventory.json`

### 9.10 次の状態

現在は**縮小計画のレビュー待ち**である。ユーザーは旧過大計画を明示的に不承認としているが、section 9.6の
縮小計画を承認したとは記録しない。無回答、利用上限、会話終了は承認・中断の証拠にしない。

次に必要なのはsection 9.6の内容に対するユーザーの明示的な承認・修正指示・中断指示のいずれかである。
承認前に`run-state.json`、`f166`、Blend、品質ゲートを変更しない。
