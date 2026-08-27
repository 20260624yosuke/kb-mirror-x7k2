---
type: build
title: Dusevnyj P3ビキニ上衣→Helen 作画資料 ハーネス設計引き継ぎ 2026-08-27
status: active
confidence: medium
evidence_level: source-backed+user-stated+inferred
created: 2026-08-27
last_reviewed: 2026-08-27
sources:
  - gf2-char-extract-handoff
  - gf2-repro-and-swimsuit-conversation-handoff-20260827
  - gf2-sabrina-summer-bikini-no-frill-reference-build
  - gf2-sabrina-summer-bikini-center-refine-attempt
  - gf2-helen-bikini-harness-loop-application-2026-08-09
related:
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
tags: [gf2, dusevnyj, helen, bikini, handoff, harness-engineering, approximation]
revision: 1
---

# Dusevnyj P3ビキニ上衣→Helen 作画資料 ハーネス設計引き継ぎ — 2026-08-27

## このページの役割

このページは、Dusevnyj SSR0101のP3衣装から**ビキニ上衣だけ**を一次データで確定し、Helen本人の
既存ビキニ状下衣を残した作画資料へ適合する、新規・高リスク案件の再開入口である。

ヘレン完全原作再現とは完成条件が異なる。目的は、絵を描くときに多方向から形状を読める資料を作ること。
照明・ポスト処理・ゲーム完全再現は主目的ではない。ただし透過処理が輪郭や布面積を変える場合は
形状問題として検査する。

> [!warning] 承認状態
> ユーザーが選んだ構成は`user-stated / selected / not-approved`として記録する。専用ハーネス、保存先、
> 部品対応、候補制作は`proposed / not-approved`であり、まだ承認・実装されていない。

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
- 候補最大2体
- `plan / pilot / batch / complete`の段階ゲート
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

## 6. 新ハーネスのファイル構成案

次は`proposed / not-approved`である。構造変更なので、次セッションで計画承認を得てから作る。
出力先と新規プロジェクトディレクトリも、その承認時に固定する。

- `project-contract.json`
  - 目的、完成条件、非対象、P3上衣＋Helen下衣、候補上限、ユーザー判断点
- `source-lock.json`
  - 原本、候補部品、除外ブラウス、Helen保持対象、各SHA
- `component-map.json`
  - P3の全成分と`adopt / exclude / unresolved`
- `rig-map.json`
  - Dusevnyj骨、Helen骨、rest変換、ウェイト移植根拠
- `render-contract.json`
  - 固定視点、表示モード、alpha診断、比較シート順
- `pose-contract.json`
  - 中立、代表姿勢群、各Action・frame、将来拡張境界
- `run-manifest.json`
  - 入力・候補・スクリプト・結果のSHAと実行時刻
- `quality-gate.json`
  - `plan / pilot / batch / complete`の状態
- 検査スクリプト群
  - source audit
  - component selection
  - preserved-object audit
  - geometry fit
  - alpha/silhouette
  - rig/weight
  - per-pose audit
  - independent review
  - return gate

## 7. 段階実行

### Phase 0: source audit

1. P3を表示したDusevnyj Blendとintermediateを直接読む。
2. P3候補をアイコン、材質、UV、位置、連結成分、可視状態で対応づける。
3. 白いブラウス、ビキニ上衣、身体、下衣、装飾を機械台帳へ分離する。
4. Helen既存下衣の対象オブジェクトを同じ方法で確定する。
5. `quality-gate plan`が通るまで候補を作らない。

### Phase 1: evaluator calibration

正例:

- Dusevnyj本人がP3を着た状態
- Helen原本の保持対象
- Helen既存下衣

既知負例:

- 白いブラウス混入
- 上衣中央接続切れ
- 左右成分の片側欠落
- Helen下衣との二重面
- 未加重頂点
- 未知骨
- UVまたは材質の欠落
- 身体内部へ移動した面
- 極端に伸ばした面
- alpha未接続
- 古いrun manifest
- 固定値を全姿勢へ複写した偽レポート

正例を受理し、対応する負例を拒否できない検査器はpilotを判定できない。

### Phase 2: neutral pilot

- 候補は別名Blendとして1体だけ作る。
- Helen原本とDusevnyj原本は変更しない。
- Helenの顔、髪、身体、シェイプキー、骨、既存下衣を保持する。
- P3上衣だけを局所fitする。
- 一様拡大で終了せず、必要なら部品構成・UV・材質を保つ局所変形へ進む。
- 中立姿勢について構造、fit、alpha、外観の4層を検査する。
- 機械検査と独立検証後、代表1〜4画像だけをユーザーへ提示する。

### Phase 3: representative poses

- 実Actionを読み、肩・腕上げ、前後屈、ひねりを実際に覆うframeを選ぶ。
- 各姿勢で交差、距離、露出、伸び、未加重、接続を再計算する。
- 中立の合格を姿勢へ流用しない。
- 失敗した姿勢群だけ局所修正し、候補は最大2体までとする。
- 代表姿勢の外観許容後に`batch`を判定する。

### Phase 4: optional full motion

- 初期完成条件には含めない。
- ユーザーが別途拡張を承認した場合だけ、対象Actionと全state数を固定する。
- 代表姿勢の許容を全モーションへ流用しない。
- 項目別の最悪frameと前後frameを抽出し、全画像監査をユーザーへ委ねない。

### Phase 5: complete

- 現行候補SHAに対して全検査を再実行する。
- 独立検証役が原本・候補・証拠を直接読む。
- ユーザーが代表外観差を許容する。
- `quality-gate complete`がPASSする。
- 完成状態は「Dusevnyj P3上衣をHelenへ適合した作画資料用近似版」とし、公式再現とは呼ばない。

## 8. モデル配分

### Luna

- P3全成分の列挙
- SHA、頂点、面、材質、UV、骨、ウェイトの台帳化
- 固定規則による差分
- 各姿勢の機械測定
- 比較シート生成
- run manifestとWiki転記

### Sol

- どの成分がビキニ上衣かの最終根拠評価
- Dusevnyj→Helenの骨・座標・ウェイト移植設計
- 正例・負例と検査器の校正設計
- 接触・貫通・正常な密着の決着
- Helen欠損身体を補う必要性と可逆案の審査
- ハーネスが形状欠陥を実際に止めるかの監査
- 最終外観差の抽出

Lunaが測定値を縮約し、Solが因果と否定試験を設計し、Lunaがその試験を再実行する。どちらのモデルも
自分の成功報告だけで完成判定しない。

## 9. 次セッションへ貼る再開プロンプト

```text
/hold

次の現行引き継ぎ資料を最初から最後まで読んでください。
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-dusevnyj-p3-bikini-to-helen-handoff-20260827.md

Plan Modeのまま、Blend、プロジェクトJSON、スクリプト、Wikiを変更しないでください。
旧サブリナ型ビキニ案件を現行の承認済み計画として扱わないでください。

今回の選択は、
- Dusevnyj P3の白い透けブラウスを使わない
- P3のビキニ上衣を使う
- Helen既存のビキニ状下衣を残す
- 初期保証は中立＋代表姿勢
- 将来の全モーション拡張可能性は残す
です。ただし、これはハーネス計画・実装の承認ではありません。

最初に、
1. 正解の所在
2. 欠けうる入力
3. 部品誤選択、二重下衣、骨・ウェイト、透過、貫通誤判定、身体創作、固定値検査、循環合格、検査肥大化の懸念
4. 旧案件から再利用できるものと流用禁止事項
5. 新ハーネスの段階ゲート
6. LunaとSolの役割境界
を説明してください。

その説明のあと、理解した前提だけを承認対象にするカードを出してください。
私が前提を承認するまで、部品対応、出力先、ハーネス構造、実装を承認済みとして扱わないでください。
```

## 10. このページ作成時に変更していないもの

- DusevnyjのBlendとintermediate
- HelenのBlend
- 両プロジェクトのJSON
- 抽出・構築・検査スクリプト
- 旧サブリナ型ビキニ案件の成果物と証拠

このページは会話再開用のWiki記録だけであり、部品確定、ハーネス実装、候補制作の証拠ではない。

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
