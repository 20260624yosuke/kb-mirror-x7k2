---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-25
sources:
  - gf2-helen-repro-v51-run
---

# キャラ再現パイプライン — コードから他キャラを抽出する導線

H0157(Helen)で検証済みの手法を、他キャラ（H0167等）に横展開するための手順書。
**すべて 2026-08-24〜25 の実測に基づく**（推測箇所には明記）。

## 現在の統合見解

- キャラ固有データは **カタログ（catalog_main_*.bin）→ bundle → UnityPy** の経路で抽出できる。
- シェーダ（GFCharForward/0357.msl）は**キャラ間で共通**のため、抽出は一回でよい（済）。
- 材質構造は f125 の実装を流用し、キャラ固有の入力（テクスチャ・RampMap・SH）を差し替える。
- 検証は f123 監査（原作フレームとの色差＋部位整合＋白飛び）で機械判定する。

## 根拠

- シェーダ実抽出: `ledger/shader-source/fragment/GFCharForward/0357.msl`（790行・f40）
- RampMap 16行抽出: `intermediate/rampmaps-rows-all.json`（f125a・11本×256×16）
- 材質構造の実装と監査PASS: `scripts/f125_shader_repro.py` / `f126_calibrate.py`
  （肌差5.7/髪差5.3/ドレス差8.0/白飛び0%・2026-08-25実測）

## パイプライン全容

### Phase 0: キャラデータの所在特定

| 手順 | 使う道具 | 出力 |
|---|---|---|
| カタログ全レコード走査 | `f79_h0157_primary_lighting.py` の `Catalog` クラス（cache26111/app21899） | key・internal_id・deps |
| キャラ名/IDで検索 | f113 のフィルタ方式（internal_id にキャラ名） | 該当bundle一覧 |
| 依存関係の実在確認 | f113 の dep_presence 方式 | 実在/欠損リスト |

- カタログは **cache版(26111)とapp版(21899)で内容が違う**（cache 25.2万件/app 5万件）。
- bundle名はディスク名(32hex)と `AssetBundle.m_Name`(別の32hex)の**2つの名前空間**がある。
  CAB = md5(m_Name)（f58実績・f116で依存グラフ構築済み）。
- 欠損bundleがあっても部品は揃う場合がある（寮: scene root欠損でも probe・lightmap・
  cubemap・LUT・post設定は実在 = f98/f99/E1/f112）。

### Phase 1: 一次データ抽出

| データ | 道具 | 出力 | 状態(Helen) |
|---|---|---|---|
| テクスチャ(BaseMap/RMO/Detail) | d02 | intermediate/texture-assignment.json | 済 |
| メッシュ | d02系 | intermediate/meshes-manifest.json | 済 |
| RampMap **全16行** | **f125a** | intermediate/rampmaps-rows-all.json | 済 |
| Ramp設定↔テクスチャ対応 | d05/f67 | intermediate/rampmaps.json | 済 |
| ランプ割当ルール | d03 `ramp_for`（メッシュ接頭辞優先） | 材質ごとの gf_ramp | 済 |
| LightProbes(部屋) | f99/f112 deep | ledger/h0157-dorm-lightprobes-primary.json | 済 |
| シェーダソース | f40 | ledger/shader-source/fragment/GFCharForward/*.msl | 済(**共通**) |
| Lightmap/ReflectionCube/LUT | E1 | logs/e1-baked-lighting.json + reports/lighting-extract/ | 済 |
| 実機フレーム(比較用) | 手動スクショ | reports/original-frames/ | キャラごとに要取得 |

### Phase 2: blend構築（f125構造）

材質の出力は **0357.msl の diffuse+SH 経路をそのままノード化**する
（`scripts/f125_shader_repro.py` の BUILD_INNER を流用・パラメタ差し替え）:

```
Emission = albedo(素テクスチャ色) × RampMap(U=NdotL, V=0.125行) × MainLightColor(仮白)
         + albedo × SH(N)[L0+L1] × RMO.b
```

- ランプは **row2 の 256サンプルを 32停止点へ圧縮した ColorRamp**（誤差<1e-4・d05流式）。
  **画像テクスチャは使わない**（生成画像はヘッドレス保存でデータ消失する・下記落とし穴参照）。
- SH係数は部屋のprobeから tetra補間（f117）か平均（f103）。係数は **L0+L1 のみ使用**
  （unity_SHAr/SHAg/SHAb = 4係数/チャンネル）。
- ランプ割当は d03 `ramp_for`（メッシュ名と同じ接頭辞のランプを優先）。
  **未回収ランプの部位は近い家族の実測ランプを流用し「近似」と記録する**
  （Helenでは顔/P1体→P3_body流用）。
- 構築後、**旧実装の未接続ノードをプルーニング**する（f125 BUILD_INNER 内の到達可能性
  判定部分）。残っていると監査の is_default 判定が誤発火する。

### Phase 3: 検証（機械監査）

1. `f123_quality_audit.py --blend X --render BED_TOP標準カメラレンダ`
   - A 部位整合: 肌材質にデフォルト黒白ランプが残っていない/RampMap割当済み
   - B 原作接近: 原作フレームROI（肌・髪・ドレス）との色差（閾値: 肌<45/髪<55）
   - C 白飛び率 <1.5%
2. `f46_visual_env_gate.py record && check`（環境記録の鮮度門）
3. **レンダ入力は BED_TOP 標準カメラに限定**（別カメラ画像を入れるとROI統計が枠外に
   なり誤FAILする・2026-08-25実事故）
4. ROI座標は色マスク密度マップ(8×16)で機械特定→クロップ実物を目視確認
   （推測座標は3回連続で外れた）

### グリッド較準（未知入力のフィッティング）

主光の向き・SH量は原作実値がblockedのため、**原作フレームを正解にグリッド探索**する
（`f126_calibrate.py` の方式: 方位4×仰角2×SH量3を f123 スコアで機械選択）。
構造は変更しない（入力値のフィットのみ）。

## 既知のblocked項目（Helen時点・他キャラ共通）

| 項目 | 阻まれている理由 |
|---|---|
| scene root / prefab root | 寮sceneの依存bundle 102本が欠損・回収ルートは将来の配信待ちのみ(#57) |
| 材質プロパティ(_AnisotropicGXX/_FinalTint/_Glitter*等) | prefab rootの材質設定依存 → スペキュラ/Glitter/V=0.625項が未実装 |
| _MainLightColor | 実値0件（dependency_inventory.direct_light_components_in_present=0） |
| fog(_GFFogColor) | scene root依存 |
| FaceSDF(_BlendTex) | face bundle 73836294… の全10テクスチャを列挙したが非在（#63） |
| 追加灯の実値 | 未回収（構造は V=0.875 で実装可能・値は投入しない） |

## Blender実装上の落とし穴（2026-08-25の事故記録・他キャラ展開時に再発防止）

1. **ソケットに `from_node` 属性は無い**: `sock.links[0].from_node` が正しい。
   `hasattr(sock,"from_node")` は False を返し silently 不発する（f125で実害）。
2. **生成画像はヘッドレス保存でデータが消失する**: `bpy.data.images.new()` +
   `pixels.foreach_set()` + `save_as_mainfile()` → レンダ時に黒サンプリング
   （`Image does not have any image data`）。EXR保存→再ロードも試したが失敗。
   **ColorRamp の停止点(32点・誤差<1e-4)で実装するのが正解**。
3. **旧実装の未接続ノードは削除する**: 残っていると監査の is_default 判定が
   古いノードを拾って誤FAILする（f125で実害）。到達可能性判定でプルーニング。
4. **Mix ノードの RGBA入力は index 6(A)/7(B)**: data_type='RGBA' のとき。
   Factor が 1.0 なら MULTIPLY は A×B。
5. **監査のレンダ入力カメラを固定する**: 標準カメラ(BED_TOP)以外の画像を
   監査に入れるとROI統計が崩れて誤FAIL（f127で実害）。
6. **長期非表示オブジェクトの `matrix_world` が identity に上書きされている罠**
   （f135で発見）: 過去の工程で det=+1 になり、**レンダに一切出ず・armature変形の
   空間も崩れる**。表示を切り替える前に `matrix_world.determinant()` が -1 か全
   メッシュで点検し、壊れていれば `matrix_world = arm.matrix_world` を直接設定する
   （親チェーン再計算では直らないケースがある）。
7. **GFOutline の「Material Index」入力は face全消しスイッチ**: 全face の
   material_index が 0 のメッシュ（P2/P3系）で入力 0 のままだと base面が消え
   断片だけ残る。99 を入れて無効化する（f135で発見）。
8. **MapUV は新コンポジタで二値出力に崩れる**（4.5実測）: blend内での3D LUT適用は
   不能。postグレードはレンダPNGへの numpy 適用（`post_grade.py`）か、
   適用しないかのどちらか。多項式近似も誤差12-21%で不採用（f130/f131で実証）。

## Helen提出（2026-08-25）で得た教訓 — 他キャラ制作時にそのまま適用する

### ルールとして守ること（守らないと拒否される）

- **服装は計画のDRESS節のルール**。原作フレームの目視から「原作はこちらの服」と
  表示を差し替えるのは**憶測**であり拒否される（f135→f137で実害）。
  計画に「どの衣装を初期表示するか」が無いキャラでは、着手前にDRESS節を新設し
  武田さんの決定を取る。
- **P1/P2/P3 は胸の変種ではなく衣装パーツセット**（P1=素肌・P2=ストッキング・
  P3=パーツ）。`Item_ClothesMod_*` アイコン画像と照合すると見分けがつく。
  胸3変種は別物（cloth2 の Flat/General/Bend）。
- **blocked の値を白仮置きのまま提出しない**: MainLightColor 等は原作フレームとの
  機械較準で置き換える（f135方式: 候補群→階調距離最小を選択・f126と同方式）。
  Helen では s=4.0 で S4のっぺり警告が消えた（shadow_mass比 4.93→1.03）。
- **clipの表情fcurveを最初に実測する**: 目が開くフレームが存在するか、どの
  シェイプキーが固定値か（Helen寝室は Eyes_Close_Down=1.0 固定）。比較が
  発生しない項目を先に特定する。

### 手順として引き継ぐこと

- **顔は体と別の灯方向で較準する**: 原作シェーダには
  `_FaceLightDirAdjustment`（顔専用の灯方向補正）が実在する（0357.msl:154・
  キャラ共通のuberシェーダ）。体と同じ方向だと眼窩・鼻周りのスムース法線の
  くぼみが ramp 暗部に乗り「シワ状の汚れ・瞳周りの濁り」になる（Helenで実害）。
  顔材質4種（face/eye/eyeblend/shetou）の灯位置定数だけを候補群
  （el55-85×az）から原作顔フレームとの比較で機械較準する
  （`f137_face_light_calibrate.py`・Helenでは el75/az-35 を採用）。
  採用値は材質プロパティ `face_light_dir_adjustment` に記録する。
- **新規表示メッシュは必ず f129系の純データLBS診断をしてから監査に掛ける**:
  ストレッチが検出されたら (a)抽出ミスか (b)原作clip由来のLBS伸びかを
  純データ再計算 vs blend評価（≤1mm）で仕分けし、(b)なら EXPLAINED_STRETCH へ
  証拠付きで登録（S3bガード付き・退行は即欠陥検知）。
- **ramp暗部の挙動を知っておく**: U<0.035 で乗算率 0.14（ほぼ黒）。ポーズと
  灯方向の組合せで脚などが真っ黒に見えるのはコードどおりの挙動であり、
  原作も同じデータを同じLBSで描けば同じ暗になり得る（f129で実証済みの考え方）。
- **f128監査をキャラごとに複製する際の固有箇所**: EXPLAINED_STRETCH（証拠文つき）・
  DEFECT dict（既知欠陥メッシュと最悪フレーム）・CAM/RES・visibility-decision の
  参照。汎用部（S1シェーダ契約・S3b仕組み・自己試験T1-T7の構造）はそのまま流用。
- **post24/LUT は部屋プロファイル由来の可能性が高い**: Helen寮では lounge候補
  プロファイルで、寝室カットへの join が未回収（#69）。適用して原作比が悪化する
  なら適用しない、が正解。式（LutBuilderHdr/UberPost）は
  `ledger/shader-source/post/` に確保済み。

### スクリプト在庫への追加（2026-08-25時点）

| スクリプト | 役割 | 再利用性 |
|---|---|---|
| `f128_quality_audit_v2.py` | 強化機械監査（S1/S2/S2b/S3/S3b/S4+自己試験7件） | Phase 3（キャラ固有箇所だけ差し替え） |
| `f129_motion_fidelity.py` | 変形の原作データ忠実度診断 | Phase 3（新規表示メッシュのたびに） |
| `f135_visibility_lightcal.py` | 可視性+MainLightColor較準 | Phase 2/3 |
| `f137_face_light_calibrate.py` | 顔灯方向補正の較準 | Phase 2/3（顔のあるキャラ全般） |
| `post_grade.py` | post24実値のレンダPNG適用 | Phase 3（採用時のみ） |

## スクリプト在庫（再利用可能な順）

| スクリプト | 役割 | 再利用性 |
|---|---|---|
| `scripts/common.py` | パス定数・UnityPyブートストラップ・SHA等 | 全工程 |
| `f79_...py` の `Catalog` | カタログパーサ | Phase 0 |
| `f113_dorm_scene_catalog_map.py` | カタログ全レコード走査の方式 | Phase 0 |
| `f125a_extract_ramp_rows.py` | RampMap全16行抽出 | Phase 1 |
| `d02/d03/d04/d05` | テクスチャ・材質・ランプの抽出基盤 | Phase 1 |
| `f40_extract_fragment_shader.py` | シェーダソース抽出 | Phase 1（共通・済） |
| `f99/f112 deep` | LightProbes抽出 | Phase 1（部屋ごと） |
| `f125_shader_repro.py` BUILD_INNER | 原作コード構造の材質構築 | Phase 2 |
| `f117_tetra_true_sh.py` | tetra真値SH補間 | Phase 2 |
| `f123_quality_audit.py` | 機械監査 | Phase 3 |
| `f126_calibrate.py` | グリッド較準 | Phase 3 |
| `f46_visual_env_gate.py` | 環境記録の鮮度門 | Phase 3 |

## 矛盾・未確定

- 追加灯（V=0.875）の構造は判明しているが、原作の実値が未回収のため投入していない。
- SH係数のキャラ位置仮定（t=1.0）は tetra真値補間で置き換え可能（f117）だが、
  位置自体は scene root 回収まで不確定。
- ReflectionProbe は寮固有 cubemap の特定が未完了（E1抽出688個は他シーン分を含む）。

## 関連リンク

- [[gf2-helen-repro-v51-handoff]]（経緯の正本・#58〜#65）
- [[gf2-helen-repro-v51-run]]（実行記録の正本）
- 実装スクリプト: `06_repro-v51/scripts/`（f79/f98〜f126）
