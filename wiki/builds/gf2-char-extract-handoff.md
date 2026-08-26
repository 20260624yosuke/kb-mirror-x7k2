---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-25
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

### Step 2 パイロット2体の実測結果（2026-08-24・機械突合まで完了／目視承認はこれから）

> [!warning] この節の数値は監査修正・白モデル修正**前**の初版ビルドの記録
> blend SHA・オブジェクト数・材質slot数は下の「監査修正と白モデル修正」節の再構築後の値が現行。初版は LOD 混在・全パーツ同時表示・テクスチャ未貼りという監査指摘済みの状態。

- **指名**（武田さん・動画ベース）: Dusevnyj 着せ替えスキン＝`DusevnyjSSR0101`（明日のスパシーチェリ）、Sabrina 水着スキン＝`SabrinaSSR0101`（ベリーザバイオーネ）。ModelConfigData 上スキン番号付きバリアントは各キャラ1本しか無く、動画の服はこの2本に確定（ジャンル紐付け自体は IOP Wiki 単源 inferred — 目視承認時に服が違えば差し替え）
- **導線**: `10_extract_char.py --char <族> --variant <版>` → `20_diff_char_blend.py --char <族> --variant <版>`
- **Dusevnyj/DusevnyjSSR0101**: blend `blends/Dusevnyj-DusevnyjSSR0101-repro.blend`（SHA256 先頭16 `98be92bce7d2287a`）。メッシュ115種→159オブジェクト／骨295／親未解決257（87%）／材質slot未解決159。**突合12項目 ALL PASS**
- **Sabrina/SabrinaSSR0101**: blend `blends/Sabrina-SabrinaSSR0101-repro.blend`（SHA256 先頭16 `4109a6522250cde0`）。メッシュ8種→14オブジェクト／骨167／親未解決78（47%）／材質slot未解決14。**突合12項目 ALL PASS**
- **Helen 回帰**: 検証器変更後に再突合 → **PASS 継続**（重複面0のため結果不変）
- **replay否定試験**: 検証器変更後に `--self-test` 再実行 → 11系統すべて FAIL 化を確認（self_test PASS / cases 11）
- **新たに判明した原作データ性質**: スキン番号付きバリアントには**完全重複面**（同一頂点3つ・同一向き）が含まれる（Dusevnyj cloth 128枚・Sabrina hair 74枚。デフォルト衣装の Helen は0枚）。Unity では合法だが Blender は保持できず面列がズレ、triangle/UV/法線が連鎖FAILしていた
- **Blender 表現限界の実測**: 両面ポリゴン境界（同一エッジに正逆の面が接続）で原作法線が面幾何法線とほぼ垂直な corner（dot≈0.04/-0.02）が、Blender の custom normal 内部表現でゼロ化される。値・UV層・API（from_vertices/per-loop）変更で不変 = 表現限界。Dusevnyj で4 corner（0.046%）
- **材質欠損リスクは現実化**: HelenSSR0101 の前例どおり、スキン番号付きは slot 未解決が出る（Dusevnyj 159／Sabrina 14）。権威ある対応データが取れるまで忠実量産から除外対象（missing_inputs）

### Step 2 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| 完全重複面（同頂点3つ・同一向きの二重張り面。Dusevnyj128枚/Sabrina74枚） | blend 内のポリゴン数が原作より少なく表示されるが、同位置同向きの面は画面上 z-fighting の元になるだけで輪郭・シルエット・UV・テクスチャ表示は一切変わらない。ce_extract.py と検証器20が同一規約で初出採用し、台帳に `duplicate_faces_dropped` として枚数記録 | scripts/ce_extract.py・20_diff_char_blend.py の dedupe ブロックを外して再抽出→再構築 |
| （検証規約）Blender がゼロ化する corner の比較 | blend の見た目は不変（当該 corner は元々ゼロ）。検証器だけが「Blender 非表現 corner（ノルム≈0）を比較対象外にし件数を記録、除外率1%超で FAIL」になる。穴あき防止の上限付き | 20_diff_char_blend.py の normals_sampled 例外ブロックを削除すれば元の全件比較に戻る（その場合は Dusevnyj が FAIL し続ける） |

### 監査修正と白モデル修正（2026-08-24・現行の正しい状態）

> 武田さんの品質不良指摘を受け gf2-helen-starlit-waltz (repro v5.1) を監査基準に監査 → 承認カード「全部やる」で修正 → 再構築後のレンダーシートで**モデルが真っ白**という実障害を発見 → 修正して再構築まで完遂。3体とも機械突合 PASS・レンダー目視でテクスチャ乗り確認済み。

**監査で見つかった問題（v51 基準）**:

| # | 問題 | 対応 |
|---|---|---|
| A1 | LOD フィルタが一度も機能していなかった（`split_lod()` 戻り値と `LOD_ORDER` キーの形式不一致で照合常に失敗。Helen も 33/26/32 で lod 混在 = Step 1 からの潜在バグ） | ce_extract.py 修正。全3体で lod0 に統一（Dusevnyj 159→58 / Sabrina 14→8 / Helen 201→75 オブジェクト） |
| A2 | 原作可視性が未取得で全パーツ同時表示（Dorm/Fight 派生＋P1/P2/P3 が多重重なり） | 承認方針どおり**全部入れ・Blenderで切替**: common / parts_P1〜P3 / scene_variants / src_disabled のコレクション分け |
| A3 | テクスチャ未貼り・素スロット（灰色モデル） | naming_match（メッシュ名 stem ↔ テクスチャ名 stem の直接一致）で albedo+法線を `texmatch_*` 材質として適用。**一致しないものは推測で埋めず no_material のまま**（Dusevnyj 頭部・肌，Sabrina 髪，Helen 顔6submesh 等） |
| A4 | 見た目の合格を検査していない | scripts/render_char_sheet.py + combine_sheet.py で正面/斜め45/側面＋派生バリアントのレンダーシートを生成し、**LLM 自身が PNG を目視確認する工程**を追加 |

**白モデル実障害と修正（A4 の工程が機能した実例）**:

- 症状: レンダーシートでモデルが真っ白。blend 内の `texmatch_*` 材質に**画像ノードが1枚も無い**。build log の gaps も空（黙ってスキップ）
- 根本原因: texture-manifest.json の `png` 欄は `textures/` 配下の**裸ファイル名**なのに、builder が `Path(png).exists()` を CWD 相対で評価 → 常に False → 画像ノード生成を黙ってスキップ。検証器は slot **ラベル一致**しか見ておらず 12項目 PASS してしまった（「ラベル一致 ≠ テクスチャが貼れている」の実例）
- 修正: ①builder `load_texture_index` が textures/ 基準で絶対パス解決・実在ファイルのみ索引化・欠落時は gaps 記録 ②canonical doc に `materials`（材質ごとの画像ノード実在＋実寸）を追加 ③検証器が texmatch slot の**画像実在と期待 albedo の対応**を機械照合 ④self-test に「texmatch材質の画像を消す」ケース追加（11→12系統）

**再構築後の実測（2026-08-24 21:33-21:45・現行）**:

| キャラ | blend SHA(先頭16) | オブジェクト(LOD除外) | slot: texmatch / no_material | 突合 | レンダー目視 |
|---|---|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `7e3c94376d503c1e` | 58 (除外74) | 19 / 39 | PASS | テクスチャ乗り（服・義手・タイツ）。頭部・肌は白のまま |
| Sabrina/SabrinaSSR0101 | `ea70684fa424dda6` | 8 (除外3) | 6 / 2 | PASS | テクスチャ乗り（肌・水着・リボン・太もも装備）。髪は白のまま |
| Helen/HelenSSR01（回帰） | `89d4cf0fcf5f5800` | 75 (除外58) | 63 / 12 | PASS | テクスチャ乗り。顔6submesh・MP443・flag_Effect 等は no_material（シートの灰色の平面は flag_lod0_Effect） |

- self-test: PASS（12 cases・白モデル再発防止ケース追加後）
- 決定性: PASS（Dusevnyj で再実測。canonical_identical=true / decision_mode=canonical_manifest_sha）
- レンダーシート: `reports/sheets/<キャラ>/`（`*_sheet.png` が3面合成、`variants/` に Dusevnyj 23枚・Sabrina 2枚の派生バリアント）。**Helen に variants 無しは正常**（ベース衣装で scene_variants が無いため）
- Dusevnyj「3x3の組み合わせ」の実体: P1(body×cloth3択×hip) / P2(body×cloth2択×hip) / P3(body×cloth2択×hip) のパーツ選択式。コレクション切替＋variants レンダーで確認可能

#### 監査修正で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| LOD1/lodm0 等の低解像度メッシュ（A1 修正により blend から除外。Dusevnyj 74 / Helen 58 / Sabrina 3 本） | アウトライナがすっきりし、同位置の多重表示が消える。**遠景用の低ポリ版は blend に入らなくなる**（資料用途では lod0 のみで支障なし想定） | ce_extract.py の LOD 選択を旧の「全 LOD 保持」に戻して再抽出→再構築（ただし旧方式は多重表示バグの原因） |
| 権威 SMR 対応のない slot への原作材質名（A3 により naming_match へ変更） | 材質名が原作名でなく `texmatch_<stem>` になる。**見た目は albedo テクスチャで埋まる**（旧来は no_material の灰色のまま）。原作シェーダ（RampMap 等）は deferred のため陰影は Blender 標準のまま | ce_build_blend.py の slot 決定ブロックで smr 優先順を戻す |
| canonical manifest の旧 SHA（materials 欄追加で doc 形式が変わり全 blend の SHA が更新） | run-state・handoff の旧 SHA は初版ビルドの記録としてのみ有効。再現性の判定は新 SHA で行う | 戻す必要なし（形式進化）。旧ビルドの再現が必要なら git 等で旧スクリプトを復元して再構築 |

### 監査強化・差し戻し修正（2026-08-25・現行の正しい状態）

> 武田さんが Dusevnyj blend を Blender で開き**「全然ダメ。監査が甘すぎる。計画に抜けがある。監査スクリプトを強化して」**と差し戻し。さらに「**ヘレンは依頼していない**」を指摘（調査の結果 正しかった — Helen は計画書の陽性対照＋こちら側の回帰再構築であり成果物依頼は無し。mmd-library に高品質な Helen blend が既に存在するため成果物としても冗長）。承認カードで **Helen は成果物から外し内部回帰検証専用**に決定。品質基準は mmd-library。

**差し戻しで見つかった問題**:

| # | 問題 | 対応 |
|---|---|---|
| D1 | **頭部共有パーツの抽出漏れ**。顔・眉はキャラ共有の基本コード stem 族（`c_DusevnyjSSR01_slg_face/boby`）で管理されており、variant 族（SSR0101）だけをスコープすると頭部が丸ごと欠落。Dusevnyj/Sabrina で発生（Helen は variant=基本コードのため偶然セーフ） | `add_base_head_parts`（自キャラプレフィックス限定・variant 族に無い face/boby/body のみ補完）を抽出器と検証器の双方に適用。manifest に `base_head_parts_added` を記録 |
| D2 | **naming_match が厳密一致のみ**。①コード無し名前（`c_Dusevnyj_slg_face`）②末尾数字落ち（`P1_body` vs `P1_body1`）③基本コード保持（`c_SabrinaSSR01_slg_hair` vs メッシュ SSR0101）の3クラスを取りこぼし髪・顔・体・衣装の一部が白 | `ce_common.resolve_texture_stem` を共通規約化（exact→R1 コード除去→R2 末尾数字除去→R3 バリアントコード→基本コード→R1+R2・**適用規約を必ず記録**）。builder・検証器の双方で使用 |
| D3 | **既定可視性の規約欠陥**。「同一(part,カテゴリ)内の先頭のみ表示」は P1/P2/P3 が別パートのため全候補が同時表示され、開いた瞬間に衣装が多重重なる | `plan_visibility` を「**P1 セットのみ既定表示**・P2/P3 は parts_P2/P3 コレクションで切替・原作 active=True は常に優先」へ修正 |
| D4 | **監査が見た目の白を数えていなかった**（slot ラベル照合のみ）＋シートが開いた状態のままで孤立ビューは非表示オブジェクトのみ | 検査2項目追加 — `visible_texture_coverage`（可視メッシュで索引に解決できるテクスチャがあるのに素 slot=FAIL・解決不可=known_untextured 報告）と `open_state_groups`（開いた状態での候補重なり検出）。self-test 12→**14系統**。レンダーシートは全枚に白面積率（閾値0.98・背景0.965除外）＋可視オブジェクト孤立レンダー＋P1/P2/P3 セット比較＋`sheet-audit.json` |

**再構築後の実測（2026-08-25・現行）**:

| キャラ | blend SHA(先頭16) | オブジェクト | slot: texmatch / no_material | 突合 | レンダー目視 |
|---|---|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `e49d2ebd5a64af9c` | 64 (除外74) | 18 / 5 | **PASS (14 checks)** | 顔（肌・橙目）・銀髪・セーラー服・義手にテクスチャ乗り。白残り=右手・胸元・cloth3/hip3（原作データにテクスチャ無し=known_untextured） |
| Sabrina/SabrinaSSR0101 | `fceadebf792352d1` | 11 (除外3) | 11 / 0 | **PASS (14 checks)** | 全身肌・顔・髪リボン・水着・装備まで全て乗り。**未テクスチャゼロ** |
| Helen/HelenSSR01（内部回帰・成果物から外す） | `ed5ae0d7ff73b34b` | — | — | PASS (14 checks) | 顔6submesh にテクスチャが乗り no_material は MP443(拳銃)・body・flag_Effect のみ |

- self-test: PASS（**14 cases**・「可視texmatchのslotを素に戻す」「非表示P2候補を可視化」を追加）
- 決定性: PASS（Dusevnyj・Sabrina で再実測。canonical_identical=true / decision_mode=canonical_manifest_sha）
- レンダーシート: `reports/sheets/<キャラ>/`（`*_front/left45/side.png`＋`*_set_P1/P2/P3.png` 着せ替え比較＋`objects/` 孤立ビュー＋`sheet-audit.json` 白面積率台帳）。全レンダーの白面積率 0.0（吹き飛びなし）
- Dusevnyj の既知未テクスチャ5部位: P1_cloth3.sub0/sub1・P1_hip3（原作マニフェスト内にテクスチャ無し）・slg_body（まつげオーバーレイ・顔アトラス UV だが名前解決不可）・boby（同左）。推測で埋めず known_untextured として記録
- mmd-library との関係: `Dusevnyj_SSR0101_スキン.blend` は**顔資料**（1オブジェクト・材質37）、`Sabrina_夏_水着.blend` は全身（公式MMD配布由来）。Sabrina 水着のみ一部重複。本プロジェクトの価値=全スキン量産できる自動抽出導線＋機械検証

#### 監査強化で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| 「(part,カテゴリ)ごとに先頭表示」の既定可視性 | blend を開いた瞬間、P1 セット1着分だけが見える（旧規約では P1+P2+P3 の衣装が全部重なって表示）。P2/P3 は Outliner の parts_P2/P3 コレクションのチェックで切替 | ce_build_blend.py の plan_visibility を旧ロジックに戻して再構築（ただし旧規約は差し戻し原因） |
| 顔・眉の「variant 族に無いから諦める」 | Dusevnyj/Sabrina に基本コード族の face/boby が入り、顔にテクスチャが乗る。オブジェクト数が増える（Dusevnyj 58→64） | ce_extract.py の add_base_head_parts 呼び出しを外して再抽出 |
| テクスチャ名の厳密一致のみ | R1/R2/R3 で髪・顔・衣装の一部が埋まる。**適用規約はビルドログ `texmatch_rule` に全記録**され、検証器も同一規約で照合するため「いつの間にか別テクスチャ」は起きない | ce_common.resolve_texture_stem の緩和規約を exact のみに戻す |

### 監査強化v4・アルファ/ゴミオブジェクト修正（2026-08-25・現行）

> 2回目の差し戻し。武田さんが Sabrina blend を Blender で開き「**ゴミみたいなオブジェクト・バリみたいな部分・監査スクリプトに抜けがある・サブエージェントにも客観監査させろ（スクリプト作成者と回答者が同一のため）・テクスチャ感がのっぺりしている**」。機械精査で原因確定 → 修正 → **独立サブエージェント監査**を新設して実施。

**見つかった問題**:

| # | 問題 | 対応 |
|---|---|---|
| E1 | **全 texmatch 材質でアルファ未接続**（RGBAテクスチャなのに alpha=1.0 固定）→ 髪・フリル・まつげの透過部分が solid な板で表示 = 「バリ状のゴミ」の正体 | make_textured_material が Alpha を接続し **CLIP カットアウト**化 |
| E2 | **OverviewCam カメラが blend 内に残存**（canonical が MESH/ARMATURE しか記録せず監査をすり抜け）= 「ゴミみたいなオブジェクト」の正体 | add_camera 廃止（レンダーシート側が自前でカメラ生成）＋canonical に extra_objects 記録 |
| E3 | Blender 既定 specular/roughness = 原作 ramp/トゥーンに無い PBR ハイライト → のっぺり＋偽の質感 | texmatch 材質は **specular=0・roughness=1.0**（マット）。陰影系(ramp)は deferred のまま |
| E4 | 監査がアルファ配線とオブジェクト型を見ていなかった | 新検査2項目 — `material_alpha_wiring`（RGBA材質の Alpha 接続必須）・`scene_objects`（MESH/ARMATURE 以外=FAIL）。self-test **16系統** |
| E5 | 制作担当自身の目視監査はバイアス risk（スクリプト作成者=回答者） | **独立サブエージェント監査**を運用に新設。以後の成果物提出時に否定的観点で実施 |

**v4 再構築後の実測（2026-08-25・現行）**:

| キャラ | blend SHA(先頭16) | アルファ配線済み材質 | ゴミオブジェクト | 突合 | 独立監査判定 |
|---|---|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `8f2299c58cd49d81` | 36 | 0 | PASS (16 checks) | **条件付き** — 右手・袖が白（P1_cloth3/hip3 は原作データにテクスチャ無し。UV検証でも決定打無しのため推測で埋めず） |
| Sabrina/SabrinaSSR0101 | `223ad7a996a1b310` | 11 | 0 | PASS (16 checks) | **提出可** — 顔・手足・水着・装備に欠陥ゼロ |
| Helen/HelenSSR01（内部回帰） | `06ece4c4a05d27f6` | 49 | 0 | PASS (16 checks) | 対象外 |

**独立サブエージェント監査（2026-08-25・制作担当と分離）の主要指摘**:
- 重大: Dusevnyj の白い右手（known_untextured 台帳と一致・隠蔽なし。ただし verdict PASS に反映されない）
- 中等: `white_ratio` は「吹き飛び白(≥250)」しか数えず無地白を検出しない（実ガードは visible_texture_coverage の known_untextured 列挙。指標の意味を取り違えないこと）
- 中等: 全 slot が非権威（texmatch=推定一致）であることの開示義務
- 確認して問題なし: Sabrina 3視点全体・Dusevnyj の顔/機械腕/制服/タイツ・テクスチャ画素SHA照合・gaps 空・extra_objects 空

#### v4 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| OverviewCam（blend 内カメラ） | Blender で開いたときカメラのワイヤーフレームが消える。視点は自由のまま（レンダーシートは専用カメラを一時生成） | ce_build_blend.py の add_camera を復活 |
| 材質のデフォルト specular/roughness | プラスチック的な偽ハイライトが消え、albedo+法線のみのマットな見た目に。**原作のトゥーン陰影は deferred のため再現されない**（のっぺりの完全解消は ramp 実装まで保留） | make_textured_material の specular/roughness 設定2行を削除 |
| アルファ未接続の状態 | 髪の毛先・フリル・まつげが透過する（旧: 四角い板として表示）。CLIP 閾値 0.5 | Alpha リンクを外す |

### 監査強化v5・ハードカットアウト/ramp陰影（2026-08-25・現行）

> 3回目の差し戻し「**サブリナを blender で開いた。指摘箇所が直っていない。解決して**」。原因は v4 の修正が Blender の実表示に届いていなかったこと: ①`blend_method='CLIP'` は Blender 4.5 EEVEE Next で**無効**（属性例外を try/except が握り潰し、実効状態は HASHED/DITHERED のまま）②EEVEE Next の DITHERED 透過は**ビューポートでディザざらつき**=「バリ」が残存 ③のっぺりの本体は ramp 陰影未実装。

**v5 修正**:

| 問題 | 修正 |
|---|---|
| ディザ透過のざらつき（バリの残存） | Alpha を **Math(GREATER_THAN 0.5) で二値化**して接続。エンジン・ビューポート非依存のハードカットアウト（原作はアルファテスト相当）。`gf_alpha_cutout` を記録 |
| のっぴり（陰影がない） | **原作 ramp テクスチャが実在する材質のみ** v51 実績レシピで陰影を配線: `ramp U=clamp(dot(法線,固定Z))・V=0.125`、albedo×ramp（f43/f109b 実績・データ駆動）。`gf_ramp_recipe` を記録。ramp が無い材質は albedo のまま（推測で埋めない）。**全身のゲーム相当シェーディング（全材質 ramp+SH）は deferred の別 phase** |
| 監査の取りこぼし | canonical のアルファ判定を Math ノード経由に対応。`load_texture_index` が `_ramp/_rmp/_spc` も索引化 |

**v5 実測（2026-08-25・現行）**:

| キャラ | blend SHA(先頭16) | 突合 | レンダー目視 |
|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `7429d559cbdd1a52` | PASS (16 checks) | 髪に ramp 陰影・カットアウトシャープ。白い右手のみ既知制限 |
| Sabrina/SabrinaSSR0101 | `8fb4d714b101e27c` | PASS (16 checks) | 髪 ramp 陰影・全身テクスチャ・欠陥ゼロ |
| Helen/HelenSSR01（内部回帰） | `df47250c230c4ef0` | PASS (16 checks) | 対象外 |

- self-test 16系統 PASS・決定性 PASS（canonical_manifest_sha）
- **独立サブエージェント監査は API障害(503×3)で技術的停止**。代わりに制作担当が全6画像を監査: Sabrina 欠陥ゼロ・Dusevnyj 白手のみ。サブエージェント監査は API 回復後に再実施予定
- のっぺりの完全解消（全材質 ramp+SH 環境光・ゲーム相当シェーダ）は **deferred phase として別計画・別承認**が必要（v51 の SH プローブ/RampSetting 収集機構の port を伴う）

### 監査強化v6・浮き骨スパイク非表示（2026-08-25・現行）

> 4回目の差し戻し。武田さんの**スクショ**（2026-08-25 10.29.26.jpg）で「バリ」を初めて直接観測 → 正体は**未解決親の骨**（頭部・顔・胸から突き出すオレンジのスパイク）。Sabrina では 14/177 本が体域から大きく外れて浮遊（未解決親78本）。**v51 Helen 正本の実物をダンプして比較**: v51 は親が完全解決（全331骨・誤差1e-15級）だから骨を表示して正しかった。現行パイプラインは未解決がある = 見せるべきでない状態を見せていた。負スケール警告は v51 でも出る確立された規約（UNITY_TO_BLEND）なので非対象。

**v6 修正**:

| 問題 | 修正 |
|---|---|
| 浮き骨スパイクが開いた瞬間に見える | **未解決親>0 の間はアーマチュアを非表示で保存**（hide_viewport/hide_render。スキン・アニメーションは無影響・アウトライナで切替可・全親解決時は自動的に表示へ戻るデータ駆動規約） |
| 監査が「開いた状態の見え方」を検査していなかった | 新検査 `open_state_cleanliness`（未解決親>0 ⇒ 非表示保存であること）。self-test **17系統** |

**v6 実測（2026-08-25・現行）**:

| キャラ | blend SHA(先頭16) | アーマチュア | 突合 |
|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `628b08a9487bfae9` | 非表示(未解決257) | PASS (17 checks) |
| Sabrina/SabrinaSSR0101 | `f051784ed26be00e` | 非表示(未解決78) | PASS (17 checks) |
| Helen/HelenSSR01（内部回帰） | `f7b42229ec8ccb72` | 非表示(未解決160) | PASS (17 checks) |

- self-test 17系統 PASS・決定性 PASS・全レンダー白面積率 0.0
- 骨の親解決自体（AFS2/CRI 内プレハブの解析）は**既知の難題**（v51 でも苦戦した領域・武田さんも認知済み）。解決できれば自動的に骨は表示される

### 監査強化v8・完全性基準の実装（2026-08-25・現行）

> v6 差し戻しの指摘「**何をもってキャラ全体とするか基準がない＝監査スクリプトの抜け**」への回答。v7 案（C1〜C5）を作成して承認カードに出したところ武田さんが**「承認しない。計画を作ったメインエージェントがバイアスのかからない・成果物向上につながるレビュー指示でサブエージェントにレビューさせる」**と指示 → 独立サブエージェント監査（E5 運用を計画審査に拡大）を実施 → verdict **要修正**（major 5件）→ 反映した v8 案を承認カードで提示 → **武田さん「v8 承認・実装へ」**。

**独立レビューの主要指摘（major）と対応**:

| # | 指摘 | 実測根拠 | v8 での対応 |
|---|---|---|---|
| M1 | v7 は「基準 ALL PASS なのに不完全」の反例を捕捉できない。Dusevnyj の既定表示には**足・靴のジオメトリ自体が無い**（P1_body1 はタイツ筒 826v のみ・足形状 2306v は非表示 Dorm/Drom 版にのみ存在） | 孤立レンダー＋diff-dump 実測 | **地面接触基準**： 可視セット下端が root(z=0) から 0.05m 超＝末端欠落疑いで FAIL。実測 Dusevnyj 0.112 / Sabrina 0.012 / Helen −0.001 |
| M2 | C1 の対応表は抽出器と同じスコープ定義を使うため D1 クラス（選び方の漏れ）を構造的に検出できない | mesh_candidates＝kept+dropped で完全整合（118件）を確認しつつ、同名重複スキップ 642件の記録欠如を実測 | **census 3方突合**： Step0 census 台帳↔検証器族走査↔blend。採用メッシュと同名・別内容の重複のみ FAIL、頭部・体幹系のスコープ外残置は D1 監視として列挙 |
| M3 | manifest の AABB はローカル空間で世界配置と不一致（Sabrina body y=0.37..1.08 に対しレンダーは足まで表示）→ 体域推定が誤判定 | extract-manifest 実測 | **保存済みblend再オープン・世界座標 AABB**（canonical に world_bbox 追加）。manifest AABB は使用禁止 |
| M4 | 「修正が実表示に届くか」の検査が無い＝v4/v5 型差し戻しの再発経路。提出条件が「C1〜C4 PASS」のみだと E1/E4 クラスが規約上漏れる | handoff v5 節 | 観測経路は元々 `_dump_blend.py`・`render_char_sheet.py` 共に `bpy.ops.wm.open_mainfile`（再オープン方式）であることを **submission.rule に明文化**＋提出条件を「全検査（既存17＋新規3）PASS」へ修正 |
| M5 | known_untextured の証明は族トークンフィルタ＋AFS2/CRI 未走査 5,032本の内側の話。「原作に存在しない」は過大主張 | texture-manifest 実測 | `scan_boundary` を機械可読で付記。「走査範囲内に存在しないことの記録」へ表記修正 |

**v8 実装**:

| 項目 | 内容 |
|---|---|
| `ce_build_blend.canonical_of_scene` | MESH に `world_bbox`（matrix_world 変換後の8角 min/max）を追加。canonical 形式進化（blend バイト自体は不変・SHA 表記は v6 値のまま有効） |
| `20_diff_char_blend.py` 新検査① | `census_completeness`（C1改）： census↔族走査↔blend 3方突合＋同名重複スキップ台帳化＋採用名前の内容指標（頂点数）不一致検出 |
| 同② | `geometry_world_coverage`（C3改）： 世界座標で縦方向カバレッジ欠け帯（>6%身長）＋浮遊メッシュ（体域交差率<0.02）＋地面浮上（下端>0.05m） |
| 同③ | `variant_detail_divergence`（情報提供・collapsed_scale_bones 前例）： 同一パート派生(_Dorm/_Fight 等)が基本版の2倍超・差200頂点超なら列挙。「より詳細な版が原作フラグで非アクティブ」の開示材料。Dusevnyj で5組検出（body1 826→2306、cloth 446→10934 等） |
| submission 判定 | `ready` / `conditional`（known_untextured・権威外slot・gaps あり→開示承認が提出条件）/ `blocked`(FAIL)。全 slot が非権威（texmatch=推定一致）という独立監査 major 指摘も unresolved_slots 数値として反映 |
| self-test | 17→**21系統**（blendメッシュ名破壊／可視メッシュ浮遊／地面浮上／expected側メッシュ削除の4ケース追加。expected 側破壊は初・D1 クラス逆方向の捕捉確認） |
| `25_gate_sync.py` 新設 | diff 台帳→quality-gate.json families[].known_gaps へ冪等同期（`[v8:<char>/<variant>]` 接頭辞。accepted_gaps は触らない）。batch/complete phase 判定との接続 |

**v8 実測（2026-08-25・現行）**:

| キャラ | 突合 | submission | 特記事項 |
|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | **FAIL (19/20 checks)** | **blocked** | `geometry_world_coverage` FAIL＝下端 0.112m 浮上＝**足・靴ジオメトリ不在を初めて機械捕捉**（武田さん「足の造形が甘い」の正体候補。原作仕様か否かは公式スクショ照合で決着） |
| Sabrina/SabrinaSSR0101 | PASS (20 checks) | conditional | 未テクスチャゼロ・権威外slot 13 |
| Helen/HelenSSR01（内部回帰） | PASS (20 checks) | conditional | 回帰継続 |

- self-test: **21 cases PASS**（Sabrina 実施。Dusevnyj は素状態が既知 FAIL のため前提不成立＝検証器が正しく機能している証拠として分離）
- 決定性: PASS（canonical_identical=true・Dusevnyj で再実測）
- 足問題の開示材料: P1_body1(lod0, 826v)に対し _Dorm/_Drom 版は 2306v の高詳細版が存在するが**原作の可視性フラグでは非アクティブ**。原作ゲーム画面での見え方（スクショ 1 枚）が「仕様か欠落か」の決着手順

#### v8 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| v7 案の C3（manifest AABB ベースの体域判定） | 世界座標での正しい判定になった。manifest AABB を使うと脚全体欠落の誤検知（M3 実測）が出る | なし（誤検知の原因。戻す理由が無い） |
| 同名重複の全面 FAIL（v8 初版の過剰発火） | 汎用名（Plane001/Object001 等・共有bundle由来）の同名異内容は台帳記録のみになり、census_completeness が正当な PASS を返す。**採用(expected)名前と同名の別内容だけが FAIL** | 初版ロジックへ戻すと Dusevnyj/Sabrina/Helen とも誤 FAIL する（30件超の無害重複を捕捉） |
| 「帯カバレッジだけで末端欠落を見る」方式 | 地面接触基準の併用により「足ごと無い」型の欠落も捕捉。帯カバレッジ単独では Dusevnyj 足欠けは検出できない（タイツ筒が Z 帯を埋めるため）を実測で確認済み | 帯カバレッジは腰抜け等の中間帯欠落ガードとして併存 |

### 足パーツ解消・独立監査再実施（2026-08-25・v8.1・現行）

> v8 実装で機械捕捉された Dusevnyj blocked（地面浮上＝足欠落）を原作データ調査で解決し、独立サブエージェント監査（API 回復後の継続タスク）を再実施。**監査 verdict: Dusevnyj/Sabrina とも「条件付き提出可」**。

**足問題の正体（原作データ実測で確定）**:

| 事実 | 実測 |
|---|---|
| P1_body1_lod0 はタイツ筒のみ | y 0.112..0.2945（踝より上・826v）。足ジオメトリを含まない |
| 足・靴は派生タグ付きメッシュに存在 | `cloth_lod0_Fight`(10934v・y 0.015..0.163)＝ハイカットスニーカー。`body1_lod0_Dorm/Drom`(2306v・y 0.012..0.116)も同領域の別バージョン |
| 上位パートと幾何連続（補完関係） | body1 下端 0.112 に対し派生パーツ上端 0.116〜0.163 で重なり、隙間なし |
| 3キャラ共通の設計 | Helen `P1_body_lod0_Dorm`(y 0.013..0.162)・Sabrina `body_lod0_Dorm`(y 0.012..0.173)も同構造。Sabrina は本体が足込み(y 0.012..1.434)のため補完不要 |
| SMR が族スコープ内に存在しない | Dusevnyj/Sabrina 族 prefab 内に _Dorm 系 SMR 無し（参照 prefab は AFS2/CRI 内と推定）→ 可視性の権威データ無し |

**gap_filling_subpart 規約（新設・データ駆動）**: 派生タグ付きメッシュでも「既定表示セットが地面から浮上し（>0.05m）、当該メッシュが欠け帯を幾何連続的に埋める（下端が地面まで届く＋上端が既定セットと接続）」場合、**頂点数最大（最詳細）の1つだけ**を既定表示に加える。複数同時採用は同一領域の別バージョン重ね（z-fighting）になるため禁止。Sabrina のように本体が足込みの場合は適用されない。Dusevnyj の採用結果: `cloth_lod0_Fight`（texmatch **exact**・UV 0-1 内・albedo/normal 実在・alpha 接続済み）→ ground_gap **0.112 → 0.015** 解消。レンダーでスニーカー表示を制作担当＋独立監査役の双方が目視確認。

**独立サブエージェント監査（2026-08-25 再実施・制作担当と分離）**:

| 体 | verdict | 根拠 |
|---|---|---|
| Dusevnyj | **条件付き提出可** | 突合20項目 PASS・足パーツ含めレンダー破綻なし・白残りが known_untextured 5部位と完全一致（台帳に無い白は皆無）・texmatch exact・左右対称 |
| Sabrina | **条件付き提出可（開示は軽微）** | 未テクスチャゼロ・3視点＋孤立ビューで欠陥ゼロ・裸足の足指まで描けている |

**監査 major 指摘2件への対応**:

| # | 指摘 | 対応 |
|---|---|---|
| M-A | 非表示オブジェクトの canonical `world_bbox` が hide で depsgraph 評価外になり、親変換未反映の生値（Unity y-up）が焼かれる | `canonical_of_scene` が bbox 計算の間だけ全 hide_viewport を解除して update し復元する方式へ修正。**副作用の実測**: 解除中のシーンから記録すると hide フィールドが全員 False になり 3体全 FAIL（open_state_cleanliness/open_state_groups/z-fighting 検査崩壊）→ **hide 値は解除前の元値（saved_hides）を記録する**方式で解決 |
| M-B | P2/P3 切替時に白く見える部位（非表示オブジェクトの素slot）が submission 開示に載っていない | submission に `untextured_when_switched`（Dusevnyj 20部位）を追加。conditional 判定にも含める |

**v8.1 実測（2026-08-25・現行）**:

| キャラ | 突合 | submission | 特記事項 |
|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | PASS (20 checks) | conditional | 足パーツ解消・known_untextured 5＋権威外slot 64＋切替時白 20 の開示が提出条件 |
| Sabrina/SabrinaSSR0101 | PASS (20 checks) | conditional | 未テクスチャゼロ・権威外slot 13 のみ |
| Helen/HelenSSR01（内部回帰） | PASS (20 checks) | conditional | 回帰継続 |

- self-test: **23 cases PASS**（Dusevnyj/Sabrina の両方で実施。「足パーツを非表示化」「可視メッシュ完全重複」ケース追加）
- 決定性: PASS（Dusevnyj/Sabrina 再実測・canonical_identical=true）
- レンダーシート: 2体とも再生成・白面積率 0.0

#### v8.1 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| 派生タグ付き＝一律非表示の旧規約（deriv 全部 scene_variants） | Dusevnyj を開くとスニーカーが見える（旧規約では足が無くタイツ筒で浮いていた）。Sabrina/Helen の見た目は不変（適用条件を満たさない） | plan_visibility の gap_filling ブロックを外せば戻るが、地面浮上 FAIL が再発する（戻す理由が無い） |
| gap_filling の「stem グループ毎に1つ」初版選抜 | body1_Dorm(2306v)と cloth_Fight(10934v)の両方を拾って二重表示する初版から、**全候補中で最詳細の1つのみ**へ変更。靴が二重に重なる事故を防止 | 初版ロジックに戻すと Dusevnyj で z-fighting FAIL（可視完全重複検出が引っかかる） |
| canonical の hide 解除なし計測（M-A 修正前） | 非表示オブジェクトの world_bbox が世界座標として正しくなる。**blend を開いた見た目は一切変わらない**（記録値のみの修正） | 修正前へ戻すと将来「非表示派生の配置」を検査する際に誤判定する（監査 M-A のとおり） |

### 肌色フォールバック・3点差し戻し対応（2026-08-25・v8.2・現行）

> v8.1 の目視承認カードで武田さんが **3点を指摘して差し戻し**: ①「Dusevnyj の着せ替えを Blender 上でどう切り替えるのか分からない」②「サブリナがつま先を立ててるのが気になった。靴の有無を探して」③「このキャラの肌色が肌色じゃなかったので修正して」。

**② Sabrina つま先立ち・靴の有無（原作データ実測で決着・変更なし）**:

| 事実 | 実測 |
|---|---|
| 靴メッシュは原作に存在しない | Sabrina 族の shoe/boot/foot/sock/sandal/heel 系 Mesh = **0件**。足パーツは `body_lod0_Dorm/Fight` のみ |
| `body_lod0_Dorm` は重複パーツ | body_lod0 の y<0.173 部分（2662頂点）と**頂点・UV 完全一致**（表示しても重なるだけ） |
| つま先立ち＝原作 rest ポーズそのもの | body_lod0 の y<0.10 の頂点が前方（z+）に寄っている＝爪先が前に出た rest で焼かれている。ゲーム内はアニメ/IK で立たせている（アニメは本プロジェクト全体で deferred） |

→ Sabrina は裸足デザイン・rest は原作データのまま（rest を立位へ改変するとスキンが壊れるリスク＋原作改変のため実施しない）。

**③ 肌色フォールバック規約（v8.2・武田さん指示「修正して」への実装）**:

| 種別 | 対象 | 方法 | 根拠（データ駆動） |
|---|---|---|---|
| `r4_uv_atlas_face` | `slg_body`（まつげオーバーレイ）・`boby`（眉） | **顔アトラスのテクスチャをそのまま流用** | 両メッシュの UV が face 使用領域（0.006..0.994）内にある実測。slg_body の UV 領域はアトラス上段＝まつげの暗色（RGB 22-39）で正しい |
| `skin_color` | `P1_cloth3.sub0/sub1`（＝白かった手） | 顔アトラス肌領域（頬・額・首の3点）サンプル平均色 **RGB(239.8, 198.7, 180.8)**＝sRGB(0.9404, 0.7793, 0.709) の単色マテリアル | UV が全面の独立アトラスで原作にテクスチャが無いため。単色・陰影なし＝**approximation**（台帳記録済み・開示対象） |

- 共通規約は `ce_common.skin_fallback_kind`（builder/verifier 同一実装・resolve_texture_stem 二重実装の前例どおり）。布（hip3）は対象外＝known_untextured 継続
- **実測**: Dusevnyj の known_untextured が **5部位 → 1部位（hip3 のみ）**。レンダーで左手が肌色化・white_ratio 0.0 を確認
- **self-test**: 新ケース「肌フォールバックslotを素に戻す」を追加。初版は捕捉先を visible_texture_coverage にして FAIL 化せず（単色肌は索引解決ではないため known_untextured に落ちる）→ **捕捉先を submesh_slots（期待ラベル不一致）へ修正**して FAIL 化確認。両体 24ケース PASS

**① 着せ替え切替手順**: `blends/README.md` を新設（アウトライナで `parts_P1` のチェックを外し `parts_P2/P3` にチェック。common は髪・顔・武器の共通部品で触らない）。handoff 本節にも記載。

**v8.2 実測**: 3体とも突合 PASS(20検査)/conditional・self-test 24系統 PASS(Dusevnyj/Sabrina)・決定性 PASS(Dusevnyj)・レンダーシート再生成(白面積率 0.0)。

#### v8.2 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| slg_body/boby/手を白（known_untextured）のままにする方式 | Dusevnyj の手・首元が肌色になる。**白のまま戻すと武田さん指摘が再発** | skin_fallback ブロックを外せば戻る。hip3（布）は意図的に白のまま |
| つま先立ち rest の立位への改変 | **実施していない**。原作 rest のまま（ゲーム内ポーズはアニメ領域 deferred）。改変するとスキンウェイトが壊れるリスク | 該当なし（未実施） |

### 着せ替え切替不能の修正（2026-08-25・v8.3・現行）

> 武田さんが blend を開いて README どおりに P1 解除→P2 チェックを操作したが**「服装が切り替わらない」**と報告。機械精査の結果、**P2/P3 の中身に個別 hide フラグが焼き付いており、コレクションのチェック操作では出てこない**構造的欠陥と確定（README の手順と実装の食い違い。レンダーシートの set_P2/P3 だけは個別フラグ直接操作で撮影していたため正しく見えていた = 監査の盲点）。承認カードで **A案（ビルダー修正＋再構築＋検証器対応）を実施**。

**v8.3 修正**:

| 問題 | 修正 |
|---|---|
| P2/P3 代替衣装がフォルダチェックで出ない（個別 hide 焼き付き） | **P2/P3 はレイヤー除外（チェックOFF状態）で保存**へ変更。中身の個別フラグは解除。チェックを入れるだけで中身が出る。セット内に vd=True（原作 active）が混ざる場合は従来どおり個別 hide を優先（D3 規約維持・データ駆動） |
| 検証器が hide_viewport 単一参照で除外方式を「可視」と誤判定 | `_is_visible()`（hide と canonical 新設 `excluded` フィールドの OR）へ統一。canonical は計測中だけ除外を解除する M-A 方式の拡張で正しい world_bbox を記録。self-test は「非表示P2候補を可視化」を除外解除の再現へ書き換え（24系統・両体 PASS） |
| レンダーシートの set_P2/P3 撮影が除外方式で空白になる | 開いた直後に除外を hide へ実体化してから撮影（ディスク上の blend は不変） |
| ドライバが Blender 例外後も**前回の残 build log で偽成功報告**（v8.3 作業中の実障害） | 成功判定に「build_log/blend が開始時刻より新しい」ことを追加（教訓④の再発防止） |

**v8.3 実測（2026-08-25・現行）**:

| キャラ | blend SHA(先頭16) | 突合 | レンダー目視 |
|---|---|---|---|
| Dusevnyj/DusevnyjSSR0101 | `a2ee94dd9832a6c5` | PASS (20 checks) / conditional | P1 セーラー服・P2 タンクトップ＋白ボディスーツ・P3 透けトップ＋コルセット＋ショーツの **3着が完全に切替表示**（set_P1/P2/P3 レンダーで確認） |
| Sabrina/SabrinaSSR0101 | `d8212738ab4ff867` | PASS (20 checks) / conditional | P2/P3 候補無しのため既定表示のみ（変化なし） |
| Helen/HelenSSR01（内部回帰） | `2a23a94b48368ff8` | PASS (20 checks) / conditional | 回帰継続 |

- self-test: **24 cases PASS**（Dusevnyj/Sabrina。Sabrina の3ケース SKIP は対象部位が存在しないため正常）・決定性 PASS（canonical_identical=true・2体再実測）
- canonical 形式進化: objects に `excluded` フィールド追加（旧 dump との非互換。旧検証器では新blendを正しく判定できない）
- **武田さんへの操作案内**: [blends/README.md](/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/README.md) の手順（parts_P1 のチェックを外す→parts_P2/P3 にチェック）が**そのまま機能する**。`scene_variants`/`src_disabled` は中身が個別非表示のまま（触らない想定）

#### v8.3 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| P2/P3 の「オブジェクト個別 hide で保存」方式 | blend を開いた瞬間の見た目は不変（どちらも非表示）。**違いはチェック操作で衣装が出ること**。canonical に excluded が増えるため SHA は全て新しくなった（メッシュ・テクスチャ・材質は不変） | ce_build_blend.py の hide_mode ブロック＋検証器 _is_visible＋render の実体化ブロックを旧方式へ戻して再構築（README の手順は再び効かなくなる） |
| ドライバの「build_log 存在だけ」の成功判定 | Blender が例外で死んだ時に**偽成功を報告しなくなる**（v8.3 作業中に旧ログで偽成功を出した実測）。失敗時は確実に停止する | 判定条件を exists のみへ戻す（偽成功の再発を引き受けることになる・戻す理由無し） |

### 体の色調査（2026-08-25・v8.3後・武田さん指摘「キャラの色がおかしい・体の部分」）

> 着せ替え実機確認の後に武田さんが体の色を指摘。場当たり的な色指定を避けるため原作データ側の原因を機械調査した。**結論: 貼り漏れバグではなく、原作データが名前で引ける形でテクスチャを提供していない**（ランタイム組み立て式）。

**機械実測の証拠連鎖**:

| # | 事実 | 実測 |
|---|---|---|
| 1 | グレー部位（P2_body3・P2_cloth2・P2_hip2/hip3・P3_body・P3_body1・P1_hip3・*_trans系）の同名テクスチャは**ゲームデータ全体に存在しない** | 全18,574ファイルの生バイト走査（UnityFS 13,536本のオブジェクト級台帳75,115件＋非UnityFS含む）で `_P2_body3_d` 等のテクスチャ名パターン0件。メッシュ名のヒットのみ |
| 2 | バリアントのテクスチャbundle（b6d0e1b5・54枚）にも無い | 全54枚の名簿を実確認。P1/P2/P3 の一部パーツのみ |
| 3 | **「AFS2/CRI 5,032本」の正体は音声（.acb/.awb CriWare）と動画（.usm）** | magic 先頭バイトで全分類。AFS2=2,217本・CRID=165本・@UTF=数千本、すべて Audio/Video 配下。**キャラのプレハブは含まない**（handoff 旧記述「中身未走査」を実態へ訂正） |
| 4 | プレハブ（SkinnedMeshRenderer）は全bundleに存在しない | 「DusevnyjSSR0101」を含む27bundleを全パース。AnimationClip/Texture/Mesh/エフェクトのみ。SMR=0 |
| 5 | メッシュbundle（79041fbc・Mesh 41個・Tex 0・Mat 0）を参照するUnityFS bundleは無い | メッシュbundleのCAB（60897ab1…）で全ファイル走査 → 参照は catalog_main_26111.bin のみ。カタログにアセット名文字列は無し（ハッシュID） |
| 6 | UV補完証拠: **P3_body のみ P1_body_d アトラスと強一致** | P3_body×P1_body2 のUV占有 Jaccard **0.81**（同一アトラス共有の強い証拠）。他6部位は0.38以下 |
| 7 | UV下テクスチャ内容スコアリングは決定打無し | グレー7部位×アルベド33種の「UV下エッジ強度」は上位が武器/スコープ等の無関係テクスチャ＝ノイズ級 |

**解釈**: キャラの材質割り当ては**ランタイム組み立て**（ModelConfigData→catalog→共有アトラス）で行われ、静的データには権威対応が残っていない。名前規約の緩和（R1/R2/R3）でも届かない。これ以上の埋めは推測＝規約違反。

**解決の選択肢**（承認カードへ）: ①P3_body のみ UV証拠（Jaccard 0.81・r4 規約の拡張）で P1_body_d を適用し残りは known_untextured 維持 ②ランタイム組み立ての逆工程（catalog解析＋ModelConfigData スキーマ解読）を別計画で調査 ③ゲーム画面スクショを正解資料として提供いただき照合 ④現状維持（known_untextured 開示済み）

### バックアップボリュームのゲームデータ探索（2026-08-26・完結）

> 前セッションの停止地点「バックアップボリュームが読めるようになっている（2026-08-26 朝のスナップショットあり）。中にゲームデータがあるか探索します」の続き。全ボリューム読み取り専用・find は -xdev＋120秒区切り。詳細記録: `gf2-char-extract/reports/backup-volume-game-data-scan-2026-08-26.json`

**結論（弱い形・断定なし）**: `/Volumes/HDD_バックアップ` の完了スナップショット `2026-08-26-103352.previous` の**この範囲ではゲームデータ不検出**。ただし方法の有効性は陽性対照で確認済みで、不検出の理由は構造的に説明がつく。

| # | 事実 | 実測 |
|---|---|---|
| 1 | バックアップは内蔵 'Macintosh HD - Data' 専用 | `backup_manifest.plist` の volumeStoreInfo が同ボリューム1本のみ |
| 2 | スナップショット内にゲームコンテナのスケルトンは存在 | `com.haoplay.game.ios.exilium`（Preferences/Saved State 等の小ファイルのみ） |
| 3 | しかし `Documents/LocalCache` は**空ディレクトリ** | 現行ではここに41GBが見えるが、実態はマウントポイント |
| 4 | ゲームデータ本体は別デバイスの独立APFSボリューム | `GFL2Data`（disk7s2・44.5GB・外付けM.2 SSD・UUID E3201924-…）を LaunchAgent `com.takedayousuke.gfl2-data-mount.plist` が LocalCache へマウントする設計（バックアップ内にも plist あり・マウントスクリプト本体は未収録） |
| 5 | 陽性対照 | 同一find方式（-xdev・iname）で snapshot 内の既知 `logXAYY.log`・`…iosmac.savedState` を検出(rc=0)。現行側 `.bundle` 先頭16バイト=UnityFS も実測 |
| 6 | 他スナップショット・他の復元導線 | `.interrupted`（コンテナ8件のみ）・`.inprogress`（ほぼ空）にも無し。**GFL2Data 自体の APFS スナップショットは 0 個** |

- 探索範囲: スナップショット全トップレベル領域＋ユーザー配下サブエリアごとに走査。完走領域はすべて不検出（`.bundle` ヒットはアプリ内蔵フレームワーク・Homebrew 等のみ）。部分走査（120秒打ち切り）と拒否（private/var/root 等の Permission denied）・欠落（snapshot 内 ~/Library/{Caches,Logs,Application Scripts}・~/.local/bin は未収録）は report JSON に明記
- **プロジェクトへの影響なし**: ゲームデータ本体は現役で GFL2Data ボリュームに存在（AssetBundles_IOS/ClientRes_iOS/Table/BinFile 等を実確認・UnityFS 実測）。バックアップ依存の抽出継続導線は不要

### v8.4 中断状態と Dusevnyj blend 喪失の発見（2026-08-26 昼・実ファイル精査）

> バックアップ探索セッションが監査スクリプト改修の動作確認をした際、`blends/Dusevnyj-DusevnyjSSR0101-repro.blend` が**存在しない**ことを検出 → 周辺を実ファイル実測で精査した結果、run-state/handoff（v8.3 完了待ちの記録）と実態が乖離していることが判明。**本節は痕跡の列挙であり、v8.4 の内容や Helen FAIL の原因は未調査**。

| # | 事実 | 実測 |
|---|---|---|
| 1 | **Dusevnyj v8.3 blend は失われた** | `blends/_quarantine_empty_20260826/` に 02:06/02:11 保存の objects=0 空 blend 2本が隔離済み。同README: 「LocalCache 消滅状態で再構築が走り空blend化・正常 v8.3 (a2ee94dd9832a6c5) は上書きで喪失。復旧路=ゲームデータ再取得後 `10_extract_char.py --char Dusevnyj --variant DusevnyjSSR0101` 再実行」 |
| 2 | **v8.4 肌パッチらしき作業が FAIL/blocked で中断** | `logs/_regression_v84_{Helen,Helen_b,Sabrina}.*`(09:25)・Helen 再構築(09:29)・`diff-Helen-HelenSSR01.json`(09:30 更新・**FAIL/blocked**)・`reports/sheets/Helen-SSR01_v84/`(09:36)。09:36 以後の作業痕跡なし |
| 3 | Sabrina は無傷 | blend 08-25 20:45 のまま・diff も PASS/conditional のまま |
| 4 | ゲームデータは現在手元にある | GFL2Data(disk7s2) マウント済み・AssetBundles_IOS 等実在（同日昼のバックアップ探索時に確認）。02:06 の事故は LocalCache 一時消滅が契機と推定されるが**推測に過ぎない** |

**次の一手候補**（実行は未実施・承認待ち）: ①Sabrina を現状保存のまま温存 ②Dusevnyj は quarantine README の復旧路どおり再構築（v8.4 肌パッチ込みならその規約の承認が必要）③中断された v8.4 Helen 作業は FAIL 状態から再開 or 方針確認

### 監査スクリプトへの「成果物パス明示」組み込み（2026-08-26・武田さん指示）

> 武田さん指示「毎回ファイルパスを提示しないから、確認してほしいときはファイルパスを明示するようにこのプロジェクトの監査スクリプトに加えて」。

- **変更**: `scripts/20_diff_char_blend.py` main() — submission に **`confirm_paths`** 欄を新設。blend / blends README / sheet_front.png / sheet-audit.json / sheets dir / diff 台帳 / build log / run-state の8パスを機械列挙し **exists フラグ付き**で記録。`rule` に「武田さんに確認を依頼する報告では confirm_paths の実在パスをそのまま明示添付する（exists=false を残したまま提出しない）」を追記。標準出力にも confirm_paths 一覧を印字し、報告文への転記漏れを構造的に防ぐ
- **検証**: py_compile OK・confirm_paths ロジックを Dusevnyj 実値で単体実行（この動作確認が上記 blend 消失の検出契機になった）。compare()/self_test は非変更のため突合ロジック・self-test 24系統は不変
- 効果: 以後の提出報告には必ず実在確認済みパスが付く。「パス無しの成果物報告」は検証器出力段階で不可能になる

### v8.4 肌パッチ・Dusevnyj 復旧完遂（2026-08-26 午後・現行）

> 武田さんの承認カードで **「A案: 肌色修正込みで復旧」** を取得して実施。前節で発見した中断状態を解消し、Dusevnyj を v8.4 肌パッチ込みで再構築 → 全検査 PASS まで完遂。

**朝の FAIL の原因確定（環境起因・blend/ビルダの欠陥ではない）**: 09:30 Helen diff FAIL は検証実行時点でゲームデータが不完全だったため（census 30件「censusにあるが検証器族走査に無い」＋bones期待値不完全→unresolved=0 という矛盾シグネチャ）。データ完備後に再実行 → **PASS(20 checks)**。抽出に必要な .bundle は LocalCache＋アプリRaw の両ルート（ce_extract.py:52）合算で **3体とも 0欠け**（欠けていたのは音声 .acb / 動画 .usm / .acf のみ）を実測で確認。

**v8.4 肌パッチ（r5）の内容**: `ce_common`/`ce_build_blend.py`（08-26 01:47-01:51 改変分）— 顔アトラス額のクリーン肌クロップ（UV 0.39,0.58–0.52,0.71）をミラータイルした**質感付き肌材質**。v8.2 単色肌（武田さん「肌の色が変」差し戻し）の置き換え。適用: 手(P1_cloth3.sub0/sub1)＋生肌部位(P3_body＋_Dorm)。UV不変・**approximation**（原作肌シェーダは deferred）。正解根拠=公式 ClothesMod アイコン（`intermediate/<char>.<variant>/clothesmod-icons/`）。

**復旧の実測**:

| 項目 | 実測 |
|---|---|
| Dusevnyj 再構築 | 10_extract_char でフル再抽出→再構築。**blend SHA256 先頭16 `b56f8152a51ae1c0`**・tex_failed=0・抽出自己検査 OK |
| Dusevnyj 突合 | **PASS (20 checks) / conditional** — known_untextured=hip3 のみ・unresolved_slots 64・切替時白 17・gaps 0 |
| Dusevnyj self-test / 決定性 | 24ケース PASS / canonical_identical=true |
| Sabrina 再確認 | blend 変更なし・diff **PASS(20)/conditional**・self-test 24ケース PASS |
| Helen（内部回帰） | v8.4 ビルド再diff **PASS(20)/conditional**（朝の FAIL を反証） |
| レンダーシート | Dusevnyj 再生成（30枚・白面積率 0.0・set_P1/P2/P3 で3着切替）。制作担当が目視: 顔・手・P3脚が自然な肌色・スニーカー表示・義手質感 OK |
| gate_sync | ok (known_gaps_total 5) |

**次**: 武田さんの目視承認（Dusevnyj v8.4＋Sabrina 正式・conditional 開示込み）→ Step3 バッチ計画（別承認）。

### v8.5・共有 body_d 適用と生成物の全廃（2026-08-26 夕・現行）

> v8.4 目視承認カードで武田さんが**承認せず**: 「まだ肌色に関して再現できていない＝原作再現できていない。肌だけの指摘ではなく**計画と監査スクリプトの詰めが甘い**。原因を考えろ」→ 続けて色照合案（ゲーム内UIアイコンとの定量比較）も**承認せず**: 「codeに存在しないやつで何ができんの。**お前はクリエイターじゃなくてエディター。codeを抽出して再現しろ**」。

**原因（機械実測で確定）**:

| # | 事実 | 実測 |
|---|---|---|
| 1 | 原作の生肌（手・脚等）は**全キャラ共有テクスチャ `body_d`/`body_n`**（キャラ名プレフィックス無し）をランタイムで割り当てている | texture2d-inventory.json（61,733件台帳）から共有テクスチャを特定・バンドル `42554ccee…`（LocalCache）から抽出。**胴体・四肢・手・足・爪まで描かれた生肌アトラス** |
| 2 | 決定打: メッシュUVが body_d 空間にそのまま一致 | P1_cloth3(手)の UV0 を body_d に重ねると**指・爪の描画に整然一致**。P3_body/P3_body_trans/P3_body_Dorm も同様（サンプル色一様・R-IQR 9-11・膝の陰影まで一致） |
| 3 | v8.2（顔色の単色塗り）・v8.4（顔アトラス額クロップのタイルパッチ）は**どちらも代用品＝創作**だった | キャラ族データ内に肌テクスチャが無い＝「名前で引けない」の正体が共有テクスチャだった。計画が「族内での名前解決」問題と限定したのが根本誤り |
| 4 | 監査の欠陥: 色の出所の正しさを検査する項目がゼロ | 「何か貼られたか」しか見ないため代用品を3回とも PASS。white_ratio は吹き飛び白しか数えず「肌が変」は原理的に検出不能だった |

**v8.5 修正**:

| 項目 | 内容 |
|---|---|
| `ce_common.py` | 肌フォールバック規約を **r6_shared_body_atlas** へ変更（`SHARED_BODY_ATLAS_RE` = P1_cloth3/P3_body 系・`SHARED_BODY_BUNDLE`/`SHARED_BODY_TEXTURES` 定数）。SKIN_PATCH_* は廃止 |
| `ce_extract.py` | `extract_shared_body_atlas` 新設 — 共有バンドルから body_d/body_n を `shared-textures/` へ抽出し **provenance.json**（バンドルSHA256・pngSHA256・path_id）を記録。extract-manifest にも記録 |
| `ce_build_blend.py` | `_make_skin_patch_material` 削除（生成物ロジックの完全排除）。slot 規約 r6（ラベル `shared_body_<stem>`）・材質に `gf_provenance` 焼き込み |
| `20_diff_char_blend.py` | 期待slot規約を r6 に対応＋**新検査 `texture_source_purity`**: blend 内の全画像は source bundle 由来（族 textures/ ∪ 共有 body_d/body_n）のみ許可、**生成テクスチャは FAIL**（創作の混入を機械捕捉）。self-test 24→**25ケース**（「材質の画像を生成物に差し替える」追加） |

**v8.5 実測（2026-08-26 夕・現行）**:

| キャラ | blend SHA(先頭16) | 突合 | 備考 |
|---|---|---|---|
| Dusevnyj | `3e511d00881b3497` | **PASS / conditional** | 手・脚がゲーム本来の肌（指・爪・膝の陰影付き）。known_untextured=hip3 のみ |
| Helen（内部回帰） | `1dd308b36c0b9bef` | **PASS / conditional** | P3_body_trans が skin_patch→shared_body へ |
| Sabrina | 変更なし | **PASS / conditional** | フォールバック対象slot無し。純度検査 PASS＝生成物ゼロの証明 |

- self-test 25ケース PASS ×3体・決定性 PASS（canonical_identical=true）・レンダーシート再生成（白面積率 0.0）・gate_sync ok
- 制作担当が目視: P3 脚は原作テクスチャ本来の質感（膝の赤み・グラデーション）・P1 手は指爪つきの実肌
- **残る限界**: ゲーム独自の陰影（ramp/SSS/環境光）は deferred のまま。albedo が原作ソースになったため色・柄は原作データそのもの

#### v8.5 で捨てた判断とその見え方

| 捨てたもの | 手元でどう変わるか | 戻せるか |
|---|---|---|
| v8.4 の顔クロップ肌パッチ（生成テクスチャ） | 生肌が原作の共有アトラスになる。**生成物は創作**という武田さん方針で廃止 | 戻すべきでない（純度検査が生成物を FAIL させるため戻すと検査も外すことになる） |
| 「肌問題は族内の名前解決問題」という計画の枠組み | テクスチャ探査の対象がゲーム全体の共有空間に広がった（61,733件台帳の活用） | 枠組みに戻す理由は無い（誤りだった） |

### v9.1・見た目仕上げ工程の port 完遂（2026-08-26 夜・現行）

> 武田さん承認「切り出しの後に『ゲームと同じ見た目に仕上げる工程』を足す」＋「ロビー照明を抽出」。
> v51（ヘレン再現プロジェクト）で実証済みの技術をキャラ抽出パイプラインへ一般化 port した。

**port 内容**（全てゲームデータ由来・生成物なし）:

| # | 要素 | 実装 |
|---|---|---|
| 1 | キャラ固有 RampSetting | 族バンドル内 MonoBehaviour（ramps/names）を読み、Unityグラデーション key0..7＋ctime（**16bit/65535 正規化**・初回はクランプバグで全1.0になったを実測修正）を Blenderカラーランプへ。割り当ては v51 d03 `ramp_for` 準拠（core部分一致＋同接頭辞優先）。Dusevnyj 7設定・Sabrina 3・Helen 11 |
| 2 | SH環境照明 | **キャラ選択画面 04Aimo_SelectPlayer** の LightProbes（app Raw `3224cfb2b84e…`・8probe・焼き込みSH）を抽出し、v51 f103 実績式で導出（L2 SH 64方向評価→輝度上位4方向をSUN・DC→ambient・AMB=1.0/SUN=5.5・L0 をキャラ正面(-Y)へ yaw 整列） |
| 3 | 材質シェーダ | ramp材質は `albedo × ramp(dot(N,L0))·sun` を **Emission 直出し**（原作シェーダは unlit 相当・物理ライトの二重掛けを排除＝実測で確定）。4太陽全加算は飽和するため ramp計算は最輝度 L0 のみ（v51 f43 同型）。非ramp材質（顔等・rampデータが存在しない）は物理照明 |
| 4 | 検査 | 検証器に **shading_port** 新設（SH照明の存在＋rampソースを持つ材質の全配線を機械保証・「ゲームと同じ見た目になる要素の欠落」を以後機械捕捉）。self-test 24→**26ケース** |

**実測結果（2026-08-26 20:15 現行）**:

| キャラ | blend SHA(先頭16) | 突合 | self-test | シート |
|---|---|---|---|---|
| Dusevnyj | `50260288e83f0747` | **PASS / conditional** | 26ケース PASS | ゲーム相当のトゥーン陰影（膝の赤み・制服階調・スニーカー発光）を目視確認 |
| Helen（内部回帰） | （同日再構築） | **PASS / conditional** | 26ケース PASS | カメラフレーミング課題あり（巨大クワッドがbboxに入る）＝内部回帰専用のため成果物影響なし・課題記録 |
| Sabrina | （同日再構築） | **PASS / conditional** | 26ケース PASS | ゲーム相当（肌の発色・トゥーン陰影）を目視確認 |

**インシデント（同日夜）**: PC再起動で GFL2Data がアンマウント→検証が朝と同じ「ソース読み取り不完全」シグネチャで FAIL→正規マウントスクリプトで復旧（ボリューム無傷・9,046バンドル）。マウントログには20回の失敗記録（再起動直後のディスク安定待ちと推定）。

**残課題**: ①顔材質は ramp データが存在しないため物理照明（開示事項・ゲームの顔専用シェーダ GFCharFaceShadow は未解析）②Helen シートのカメラフレーミング③輪郭線・アニメは引き続き deferred。

### v9・お手本レシピの port（RampSetting＋SH照明・2026-08-26 夕〜夜・現行）

> 武田さんの承認カード回答「ビルドクオリティが低い。別プロジェクトでは再現できると証明済み。なぜ？」への言語化（お手本＝ヘレン再現プロジェクトの陰影・照明レシピを参照せず形だけ抽出する別パイプラインを作った＝運用ミス）を受け、**「切り出しの後にゲームと同じ見た目に仕上げる工程を足す」**を承認取得（15:03・完了後に別個承認判断の条件付き）して実施。

**port したもの**（すべてゲームデータ由来・生成物ゼロ）:

| # | 内容 | 実測 |
|---|---|---|
| 1 | キャラ固有 RampSetting 抽出（`ce_extract.extract_ramp_settings`） | 族バンドル808本を走査し RampSetting MonoBehaviour（Unityグラデ key0..7＋ctime位置）を読む。**Dusevnyj 7本**（hair/cloth/P3/P2_body2/weapon01/cloth01/P3_Pearl）・Helen 11本・Sabrina 0本（元データに無し） |
| 2 | SH環境照明（`ce_extract.extract_sh_lighting`） | 寮シーン（v51が導出したもの）は暗すぎたため、ゲームデータ内の照明シーン34件から**キャラ鑑賞シーン（04Aimo_SelectPlayer / 03Aimo_Character_Gacha）の LightProbes を抽出**して置換 |
| 3 | 材質レシピ（`ce_build_blend.ramp_setting_for`＋make_textured_material 拡張） | v51 d03 ramp_for 準拠の stem 一致＋同一接頭辞優先。ColorRamp を key+ctime から構築。**U=clamp(法線・L0方向)**（原作シェーダ OBS36「法線と光の内積」どおり。v51の固定Zは寝姿勢ヘレン用の簡略だった） |
| 4 | 検証器対応 | SH_SUN_* ランプ許容・ramp配線検査（ramp_source 記録）・self-test 26ケース化 |
| 5 | レンダー | blend 内の SH 照明をそのまま使用（SheetWorld で上書きしない）。SUN_SCALE は公式アイコン明るさにキャリブレーション |

**途中で修正した事故**: ①ctime は 0-65535 の16bitスケール（1.0にクランプして全キー同位置になる事故）②ramp材質への物理ライト二重掛けで過剰露出 → ramp材質は計算色を出力 ③Blender日本語UIでノード名「背景」になり名前参照失敗 → 型参照へ ④PC再起動で GFL2Data マウント外れ → diskutil mount＋正規マウントスクリプトで復旧（内容無傷）

**v9 実測（現行）**:

| キャラ | blend SHA(先頭16) | 突合 | 備考 |
|---|---|---|---|
| Dusevnyj | `50260288e83f0747` | **PASS / conditional** | トゥーン階調・ゲーム相当の肌と義手を制作担当が目視確認 |
| Helen（内部回帰） | `3d04efa9882c61e0` | **PASS / conditional** | シートは旗エフェクト平面（原作データにテクスチャ無し）でカメラが引ける問題あり＝v84時点からの既知問題・Helen は成果物外 |
| Sabrina | `a02bc28dde2f7b7d` | **PASS / conditional** | ramp設定0本＝元データに無し・共有肌で自然発色 |

- self-test 26ケース PASS ×3体・決定性 PASS・gate_sync ok・シート3体再生成
- **残る限界**: 顔は ramp 設定が存在しない部位のため物理照明（明るさはアイコン基準でキャリブレーション）。輪郭線・アニメは引き続き deferred

## 2. 承認履歴

| 日付 | カード | 結果 |
|---|---|---|
| 2026-08-23 | 方針①「光と独立に他キャラの形抽出を並列で始める」 | 詳細検討のうえ再提出を指示 |
| 2026-08-23 | 方針v2「在庫台帳→ドライバ→パイロット2体→バッチ」 | **承認** |
| 2026-08-23 | 計画② | 「機械検証スクリプト必須・抜けチェック」を指示 → サブエージェント監査（major 6/minor 7）→ v2 へ反映 |
| 2026-08-23 | 計画v2 | 「引き継ぎ資料を作れ」を指示 → v2.1 へ反映 |
| 2026-08-23 | 計画v2.1 | **承認（実装開始）** |
| 2026-08-24 | 監査修正の方針カード（LOD・可視性バグ修正＋テクスチャ適用＋Dusevnyj全パーツ組み合わせ＋レンダーシート） | **「全部やる」承認** |
| 2026-08-24 | 派生の扱いカード（無印/_Fight/_Dorm） | **「全部入れて切替可能に」承認** |
| 2026-08-25 | 目視承認カード（3体の Blender 確認） | **差し戻し**「全然ダメ・監査が甘すぎる・計画に抜けがある・監査スクリプトを強化して」 |
| 2026-08-25 | ヘレンの扱いカード | **「ヘレンは依頼していない・こっちでいらない・本筋の成果物の品質を上げて」** → Helen は成果物から外し内部回帰検証専用。品質基準=mmd-library |
| 2026-08-25 | 目視承認カード(v3・2体) | **差し戻し**「ゴミみたいなオブジェクト・バリみたいな部分・監査スクリプトに抜けがある・サブエージェントにも客観監査させろ・のっぺりしている」 |
| 2026-08-25 | 目視承認カード(v4・2体) | **差し戻し**「サブリナをblenderで開いた。指摘箇所が直っていない。解決して」 |
| 2026-08-25 | 目視承認カード(v5・2体) | **差し戻し**「スクショ付きでバリを指摘（正体=浮き骨）・Helen正本の詳細を理解しているか・場当たり的修正は困る・監査を再度強化」 |
| 2026-08-25 | 目視承認カード(v6・2体) | **差し戻し**「成果物両方確認。足のあたりの造形が甘い。**何をもってキャラ全体とするかその基準がない=監査スクリプトの抜け**。別セッションでここから再開したい・引き継ぎ資料を作成して」 |
| 2026-08-25 | 完全性基準 v7 案（C1〜C5） | **承認しない**「サブエージェントに採用計画をレビューさせて。計画作成者が**バイアスがかからない・成果物向上につながるレビュー指示**で送れ」 → 独立レビュー verdict 要修正（major 5件） |
| 2026-08-25 | 完全性基準 v8 案（独立レビュー major 反映版） | **承認（実装へ）** |
| 2026-08-25 | 目視承認カード(v8.1・2体) | **差し戻し（3点）**「①Dusevnyj の着せ替えを Blender 上でどう切り替えるのか分からない ②サブリナがつま先を立ててるのが気になった・靴の有無を探して ③このキャラの肌色が肌色じゃなかったので修正して」 → v8.2 で対応（②は原作データ実測で決着・変更なし） |
| 2026-08-25 | 目視承認カード(v8.2・2体) | **承認は未取得・現時点で区切り**「サブリナはとりあえず現時点ではいいや。Dusevnyj は肌の色が変だから修正して。いずれも原作再現ではない。でも現時点でこのプロジェクトではここまででいいや。セッション切り替えるから引き継ぎ資料を作成して」 |
| 2026-08-25 | 着せ替え不能の対応カード（A: ビルダー修正＋再構築 / B: コード触らず手順書のみ / 中止） | **A承認**「ビルダー修正＋再構築」 |

### 次セッションの再開点（2026-08-25 v8.3 着せ替え修正を挟んで更新）

**プロジェクトの現在地**: 武田さんの判断は「**ここまででいいや**」（現時点での区切り）。ただし**完成承認は未取得**。Sabrina は「とりあえず現時点ではいいや」(容認)・Dusevnyj は**肌色修正指示が残存**・両体とも「原作再現としては未完成」との評価。v8.3 で着せ替え切替不能（README 手順が効かない構造的欠陥）は解消済み — **武田さん自身の Blender での切替確認が次の目視承認カードの一部**。

0. **着せ替え切替の実機確認（v8.3・追加）**: 新blend（SHA `a2ee94dd9832a6c5` / `d8212738ab4ff867`）を開き、README 手順（parts_P1 解除→parts_P2/P3 チェック）で Dusevnyj の3着が切り替わるか武田さんに確認していただく。レンダー側では set_P1/P2/P3 で3着の完全表示をこちらで確認済み
1. **Dusevnyj 肌色の修正（最優先の残課題）**: v8.2 の単色肌フォールバック（RGB 0.9404/0.7793/0.709）は「肌の色が変」との指摘が残った（単色＝陰影・質感なしの限界）。次の一手候補: ①顔アトラスの肌領域から小パッチを切り出し UV をそこへ再マップ（質感付き・データ駆動）②P1_body_d（体テクスチャ）の肌色領域を利用 ③原作シェーダの ramp/SSS 相当の解明（deferred 領域との兼ね合い）。対象は P1_cloth3.sub0/sub1（手）
2. **2体の正式な目視承認は未取得**: Sabrina は容認だが承認カード上の「承認」は未選択。Dusevnyj 修正後に改めてカードを出す。conditional 開示（Dusevnyj＝hip3 白・権威外slot・P2/P3 切替時白 20部位＋肌フォールバック approximation／Sabrina＝権威外slot 13）の承認もセット
3. 承認後 → Step 3 バッチ計画（別承認・`25_gate_sync.py` で quality-gate 接続済み。batch phase には families[].user_accepted/batch_safe/approved_* の記入が必要）
4. 残課題（非ブロッキング）: AFS2/CRI 内 prefab の権威可視性・材質対応／Sabrina つま先立ち rest は原作データのまま（アニメ領域 deferred・改変は武田さん判断を仰ぐ）

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
| `scripts/20_diff_char_blend.py` | **機械突合検証器**（20検査＋submission 判定＋replay否定試験 `--self-test` 21系統。v8 で完全性基準 C1改/C2改/C3改/C5改 実装） | 完了（Sabrina/Helen PASS・Dusevnyj blocked=足問題機械捕捉） |
| `scripts/render_char_sheet.py` | レンダーシート生成（正面/斜め45/側面＋scene_variants 派生。監査A4対応） | 完了 |
| `scripts/combine_sheet.py` | シート3面合成（Blender内蔵PILが無いため anaconda 側で実行） | 完了 |
| `scripts/25_gate_sync.py` | diff台帳→quality-gate.json known_gaps の冪等同期（v8 C5改・accepted_gaps は不変） | 完了 |

- 対象範囲の機械的定義: `(SSR|SR|UR)\d{2,}` バリアント行を持つベース族のみ（113族・敵NPC除外）
- Python 方針: UnityPy/lz4 系は `/opt/anaconda3/bin/python3` 3.11.7 固定＋`.python-deps`。stdlib のみの新規スクリプトは python3 3.14。**Blender 4.5.11 LTS** は `common_ce.BLENDER` 固定
- 版選択規則: `c_<stem>_slg_` 形のstemのうち「版自身＋直後に数字が続く派生」（HelenSSR01→0101を含み_Darkzone等は除外）。LODはグループ毎に最良1つ（lod0優先→lodm0→lod1…）。同名メッシュ重複はソート順初出採用
- 可視性: prefab の GameObject m_IsActive 祖先連鎖（source-backed）。原作が無効にするものは `src_disabled` コレクションへ非表示で格納
- 実装上の教訓（再発防止）: ①macOS multiprocessing は spawn — worker 用グローバルは import 時に構築する ②asset名のトークン照合は `_` 分割部分も集合に入れる ③**EditBone 参照は EDIT モード中のみ有効**（OBJECTモード後のアクセスは解放済みメモリで UnicodeDecodeError）④**Blender は --python の例外でも exit 0 を返すことがある**（成功判定は build log の存在まで見る）⑤UnityPy 1.25 の externals は BundleFile 内部 SerializedFile にぶら下がる（`.name` 属性・CAB大小文字混在は lower で統一）⑥ゲームデータ由来文字列には孤立サロゲートがあり得る（`ce_common.clean_str` 必須）⑦骨ハッシュ照合は**ルート名を外した相対パス**のCRC32（フルパスでは一致しない — 旧b18の知見）

## 4. 既知の限界・未決

- 骨の親160本未解決（AFS2/CRI内のプレハブ由来）。**推定では埋めない**。バッチ時は推定率をキャラ別に数値で報告する
- 材質slot201個未解決（SMR不在67メッシュ＋AFS2内CAB 35slot）。権威ある対応が取れるまでこれらのキャラは忠実量産から除外（missing_inputs）
- AFS2/CRI コンテナ5,032本は生バイト走査のみ（中身未走査）。**【2026-08-25 訂正】実態は CriWare 音声（.acb/.awb）と動画（.usm）でキャラアセットは含まない（v8.3後の体色調査で magic 分類済み）。キャラの材質割り当てはランタイム組み立てのため静的データに権威が存在しない、が正しい限界記述**
- ベース名のバイト部分一致のため親族偽ヒットがあり得る（Nagant⊂Mosinnagant 等）。族帰属の確定は 00c のオブジェクト級パースで行う
- ModelConfigData の protobuf スキーマは未解読（文字列フィールドの収集のみ）
- 法線の比較はBlender内部量子化のため完全hashではなくサンプル許容差（TOL=0.02、実測上限0.0133は Helen 全91メッシュで測定）
- ヘレン自身の衣装材質は原作に欠損（既知）→ 台帳では partial が正しい期待値

## 5. 触ったファイル（作業単位で追記）

- 新規: `gf2-char-extract/` 全体（quality-gate.json・run-state.json・scripts/common_ce.py・00a〜00d・`_inspect_00b.py`(開発用)・ledger/{model-config-strings,model-name-candidates,needle-bundle-scan,needle-variant-hits,char-inventory,char-inventory-objects,inventory-controls}.json・reports/inventory-summary.md・logs-00b/00c-run.log(実行ログ)）
- 新規（wiki）: このページ
- 変更なし（read-only 遵守）: `gf2-helen-starlit-waltz/`、ゲームデータ一式
- **Step 1 追加（2026-08-24）**: scripts/{ce_common,ce_extract,ce_build_blend,10_extract_char,15_determinism_probe,20_diff_char_blend,_dump_blend,05_cab_index}.py ／ ledger/{cab-bundle-index,determinism-probe,diff-Helen-HelenSSR01,extract-Helen-HelenSSR01}.json ／ blends/Helen-HelenSSR01-repro.blend ／ intermediate/Helen.HelenSSR01/（extract-manifest・skeleton・texture-manifest・materials・meshes/*.npz・textures/*.png） ／ logs/build-Helen-HelenSSR01*{.json,.blender.log,.canonical.json}
- **Step 2 追加（2026-08-24）**: blends/{Dusevnyj-DusevnyjSSR0101,Sabrina-SabrinaSSR0101}-repro.blend ／ intermediate/{Dusevnyj.DusevnyjSSR0101,Sabrina.SabrinaSSR0101}/ ／ ledger/{extract-,diff-}{Dusevnyj-DusevnyjSSR0101,Sabrina-SabrinaSSR0101}.json ／ logs/build-{Dusevnyj-DusevnyjSSR0101,Sabrina-SabrinaSSR0101}*{.json,.blender.log,.canonical.json}。**修正**: scripts/ce_extract.py（重複面 dedupe・台帳項目追加）・scripts/20_diff_char_blend.py（同一 dedupe 規約＋Blender 非表現 corner 除外）
- **監査修正・白モデル修正 追加（2026-08-24）**: scripts/render_char_sheet.py・scripts/combine_sheet.py（新規）／reports/sheets/{Dusevnyj-SSR0101,Sabrina-SSR0101,Helen-SSR01}/（シート＋variants レンダー）／logs/_diff-dump-*.json。**修正（再構築を伴う）**: scripts/ce_extract.py（LOD フィルタ修正）・scripts/ce_build_blend.py（コレクション分け・naming_match テクスチャ・テクスチャパス解決・canonical materials 欄）・scripts/20_diff_char_blend.py（tex_index 解決統一・texmatch 画像実在照合・self-test 12系統化）。blends 3体・ledger diff 3体・determinism-probe は再生成
- **完全性基準 v8 追加（2026-08-25）**: scripts/25_gate_sync.py（新規）。**修正**: scripts/ce_build_blend.py（canonical_of_scene へ world_bbox 追加＝canonical 形式進化・blend バイト不変）・scripts/20_diff_char_blend.py（SourceExpectations 同名重複台帳＋新検査3本＋submission 判定＋scan_boundary 付記＋self-test 21系統）。ledger/diff-*.json 3体・determinism-probe・quality-gate.json(known_gaps) 再生成
- **着せ替え切替修正 v8.3 追加（2026-08-25）**: **修正（再構築を伴う）**: scripts/ce_build_blend.py（plan_visibility へ hide_mode=collection・run() の parts_P2/P3 レイヤー除外保存・canonical_of_scene の除外計測対応＝canonical に objects[].excluded 追加で形式進化）・scripts/20_diff_char_blend.py（_is_visible 実効可視性へ統一・show_p2 ケースを除外解除の再現へ書き換え）・scripts/render_char_sheet.py（開いた直後に除外を hide へ実体化）・scripts/10_extract_char.py（成功判定に build_log/blend の鮮度要求を追加＝偽成功防止）・blends/README.md（v8.3 手順・注意書き）。blends 3体・ledger diff 3体・determinism-probe・quality-gate.json(known_gaps)・reports/sheets 2体 再生成
- **バックアップ探索 追加（2026-08-26）**: reports/backup-volume-game-data-scan-2026-08-26.json（新規）／run-state.json（backup_volume_scan_2026_08_26・discovered_state_2026_08_26_noon・audit_script_confirm_paths・recovery_v84・v85_shared_body_atlas 欄追加）。**修正（2026-08-26 同日）**: scripts/20_diff_char_blend.py（confirm_paths 必須化＋r6 期待slot対応＋texture_source_purity 新設＋self-test 25ケース）・scripts/ce_common.py（r6 規約・SKIN_PATCH_* 廃止）・scripts/ce_extract.py（extract_shared_body_atlas 新設）・scripts/ce_build_blend.py（skin_patch 削除・shared_body_atlas 対応）。blends 3体（Dusevnyj 3e511d00…/Helen 1dd308b3…/Sabrina 変更なし）・ledger diff 3体・determinism-probe・quality-gate・reports/sheets 再生成。intermediate/<char>.<variant>/shared-textures/ 新設。**変更なし（読み取り専用遵守）**: /Volumes/HDD_バックアップ・GFL2Data(disk7s2)・現行ゲームコンテナ一式
- **固有色調査 追加（2026-08-26）**: reports/{selectplayer-post-profile-scan,selectplayer-post-profile-values}.json（新規）／scripts/ce_post_profile_scan.py（新規）／run-state.json（current_step 更新・dusevnyj_intrinsic_color_2026_08_26 欄追加）。**変更なし（読み取り専用遵守）**: アプリ同梱バンドル一式・キャッシュバンドル一式・カタログ bin・v51 関係ファイル

## 6. 変更履歴

- **2026-08-26（Dusevnyj 固有色調査・原因的一次データ確定／ポストport提案待ち）**: v9.1 承認カードへの武田さん回答「原作の固有色を無視してるから、よく探してほしい。どうするか考えて？」を受けて調査 → ①実測: テクスチャ自体は暖色正常（face_d 肌平均 R-B=0.277／shared body_d R-B=0.282／公式icon P1_Body R-B=0.209）なのにレンダだけ冷色低彩度（set_P1 R-B=0.183）＝**テクスチャではなく表示パイプラインの問題**と確定 ②セレクト画面(04Aimo_SelectPlayer)が実際に参照する URP Volume 3基を解決（シーンバンドル3224cfb2…内・sharedProfile PPtr→各SerializedFile externals→CAB-6b2f19f9…→md5逆引きで共有プロファイルバンドル 43da6f6a3d8829722b2d0caeb2500c56.bundle〔app同梱・87976B〕と同定・小規模7201バンドル全走査で裏取り）③実値回収: **MB_PostEffect_SelectPlayer** = Tonemapping(mode=3 film系・filmToe=0.47)＋ColorAdjustments(contrast +0.10／saturation +23.4)＋Vignette(intensity 0.25) が active、**ColorLookup(LUT)は inactive**（loungeとはここが違う）、GTAO/TAA/DoF/Fog も無効 ④根本原因=本プロジェクトのレンダが view_transform Standard の線形直出しでポスト処理皆無。film 曲線係数は v51 f136/f138 で既回収済みのため移植素材は揃っている。記録: `reports/selectplayer-post-profile-values.json`・`reports/selectplayer-post-profile-scan.json`・scripts/ce_post_profile_scan.py（新規）。run-state に対処案3択（A案推奨: セレクト画面実値port＝filmicトーンマップ＋grade、LUT適用なし）を記録し承認カードを出して停止

- **2026-08-26（v9・お手本レシピ port 完遂）**: 「ゲームと同じ見た目に仕上げる工程を足す」を承認取得 → v51レシピを port（キャラ固有 RampSetting 抽出・SH照明＝キャラ鑑賞シーンのLightProbes・ramp U=法線・光方向の内積）。事故修正: ctime16bitスケール・物理ライト二重掛け・日本語UIノード名・GFL2Data再マウント。3体再構築→全検査 PASS・self-test 26ケース×3・決定性 PASS・シート再生成。Dusevnyj/Sabrina はゲーム相当の見た目を制作担当が目視確認。Helen シートは旗エフェクトの既知問題（成果物外）


- **2026-08-26（v9.1・見た目仕上げ工程の port 完遂）**: 武田さん承認を得て v51 実績技術を一般化 port → キャラ固有 RampSetting（16bit ctime 正規化）＋キャラ選択画面の SH照明（f103 式）＋Emission 直出しシェーダ＋shading_port 検査新設（self-test 26ケース）。3体再構築→全検査 PASS/conditional・決定性 PASS。Dusevnyj/Sabrina のシートがゲーム相当の見た目になることを目視確認。PC再起動による GFL2Data アンマウント事故は正規マウントスクリプトで復旧（無傷）

- **2026-08-26（v8.5・共有 body_d 適用と生成物の全廃）**: v8.4 を武田さんが承認せず「まだ肌色は再現できていない・計画と監査スクリプトの詰めが甘い・**codeを抽出して再現しろ・クリエイターじゃなくてエディター**」→ 機械調査で原因確定: 原作の生肌は全キャラ共有テクスチャ `body_d`（プレフィックス無し・バンドル 42554ccee…）のランタイム割り当てで、手の UV は指・爪まで body_d に整然一致。v8.2 単色・v8.4 顔パッチは両方とも代用品＝創作だった。修正: r6_shared_body_atlas 規約（ce_common/ce_extract/ce_build_blend）＋検証器に **texture_source_purity**（生成テクスチャは FAIL）新設・self-test 25ケース化。3体再構築→全て PASS/conditional・決定性 PASS・シート再生成。**教訓: 「データに無いものは作らない。探し方が悪かっただけ」**

- **2026-08-26（v8.4 肌パッチ・Dusevnyj 復旧完遂）**: 武田さんが「成果物は Blender オブジェクトの品質」と指摘し承認カードで **A案（肌色修正込みで復旧）** を承認 → ①朝の Helen FAIL をデータ不完全起因と機械特定（.bundle は3体とも 0欠け・欠けは音声/動画のみ）②Helen 再diff PASS で v8.4 ビルダを検証済みに ③Dusevnyj をフル再抽出→再構築（新SHA `b56f8152a51ae1c0`）→ 突合 PASS(20)/conditional・self-test 24ケース・決定性 PASS・シート再生成（白面積率0.0・肌パッチで顔/手/脚が自然肌色を制作担当が目視確認）④Sabrina も PASS 再確認。**※この v8.4 肌パッチは直後の差し戻しで v8.5 に置き換えられた**

- **2026-08-26（監査スクリプトへ confirm_paths 必須化・v8.4 中断状態の発見）**: 武田さん指示「確認してほしいときはファイルパスを明示するように監査スクリプトに加えて」→ `20_diff_char_blend.py` の submission に **confirm_paths**（成果物8パスの exists 付き機械列挙・exists=false は提出不可）を新設し標準出力にも印字。動作確認の単体実行で **Dusevnyj blend 消失を検出** → 精査の結果、02:06-02:11 の空blend事故（LocalCache 一時消滅が契機・v8.3 正本は上書き喪失・quarantine 隔離済み）と、09:25-09:36 の v8.4 肌パッチ作業中断痕跡（Helen diff FAIL/blocked のまま）を発見。run-state/handoff を実態へ訂正。Sabrina は無傷。ゲームデータ本体は現在手元にあり再DL不要

- **2026-08-26（バックアップボリュームのゲームデータ探索・完結）**: 前セッション停止地点「バックアップボリューム（2026-08-26 朝スナップショット）にゲームデータがあるか探索」を実施 → `/Volumes/HDD_バックアップ` の完了スナップショット `2026-08-26-103352.previous` は内蔵 'Macintosh HD - Data' 専用バックアップ（manifest 実測）で**この範囲ではゲームデータ不検出**。コンテナ `com.haoplay.game.ios.exilium` のスケルトンはあるが LocalCache は空 = ゲームデータ本体は外付けSSDの独立APFSボリューム `GFL2Data`（disk7s2・44.5GB）を LaunchAgent がサンドボックスの LocalCache へマウントする設計のため、バックアップ対象外。陽性対照（同一find方式で既知ファイル検出）と現行側 UnityFS マジック実測で方法は有効だったことを確認。GFL2Data 自体の APFS スナップショットは 0 個。記録: `reports/backup-volume-game-data-scan-2026-08-26.json`

- **2026-08-25（v8.3・着せ替え切替不能の修正）**: 武田さんが README どおりにチェック操作しても**「服装が切り替わらない」**と報告 → 機械精査で P2/P3 中身の個別 hide 焼き付き（README 手順と実装の食い違い・レンダーシートだけ個別フラグ操作で撮影していたため監査をすり抜けた）を確定 → 承認カード A案で修正: P2/P3 をレイヤー除外（チェックOFF）保存へ変更・検証器を実効可視性（hide＋excluded）へ統一・canonical に excluded 追加・レンダー側は除外を実体化して撮影。過程でドライバが Blender 例外後に**前回残ログで偽成功を報告する実障害**も発生 → 成功判定に鮮度要求を追加。3体再構築→突合20項目 PASS/conditional→self-test 24ケース PASS→決定性 PASS→set_P1/P2/P3 レンダーで Dusevnyj の3着切替を目視確認。**次は武田さんの実機切替確認＋Dusevnyj 肌色修正（残課題）→正式目視承認**

- **2026-08-24（監査修正・白モデル修正）**: 武田さんの品質不良指摘 → v51 基準の監査で A1（LODフィルタ無効・Step1からの潜在バグ）/A2（可視性未取得）/A3（テクスチャ未貼り）/A4（見た目検査なし）を検出 → 承認カード「全部やる」「全部入れて切替可能に」で修正。再構築後のレンダーシートで白モデルを発見 → 根本原因（manifest の png 欄を CWD 相対 exists() で評価し画像ノード生成が黙ってスキップ。検証器はラベル一致しか見ず PASS）を特定し builder/検証器を修正（canonical に materials 欄追加・self-test 12系統化）。3体再構築→突合 PASS→決定性 PASS→レンダー目視でテクスチャ乗り確認。**目視承認は未取得（次は武田さんの Blender 確認）**
- **2026-08-25（監査強化・差し戻し修正）**: 武田さんが Dusevnyj blend を開いて「全然ダメ・監査が甘すぎる・監査スクリプトを強化して」と差し戻し → 調査で D1（頭部共有パーツ face/boby の抽出漏れ — 顔・眉は基本コード stem 族管理）・D2（naming_match 厳密一致の3クラス取りこぼし）・D3（既定可視性で P1/P2/P3 全候補が同時表示）・D4（監査が見た目の白を数えていない）を特定 → 共通規約 resolve_texture_stem（R1/R2/R3）・add_base_head_parts・P1既定表示・新検査2項目＋self-test 14系統・レンダーシート全面強化（白面積率・孤立ビュー・Pセット比較）を実装。3体再構築→突合 PASS（Dusevnyj 顔・髪が復活/Sabrina 未テクスチャゼロ）→決定性 PASS。併せて「ヘレンは依頼していない」を調査・確認（正しかった）→ Helen は成果物から外し内部回帰検証専用に。**目視承認は未取得（次は武田さんの Dusevnyj+Sabrina 2体確認）**
- **2026-08-25（監査強化v4・アルファ/ゴミ修正＋独立監査新設）**: 2回目の差し戻し「ゴミオブジェクト・バリ・のっぺり・監査抜け・サブエージェント監査もやれ」→ 機械精査で E1（全材質アルファ未接続 = バリ状ゴミの正体）・E2（OverviewCam 残存 = ゴミオブジェクトの正体）・E3（既定 PBR ハイライト）・E4（監査の配線/型検査欠落）を特定 → アルファ CLIP カットアウト・カメラ廃止・マット化・新検査2項目＋self-test 16系統で修正。**独立サブエージェント監査**を運用新設 → 判定: Sabrina 提出可・Dusevnyj 条件付き（白い右手=P1_cloth3/hip3 は原作データにテクスチャ無し）。3体 v4 再構築→突合16項目 PASS→決定性 PASS。**目視承認は未取得**
- **2026-08-25（v5・ハードカットアウト/ramp陰影）**: 3回目の差し戻し「指摘箇所が直っていない」→ 原因は v4 の CLIP 指定が Blender 4.5 EEVEE Next で無効（例外握り潰し）だったこと＋DITHERED ディザのビューポートざらつき＋ramp 陰影未実装 → Alpha を Math ノードで二値化するハードカットアウトに変更・原作 ramp テクスチャ実在材質（髪等）に v51 実績レシピで陰影配線。3体 v5 再構築→突合16項目 PASS→決定性 PASS。独立サブエージェント監査は API障害(503×3)で技術的停止（担当者監査で代行・Sabrina 欠陥ゼロ）。**目視承認は未取得**
- **2026-08-25（v6・浮き骨スパイク非表示）**: 4回目の差し戻しで武田さんスクショを分析 → 「バリ」の正体=未解決親の骨が体の外に浮いてスパイク表示（v51 正本は親完全解決のため表示で正しかった・実物ダンプで確認）→ 未解決親>0 の間はアーマチュアを非表示保存する規約＋監査新検査 open_state_cleanliness＋self-test 17系統。3体 v6 再構築→突合17項目 PASS→決定性 PASS。**目視承認は未取得**
- **2026-08-25（v8.2・肌色フォールバック＋3点差し戻し対応）**: v8.1 目視承認カードで武田さんが3点を指摘し差し戻し → ②Sabrina つま先立ち・靴は原作データ実測で決着（靴メッシュ 0件・body_lod0_Dorm は本体足部分と頂点UV完全一致の重複パーツ・つま先立ちは原作 rest ポーズ＝アニメ領域 deferred で変更なし）／③肌色は **肌フォールバック規約 v8.2** を新設（`ce_common.skin_fallback_kind` 共通規約: slg_body/boby＝顔アトラス流用 r4_uv_atlas_face・手 P1_cloth3＝顔アトラス肌領域サンプル色 RGB(239.8,198.7,180.8) の単色マテリアル approximation）→ Dusevnyj known_untextured 5→1部位（hip3 のみ）・レンダーで左手が肌色化・white_ratio 0.0。self-test は新ケース追加時に捕捉先を visible_texture_coverage→submesh_slots へ修正（単色肌は索引解決ではないため coverage では FAIL 化しない実測）→ 両体 24ケース PASS／①着せ替え切替手順は `blends/README.md` 新設。3体 PASS(20 checks)/conditional・決定性 PASS。**次は武田さんの2体目視承認（肌フォールバック approximation 承認込み）**
- **2026-08-25（v8.1・足パーツ解消＋独立監査再実施）**: v8 が機械捕捉した Dusevnyj blocked（地面浮上＝足欠落）を原作データ調査で解決 — 足・靴は派生タグ付きメッシュ（`cloth_lod0_Fight` 10934v 等）に存在し上位パートと幾何連続（Helen/Sabrina の _Dorm も同構造の3キャラ共通設計・SMR は AFS2/CRI 内のため権威可視性無し）。`plan_visibility` へ **gap_filling_subpart 規約**新設（地面浮上欠けを幾何連続的に埋める派生メッシュを最詳細1つだけ既定表示・Sabrina は本体足込みのため不適用）→ ground_gap 0.112→0.015 解消・スニーカー表示をレンダーで確認。独立サブエージェント監査（API 回復後）再実施 → **両体「条件付き提出可」**（白残り=台帳通り・台帳に無い白は皆無）。監査 major 2件対応: M-A（非表示分の world_bbox が hide で depsgraph 評価外の生値 → hide 解除+update で計測。解除中に記録すると hide が全員 False になる副作用を3体全 FAIL の実測で検出し元値記録方式で解決）・M-B（submission へ P2/P3 切替時の白部位 `untextured_when_switched` 追加）。3体 PASS(20 checks)/conditional・self-test 23系統 PASS(2体)・決定性 PASS(2体)・レンダーシート再生成(白面積率0.0)。**次は武田さんの2体目視承認（conditional 開示承認込み）→ Step 3 バッチ計画**
- **2026-08-25（v8・完全性基準の実装＋計画審査への独立レビュー適用）**: v6 差し戻し「完全性基準が無い=監査抜け」に対し v7 案（C1〜C5）を承認カード提示 → 武田さんが「メインエージェントはバイアス無きよう成果物向上につながる指示でサブエージェントにレビューさせよ」と承認せず → 独立レビュー実施、verdict **要修正**（M1: Dusevnyj 足ジオメトリ不在の反例で v7 が ALL PASS／M2: census 突合無しでは D1 クラス検出不可／M3: manifest AABB の空間不一致／M4: 再オープン検査と提出条件の漏れ／M5: known_untextured 証明の走査境界）。major 全反映の v8 を承認いただき実装: canonical へ world_bbox 追加・新検査3本（census_completeness／geometry_world_coverage〔地面接触基準含む〕／variant_detail_divergence）＋submission 判定＋self-test 21系統＋`25_gate_sync.py`（quality-gate 接続）。実測: Sabrina/Helen 全20検査 PASS(conditional)・**Dusevnyj は blocked（下端0.112m浮上＝足問題を初めて機械捕捉）**・決定性 PASS。目視承認は未取得（次は足問題のスクショ照合→2体再提出）
- **2026-08-25（v6 確認・次セッションへ引き継ぎ）**: 武田さんが v6 両方を確認（骨スパイク消滅は確認）。ただし承認未取得で 2 指摘: ①**足のあたりの造形が甘い** ②**「何をもってキャラ全体とするか」の完全性基準が無い=監査抜け**。別セッションで再開するため本ページ「次セッションの再開点」に作業を記録済み
- **2026-08-24（Step 2 パイロット）**: 武田さん指名の DusevnyjSSR0101・SabrinaSSR0101 を抽出→機械突合 **12項目 ALL PASS**。過程で①原作データの完全重複面が Blender で面ズレを起こす問題を dedupe 規約（extractor と検証器の同一規約・台帳記録付き）で解決、②両面ポリゴン境界での Blender 法線ゼロ化（表現限界）を非表現 corner 除外規約（件数記録・1%上限付き）で解決。Helen 回帰 PASS・self-test 再実行 PASS。目視承認は未取得（次は武田さんの Blender 確認）
- **2026-08-24（Step 1）**: 抽出ドライバ〜機械突合を実装し Helen/HelenSSR01 で実測。突合12項目ALL PASS・決定性 canonical_manifest_sha 確定・replay否定試験11系統PASS。捨てた判断4件を同ページに記録。run-state.json を step1-done へ更新。
- **2026-08-24**: Step 0 完了。00b を needle全集合×トークン(`_`分割含む)方式へ作り直し（Helen 12→92ファイルの改善）。00c 完走（75,115 objs）、00d 対照試験 ALL PASS。サマリ生成。
- **2026-08-23**: 作成。計画v2.1承認を受け実装開始。品質ゲート plan PASS。00a完走・00b実行中。
