---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-24
sources:
  - gf2-helen-repro-v51-handoff
---

# 引き継ぎ資料 — GF2 CHAR EXTRACT（他キャラ原作抽出・並列）

> [!info] 正本の所在
> このページがセッション横断の引き継ぎ正本（[[gf2-helen-repro-v51-handoff]] の wiki 移行と同じ運用）。
> プロジェクト側 `run-state.json` の `handoff_file` がこのページを指す。
> **段階追記型**： 各Step完了・検証器合否が出た時点で追記する（死亡セッションの完了主張を正本にしない教訓）。

## 0. まず読むもの

| # | 何 | どこ |
|---|---|---|
| 1 | **このファイル** | wiki `wiki/builds/gf2-char-extract-handoff.md` |
| 2 | 承認済み計画書 v2.1 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/PLAN-CHAR-EXTRACT-2026-08-23.md` |
| 3 | 機械可読現在位置 | `gf2-char-extract/run-state.json` |
| 4 | 品質ゲート | `gf2-char-extract/quality-gate.json`（plan PASS 済み） |

## 1. 目的と現在地（2026-08-24 時点）

- **目的**: 光の再現と独立に、ドルフロ2原作データから複数キャラの形（メッシュ＋骨＋テクスチャ）をBlenderへ抽出する量産導線を作る。光・輪郭線・RampMap・アニメは全て deferred
- **経緯**: ヘレンの光再現が停止中（[[gf2-helen-lighting-diagnosis-summary-20260823]]）のため、武田さんが並列抽出を提案 → hold で方針v2・計画v2.1が承認（2026-08-23）
- **現在位置**: **Step 1 完了（抽出ドライバ＋機械突合12項目PASS＋決定性実測＋replay否定試験11系統PASS、Helenで実測）**。次は Step 2（武田さんによるパイロット2体の指名 → 目視承認）

### Step 0 の主な実測結果（2026-08-24）

- 対象: レアリティ行を持つ族 113族（敵NPC除外）。うち **complete_shape=69族**
- 走査: local bundle 18,568本 → UnityFS 13,536本を展開レベル走査、ヒット5,321ファイルをオブジェクト級でパース（75,115オブジェクト・エラー0）
- 陽性対照(Helen): complete_shape・face有り・dorm有り・mesh138/tex126/anim698 — 既知事実と一致。衣装材質の少なさ(mat=8 vs Sabrina45)も既知の欠損と整合
- 陰性対照: 実在しない名前は0ヒット。**両対照とも ALL PASS**（`ledger/inventory-controls.json`）
- 成果物: `ledger/char-inventory.json`・`reports/inventory-summary.md`・`ledger/needle-bundle-scan.json`
- 既知の限界: AFS2/CRI コンテナ5,032本は生バイト走査のみ（中身未走査）。RX_/Summon系の設定行は本体assetを別名で共有している可能性（partial/no_assets の主因）

### Step 1 の主な実測結果（2026-08-24・開発ターゲット Helen/HelenSSR01）

- **導線**: `10_extract_char.py --char <族> [--variant <版>]` 1本で 抽出→Blender headless構築→台帳記録まで。突合は `20_diff_char_blend.py --char <族>` 
- **成果物blend**: `blends/Helen-HelenSSR01-repro.blend`（SHA256 先頭16 `1ac90c8280146076`）。メッシュ91種→サブメッシュ分離201オブジェクト／頂点259,772／面351,258／骨375／表情24／テクスチャPNG127枚
- **機械突合**: `ledger/diff-Helen-HelenSSR01.json` — 頂点・UV・法線(サンプル許容差)・シェイプキー・サブメッシュ構造・可視性・出所・スキニング・骨(rest prop完全照合＋回転＋遠方骨)・潰れ骨・slot名・テクスチャ画素SHA の **12項目すべて PASS**
- **決定性試験**: blendバイトは非決定（UUID等のため想定内）→ canonical manifest SHA なら一致 → 判定方式を `canonical_manifest_sha` に確定（`ledger/determinism-probe.json`・計画書 作法4どおり）
- **replay否定試験**: 座標・三角形index・UV・法線・骨落下・骨移動・rest prop・slot名・可視性反転・ウェイト・出所 の11系統の破壊がすべてFAILになることを実測（`--self-test`）
- **骨の親**: prefab Transform階層の相対パスCRC32照合（旧b18方式）。**375本中160本が未解決** — 版違い衣装(c_HelenSSR0101_slg_* 等67メッシュ)のプレハブ/SMRがAFS2/CRIコンテナ内にあり族ファイル集合から届かないため。**推定では埋めず unresolved として記録**（推定率はキャラ別に skeleton.json の unresolved_parent_count で機械判定可能）
- **材質slot**: SMR由来の権威ある対応を持つのは24/91メッシュ。201 slotが未解決（SMR不在＋外部CABがAFS2内）→ `no_material_<i>` 表記で blend に入る。**権威ある対応データが取れない限り忠実量産から除外する対象**（missing_inputs）
- **CAB索引**: `scripts/05_cab_index.py` が全13,539 bundle の CAB名→ファイル名索引を作成（`ledger/cab-bundle-index.json`、13,307 CAB）。source由来の走査索引であり検証器も source データとして読む
- **テクスチャ**: 族トークンを名前に含む127枚をPNG書き出し。検証器は source Texture2D を RGBA 画素SHA で再計算し書き出しPNGと突合（全件一致）

### 捨てた判断とその見え方（Step 1）

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| 旧c01方式「全サブメッシュを1メッシュに連結」 | blend内で服・肌・顔・武器などが**別オブジェクト**になり、アウトライナで部位ごとに表示切替できる。代わりにオブジェクト数は増え（201個）、サブメッシュ間で頂点プールを共有するため頂点合計は原作より膨らむ。検証器20も分離前提で書かれている | 戻すなら ce_build_blend.py の SUBMESH_SEPARATED と検証器20の比較規約を連結前提へ双方書き直す必要がある（片方だけ戻すと突合がFAILする） |
| 「Transform数最大のプレハブ1本だけ解析」初版方式 | Helen実測で誤った共有bundleを掴み親380本が全部未解決になった。現在は**族内の全プレハブ候補を統合**し、SMRはGO名完全一致で決定的に探す。見た目上は骨の親子が繋がる範囲が広がった | 戻す理由はない（誤選択の再発）。単一プレハブ前提のコードは残していない |
| 骨へのscale保持（EditBone仕様の制約回避） | BlenderのBoneはscaleを持てないため、潰れ骨(General/Bend等 scale 0.01)の**大きさ情報は骨本体には載らない**。原作のrest行列は各骨のカスタムプロパティ `gf_rest_matrix` に入れてあり、スクリプトから参照すれば元の潰れ具合も復元できる。画面上の骨の太さ・長さは表示用の一定値 | プロパティを読んでボーン行列へ掛ければ戻せる。検証器20はpropで原作と完全突合済み |
| テクスチャの族全件書き出し（3091枚）初版方式 | 共有bundleの無関係テクスチャまで書き出していた初版をやめ、**名前に族トークンを含む127枚のみ**に絞った。textures/ フォルダが軽くなり、不要なUI画像等が混ざらない | 絞りすぎが疑われるキャラが出たら ce_extract.py の tokens_by_len フィルタを外せば全件に戻る |

## 2. 承認履歴

| 日付 | カード | 結果 |
|---|---|---|
| 2026-08-23 | 方針①「光と独立に他キャラの形抽出を並列で始める」 | 詳細検討のうえ再提出を指示 |
| 2026-08-23 | 方針v2「在庫台帳→ドライバ→パイロット2体→バッチ」 | **承認** |
| 2026-08-23 | 計画② | 「機械検証スクリプト必須・抜けチェック」を指示 → サブエージェント監査（major 6/minor 7）→ v2 へ反映 |
| 2026-08-23 | 計画v2 | 「引き継ぎ資料を作れ」を指示 → v2.1 へ反映 |
| 2026-08-23 | 計画v2.1 | **承認（実装開始）** |
| ― | （Step 2 パイロット目視の承認は**まだ取っていない**。Helenの機械突合PASSは原作データとの整合であって見た目の合格ではない） | |

## 3. 実装メモ（スクリプト構成）

| スクリプト | 役割 | 状態 |
|---|---|---|
| `scripts/common_ce.py` | パス定数・インタプリタ/lz4 関所 | 完了 |
| `scripts/ce_common.py` | anaconda⇔Blender共有の規約（座標変換・骨表示名・ハッシュ・safe名・オブジェクト命名） | 完了 |
| `scripts/00a_extract_model_names.py` | ModelConfigData.bytes の汎用 protobuf ウォーク → 文字列宇宙・バリアント名 | 完了（walk_completed=true） |
| `scripts/00b_needle_bundle_scan.py` | 全local bundle 展開レベル走査（needle全449名×トークン集合積・単一パス） | 完了（pipeline_valid=true） |
| `scripts/00c_classify_assets.py` | ヒットbundleの UnityPy オブジェクト級パース → 族ごと分類（最長一致帰属） | 完了（75,115 objs・エラー0） |
| `scripts/00d_controls_summary.py` | 陽性対照(Helen期待値表)・陰性対照・サマリ生成 | **完了（ALL PASS）** |
| `scripts/05_cab_index.py` | 全bundleの CAB名→ファイル名 索引（外部PPtr解決の土台） | 完了（13,307 CAB） |
| `scripts/ce_extract.py` | 抽出ライブラリ（prefab統合パース・MeshHandler・AABB自己検査・テクスチャ・来歴） | 完了 |
| `scripts/10_extract_char.py` | **抽出ドライバ**（抽出→Blender構築→台帳記録まで1本） | 完了 |
| `scripts/15_determinism_probe.py` | 決定性試験（byte SHA / canonical manifest SHA の実測判定） | 完了（canonical_manifest_sha 採用） |
| `scripts/ce_build_blend.py` | Blender側ビルダ（サブメッシュ分離・素スロット・来歴焼き込み・canonical生成） | 完了 |
| `scripts/_dump_blend.py` | blend再オープンダンプ（検証器の観測側） | 完了 |
| `scripts/20_diff_char_blend.py` | **機械突合検証器**（12項目＋replay否定試験 `--self-test`） | 完了（Helen ALL PASS） |

- 対象範囲の機械的定義: `(SSR|SR|UR)\d{2,}` バリアント行を持つベース族のみ（113族・敵NPC除外）
- Python 方針: UnityPy/lz4 系は `/opt/anaconda3/bin/python3` 3.11.7 固定＋`.python-deps`。stdlib のみの新規スクリプトは python3 3.14。**Blender 4.5.11 LTS** は `common_ce.BLENDER` 固定
- 版選択規則: `c_<stem>_slg_` 形のstemのうち「版自身＋直後に数字が続く派生」（HelenSSR01→0101を含み_Darkzone等は除外）。LODはグループ毎に最良1つ（lod0優先→lodm0→lod1…）。同名メッシュ重複はソート順初出採用
- 可視性: prefab の GameObject m_IsActive 祖先連鎖（source-backed）。原作が無効にするものは `src_disabled` コレクションへ非表示で格納
- 実装上の教訓（再発防止）: ①macOS multiprocessing は spawn — worker 用グローバルは import 時に構築する ②asset名のトークン照合は `_` 分割部分も集合に入れる ③**EditBone 参照は EDIT モード中のみ有効**（OBJECTモード後のアクセスは解放済みメモリで UnicodeDecodeError）④**Blender は --python の例外でも exit 0 を返すことがある**（成功判定は build log の存在まで見る）⑤UnityPy 1.25 の externals は BundleFile 内部 SerializedFile にぶら下がる（`.name` 属性・CAB大小文字混在は lower で統一）⑥ゲームデータ由来文字列には孤立サロゲートがあり得る（`ce_common.clean_str` 必須）⑦骨ハッシュ照合は**ルート名を外した相対パス**のCRC32（フルパスでは一致しない — 旧b18の知見）

## 4. 既知の限界・未決

- 骨の親160本未解決（AFS2/CRI内のプレハブ由来）。**推定では埋めない**。バッチ時は推定率をキャラ別に数値で報告する
- 材質slot201個未解決（SMR不在67メッシュ＋AFS2内CAB 35slot）。権威ある対応が取れるまでこれらのキャラは忠実量産から除外（missing_inputs）
- AFS2/CRI コンテナ5,032本は生バイト走査のみ（中身未走査）
- ベース名のバイト部分一致のため親族偽ヒットがあり得る（Nagant⊂Mosinnagant 等）。族帰属の確定は 00c のオブジェクト級パースで行う
- ModelConfigData の protobuf スキーマは未解読（文字列フィールドの収集のみ）
- 法線の比較はBlender内部量子化のため完全hashではなくサンプル許容差（TOL=0.02、実測上限0.0133は Helen 全91メッシュで測定）
- ヘレン自身の衣装材質は原作に欠損（既知）→ 台帳では partial が正しい期待値

## 5. 触ったファイル（作業単位で追記）

- 新規: `gf2-char-extract/` 全体（quality-gate.json・run-state.json・scripts/common_ce.py・00a〜00d・`_inspect_00b.py`(開発用)・ledger/{model-config-strings,model-name-candidates,needle-bundle-scan,needle-variant-hits,char-inventory,char-inventory-objects,inventory-controls}.json・reports/inventory-summary.md・logs-00b/00c-run.log(実行ログ)）
- 新規（wiki）: このページ
- 変更なし（read-only 遵守）: `gf2-helen-starlit-waltz/`、ゲームデータ一式
- **Step 1 追加（2026-08-24）**: scripts/{ce_common,ce_extract,ce_build_blend,10_extract_char,15_determinism_probe,20_diff_char_blend,_dump_blend,05_cab_index}.py ／ ledger/{cab-bundle-index,determinism-probe,diff-Helen-HelenSSR01,extract-Helen-HelenSSR01}.json ／ blends/Helen-HelenSSR01-repro.blend ／ intermediate/Helen.HelenSSR01/（extract-manifest・skeleton・texture-manifest・materials・meshes/*.npz・textures/*.png） ／ logs/build-Helen-HelenSSR01*{.json,.blender.log,.canonical.json}

## 6. 変更履歴

- **2026-08-24（Step 1）**: 抽出ドライバ〜機械突合を実装し Helen/HelenSSR01 で実測。突合12項目ALL PASS・決定性 canonical_manifest_sha 確定・replay否定試験11系統PASS。捨てた判断4件を同ページに記録。run-state.json を step1-done へ更新。
- **2026-08-24**: Step 0 完了。00b を needle全集合×トークン(`_`分割含む)方式へ作り直し（Helen 12→92ファイルの改善）。00c 完走（75,115 objs）、00d 対照試験 ALL PASS。サマリ生成。
- **2026-08-23**: 作成。計画v2.1承認を受け実装開始。品質ゲート plan PASS。00a完走・00b実行中。
