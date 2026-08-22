---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-10
sources: []
---

# ヘレン SSR0101 観測台帳（計画 v5.1 の OBS 全件 + 再現確認）

承認済み計画 `mellow-questing-elephant-v5.1.md` の **OBS 節に書かれた全観測**と、
工程Aで保存済みスクリプトから**再測定した値**を並べたもの。実行記録の正本は [[gf2-helen-repro-v51-run]]。

> [!note] このページの読み方
> - 「計画の記述」は 2026-08-10 時点の計画 v5.1 の主張をそのまま写したもの。
> - 「再測定」は今回この環境で実際にコマンドを実行して得た値。スクリプトは `06_repro-v51/scripts/a0*.py` に保存してある。
> - 不一致は旧値を黙って直さず、新値を自動的に正しいともしない（計画「重要OBS再測定ルール」）。

## 現在の統合見解

- 照合した項目は **96 件**。うち **一致 94 / 不一致 2**。
- 不一致は **OBS9 のみ**。OBS9 はどの GATE にも使われていないため、後続の判定には使わない。
- 入力は `06_repro-v51/ledger/materials.json` に SHA-256 で固定してある。

## 根拠 — OBS 全件

### OBS1

**計画の記述**: ゲームキャッシュ .bundle = 9035件。台帳 inventory-v2.sqlite=9031件との差分4件は Sprite と Texture2D のみで Mesh/GameObject/Helen 名は0件

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| キャッシュ .bundle 件数 | `9035` | `9035` | 一致 |

### OBS2

**計画の記述**: アプリ内蔵バンドル = 4504件（読めなかった1件）。キャッシュとはファイル名が1187件重複し、完全に別のセットではない。カタログは app=catalog_main_21899 / キャッシュ=catalog_main_26111

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| アプリ内蔵 .bundle 件数 | `4504` | `4504` | 一致 |
| アプリ内蔵で読めなかった件数 | `1` | `1` | 一致 |

### OBS3

**計画の記述**: HelenSSR0101 を含むバンドル: キャッシュ16件 / アプリ内蔵 0件

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| HelenSSR0101 を含むキャッシュ側バンドル数 | `16` | `16` | 一致 |
| HelenSSR0101 を含むアプリ内蔵バンドル数 | `0` | `0` | 一致 |

### OBS4

**計画の記述**: 胸メッシュ3変種の頂点数 General=5589 Flat=5525 Bend=5521。3つともブレンドシェイプ0個

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| General 頂点数 | `5589` | `5589` | 一致 |
| Flat 頂点数 | `5525` | `5525` | 一致 |
| Bend 頂点数 | `5521` | `5521` | 一致 |
| 3変種のブレンドシェイプ数 | `[0, 0, 0]` | `[0, 0, 0]` | 一致 |

### OBS5

**計画の記述**: LOD3段(lod0/lod1/lodm0)すべてに3変種が存在

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| LOD3段すべてに3変種があるか | `9` | `9` | 一致 |

### OBS6

**計画の記述**: `_Bend/_Flat/_General` 形式のメッシュ名を持つのは HelenSSR0101 のみ（4バンドル・15出現）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 変種メッシュ名を持つバンドル数 | `4` | `4` | 一致 |
| 変種メッシュ名の出現数（生） | `15` | `15` | 一致 |

### OBS7

**計画の記述**: ヘレンの全76メッシュのうち、H0157 の path_lookup で名前が解決できる胸8本を使うのは General の lod0/lod1/lodm0 の3つだけ

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| General の骨で名前が解決できる本数 | `8` | `8` | 一致 |

### OBS8

**計画の記述**: 3変種の骨ハッシュの重なりは0。Bend/Flat の骨名はこの方法では 0/29・0/27 で解決できない

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| Bend の骨で名前が解決できる本数 | `0` | `0` | 一致 |
| Flat の骨で名前が解決できる本数 | `0` | `0` | 一致 |
| 3変種の骨ハッシュの重なり | `[0, 0, 0]` | `[0, 0, 0]` | 一致 |

### OBS9

**計画の記述**: 3変種の骨の位置（bindpose を逆行列に戻して算出）は Bend↔Flat で最大12.1cm、Flat は約19.1度傾く

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| Bend↔Flat 骨位置の最大距離(cm) | `12.1` | `15.7` | **不一致** |
| Flat の傾き(度) | `19.1` | `180.0` | **不一致** |

### OBS10

**計画の記述**: サブメッシュの並び順は変種間で対応しない（General sub0=1839頂点 / Flat sub0=1907頂点）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| General sub0 頂点数 | `1839` | `1839` | 一致 |
| Flat sub0 頂点数 | `1907` | `1907` | 一致 |

### OBS11

**計画の記述**: H0157(300f) `Chest_L`/`Chest_R` の scale = 全フレーム0.01の定数。子6本は1.0の定数。回転は300フレーム可変で最大23.8度

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| H0157 Chest_L スケールが全フレーム0.01か | `True` | `True` | 一致 |
| H0157 胸の子6本が1.0か | `True` | `True` | 一致 |
| H0157 胸の骨の回転 最大(度・小数1桁) | `23.8` | `23.8` | 一致 |

### OBS12

**計画の記述**: H0157 各変種の骨スケール: General 19本0.01/6本1.0、Bend 21本0.01/8本1.0、Flat 27本すべて1.0

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| H0157 General 0.01/1.0 の本数 | `[19, 6]` | `[19, 6]` | 一致 |
| H0157 Bend 0.01/1.0 の本数 | `[21, 8]` | `[21, 8]` | 一致 |
| H0157 Flat 0.01/1.0 の本数 | `[0, 27]` | `[0, 27]` | 一致 |

### OBS13

**計画の記述**: H0167(1341f) frame383で1.0→0.01、frame665で0.01→1.0。282フレーム分。1フレームで切替・補間なし

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| H0167 フレーム数 | `1341` | `1341` | 一致 |
| H0167 切替フレーム | `[383, 665]` | `[383, 665]` | 一致 |
| H0167 中間区間のフレーム数 | `282` | `282` | 一致 |

### OBS14

**計画の記述**: 保存済み15クリップ中、General が0.01なのは H0157/H0158/H0165/H0166。途中で切替は H0167 のみ

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| General が0.01のクリップ (motion_id) | `['H0157', 'H0158', 'H0165', 'H0166']` | `['H0157', 'H0158', 'H0165', 'H0166']` | 一致 |
| 変種の骨が途中で切り替わるクリップ | `['H0167']` | `['H0167']` | 一致 |

### OBS15

**計画の記述**: 胸の子ボーンのオイラー角の変化幅: H0159=5.11 H0160=12.76 H0163=37.30 H0164=16.48 度

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 胸の子ボーンのオイラー角 変化幅 H0159/H0160/H0163/H0164（小数2桁） | `[5.11, 12.76, 37.3, 16.48]` | `[5.11, 12.76, 37.3, 16.48]` | 一致 |

### OBS16

**計画の記述**: H0157 binding=1011件、一意パスハッシュ331、名前判明172。3変種の骨81本は全部H0157に出現

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| H0157 binding 件数 | `1011` | `1011` | 一致 |
| H0157 一意パスハッシュ | `331` | `331` | 一致 |
| H0157 名前判明 | `172` | `172` | 一致 |
| 3変種の骨81本が全部H0157に出現するか | `81` | `81` | 一致 |

### OBS17

**計画の記述**: 寝室系14クリップ+H0157 の原作アニメは抽出・保存済み

**再測定**: 今回は再測定していない（GATE の判定基準にも後続工程の分岐条件にも使われていないため）。

### OBS18

**計画の記述**: HelenSSR0101 の16バンドルの中身 = AnimationClip374 / Mesh76 / Texture2D87 / AnimatorController4 / AnimatorOverrideController1 / MonoBehaviour11。GameObject・Transform・SkinnedMeshRenderer・Material・Shader は0個

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 16バンドルの型別件数 | `{'AnimationClip': 374, 'AnimatorController': 4, 'AnimatorOverrideController': 1, 'Mesh': 76, 'MonoBehaviour': 11, 'Texture2D': 87}` | `{'AnimationClip': 374, 'AnimatorController': 4, 'AnimatorOverrideController': 1, 'Mesh': 76, 'MonoBehaviour': 11, 'Texture2D': 87}` | 一致 |
| GameObject/Transform/SkinnedMeshRenderer/Material/Shader の件数 | `0` | `0` | 一致 |

### OBS19

**計画の記述**: Unity `Cloth` の型固有フィールド名がキャッシュ9035件の展開バイト列に0件

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| Cloth 型固有フィールドのヒット数 | `0` | `0` | 一致 |

### OBS20

**計画の記述**: 物理を持つ24バンドル ∩ Chest_L_Upper を含むバンドル = 9件。その9件で Chest_L/Chest_R とその子に物理は0件。親 Chest_M には9件中5件で Rigidbody+ConfigurableJoint

**再測定**: 今回は再測定していない（GATE の判定基準にも後続工程の分岐条件にも使われていないため）。

### OBS21

**計画の記述**: ゲーム本体のコード35ファイルの平文文字列として `_Bend` `_Flat` `cloth2` `Chest_L` はASCII/UTF-16LEとも0件。`_General` は mscorlib に2件あるが無関係。対照語はヒットする。35ファイルは全部が素の .NET アセンブリ（難読化ストリーム0）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| コードファイル数 | `35` | `35` | 一致 |
| _Bend/_Flat/cloth2/Chest_L の平文一致 (ASCII+UTF16LE) | `0` | `0` | 一致 |
| _General の ASCII 一致 | `2` | `2` | 一致 |
| 難読化ストリーム数 | `0` | `0` | 一致 |

### OBS22

**計画の記述**: 読み出した頂点から計算した境界箱が、ゲームが書いた m_LocalAABB とズレ0.000000で一致

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 境界箱の一致（小数6桁） | `0.0` | `0.0` | 一致 |

### OBS23

**計画の記述**: ゲーム由来の独立照合点 = メッシュ全体AABB 27個 + サブメッシュ単位AABB 62個 + 各頂点数 + 各面数

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| メッシュ全体AABB の個数 | `27` | `27` | 一致 |
| サブメッシュ単位AABB の個数 | `62` | `62` | 一致 |

### OBS24

**計画の記述**: テクスチャ87枚 = _d21 / _n21 / _rmo21 / _da9 / _spc2 / その他2 + RampMap_Linear_RGBAHalf 11枚(256x16) + 部位別ramp設定11個

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| テクスチャ枚数 | `87` | `87` | 一致 |
| テクスチャ内訳 | `{'RampMap': 11, '_d': 21, '_da': 9, '_n': 21, '_rmo': 21, '_spc': 2, 'その他': 2}` | `{'RampMap': 11, '_d': 21, '_da': 9, '_n': 21, '_rmo': 21, '_spc': 2, 'その他': 2}` | 一致 |
| 部位別 ramp 設定の個数 | `11` | `11` | 一致 |

### OBS25

**計画の記述**: メッシュ27個中23個が、テクスチャ名の部位部分と文字列一致。`c_HelenSSR0101_slg_cloth2_Bend_d/n/rmo` と `..._Flat_d/n/rmo` が実在

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| lod0 27メッシュ中テクスチャ名と一致する数 | `23` | `23` | 一致 |
| 一致しなかったメッシュ数 | `4` | `4` | 一致 |

### OBS26

**計画の記述**: 顔メッシュ c_HelenSSR01_slg_face_lod0 = 2676頂点・表情24本。24本の名前は H0157 の24カーブと 24/24 一致

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 顔メッシュ頂点数 | `2676` | `2676` | 一致 |
| 顔メッシュ 表情本数 | `24` | `24` | 一致 |
| 表情24本とH0157の24カーブの一致数 | `24` | `24` | 一致 |

### OBS27

**計画の記述**: Shader を含むバンドル = キャッシュ13件 + アプリ内蔵45件。アプリ内蔵に `gf_shader/pbr/character/shadowcaster` が実在

**再測定**: 今回は再測定していない（GATE の判定基準にも後続工程の分岐条件にも使われていないため）。

### OBS28

**計画の記述**: `gf2-helen-starlit-waltz/` 配下で `from_pydata` を含む .py と `bpy` と `UnityPy` の両方を import する .py がどちらも0件

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| from_pydata を含む .py | `0` | `0` | 一致 |
| bpy と UnityPy を両方 import する .py | `0` | `0` | 一致 |

### OBS29

**計画の記述**: rest-room-v2.2/blends/ の実数は .blend 17個 / .blend1 8個

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| .blend の個数 | `17` | `17` | 一致 |
| .blend1 の個数 | `8` | `8` | 一致 |

### OBS30

**計画の記述**: quality-gate.json は gf2-helen-starlit-waltz/ 配下に0件（着手前）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 着手前の quality-gate.json の個数 | `0` | `0` | 一致 |

### OBS31

**計画の記述**: 座標変換行列が既存: UNITY_TO_BLEND=((-1,0,0),(0,0,-1),(0,1,0))

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| UNITY_TO_BLEND の所在行 | `32` | `32` | 一致 |

### OBS32

**計画の記述**: ゲーム固有シェーダ `gf_shader/...` は、キャッシュ+アプリ内蔵で218件（一意135件）実在

**再測定**: 今回は再測定していない（GATE の判定基準にも後続工程の分岐条件にも使われていないため）。

### OBS33

**計画の記述**: `gf_shader/pbr/character/` のシェーダは6件。うち uber/ubertrans/eye/eyeblend_add/eyeblend_multiply の5件がキャッシュの ed19937c… に同居、shadowcaster はアプリ内蔵側

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| gf_shader/pbr/character/ のシェーダ本数 | `6` | `6` | 一致 |

### OBS34

**計画の記述**: `uber` のプロパティは全107個。`_BaseMap` `_BumpMap` `_RMOTex` `_UseRampMap` `_RampMap` `_OutlineColor` ほか、手元のテクスチャと対応する入力が実在する

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| uber のプロパティ数 | `107` | `107` | 一致 |

### OBS35

**計画の記述**: `uber` は SubShader1個・Pass10個。GFCharForward / GFCharUnderBG / GFCharUnderBGOutline / GFOutline / WeaponInteractiveVFX / ShadowCaster / GFHairShadow / GFCharFaceShadow / GFCharHairTransE / ContourPass_EnvStd

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| uber の Pass 数 | `10` | `10` | 一致 |
| uber の Pass 名 | `['GFCharForward', 'GFCharUnderBG', 'GFCharUnderBGOutline', 'GFOutline', 'WeaponInteractiveVFX', 'ShadowCaster', 'GFHairShadow', 'GFCharFaceShadow', 'GFCharHairTransE', 'ContourPass_EnvStd']` | `['GFCharForward', 'GFCharUnderBG', 'GFCharUnderBGOutline', 'GFOutline', 'WeaponInteractiveVFX', 'ShadowCaster', 'GFHairShadow', 'GFCharFaceShadow', 'GFCharHairTransE', 'ContourPass_EnvStd']` | 一致 |

### OBS36

**計画の記述**: compressedBlob 701,033バイトを lz4 展開すると 10,377,504バイトになり、94.85%が印字可能ASCII＝可読の Metal ソースコード。プログラムは502個（vertex 239 / fragment 263）。パス別フラグメント変種数は GFCharForward 216 ほか

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| compressedBlob のバイト数 | `701033` | `701033` | 一致 |
| 展開後のバイト数 | `10377504` | `10377504` | 一致 |
| 印字可能ASCIIの割合(%) | `94.85` | `94.85` | 一致 |
| 文字列カウント | `{'#include <metal_stdlib>': 502, 'using namespace metal': 502, 'xlatMtlMain': 1004, '.sample(': 2351, 'fma(': 19125, '_RampMap': 1284, 'MTLB': 0, 'air64': 0}` | `{'#include <metal_stdlib>': 502, 'using namespace metal': 502, 'xlatMtlMain': 1004, '.sample(': 2351, 'fma(': 19125, '_RampMap': 1284, 'MTLB': 0, 'air64': 0}` | 一致 |
| プログラム数 vertex/fragment/合計 | `[239, 263, 502]` | `[239, 263, 502]` | 一致 |
| パス別フラグメント変種数 | `{'GFCharForward': 216, 'GFCharHairTransE': 24, 'GFCharFaceShadow': 12, 'ShadowCaster': 4, 'ContourPass_EnvStd': 3, 'GFOutline': 1, 'GFHairShadow': 1, 'GFCharUnderBG': 1, 'GFCharUnderBGOutline': 1, 'WeaponInteractiveVFX': 0}` | `{'GFCharForward': 216, 'GFCharHairTransE': 24, 'GFCharFaceShadow': 12, 'ShadowCaster': 4, 'ContourPass_EnvStd': 3, 'GFOutline': 1, 'GFHairShadow': 1, 'GFCharUnderBG': 1, 'GFCharUnderBGOutline': 1, 'WeaponInteractiveVFX': 0}` | 一致 |

### OBS37

**計画の記述**: ramp設定11個は typetree でそのまま読める。各オブジェクトが Ramp_Diffuse / Ramp_Specular / Ramp_Fresnel / Ramp_Additional の4本を持ち、各本が key0..key7 の RGBA を保持。有効キー数は2〜4。overrideRamps=0

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| ramp 設定の個数 | `11` | `11` | 一致 |
| cloth2_ramp の有効キー数 | `[2, 4, 2, 3]` | `[2, 4, 2, 3]` | 一致 |
| hair_ramp の有効キー数 | `[4, 2, 2, 2]` | `[4, 2, 2, 2]` | 一致 |
| Zuanshi_ramp の有効キー数 | `[2, 2, 2, 2]` | `[2, 2, 2, 2]` | 一致 |

### OBS38

**計画の記述**: キャラ用シェーダ6本すべての中身を読了。uber(107/10) ubertrans(78/8) eye(17/1) eyeblend_add(10/1) eyeblend_multiply(9/1) shadowcaster(5/3)

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 各シェーダの プロパティ数/パス数 | `{'uber': [107, 10], 'ubertrans': [78, 8], 'eye': [17, 1], 'eyeblend_add': [10, 1], 'eyeblend_multiply': [9, 1], 'shadowcaster': [5, 3]}` | `{'eye': [17, 1], 'eyeblend_add': [10, 1], 'eyeblend_multiply': [9, 1], 'shadowcaster': [5, 3], 'uber': [107, 10], 'ubertrans': [78, 8]}` | 一致 |

### OBS39

**計画の記述**: GFCharForward の216変種は全部が別々。グローバル9種 + ローカル4種。ヘレンのテクスチャ在庫で絞ると胸メッシュでは実質1本（blobIndex 216）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| GFCharForward の変種数 | `216` | `216` | 一致 |
| 216変種がすべて別々のキーワード組合せか | `216` | `216` | 一致 |
| グローバルキーワード | `['_ALPHATEST_ON', '_CHARACTER_EFFECT', '_GF_CHAR_LOD1', '_GF_CHAR_LOD2', '_MAIN_LIGHT_SHADOWS', '_MRT', '_USE_BLEND_TEX', '_USE_STOCKING', '_WET_EFFECT']` | `['_ALPHATEST_ON', '_CHARACTER_EFFECT', '_GF_CHAR_LOD1', '_GF_CHAR_LOD2', '_MAIN_LIGHT_SHADOWS', '_MRT', '_USE_BLEND_TEX', '_USE_STOCKING', '_WET_EFFECT']` | 一致 |
| ローカルキーワード | `['_ANISOTROPIC_SPECULAR', '_DETAIL_MAP', '_USE_FUR_SHELL', '_USE_VOLUMETRIC']` | `['_ANISOTROPIC_SPECULAR', '_DETAIL_MAP', '_USE_FUR_SHELL', '_USE_VOLUMETRIC']` | 一致 |

### OBS40

**計画の記述**: ramp設定の Ramp_Diffuse.key0 と RampMap テクスチャ row0/px0 が 11/11 一致（最大誤差 0.000058）

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| ramp設定とRampMapの対応組数 | `11` | `11` | 一致 |
| half と float の差（最大・小数6桁） | `5.8e-05` | `5.8e-05` | 一致 |

### OBS41

**計画の記述**: `RampMap` は 256x16 の RGBAHalf、32,768バイト

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| RampMap の枚数 | `11` | `11` | 一致 |
| RampMap の形式 | `['RampMap_Linear_RGBAHalf', 256, 16, 'RGBAHalf', 32768]` | `['RampMap_Linear_RGBAHalf', 256, 16, 'RGBAHalf', 32768]` | 一致 |

### OBS42

**計画の記述**: ヘレンの衣装の構成: lod0 27メッシュのうち SSR0101 が24個 / SSR01 が3個。SSR0101 の中に P1/P2/P3 の3組。body には _Dorm・_Fight・(無印) の派生。胸3変種を持つのは cloth2 だけ

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| lod0 メッシュ数 | `27` | `27` | 一致 |
| SSR0101 / SSR01 の内訳 | `[24, 3]` | `[24, 3]` | 一致 |
| 頂点数（P1/P2/P3/共通の19メッシュ） | `[1816, 3516, 5943, 6214, 4502, 5332, 3860, 7507, 4786, 2278, 3936, 1842, 10223, 5812, 534, 5593, 15387, 454, 5604]` | `[1816, 3516, 5943, 6214, 4502, 5332, 3860, 7507, 4786, 2278, 3936, 1842, 10223, 5812, 534, 5593, 15387, 454, 5604]` | 一致 |

### OBS43

**計画の記述**: 配布MMD(礼服)は 頂点70,690 / 面93,008 / テクスチャ23 / 材質40個。P- 接頭辞の材質はすべて P1- で、P2-・P3- は1つも無い

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| 配布MMD 頂点数 | `70690` | `70690` | 一致 |
| 配布MMD 面数 | `93008` | `93008` | 一致 |
| 配布MMD テクスチャ数 | `23` | `23` | 一致 |
| 配布MMD 材質数 | `40` | `40` | 一致 |
| P2- / P3- 接頭辞の材質数 | `[0, 0]` | `[0, 0]` | 一致 |

### OBS44

**計画の記述**: H0157 は P1/P2/P3 を骨で切り替えていない。骨スケールで全部が0.01なのは SSR01 の cloth3_lod0 と weapon_slg_lod0。cloth2 の General 19/25・Bend 21/29 は部分的

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| H0157 で全骨が0.01の lod0 メッシュ | `['c_HelenSSR01_slg_cloth3_lod0', 'c_HelenSSR01_weapon_slg_lod0']` | `['c_HelenSSR01_slg_cloth3_lod0', 'c_HelenSSR01_weapon_slg_lod0']` | 一致 |
| P1/P2/P3 の body・cloth・hand で0.01の骨の合計 | `0` | `0` | 一致 |

### OBS45

**計画の記述**: SSR0102(魅魔スキン)の一次データはこの環境に無い。キャッシュ・アプリ内蔵とも HelenSSR0102 は0件。手元にあるのは配布MMDだけ

| 項目 | 計画の値 | 再測定値 | 判定 |
|---|---|---|---|
| HelenSSR0102 を含むバンドル（キャッシュ/アプリ内蔵） | `[0, 0]` | `[0, 0]` | 一致 |

### OBS46

**計画の記述**: Blender 4.5.11 でコレクション5個を作り表示を切り替えて保存 → 開き直しても状態が保持された

**再測定**: 今回は再測定していない（GATE の判定基準にも後続工程の分岐条件にも使われていないため）。

## 矛盾・未確定

### OBS9（不一致・原因未確定）

計画は「bindpose を逆行列に戻して算出」とだけ書いており、**どの骨をどの骨に対応づけたか**が書かれていない。3変種は骨の本数が違い（General 25 / Flat 27 / Bend 29）、骨ハッシュの重なりも0（OBS8・再測定で一致）なので、対応づけ方法を決めないと値が定まらない。

対応づけ方法を3通り実測した結果:

| 方法 | Bend↔Flat 最大距離 | General↔Flat 最大角度 |
|---|---|---|
| 位置が最も近い骨に対応づけ | 15.7 cm | 180.0 度 |
| 骨の並び順で対応づけ | 37.52 cm | 179.9965 度 |
| 相互に最近傍のペアだけ | 7.82 cm | 180.0 度 |

どの方法でも計画の 12.1cm / 19.1度 にはならなかった。**計画値をこの環境で再現する手順が特定できていない**ため、原因が確認できるまで OBS9 に依存する判断は行わない。
OBS9 は INF3（Bend/Flat の骨は General とは別の場所に置かれた別インスタンス）の根拠だが、INF3 も GATE には使われていない。3変種の骨ハッシュが重ならないこと（OBS8）自体は一致している。

### 判定不能のまま残るもの（計画 UNK 節）

- **UNK1** H0157 で画面に描画されるのが Flat か。プレハブ不在（OBS18/OBS3）で確かめる経路が無い。
- **UNK2** 原作が重力から形を計算しているか。
- **UNK3** 変種の選択が姿勢で決まるか。H0163/H0164 が反例。
- **UNK5** SSR0101 が「星夜のワルツ」か。

## 変遷

- 2026-08-10 作成。計画 v5.1 の OBS を全件記録し、工程Aで再測定した結果を併記。

## 関連リンク

- [[gf2-helen-repro-v51-run]]
- [[gf2-helen-rest-room-motion-v22]]
