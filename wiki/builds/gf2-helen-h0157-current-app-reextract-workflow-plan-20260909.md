---
type: build
title: Helen H0157 — 現行アプリ全量コード再抽出から忠実Blendまでの実行計画
status: superseded
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-09-09
last_reviewed: 2026-09-09
revision: 1
implementation_started: false
superseded_by: gf2-helen-h0157-current-app-reextract-workflow-plan-v2-20260910
supersedes:
  - gf2-helen-h0157-frontier-cheap-agent-workflow-plan-20260909
first_task_contract: gf2-helen-h0157-current-app-reextract-task-contract-20260909
user_view: wiki/_attachments/project-hub-index/20260909-h0157-current-app-reextract-plan.html
---

# Helen H0157 — 現行アプリ全量コード再抽出から忠実Blendまでの実行計画

> [!warning] 2026-09-10にrevision 2へ置換
> 本版はアプリ全体5,326件ではなくAssetBundles 4,513件を全量分母にしていたこと、file/object分母の混在、quality-gate接続、parser/edge/coverage/test/canonical化/write-scopeの不足が独立レビューで判明した。現行正本は [[gf2-helen-h0157-current-app-reextract-workflow-plan-v2-20260910]]。

## 0. 固定済みの決定

2026-09-09 の会話で次を固定した。

| decision_id | 決定 | 状態 |
|---|---|---|
| D1 | 完成条件は H0157 の忠実再現。原作入力で裏づけられない近似を完成へ含めない | user-selected |
| D2 | 現在読めるドルフロ2アプリからコードを全量抽出し、その後に H0157 だけを分析する | user-selected |
| D3 | Time Machine 探索はユーザー要件ではなく、既定経路にしない | user-corrected |
| D4 | 特定の欠損bundleを見つけること自体をゴールにしない | user-corrected |
| D5 | 憶測で結論する直前に、結果を変える分岐だけ選択肢として提示する | user-required |

この計画は計画作成までであり、コード抽出、Blend変更、前面GUI操作、ネット取得をまだ開始していない。

## 1. 高リスク品質ゲート

### 1.1 正解の所在

- 現在読める原作アプリ本体、同梱データ、そこから再抽出したコードと値。
- H0157 に対応する原作メッシュ、モーション、材質、シェーダ、照明、ポスト処理の参照関係。
- 原本 Blend と候補 Blend の直接差分。
- 最終的な見た目の受入れは武田さんの判断。

### 1.2 欠けうる入力

- 現行アプリ 4,513 regular files を現在の抽出器で全処理した再抽出結果。
- 抽出コードから H0157 の実行条件へ接続する参照鎖。
- 原作の値を Blender のどの値へ移すかを示す対応表。
- 現行アプリに必要コードが無かった場合の取得経路。これは全量再抽出後まで未選択とする。

特定の2本のbundle、旧cache、Time Machineを「必須入力」とは定義しない。必要性が直接証明された場合だけ、分岐候補へ戻す。

### 1.3 性質の違う対象群

| family_id | 対象 | 混ぜてはいけない判定 |
|---|---|---|
| mesh-static | 全身形状、骨、ウェイト、シェイプキー | 動き・陰影の合格を形状合格へ流用しない |
| motion-h0157 | H0157 の300フレーム | 静止画や別アクションで代替しない |
| shading | 材質、Ramp、顔陰影、照明、ポスト処理 | 内部数値だけで見た目合格にしない |
| hair-s8 | 髪メッシュ、透過、被覆 | shading 単独または mesh 単独へ先に固定しない |
| artifact | 候補Blendと来歴 | 別SHAのBlendへの検証・承認を流用しない |

### 1.4 代表例

H0157 一件だけ。別アクション、別衣装、水着資料へ展開しない。

### 1.5 原作比較方法

1. 抽出物は `source_path + source_sha256 + source_offset + extractor_sha256 + output_sha256` で原作へ戻れるようにする。
2. H0157候補は、抽出コード内の参照元と参照先を両方保存する。
3. Blender変更は `原作値 -> 変換式 -> Blender対象 -> 候補SHA` の対応を保存する。
4. メッシュ、骨、材質、ノード、キー、300フレームを機械比較する。
5. 機械比較後に、原作との差を武田さんへ提示して見た目を受け入れるか確認する。

### 1.6 停止条件

- 入力SHAが実行中に変わった。
- 全量の分母と `処理済み + 失敗` が一致しない。
- 陽性対照が検出できない。
- 抽出器の再実行で出力SHAが変わる。
- 原作コードとH0157の接続が候補文字列だけで、参照関係を示せない。
- 原作値からBlender値への変換が定義できない。
- 廉価モデルが意味解釈、候補採用、完成判断を必要とする。
- 忠実版に未承認の推定または近似が混ざる。

## 2. 2026-09-09 に直接確認した入力

| input_id | 実体 | SHA-256 / 件数 | 用途 |
|---|---|---|---|
| app-main | `.../SnqxExilium.app/SnqxExilium` | `025aaafc9c49b6ba37b37678df01d67a7a408d41bb48bbfba0008f34412881b8` | アプリ入口 |
| unity-framework | `.../Frameworks/UnityFramework.framework/UnityFramework` | `8be85a1c692b741be3619eba40b006c3133c2ff8a137030c34090650e75b5c4d` | IL2CPP/AOT入力 |
| global-metadata | `.../Data/Managed/Metadata/global-metadata.dat` | `10609117460b9375c6a9d768f3a489120ac5f4312597c12a99b2df597512a197` | IL2CPP metadata入力 |
| app-assets | `.../Data/Raw/AssetBundles_IOS` | root直下4,510 files、再帰4,513 regular files | bundle・catalog・コード資産入力 |
| positive-bundle | `01fc2bee1d07da85dcfee3f3caf0f766.bundle` | app側で実在、211,426 bytes | `_FilmWhiteClip` 4件、`_Lut_Params` 322件の既知対照 |
| current-blend | `06_repro-v51/blends/helen-h0157-repro.blend` | `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` | 読み取り専用親Blend |
| extracted-msl | `06_repro-v51/ledger/shader-source/` | fragment 144、post 362、計506 files | 抽出器の照合対象。現行全量とはみなさない |

旧 `f166-code-inventory.json` は 2026-08-26 22:42:48 に生成され、`f166_code_inventory.py` は同日22:49:42に変更された。旧結果を現行抽出結果として採用せず、陽性対照と形式知だけ再利用する。

## 3. ゴールではないもの

- Time Machine から旧cacheを見つけること。
- `d128...bundle` または `7648...bundle` という特定ファイルを見つけること。
- GFF末尾の二進部分を解読すること。
- 7,424件を意味解析すること。
- 監査の合格件数を増やすこと。
- Muse由来 `f195-opencheck.blend` を根拠なしで正規候補へ昇格すること。

これらは H0157 の必要経路へ直接接続した場合だけ作業候補になる。

## 4. 役割

### 4.1 廉価モデル実装役

担当:

- 入力目録、SHA、件数、形式分類。
- 現行アプリからの全量コード抽出。
- 抽出器、索引器、値変換器、Blender適用スクリプトの実装。
- 陽性・陰性対照、単一変異、再現性試験。
- H0157検索語と参照辺の列挙。
- 固定済み変換契約に従う候補Blend生成。

禁止:

- 何が原作の正解かを決める。
- 候補文字列をH0157接続の証明にする。
- 不検出を不存在にする。
- 新しい取得経路へ勝手に広げる。
- 自分の出力を正本、忠実、完成、採用済みにする。

### 4.2 フロンティア主担当

担当:

- 抽出結果と原文コードを直接比較する。
- H0157への参照鎖を採用または棄却する。
- 原作値からBlender値への対応を決める。
- 見た目が異なる複数枝を武田さんへ選択肢として提示する。
- 変更契約と完成判定を作る。

全量抽出、全件SHA、候補一覧の転記を自分では行わない。

### 4.3 独立検証役

- 実装者の説明ではなく、入力、コード、抽出物、Blendを直接読む。
- SHA、分母、対照、再現性、参照辺、変更範囲を再実行する。
- 意味判断が必要な昇格だけフロンティア級を使う。件数・SHA・再実行確認は機械検査または廉価モデルへ渡す。

## 5. 実行工程

| phase | 主担当 | 入力 | 出力 | 次へ進む肯定証拠 |
|---|---|---|---|---|
| R0 input-freeze | 廉価 | 現行アプリ、原本Blend | `input-manifest.json` | 全入力のpath/size/mtime/SHA、分母確定 |
| R1 full-extract | 廉価 | R0固定入力 | raw抽出物、`extraction-index.jsonl`、`failures.jsonl` | 全量が処理済みか失敗へ分類 |
| R2 extraction-verify | 機械＋廉価別実行 | R1出力 | `verification.json` | 陽性対照、変異、復元、再実行同値が全PASS |
| R3 h0157-index | 廉価 | 検証済み全量コード | `h0157-candidates.jsonl` | 各候補にsource/offset/参照辺がある |
| R4 causal-review | フロンティア＋独立検証 | R3候補と原文 | `causal-map.json` | family別に原作値まで参照鎖が閉じる |
| R5 value-extract | 廉価 | 採用済みcausal map | mesh/motion/material/shading JSON | 値、単位、座標系、出所SHAが固定 |
| R6 blend-candidate | 廉価 | R5、親Blend、変更契約 | 新規候補Blend、差分、画像、receipt | 親Blend不変、契約内変更、非対象回帰PASS |
| R7 acceptance | 独立検証＋武田さん | 同一候補SHA | 比較報告、受入記録 | 全family検証＋見た目受入 |

## 6. R1 全量再抽出の対象

R1は「全部を意味解析」ではなく「現在のアプリにあるコードらしいデータを、来歴付きで取り出す」工程である。

| class_id | 抽出対象 | 必須来歴 |
|---|---|---|
| native-il2cpp | UnityFrameworkとglobal metadataから取得可能なimage/type/method/string/call情報 | binary SHA、metadata SHA、VA/file offset |
| managed-pe | PEとして読めるDLL、HybridCLR用コード資産 | 元bundle、object/path、offset、output SHA |
| shader-msl | Metal MSL本文、stage、entry point、uniform/texture名 | 元bundle、blob offset、圧縮形式、output SHA |
| shaderlab | ShaderLabまたはserialized Shader情報 | 元bundle、object ID、name、output SHA |
| script-bytecode | Lua等、現在のapp側に実在するスクリプト資産 | 元bundle、形式、解析成否、output SHA |
| unknown-code | codeらしいが既知分類に入らないbytes | 元位置、magic、entropy、失敗理由 |

同一内容はSHAで重複排除するが、全ての出現元を `origins[]` に残す。

## 7. R3 H0157候補索引

最低限、次の既知語と参照元を全量コードへ照合する。

```text
H0157
HelenSSR0101
c_HelenDorm_Bedroom_0101
LoadRoomById
GFCharForward
GFCharFaceShadow
GFCharHairTransE
GFHairShadow
_RampMap
RampSetting
_Lut_Params
_FilmWhiteClip
```

各候補行の必須項目:

```yaml
candidate_id: stable-id
family_id: mesh-static | motion-h0157 | shading | hair-s8 | unknown
source_path: absolute path
source_sha256: sha256
source_offset: integer or structured object id
matched_term: exact observed term
incoming_refs: observed references only
outgoing_refs: observed references only
positive_control_id: control id
interpretation: null
```

`interpretation` は廉価モデル段階では必ず `null`。意味づけはR4で原文を読み直して追加する。

## 8. R4で作るH0157対応表

| family | 原作側で閉じる参照 | Blender側の対象 |
|---|---|---|
| mesh-static | prefab/renderer/mesh/bone/weight/shape参照 | object、mesh、armature、vertex group、shape key |
| motion-h0157 | H0157 clip/timelineから300フレームのbone/shape/root値 | action、FCurve、keyframe、root transform |
| shading | renderer/submesh/material/Ramp/face/post/lighting | material slot、node、texture、color management、compositor |
| hair-s8 | hair meshと透過・深度・被覆処理 | hair object、alpha、material、render order相当 |

文字列の共起、同一ファイル内存在、名前の類似だけでは参照鎖を閉じない。参照ID、call、object link、serialized pointerのいずれかを直接示す。

## 9. R6 Blender候補契約

候補生成前に次を固定する。

```yaml
parent_blend_sha256: 04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5
mode: faithful
candidate_path: new file only
allowed_objects: explicit list from R4
allowed_properties: explicit list from R4
expected_visible_change: family-specific statement
non_regression_objects: explicit list
source_values: R5 evidence ids
rollback: discard candidate; parent remains byte-identical
```

H0157の300フレーム、全身形状、胸、顔、髪、材質、陰影を同じ候補SHAで検査する。一部のPASSを完成へ昇格しない。

## 10. 分岐規則

### B1 抽出検査がFAIL

- ユーザーへ選択を求めない。
- 入力、抽出器、対照のどこが壊れたかを機械的に切り分け、同じR工程で修正する。
- FAILを「コードが無い」と解釈しない。

### B2 H0157参照鎖が一意に閉じる

- ユーザーへ選択を求めずR5へ進む。
- 一意性、反証、原文位置を独立検証する。

### B3 複数候補でBlenderの見た目が変わる

- 推論前に武田さんへカードを出す。
- 各候補について、直接証拠、残る未知、選ぶと捨てる見た目、戻し方を示す。

### B4 現行アプリ全量に必要参照がない

- 「無い」と断定せず、処理分母、陽性対照、未処理形式を提示する。
- 次の3択をカードで出す: 現行アプリ内の未処理形式を深掘り、実行中ゲーム/ネットから取得、忠実版をblockedで維持。
- Time Machineは武田さんが明示的に選んだ場合だけ候補へ入れる。

### B5 機械検査PASS後の見た目差

- LLMが差を先に列挙する。
- 同じ候補SHAを提示し、武田さんが受入れまたは差戻しを選ぶ。

## 11. ハルシネーションを成果物へ入れない条件

- 廉価モデルの自然文成功報告を状態遷移証拠にしない。
- `source_path + offset + SHA` の無い抽出結果をR4へ渡さない。
- 陽性対照FAIL時の0件を採用しない。
- 候補の意味づけを、抽出した本人の説明から採用しない。
- 変換式の入力単位、座標系、軸、フレーム番号が空ならBlendを生成しない。
- 原本Blendの実SHAを候補生成前後に再測定する。
- 検証した候補と提示した候補のSHAが違えば受入れへ進めない。

## 12. 最初の実行単位

正規の最初の作業は [[gf2-helen-h0157-current-app-reextract-task-contract-20260909]] のR0〜R3である。

- 廉価モデルが現行アプリを読み取り、隔離出力へ全量コードを再抽出する。
- 原本Blend、Wiki正本、アプリ本体はread-only。
- R0〜R3はBlenderの見た目を変えない。
- R3の検証済み候補一覧ができるまで、GFF、特定bundle、Time Machine、F12を次工程へ固定しない。

## 13. 現在状態

| item | state |
|---|---|
| ユーザー分岐 D1〜D5 | fixed |
| 本計画 | proposed |
| 最初の廉価モデル用契約 | drafted |
| 独立レビュー | not-run |
| R0〜R3実装 | not-started |
| Blend変更 | none |
| 原本Blend | SHA `04ef8b79...d7f5`、未変更 |

## 14. 使わなかったもの・落とした情報

### Time Machineを既定の次工程にする案

- 捨てたもの: 消失前cacheをTime Machineから探す工程。
- 手元でどう変わるか: バックアップ探索をせず、現在読めるドルフロ2本体の全量コード再抽出から始める。Blendの見た目はまだ変わらない。
- 戻せるか: 現行アプリ全量に必要参照がなく、武田さんが選択した場合だけ再開できる。

### 特定2本のbundleを必須入力にする案

- 捨てたもの: `d128...bundle` と `7648...bundle` の発見を開始条件にすること。
- 手元でどう変わるか: ファイル探しではなく、原作コードからH0157参照鎖を取り直す。Blendの見た目はまだ変わらない。
- 戻せるか: R4でそのbundleが必須だと直接証明された場合だけ取得候補へ戻す。

### F0をフロンティア級だけで行う旧案

- 捨てたもの: 入力目録、SHA、全量抽出をフロンティア級が行う配分。
- 手元でどう変わるか: 廉価モデルが機械作業を担当し、フロンティア級はH0157への意味接続だけを読む。Blendの見た目はまだ変わらない。
- 戻せるか: 廉価モデルの検査が成立しない場合でも、まず抽出器を修正する。意味判断だけは最初からフロンティア級に残る。

## 15. 根拠

- [[helen-h0157-new-agent-entry-20260909]]
- [[gf2-helen-deliverable-unified-route-plan-20260831]]
- [[gf2-helen-repro-v51-run]]
- [[gf2-helen-repro-v51-handoff]]
- `06_repro-v51/logs/f166-code-inventory.json`
- `06_repro-v51/logs/f166-msl-per-bundle.json`
- `06_repro-v51/logs/f198-gff-unopened-accounting.json`
- `06_repro-v51/logs/f200-cache-side-availability.json`
- `06_repro-v51/logs/f201-backup-volume-rescan.json`

## 矛盾・未確定

- 現行アプリだけを対象にした全量再抽出は未実行であり、抽出可能なコード総数は未確定。
- H0157へ接続する参照鎖はR4前なので未確定。
- 現行アプリに必要参照が無い場合の取得経路は、B4まで未選択。
- 本計画と最初の作業契約は独立レビュー未実施。
- 実装・実機・Blender見た目は未試験。
