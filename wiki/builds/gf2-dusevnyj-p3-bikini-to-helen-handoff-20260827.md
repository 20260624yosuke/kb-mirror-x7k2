---
type: build
title: Dusevnyj P3ビキニ上衣→Helen 作画資料 ハーネス設計引き継ぎ 2026-08-27
status: active
confidence: medium
evidence_level: source-backed+user-stated+inferred
created: 2026-08-27
last_reviewed: 2026-08-28
sources:
  - gf2-char-extract-handoff
  - gf2-repro-and-swimsuit-conversation-handoff-20260827
  - gf2-sabrina-summer-bikini-no-frill-reference-build
  - gf2-sabrina-summer-bikini-center-refine-attempt
  - gf2-helen-bikini-harness-loop-application-2026-08-09
related:
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
tags: [gf2, dusevnyj, helen, bikini, handoff, harness-engineering, approximation]
revision: 2
---

# Dusevnyj P3ビキニ上衣→Helen 作画資料 ハーネス設計引き継ぎ — 2026-08-27

## このページの役割

このページは、Dusevnyj SSR0101のP3衣装から**ビキニ上衣だけ**を一次データで確定し、Helen本人の
既存ビキニ状下衣を残した作画資料へ適合する、新規・高リスク案件の再開入口である。

ヘレン完全原作再現とは完成条件が異なる。目的は、絵を描くときに多方向から形状を読める資料を作ること。
照明・ポスト処理・ゲーム完全再現は主目的ではない。ただし透過処理が輪郭や布面積を変える場合は
形状問題として検査する。

> [!warning] 現在の承認状態 — 2026-08-28
> 「Dusevnyj衣装とHelen体型の差を直接扱う専用監査ハーネスを、候補制作より先に作る」**方針**は
> ユーザー承認済み。独立監査役を付ける選択も承認済み。
> 一方、保存先・ファイル構成・診断fixture・実装順を含む**実装計画は未承認**。計画承認カードに対し
> ユーザーが「ここまでの会話の経緯を記録して。また再開します」と明示して中断した。
> ハーネス、診断fixture、候補Blend、納品物は未作成であり、実装へ進んではならない。

## 0. 2026-08-28 Codex再開調査の確定事項

### 0.1 中断までの状態遷移

1. 旧handoffを読んだ直後、Codexは一般的な段階実行と候補制作の流れを中心に方針提示した。
2. ユーザーは「オブジェクト生成パイプラインは証明済みで、問題は成果物へ到達できるか。まず監査スクリプトと
   ハーネスを徹底的に作る」と訂正し、最初の方針を不承認にした。
3. Codexは「監査先行」へ直したが、DusevnyjとHelenの体型差を具体的に扱えておらず、再度不承認となった。
4. 過去Codexタスク`codex://threads/01a04279-b8ec-74c3-8adb-f538c56873ed`、本ページ、関連Wiki、
   Dusevnyj/Helenの一次データ、旧Sabrina案件の成功・失敗証拠を読み直した。
5. 修正後の方針を「このドナー上衣とこのHelen胸の組合せ専用の監査ハーネスを先に作り、候補制作はまだ
   行わない」と提示。ユーザーは**独立監査つき**で方針承認した。
6. 独立監査のmajor指摘を反映した実装計画を提示したが、ユーザーは計画を承認せず、記録して後日再開すると
   明示した。現在は`中断 / 方針承認済み / 計画未承認 / 実装未開始`。

### 0.2 ユーザー目的の修正済み理解

- これは、既に成立しているオブジェクト生成パイプラインをもう一度証明する案件ではない。
- Dusevnyj本人向けのP3ビキニ構造を、体型の違うHelenの長い胸へ適合し、絵を描くときに多方向から
  接触・支持・食い込み・胸下露出を読める3D資料へ到達できるかが核心。
- ドナー衣装の忠実な抽出と、Helen体型への創作的な適合は別の根拠層として扱う。
- 公式の「P3上衣を着たHelen」は存在しない。技術検査が合格しても公式再現や用途合格とは呼ばず、
  最終的な見た目だけはユーザーの許容判断が必要。
- 今回再開時の作業対象は**専用監査ハーネスの実装計画を再提示し、承認を得るところから**。候補制作から
  始めてはならない。

### 0.3 一次データから追加確認した実物差

#### Dusevnyj P3上衣

- 現行Blend SHA-256:
  `50260288e83f07471f57db613ffc805ca80e8327c1db414d2c4a6ae8c22b1b47`。
- `c_DusevnyjSSR0101_slg_P3_cloth1_lod0`は、孤立投影で左右三角カップ、首へ伸びる線、下側の結びを持つ
  ビキニ上衣として確認した。2562頂点、4006面、15 bindpose。
- 範囲は幅`0.192427`、高さ`0.249003`、奥行き`0.192090`メートル。
- 白い上衣は単一meshではない。少なくとも`P3_cloth1_trans_lod0`の胴体状面、
  `P3_cloth1_trans_lod0_Dorm`の袖状面、`P3_cloth2_lod0`の高襟・腕部が見える。
- ただし`extract-manifest.json`では`P3_cloth1_lod0`のmaterialが`null`、sourceが`no_smr`であり、
  名前と再構築Blendの見た目だけを公式材質対応の権威にしてはならない。P3全LOD0とDorm/Fight派生を
  孤立表示し、位置・UV・連結・アイコン・可視状態で`adopt / exclude / unresolved`へ分ける必要がある。

#### Helen受け手

- 現行Blend SHA-256:
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- 現行表示対象は`c_HelenSSR0101_slg_cloth2_lod0_Flat`の肌submesh 0。1907頂点、3468面。
- 同肌面の範囲は幅`0.383305`、高さ`0.310743`、奥行き`0.229354`メートル。Dusevnyj上衣に対し
  約`1.992 / 1.248 / 1.194`倍であり、一様拡大や直接貼付では適合を判定できない。
- HelenにはGeneral / Flat / Bendの異なる胸meshがある。肌submesh 0の幅はFlat`0.383305`、
  General`0.282527`、Bend`0.257666`メートルで、無言の差替えは別の身体を検査することになる。
- `logs/c03-scene.json`と`visibility-decision.json`ではFlatが現行表示、General/Bendは非表示。
- `logs/f128-audit.json`はFlatを300 frame走査し、最大辺長比`17.158 @ frame 65`、`>1.5`辺累計
  `21880`、`>2`辺`5851`を既知変形として記録している。したがってHelen胸を静的な一般胴体として扱わず、
  neutralのAction/frameと代表姿勢ごとの実変形を固定する必要がある。

#### 骨・ウェイト

- Dusevnyj上衣は15/15骨を使用。重み量はChest_M`30.783%`、Spine2_M`20.247%`、
  Chest_L/R各`6.869%`などだが、名前未解決のhash骨6本が約`28.65%`を占める。
- Dusevnyj `skeleton.json`も親未解決が257/299。Helen Flat肌submesh 0は27 bindpose中24本を使うが、
  その24 hashは現行`skeleton.json`で名前対応を直接確定できない。
- よって単純な`rig-map.json`で骨名対応を作る案は撤回する。`rig-strategy.json`として、ドナーweightの
  直コピー禁止、Helen実表面由来weightの診断fixture試験、衣装と肌の相対変形の姿勢別実測を記録する。
  これは計画案であり、まだ実装・成功していない。

### 0.4 独立監査の結論

独立監査役はファイルを変更せず、次をmajor findingとして返した。

- 全体寸法比は基準線にすぎず、局所断面、カップ頂点、下乳境界、紐の支持点、接触分布を測る必要がある。
- Helenの現行対象をFlatへ固定し、neutralの定義と代表姿勢の実変形を契約へ含める。
- 骨の直接対応は現時点で成立せず、ドナーweight直コピーを負例にする。
- 部品同定は名前で確定せず、P3全候補を孤立表示して許可リスト化する。
- 候補前に証明できるのは、入力固定、既知負例の拒否、姿勢別再計算、独立再現まで。成功するfitの存在や
  納品可能性までは証明できない。
- Helen固有の偽陽性・偽陰性を校正するには、納品物でない診断用変形コピーが最低1つ必要。
- 別Blenderプロセスだけでは独立にならない。制作器と一次評価器で共通の合否helperを使わず、独立役は
  原本・fixture・生測定から別経路で再計算する。
- 旧Sabrinaの部品番号、閾値、18姿勢、838 state、PASS、bridge寸法、外観許容は流用禁止。

### 0.5 方針承認の有効範囲

承認済み:

- Dusevnyj/Helenの実物差を直接扱う専用監査ハーネスを候補制作より先に作る。
- ハーネス校正用の診断fixtureは許容するが、候補・納品物と明確に分離する。
- 独立監査役を付ける。

未承認:

- 保存先候補
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/helen-dusevnyj-p3-bikini-harness/`。
- `project-contract.json`、`source-lock.json`、`component-map.json`、`target-body-contract.json`、
  `pose-contract.json`、`rig-strategy.json`、`evaluator-contract.json`、`fixture-manifest.json`、
  `calibration-report.json`、`run-manifest.json`、`independent-review.json`、`quality-gate.json`を中心とする構成。
- 診断fixtureの具体的な生成方法、検査スクリプト、判定しきい値、代表Action/frame。
- ハーネス、候補Blend、納品物の実装。

### 0.6 計画案へ必ず含める正負例と停止条件

正例:

- Dusevnyj原着装のP3上衣構造。
- Helen現行Flat胸肌面と既存下衣の保持状態。

負例:

- 白ブラウスまたはDorm/Fight派生の重複混入。
- 片カップ、首紐、下紐の欠落・接続切れ。
- 一様拡大貼付、幅だけ広げてカップ奥行きを潰した形。
- 浮き、体内侵入、下縁が支持しない形、下乳を全部隠す／露出しすぎる形。
- donor weight直コピー、未知骨、未加重頂点。
- FlatからGeneral/Bendへの誤差替え。
- ベタ塗り画像だけによる前後誤認。
- 古いrun、固定値複写、異常終了後に残った古い成果。

停止条件:

- 部品、Helen対象肌面、保持下衣、neutral・代表frameを一次データで固定できない。
- 正例と上記負例を意図した理由コードで区別できない。
- 独立経路の再計算が一致しない。
- 入力・スクリプト・結果SHAまたは実行時刻が契約と一致しない。
- Helenの欠損肌面を推測で創作しなければ続行できない。この場合は`blocked`とし、bridge案は別の
  `approximation`承認へ分ける。

## 1. 今回固定するユーザー選択

- Dusevnyj P3の白い透けブラウスは使わない。
- 移植対象はP3のビキニ上衣。
- Helenの完成形では、Helen既存のビキニ状下衣を残す。
- 初期の姿勢保証は中立＋代表姿勢。
- 将来、全モーション検査へ拡張する可能性は残す。
- 以上は構成と検査範囲の選択であり、ハーネス計画や実装の承認ではない。

### 使わないものと手元での見え方

| 使わないもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| 白い透けブラウス | Helenはブラウスを着ず、P3のビキニ上衣が直接見える | Dusevnyj原本と除外部品台帳を保持し、別候補で再追加できる |
| P3下衣 | Dusevnyj P3上下セットではなく、P3上衣＋Helen既存下衣になる | P3下衣は原本に残し、別承認の比較候補として追加できる |
| 初期の全モーション保証 | 未検査動作で衣装追従を保証しない | 代表姿勢合格後、対象Actionと全stateを固定して別バッチへ拡張できる |

## 2. 高リスク案件として固定する6項目

### 2.1 正解の所在

- ドナー衣装の一次データ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/intermediate/Dusevnyj.DusevnyjSSR0101`
- ドナー確認用Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/Dusevnyj-DusevnyjSSR0101-repro.blend`
- 受け手のHelen:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend`
- 最終用途の正解:
  ユーザーが作画資料として形状差を許容できること。

公式の「Dusevnyj P3上衣を着たHelen」は存在しない。完成しても公式再現とは呼ばず、
`approximation`（根拠を限定した近似版）とする。

### 2.2 欠けうる入力

- P3の各mesh名と「白い透けブラウス」「ビキニ上衣」の権威対応
- Helenの衣装下で欠けている身体表面
- DusevnyjとHelenの骨対応
- 代表姿勢での公式な正解fit
- Helen用に作られた公式ウェイト
- 公式のHelen＋P3水着見本

`P3_cloth1_trans`等の名前だけから白いブラウスや上衣を確定しない。位置、材質、UV、アイコン、
連結成分、Blend上の表示を直接突合する。

### 2.3 性質の違う対象群

- `source-donor`: Dusevnyj本人のP3
- `neutral-upper`: Helen中立姿勢の上衣
- `existing-bottom`: Helen既存下衣との接続・重複
- `shoulder-arm`: 肩・腕上げ
- `torso-bend`: 前後屈
- `torso-twist`: ひねり
- 将来の`full-motion`: 別承認後のみ

### 2.4 代表例

- Dusevnyj本人のP3表示
- Helen中立姿勢
- 肩・腕上げ、前後屈、ひねりから各1〜少数例

代表姿勢数は既存Action名や旧案件の「18姿勢」から推測せず、Action内容を直接読んで決める。

### 2.5 比較方法

- 原本・候補を同一Blender版、固定床、固定縮尺、固定カメラで比較する。
- 実材質、半透明、ワイヤーフレーム、断面を併用する。
- 交差候補、身体表面との距離、露出境界、接続、伸びを機械測定する。
- LLMが差を1〜4件へ絞った後だけ、ユーザーへ外観許容を求める。

### 2.6 停止条件

- ビキニ上衣を一次データから確定できない間は候補を作らない。
- 白いブラウスを除外した証拠が無い間は候補を作らない。
- Helen既存下衣との二重表示を解消できない間は代表姿勢へ進まない。
- 中立姿勢失敗、検査器不足、候補1体の失敗だけでは技術的停止にしない。
- 入力不足を推測で埋める場合は、忠実な入力だけの版と分離し、別の`approximation`承認を取る。
- 全モーション検査は初期完成条件に含めず、代表姿勢合格後の別拡張とする。

## 3. 現在確認できる入力と未確定点

### 3.1 Dusevnyj側

- 現行Blendは`blends/Dusevnyj-DusevnyjSSR0101-repro.blend`に実在する。
- intermediateにはP1/P2/P3の衣装アイコンがあり、P3は透けトップを含む衣装として確認できる。
- P3候補meshとして次が実在する。
  - `P3_body`
  - `P3_body1`
  - `P3_cloth1`
  - `P3_cloth1_trans`
  - `P3_cloth2`
  - `P3_hip1`
  - `P3_hip2`
- これらの名前だけではビキニ上衣の採用成分を確定できない。
- `P3_cloth1_trans`には`Dorm`、`Fight`等の派生もあり、複数版を同時採用すると重複面や
  z-fighting（ほぼ同じ面が重なってちらつく状態）を起こしうる。
- [[gf2-char-extract-handoff]]では、衣装切替、alpha、共有肌テクスチャ、Ramp、SH照明などの抽出実績がある。
  ただしこの実績は、P3ビキニ上衣の部品対応やHelenへのfitを証明しない。

### 3.2 Helen側

- 受け手は`blends/helen-h0157-repro.blend`、SHA-256は2026-08-27時点で
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- Helenには既存のビキニ状下衣があるというユーザー指摘を採用する。
- その具体的なオブジェクト、材質、保持すべき成分は、次セッションのsource auditで確定する。
- 衣装を外したときの胸・首・脇・腹・腰の身体表面が完全とは仮定しない。

## 4. 旧サブリナ案件から再利用するもの・流用しないもの

### 4.1 再利用する技術

- 入力SHA固定
- 正例・負例による検査器校正
- 部品台帳
- `structure / projection-diagnostic / geometry-fit / appearance`の4層分離
- 交差候補、表面距離、断面、半透明、wireの併用
- 原本を上書きしない別名Blend
- 別Blenderプロセスによる再読込
- 姿勢別実測
- 将来候補を作る場合の有界な候補数（今回のハーネス工程には候補制作を含めない）
- 内部工程としてのpilotと、機械ゲート`plan / batch / complete`の分離
- 全件画像ではなく項目別の最悪例をユーザーへ提示する方法

### 4.2 流用を禁止するもの

- サブリナの部品番号
- サブリナとHelenの骨対応
- 旧閾値
- 旧PASS
- 代表18姿勢
- 838 state
- 旧候補Blend
- 旧ユーザー許容
- ベタ塗り画像による貫通判定
- 姿勢別に見せかけた固定値
- 「検査器がPASSしたから水着として良い」という結論

旧案件では、ベタ塗り画像から3D貫通を誤認し、旧独立レビューも同じ解釈を追認した。また、姿勢別実測に
見えた4項目が固定値だった。これらを新ハーネスの既知負例として使う。

## 5. 懸念とハーネスの対応

| 懸念 | 防止・検出 | 不合格時の動作 |
|---|---|---|
| 白いブラウスを誤採用 | P3全候補を位置・材質・UV・表示状態で台帳化し、除外成分SHAを固定 | `source-audit`へ戻る |
| ビキニ以外のP3部品混入 | 採用連結成分の許可リスト方式 | 未登録成分が候補にあればFAIL |
| Helen下衣との二重表示 | 同一領域の表面層数、近接面、z-fighting候補を測る | 上衣fitへ進まず構成を修正 |
| Helen身体の欠損 | 衣装を外して胸・首・脇・腹・腰を走査 | 既存身体部品を探し、無ければblockedまたは分離bridge案 |
| LLMによる身体創作 | 新規面は別オブジェクト・別材質・来歴・可逆削除を必須化 | 原作身体とは呼ばず別承認 |
| 骨対応の誤り | 骨名、rest変換、親子、影響頂点、ウェイト分布を比較 | 自動対応を拒否し局所対応へ |
| 静止時だけ合う | 中立後に肩・曲げ・ひねりを姿勢ごとに再計算 | 失敗群だけfitを修正 |
| 固定値の使い回し | JSONへ`measurement_scope=per_pose/static`を必須化 | per-pose項目の不自然な全一致はFAIL |
| 接触を貫通と誤認 | 正例にも同じ検査を行い、交差・距離・断面を組み合わせる | 画像単独では停止しない |
| 貫通の見逃し | 表面距離、三角形交差、法線向き、断面を併用 | 最悪位置を自動抽出 |
| 透過で布面積が変わる | 実材質・alpha接続・しきい値と不透明診断を比較 | alpha未接続や輪郭不一致はFAIL |
| 形状が水着に見えない | 正面・側面・斜めの形、接続、被覆を外観ゲートとして分離 | 機械PASSでもユーザー許容前は未完成 |
| 制作器と検査器の共通誤り | 既知負例、別実装監査、別プロセス再計算 | 同じ関数の再利用だけで独立監査としない |
| 古い結果流用 | 入力・候補・スクリプト・出力SHAを1 run manifestへ拘束 | 1つでも不一致なら結果無効 |
| 検査の肥大化 | neutral→代表姿勢→任意の全モーションの順に限定 | 代表合格前に全stateへ進まない |
| 前回ログで偽成功 | 開始時刻より新しい出力と正常終了状態を必須化 | 古いログしか無ければFAIL |

## 6. 最新ハーネス構成案（未承認）

> [!warning] revision 1案からの変更
> revision 1の`rig-map.json`と、校正直後に候補制作へ進むPhase 2以降は`superseded`。
> 骨対応を推測する危険と「まずハーネスを徹底的に作る」というユーザー訂正を反映し、現在の案は
> `rig-strategy.json`と診断fixtureまでで停止する。以下は依然として`proposed / not-approved`。

保存先候補:

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/helen-dusevnyj-p3-bikini-harness/`

- `project-contract.json`
  - 目的、完成条件、非対象、原本不変、診断fixtureと候補・納品物の境界
- `source-lock.json`
  - 両Blend、intermediate、Blender 4.5.11 LTS、各SHA
- `component-map.json`
  - P3全LOD0・Dorm/Fight派生と`adopt / exclude / unresolved`
- `target-body-contract.json`
  - Helen Flat肌面、General/Bend除外、既存下衣、局所断面・支持点
- `pose-contract.json`
  - neutralのAction/frame、代表姿勢の選定根拠、将来拡張境界
- `rig-strategy.json`
  - donor weight直コピー禁止、Helen実表面由来weightの診断方法、未解決骨
- `evaluator-contract.json`
  - 構造、接触、露出、変形、表示の測定定義と`static / per_pose`
- `fixture-manifest.json`
  - 正例・負例の生成根拠、`diagnostic-only / not-candidate / not-deliverable`
- `forbidden-legacy-values.json`
  - 旧Sabrina固有値が期待値・閾値・PASSへ混入した場合のFAIL条件
- `render-contract.json`
  - 固定視点、断面、実材質、半透明、wire、比較順
- `calibration-report.json` / `run-manifest.json`
  - 分類結果、理由コード、入力・script・出力SHA、時刻、正常終了
- `independent-review.json`
  - 原本・fixture・生測定から別経路で再計算した結果
- `quality-gate.json`
  - 機械ゲート`plan / batch / complete`。`pilot`は内部工程名に限定

## 7. 最新段階案（未承認）

### Phase 0: 契約固定とplanゲート

1. 保存先、原本不変、fixtureだけを許す作業境界を固定する。
2. 正解の所在、欠損入力、対象群、比較方法、停止条件、独立検証を`quality-gate.json`へ記録する。
3. `project_quality_gate.py check ... --phase plan`がPASSするまで、fixtureを含む生成へ進まない。

### Phase 1: source / component / target-body / pose audit

1. P3全候補を孤立表示し、ビキニ上衣、白い上衣、身体、下衣、派生重複を台帳化する。
2. Helen現行Flat肌面と既存下衣を実オブジェクトで固定し、General/Bendへの差替えを負例化する。
3. 全体寸法だけでなく局所断面、カップ頂点、下乳境界、首紐・下紐の支持点を記録する。
4. 実Actionを読み、neutralと肩・腕、曲げ、ひねりを覆う代表frameを固定する。旧18姿勢は使わない。

### Phase 2: evaluator実装と診断fixture校正

1. section 0.6の正例と全負例を、成果候補でないfixtureとして作る。
2. 構造、接触、露出、変形、表示を分離測定し、各値へ取得元、scope、Action/frame、SHA、時刻を付ける。
3. donor weight直コピーを拒否し、Helen実表面由来weightだけを診断fixtureで試験する。
4. 正例を受理し、各負例を意図した理由コードで拒否できなければ内部修正する。
5. 旧Sabrina値を使わず、今回の生測定で正負例を分離できない場合は候補制作禁止のまま停止する。

### Phase 3: 独立再計算

1. 一次評価器のPASSや共通合否helperを入力にせず、別計算経路で原本・fixture・生測定を読む。
2. 三角形交差、表面距離、断面、実材質表示を相互照合する。
3. 原本SHA不変、新規時刻、正常終了、正負例分類、姿勢別再計算を別Blenderプロセスで再確認する。

### Phase 4: harness-readyで停止

- 全契約、planゲート、正負例校正、独立再計算が一致した場合のみ`harness-ready / candidate-not-started`。
- これは「次の候補を判定できる基盤ができた」という意味だけで、fit成立、納品可能、外観合格を意味しない。
- 候補制作は別の明示依頼・承認後に始める。今回の工程では候補Blendを作らない。

## 8. 実装役と独立監査役の境界

- 実装役: 入力台帳、契約、fixture、一次評価器、校正結果を作る。
- 独立監査役: 一次評価器の成功報告を根拠にせず、原本・fixture・生測定を直接読み、別経路で再計算する。
- 両者は同じ合否判定helperを共有しない。別プロセスだけで独立と呼ばない。
- 特定のモデル名・Luna/Sol配分は未承認。revision 1のモデル配分を自動採用せず、計画承認時に必要なら固定する。

## 9. Claude Codeへ貼る最新再開プロンプト

> [!important] 2026-08-28更新
> 下記が現行の再開指示。revision 1にあった「前提説明からやり直す」旧プロンプトは、方針承認前の状態を
> 前提としていたため`superseded`。今回の方針承認を巻き戻さず、実装計画の承認から再開する。

```text
/hold

次の現行引き継ぎ資料 revision 2 を、冒頭から末尾まで読んでください。
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827.md

このページの「0. 2026-08-28 Codex再開調査の確定事項」を現行checkpointとして扱ってください。
会話の記憶やrevision 1の旧プロンプトより、ディスク上のrevision 2を正本にしてください。

現在の状態は次です。
- 中断
- 「Dusevnyj衣装とHelen体型の差を直接扱う専用監査ハーネスを候補制作より先に作る」方針は承認済み
- 独立監査役を付ける方針も承認済み
- 保存先、ファイル構成、診断fixture、実装順を含む実装計画は未承認
- ハーネス、fixture、候補Blend、納品物は未作成

承認済み方針を再質問しないでください。まずディスク上の一次データと関連Wikiを読み直し、revision 2に
記録されたSHA、部品観測、Helen Flat/General/Bend、f128変形、骨名未解決、旧Sabrina流用禁止が現在も
一致するか、読み取り専用で確認してください。変化があれば、計画カードの前に差分と影響を説明してください。

次に、section 0.5〜0.6と独立監査major findingsを必須条件として、専用監査ハーネスの実装計画を
decision-complete（実装担当が追加判断を要しない状態）にしてください。最低限、次を固定してください。
1. 保存先と原本不変
2. source/component/target-body/pose/rig-strategy/evaluator/fixture/run/independent-review/quality-gateの契約
3. Flat胸を現行対象にすることと、neutral・代表Action/frameの確定方法
4. donor weight直コピー禁止とHelen実表面由来weightの診断fixture試験
5. 正例・全負例、測定scope、SHA・時刻拘束、別計算経路の独立監査
6. 旧Sabrinaの部品番号・閾値・18姿勢・838 state・PASS・bridge寸法を期待値へ流用しない機械的防止
7. ハーネス合格はcandidate-readyに限定し、fit成立・納品可能・外観合格を意味しないこと
8. 欠損肌面を創作しなければ進めない場合はblockedとし、bridgeを別approximation承認へ分離する停止条件

計画を提示したら、/holdの第2段階としてAskUserQuestionの承認カードで終えてください。
ユーザーが計画を明示承認するまで、ディレクトリ、JSON、スクリプト、Blend、Wikiを変更しないでください。
旧Sabrina案件、revision 1、Codexの未承認計画案を、現行の承認済み実装計画として扱わないでください。
```

## 10. このページ作成時に変更していないもの

- DusevnyjのBlendとintermediate
- HelenのBlend
- 両プロジェクトのJSON
- 抽出・構築・検査スクリプト
- 旧サブリナ型ビキニ案件の成果物と証拠

revision 2でも、このページは会話再開用のWiki記録だけであり、部品確定、ハーネス実装、fixture作成、
候補制作の証拠ではない。2026-08-28のCodex作業は読み取り専用調査と独立監査だけを実施した。

## 使わなかったもの・落とした情報

- **ヘレン完全原作再現の照明・ポスト処理を水着資料の完成条件から外した。**
  手元でどう変わるか: 作画資料として形状、接続、被覆、姿勢追従を優先し、原作ゲームと完全に同じ光や
  色であることは初期完成条件にしない。未確認: 実候補はまだ無いため最終的な見え方は未確認。
  戻せるか: [[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]を別案件として再開できる。
- **白い透けブラウスを採用対象から外した。**
  手元でどう変わるか: Helenはブラウスを着ず、ビキニ上衣が直接見える。未確認: 部品対応は未確定。
  戻せるか: 原本と除外台帳を保持し、別候補として再追加できる。
- **P3下衣を採用対象から外した。**
  手元でどう変わるか: Helen既存のビキニ状下衣を使い、Dusevnyj P3上下セットの再現にはならない。
  未確認: Helen下衣との最終的な上下のつながりは未確認。戻せるか: P3下衣を別候補で比較できる。
- **全モーション検査を初期完成条件から外した。**
  手元でどう変わるか: 中立＋代表姿勢以外の動きでは衣装追従を保証しない。戻せるか: 代表姿勢合格後、
  別承認で全Actionへ拡張できる。

## 関連リンク

- [[gf2-char-extract-handoff]]
- [[gf2-repro-and-swimsuit-conversation-handoff-20260827]]
- [[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]
- [[gf2-sabrina-summer-bikini-no-frill-reference-build]]
- [[gf2-sabrina-summer-bikini-center-refine-attempt]]
- [[gf2-helen-bikini-harness-loop-application-2026-08-09]]
