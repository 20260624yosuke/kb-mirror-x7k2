---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
sources:
  - gf2-helen-repro-v51-run
---

# 引き継ぎ資料 — HELEN-REPRO v5.1（2026-08-20 作成・**2026-08-23 再更新**）

> [!info] 2026-08-23 の所在変更（武田さん決定）
> このファイルの実体は `06_repro-v51/reports/HANDOFF-2026-08-20.md` から
> **LLM Wiki のこのページへ移動した**。旧パスには行き先だけ書いたポインタを残してある。
> `run-state.json` の `handoff_file` もこの wiki ページを指すよう更新済み。
> 以後の更新はここへ直接書く（運用ルール14の「wiki 正本への同期」と同じ位置づけ）。
> 戻すなら: このページの本文を旧パスへ `mv` し、`run-state.json` の参照を戻す。

> [!warning] 2026-08-21の重要訂正
> 旧記録の `H0157 = Lobby_01_Public`、`H0157 = 06Aimo_Dorm_GFMB`、`q は未確定`、
> `世界座標化は判断待ち`、`機械ハーネスの自動起動はユーザー指示で保留` は撤回した。
> H0157は表から「Helen・room type 2（卧室）」まで確定するが、sceneを選ぶruntime joinは未回収。
> 輪郭線は世界座標入力へ変更し、q/P22の規約はMetal reversed-Zで解決したが、輪郭線全体は未完成。

> **2026-08-21の更新**: 3節（checker の指摘 3-1〜3-10）と 4節（武田さんの新しい要望＝門 `f72`）は
> **全件処理済み**になりました。処理の内容は各節に書き足してあります。新しく増えた未決は 6節。
>
> このファイルの数値は **2026-08-19 夜〜2026-08-22 に実ファイルから測った値**です。
> 前の版は `reports/HANDOFF.md`（2026-08-19 夜まで反映）。**衝突したらこちらが新しい。**
> 版の正本は `run-state.json` の `成果物の現在の版` 節。

> [!warning] 2026-08-23 の更新（2026-08-22 深夜〜08-23 未明の分）— 旧数値は下のとおり変わった
> ①**成果物が変わった**: 武田さん承認のもと**足元を `_Fight`(ハイヒール)→`_Dorm`(裸足) へ切替**。
> **GATE は 14 PASS / 1 FAIL（G10 のみ）**。SHA は **`e0ba175651c20251…`**（旧 `b1214f28…`）。
> blend が変わったため **`f42`/`f43` の確認画像は古い**（見た目の報告の前に撮り直し必須）。
> ②**通信キャプチャ実施済み**: ピン留めなし・`{0}` 生確定・**サーバー版＝手元版のため
> 通常プレイでは欠損 bundle は絶対に落ちない**と確定。CDN 直接取得は通常フローでは道が尽きた
> （プロキシ解除済み・mitmdump 停止済み・CA証明書は System keychain に残置）。
> ③**「scene root 欠損」前提に反証**: 圧縮展開走査 `f98` により GFMB lightProbes が
> **app 同梱 `29684a9f…bundle`(LZ4HC) の中に実在**。`f99` で焼き込み照明（8プローブ×RGB SH27）を台帳化。
> 「欠損103本」リスト自体の再導出が必要。門 `f97` を新設。
> 詳細は「2026-08-22 深夜〜08-23 未明 に足したこと」節（39〜45番）と wiki 正本。

---

## 0. まず読むもの（この順で）

| # | 何 | どこ |
|---|---|---|
| 1 | **このファイル** | LLM Wiki `wiki/builds/gf2-helen-repro-v51-handoff.md`（2026-08-23に作業フォルダから移動） |
| 2 | **wiki 正本**（現在位置・全事故履歴・運用ルール） | LLM Wiki `wiki/builds/gf2-helen-repro-v51-run.md` |
| 3 | 承認済み計画 | `~/.claude/plans/mellow-questing-elephant-v5.1.md` |
| 4 | 実装ルール | `~/.claude/plans/mellow-questing-elephant-implementation-instructions-v2.md` |
| 5 | 現在位置（機械可読） | `06_repro-v51/run-state.json` |

---

## 1. 成果物と現在位置

- 成果物 `blends/helen-h0157-repro.blend` — **SHA-256 `e0ba175651c20251…`**
  （`f95` で足元を `_Fight`(ハイヒール)→`_Dorm`(裸足) へ切替・武田さん承認。直前版は `_pre-f95-dorm-feet/`）
- **GATE 14 PASS / 1 FAIL**（`G10`）。判定は一度も緩めていない（G13は2026-08-23に裸足切替でPASS復帰）
- 機械監査（`f50`）: 事実 **57件**（2026-08-22夜時点）。最終再監査結果を11節に記録
- 既存 blend 25個（`rest-room-v2.2/blends/`）は**全件無傷**（`a01 verify` で確認）
- 工程F（武田さんが Blender で見て判断）には**まだ入っていない**
- **blend が変わったため `f42`/`f43` の確認画像は古い**（見た目の報告の前に撮り直す）

### 控え（版つきの名前。上書きしない作りになっている）

| 場所 | 中身 |
|---|---|
| `blends/_pre-f54-outline/helen-h0157-repro__2d10e19d3f398472.blend` | **セッション開始時。全部戻すならここ** |
| `blends/_pre-f56-outline/` `_pre-f57-outline-color/` `_pre-f63-clamp/` | 各工程の直前 |
| `blends/_pre-f77-world-clip/helen-h0157-repro__be227dbc790bfcaf.blend` | **世界座標化直前。f77だけ戻すならここ** |
| `blends/_pre-f95-dorm-feet/helen-h0157-repro__b1214f28194caddf.blend` | **足元切替(f95)直前。f95だけ戻すならここ** |
| `blends/_candidate-ramp-neutralized/` | 階調表の既定値を白にした**候補版**（成果物ではない） |

---

## 2. このセッションで何をしたか（1行ずつ）

1. **輪郭線を原作の式へ作り直した**（`f54` 向き・厚み・倍率 → `f56` 骨の後で厚みを揃える → `f57` 色）
2. **画素クランプを実装した**（`f63`。常駐スクリプト方式・武田さん承認）
3. **輪郭線の色を黒に訂正**（`f61` のセンサスで、既定値0.6は実際には使われていないと判明）
4. **階調表を調べ直した**（`f65`〜`f67`）
5. **H0157 の部屋・照明候補を調査**（`f68`〜`f71`）。当時の `Lobby_01_Public` 対応は後に撤回
6. 機械監査の門 `f50` を新設し、抜け道を塞ぐ作業を2回行った（**2026-08-20 に C1〜C10 を追加で塞いだ**）
7. 独立 checker を2回実行（2回目の指摘は **2026-08-20 に全件処理済み**。3節）

### 2026-08-20 に足したこと

8. **門 `f72`（証明の無い否定・不能の結論を止める）を実装**（4節）
9. checker の指摘 **3-1〜3-10 を全件処理**（3節）
10. 検証器が空だった台帳4件（`LT-2` `LT-4` `LT-7` `RM-5`）を**一次データからの再測定**へ差し替え。
    その過程で **旧主張2件が誤りと判明**（`f64` の「0.0000画素 一致」／`RM-5` の「いずれも真っ黒ではない」）
11. 新しく分かったこと `LT-8`（クリップ名 `Bedroom_0101` はキャラ別の寮タイムライン7本に在り、
    それらは**動作を再生する側**で部屋の照明を持たない）
12. `Data/Table`を読んでH0157→Helen 1067→formation 106701→room type 2（卧室）を確定。
    scene addressへ結ぶruntime joinは未回収なので、`06Aimo_Dorm_GFMB`は候補止まり。
13. Dorm scene候補のapp/cache依存を和集合288件で再走査（実在186・欠損102・実在側Light 0）。
14. Helen SSR0101 RampSettingはdress 9件だけでなくhair 1件も回収し、少なくとも10件へ訂正。
15. q/P22の規約はUnity 2019.4 iOS Metal reversed-Zで解決。P22=+1の仮定を撤去。
16. `f77`で世界座標入力へ変更。`f78`はf51参照を実呼出しし、det=+1の実在matrixも静的試験する形へ修正。
17. `f82`でDorm/Drom表19本とIL2CPP入力3本の直接文字列を追加調査。runtime joinは未回収で、数値キー・間接呼出し・動的組み立ては未解析。
18. 独立checkerの再監査で、候補sceneへの未証明join、appだけの依存走査、hair rampの集計漏れ、f78の限定範囲、f81/f82の入力変化時の抜け道を発見。全件を訂正。

### 2026-08-21 に足したこと

19. cache配信版とapp同梱版のHybridCLR `Assembly-CSharp.dll.bytes`をECMA-335 metadataから直接解析し、両版の`Dorm_bedroom/weapon/bathroom=12/13/14`と基本scene pathを回収。ただし保存method bodyの標準IL走査では#US参照命令を復号できず、H0157→sceneのjoinには使っていない。
20. `f84`でlocal bundle 13,539本をliteral走査、`f85`で読める4 rootを完全一致ファイル名走査。どちらも対象は0件で、同じ探索器の陽性対照は検出。読めないbackup 2台は不在範囲から除外した。
21. `f81`をLT-10複合検査を含む40項目へ拡張し、`f72`にはf84/f85を同じ入力で再実行する否定主張証拠を登録した。
22. `f86`で保存済みゲーム実行ログを走査。既存2ログでGFMB lightProbesとHelen Dorm Bedroom_02/03/04 Idleの併存を直接確認した。**2026-08-22 15:40訂正**: 現行入力の非空21ログ（展開後150,468,186 bytes）では併存3件で、新規1件はGFMB lightProbesとBedroom_0101/SSR0101 clipを同じ8MiB ring chunk内に含む。ただしscene load要求、active/唯一scene、照明値の証明には拡張しない。

### 2026-08-22 に足したこと

23. `f87`で`UnityFramework` / `global-metadata.dat`をpinned IL2CPP DumperとARM64逆アセンブルから再測定。AOT image 124件の名前一致は`Assembly-CSharp.dll`が0件、陽性対照`GameAOTMain.dll`が1件。`GameAOTLoader.LoadHotUpdateDll`が`LoadBytesMMap`の後に`HybridCLR.RuntimeApi.LoadAssemblyRaw_Internal`を呼ぶ境界を直接確認。`Addressables.LoadScene/LoadSceneAsync`のAOT入口6本を次の実行時観測点に固定した。H0157の実行時keyはまだ観測しておらず、blendも変更していない。
24. 独立checkerが指摘した4点を処理。`f87`へ同じ`ScriptString`経路の陽性対照、toolのdirty tree拒否、`llvm-objdump`版・実体SHAを追加し、scene選択method所在の断定を撤回。`LT-13`と否定主張2件を機械台帳へ登録し、`f50` 53/53、`f72` 強制21件、両方の再現試験を再実行して合格。`run-state.json`の正規SHAキーも現物`b1214f…`へ同期した。
25. `f88`で対象appを起動せずruntime traceの事前条件を測定。LLDBのfile address解決、Addressables object-key入口3本、Il2CppString復号、JSONL出力schemaの静的scaffoldは自己試験済み。対象appの署名列挙に`get-task-allow`が含まれないことを、同じXMLの`application-identifier`を陽性対照にして記録した。これだけで全接続方法の失敗は断定しない。対象attach・ASLR実アドレス・陽性対照breakpoint・実機key復号・停止条件は未検証のためruntime gateは意図どおりFAIL。app起動・attach・再署名・注入・blend変更は行っていない。
26. 通常の`python3`が3.14に変わり、既存検証5件がPython 3.11向けの`numpy` / `_brotli` を読めず停止した。主張・期待値・判定条件は変えず、`f50`が検証器ごとに明記したPython実体を使うように修正。`OL-2/5/9` `GL-1` `LT-4`だけを`/opt/anaconda3/bin/python3` 3.11.7へ固定し、シンク先の実体・SHA-256・版の一致も必須にした。現行`python3` 3.14から`f50` **55/55 PASS**を直接確認した。
27. 独立checkerのf88指摘を反映。文字列`"false"`を承認としない型判定、出力JSONLのイベント別schema、空文字の復号拒否、callback登録失敗の検出、部分作成済みbreakpointの無効化・削除・残留確認、trace本文のSHA-256照合とセッション鍵にるHMAC連鎖（改ざん検出用署名）、Mach-O UUID、3入口の同一ASLR slide、実際のbreakpoint無効化結果まで必須にした。合成replayは本番ゲートが拒否し、11ケースの再現試験で検査する。ただし同じ利用者が読めるローカル鍵は「誰の承認・どの実機か」の真正性を証明しない。信頼の起点をユーザーが選ぶまで、production pass、session作成、breakpoint設置はfail-closed（必ず停止）にした。
28. 修正後の第3回独立checkerは **major findingなし**。追加の軽微指摘も処理し、sessionファイルは権限`0600`の完全一致を必須にし、既存breakpointは新manifest検査前に削除・残留確認する。`f50`の真偽値/文字列取り違え試験も含む保存ログ25件を再生成して全件合格した。
29. 承認済み改訂計画に合わせ、`f88`を6 scene入口（object key 3 + IResourceLocation 3）、control/target二段、連続HMAC、action marker、breakpoint無効化・削除・resume・detach・残留0のcleanup実行役へ更新。IResourceLocationはcallのみ記録し、targetのobject keyは陽性対照で確立したstring class pointerと一致する場合だけ復号する。
30. 変更後の静的scaffold、再現試験13件、別実装の監査、`f50` 55/55、汎用品質ゲート`plan`を実機前に合格。Command Center scene keyは現行catalog SHA-256 `13312867…`から`Assets/Scene/CommandCenter.unity`の1件に解決した。
31. 2026-08-22 14:31、武田さんの明示承認「1で準備できました。」を会話記録・非暗号的attestationとしてsessionに結合し、原本PID 30908へLLDB attachを1回だけ試行。macOS/debugserverが`Not allowed to attach to process`で拒否したため停止条件に従い再試行していない。attachは成立せずbreakpoint 0、traceファイルなし、ゲームprocessは継続稼働を直接確認。
32. post-attach検証では、`f48`の正本42件、`f72`の今回変更3文と再現試験15件、変更対象のLT-12/LT-15個別検証は合格。`f72`全量監査は`f87/il2cpp_dumper_py`待機、`f50`全量再監査は`f81`が900秒上限に達した後の同じ`f87`待機により技術的停止。実装前の全量合格をpost-attach全量合格へ読み替えない。今回起動した孤立子プロセスだけを終了し、既存の別作業には触れていない。

### 2026-08-22 夜に足したこと（f90・全量監査の解消）

33. `f90`が`f86`の抽出器のASCII限定（UTF-16LE `\x20-\x7e`）を発見。中国語ログ行
    （`切换场景的探针为:`＝シーン切替probe、`[Lounge] 播放人形…的Timeline:`＝寮timeline再生）を
    数え直し、**probe切替→71〜327文字でHelen寮timelineが続く近接併存6窓**と
    **皮膚Id 1106701 ↔ Model HelenSSR0101 ↔ clip c_HelenSSR0101_Bedroom_0101** の隣接対応を
    LT-16として台帳化。他3人形も同じprobe直後にBedroom再生しており対照になる。
    Addressables要求キー・active scene一意性・照明値の証明には届かない（限定はLT-16本文）。
34. 同じ実測で **AssetBundles_IOS の `*.bundle` 9,035本は2026-08-08以降1本も新規取得されていない**
    （ClientRes_iOS 38ファイル更新を陽性対照にLT-17登録）。入手試行ゼロは3回目
    （08-19衣装表示・08-22寮replay・本census）。欠損102依存は通常ゲーム操作では落ちない。
35. 技術的停止だった全量監査を解消: `f87`完走（11秒）、LT-2 verifier timeoutのみ900→1800秒
    （判定条件不変）、`f50` **57/57 PASS**・replay25件PASS、`f72` 強制22件PASS・replay15件PASS、
    `f48`合格。`f46`は確認画像3枚の撮り直し＋`f45`比較JSON取り直しで**PASSに復帰**
    （参考: 原作 肌÷髪 3.52 対 現行 2.70。数値は見た目合否ではない）。blendは無変更。
36. 武田さんが**CDN直接取得ルートを選択**。計画正本は
    `reports/CDN-RECOVERY-PLAN-2026-08-22.md`。事前偵察（すべてローカル読み取り）で、
    端末内URLキャッシュの `client_res_v1` 応答JSONから
    **CDNベースURL `https://gf2-jp-cdn.17996cdn.com/prod`** を回収済み。
37. **CDN計画 Phase A 完了（ネットワーク接触なし・監査PASS）**: `f91_cdn_target_catalog.py` が
    カタログから **cache53,822件＋app6,612件** のbundleレコードを抽出。レコードの+72固定オフセットに
    あるsize欄は手元実在ファイルと一致（cache1500/1500・app1499/1500＝陽性対照）。
    **appカタログのinternal_idは文字どおり `{0}/<name>.bundle` 形式**で、`{0}` には
    `ResUrlCdn` が入る構造まで確認。欠損 **103本**（scene依存102＋prefab root）を
    hash・sizeつきで `ledger/cdn-targets.json` へ台帳化済み。
38. **CDN計画 Phase B1 実施→停止条件で打ち切り**: 武田さん承認「まず1本だけ試す」のもと
    `f92_cdn_single_fetch.py` が scene root の候補6URLへ Range GET → **全て403**
    （フル取得・保存なし・ゲームデータ書き込みなし）。陽性対照として同一CDNの既知実績URL
    （`/prod/website/gm/<大文字MD5>.png`）は同じUAで**200**、乱数名も403
    （Server: AmazonS3 / X-Cache: cloudfront）。つまりS3+CloudFrontは未知キーを404でなく
    403で返すため、外側から「パス違い」と「認証必要」を区別できない。AOT文字列にURL管理コードの
    存在（`GetGameResUrls`等）を確認。`{0}`の展開値を確定する残りの道は計画書に記載:
    ①通信キャプチャ（mitmproxy等・別計画）②ゲーム内リソース一括DL機能の有無確認（技術リスクゼロ）
    ③backup volume読める化。いずれも武田さんの選択待ち。

### 2026-08-22 深夜〜08-23 未明 に足したこと

39. `f94`で `GlobalCharOutlineZBias` 実値とH0157カメラnear/farを手元全入力
    （metadata／UnityFramework／managed両版／HUDll／local bundle約1.9万本の生バイト／実行ログ24本）から
    探索 → **全て0件**。陽性対照は7入力すべて合格
    （`ledger/h0157-outline-zbias-camera-primary.json`）。
    限定: 生バイト走査はUnityFS圧縮ブロック内部を見ない。
40. **足元を切替**（`f95_p1_feet_dorm_switch.py`・武田さん承認）: 前記録「靴あり(_Fight)＝礼服の正解・
    H0157側に手がかりは無い」は誤りだった。原作フレーム `h0157_20.png`/`h0157_26.png`
    （実機・裸足が写る）＋単独プレビュー突合（_Dorm=裸足／_Fight=シルバーハイヒール）。
    **G13は判定緩めなしでPASS復帰＝14 PASS / 1 FAIL（G10のみ）**。
    SHA `e0ba175651c20251…`、控え `_pre-f95-dorm-feet/helen-h0157-repro__b1214f28194caddf.blend`。
41. **通信キャプチャ初実施**（mitmproxy導入・Wi-Fiシステムプロキシ経由・2026-08-22 22:39–23:03）:
    ゲームUA(`EXILIUM/4517`)のHTTPSはピン留めなしで全部記録できた。
    `{0}`=`https://gf2-jp-cdn.17996cdn.com/prod` を生きた通信で確定（website/gm PNG4枚が200）。
    `client_res_v1`応答の ResVersion `2.12.4517.13535.26111` ＝ 手元カタログ(26111)と一致 →
    **ゲームは自分のキャッシュを完全と見なすので、通常プレイでは欠損bundleを絶対に取りに来ない**。
    武田さんはキャプチャ中に寮→ヘレンの部屋→H0157本体clip(Bedroom_0101)まで再生したが
    bundle GET **0本**・新規ファイル**0個**（入手試行ゼロ4回目）。
    作業後プロキシ解除・mitmdump停止済み。記録
    `intermediate/cdn-capture/capture-20260822.flow`（82MB・個人通信混在につき取扱い要検討）。
    ゲーム内一括DL機能は武田さん確認により不存在。
42. **「scene root欠損」前提に反証が出た**: 寮シーンはエラー0・DL0で動作し部屋も普通に表示
    （武田さん目視A）。生バイト走査ではGFMB文字列がどのlocal bundleにも無いという矛盾 →
    圧縮ブロック内部の未点検が原因の可能性。全量展開走査 `f96` は速度不足で中断
    （第1版はimport不備の無効台帳を出した。`ledger/h0157-decompressed-scene-scan.json` は
    **参照しないこと**）。
43. **高速版展開走査 `f98_bundle_block_scan.py` で解決**: UnityFSブロック直読み+lz4/lzma展開
    （オブジェクト解析なし・ブロック情報先頭16バイトhash読み飛ばしを実装）で18,568ファイルを
    約3分で完走・エラー0。陽性対照2件合格（c_HelenDorm_Bedroom_05=10ヒット／_OutlineWidth=8142）のもと
    **`06Aimo_Dorm_GFMB`/`GFMB_lightProbes` が app 同梱
    `29684a9f82183f96b0cdf1a05b4c517e.bundle`（v7/LZ4HC圧縮）の中に実在**
    （`ledger/h0157-decompressed-scene-scan-v2.json`）。「欠損103本」リストには生バイト走査で
    見えない場所に実データがあるケースが含まれており、**リスト自体の再導出が必要**（門 f97 要求）。
44. **`f99_dorm_lightprobes_extract.py` が寮の焼き込み照明を台帳化**
    （`ledger/h0157-dorm-lightprobes-primary.json`）: プローブ8点×RGB球面調和27係数(9×3ch)・
    オクルージョン8組・probe[0] DC項 RGB=(0.626,0.655,0.592)=暖色寄り中性。
    同一bundle内にshine/glow系GameObject+ParticleSystem16体（**きらきら層の候補**）。
    8節に記載の旧回収分との突合は未了。Blender適用(SH評価→ライティング再構成)は
    **別計画として承認を取る**。
45. **門 `f97_local_first_gate.py` を新設**（武田さん指示「ルールではなく機械スクリプトで」）:
    静的な差分・生バイト走査だけでの「手元に無い／欠損／回収不可」断定を止める。
    展開レベル未点検の主張は弱めた言い方へ、「動いた観測あり」で再導出なしの強い否定は不合格。
    再現試験4/4合格（`logs/f97-gate-replay-test.json`）、quality-gate.json登録済み。

### 2026-08-23（朝〜昼）に足したこと

46. **「欠損103本」リストを3レベルで再導出**（`f100_missing_deps_rederive.py`・f97門の要求を履行）:
    cache/app全18,568ファイルを①ノード表（圧縮ディレクトリ部を展開）②展開後内容③
    `CAB-<md5(hash)>`/`<hash>.bundle`文字列で走査。結果: **7件は別ディスク名で実在**
    （CAB一致・中身は寮の小物材質/テクスチャ）、**87件は参照のみ**、**15件+special1件は不検出**
    （`ledger/h0157-missing-rederive-decompressed-v1.json`・陽性対照183/184＋GFMB再現）。
    **scene root `d128870a…`(CAB-5dde1387…)とHelen prefab root `7648416f…`(CAB-38db6dba…)
    は参照を含め不検出**。ただし実行時に寮がDL0で動いた観測(LT-16/17)とは
    「実行時は別scene/構成の可能性」として整合させ、強い否定には使わない
    （negative-claims `h0157-scene-prefab-root-local-absence-rederived` 登録済み）。
47. **CAB導出規則を回収（LT-19）**: `m_Name=<catalog_hash>.bundle`、ノード名=`md5(m_Name)`。
    例外は monoscripts ラベル付 `<hash>_monoscripts.bundle`。現存184hash中183が自己一致。
    Helen SSR0101関連は展開レベルで多数検出（c_HelenSSR0101×663/12バンドル＝AnimationClip・
    LOD1メッシュ22本・RampSetting10件〈f80既回収と同一〉）。**Material は展開レベルでも0件**
    （`f35b_ssr0101_material_verify.py` で再計数・対照の同形式衣装材質217件実在）
48. **f46を合格へ復帰**: blendが変わって古かった確認画像3枚を**現行blend `e0ba175651c20251…`
    から撮り直し**、`f45`比較JSONも取り直し（原作 肌÷髪 3.52 対 現行 2.7・参考値）。
 49. **台帳・門の同期**: fact-ledger へ LT-18/LT-18b/LT-19 追加（計60事実）、陳腐化した
     OL-7（SHA）/OL-12（GATE 14/1）/LT-12（ログローテーションで16ファイル・併存4件）を実測へ更新、
     negative-claims へ3件追加。監査: `f50` PASS／`f72` enforced29件 PASS／`f48` PASS／
     再現試験 f50(25)・f72(15)・f97(4) 全PASS／品質ゲート plan PASS・batchは意図どおりFAIL。
     **blend 無変更（`e0ba175651c20251…`）**
50. **A/B/C並列の結果（2026-08-23午前〜昼・武田さん指示）**:
    **A=SH方式2試作（`f103`）**: 焼き込みSH8プローブの評価器を実装、自己検査合格
    （球面平均==DC 誤差5e-4）。候補blend `blends/_candidate-sh-lighting/` に生成。
    機械比較: 原作 肌÷髪 **3.52** / SH候補 **2.23** / 現行旧灯は暗すぎて測定不能(null)。
    画像 `reports/matpreview/f103_contact_sheet.png`・数値 `logs/f103-compare.json`。
    本blend反映は未実施（武田さんの見比べ待ち）。
    **【2026-08-23 午後 追記】輝度を110に正規化して再測定**（`logs/f103-compare-normalized.json`）:
    旧灯 face **3.66**/full **4.17**、SH候補 face **2.94**/full **2.11**（原作3.52）。
    正規化すると旧灯の肌÷髪比の方が原作に近い（旧灯は暗かっただけで比自体は取れていた）。
    R/B色味（`logs/f103-rb-color.json`）: SH候補 **0.972** / 旧灯 **1.00** / 原作 **0.952**。
    数値は参考値。合否は `reports/stage1-review-sheet.png`
    （旧灯・SH候補・原作を見比べる第1段目視判断用シート）での武田さんの判断。
    **B=backup volume点検**（`ledger/backup-volume-access-20260823.json`）: HDD_02 全深度で
    ゲームデータ無し。HDD_バックアップ/HDD_バックアップ_macbookpro の2台は macOS TCC で
    root列挙拒否 → **フルディスクアクセス許可が武田さんの操作として必要**。
    **C=スキン構造の解明**（武田さんの指示「ドルフロにはスキンというコンテンツがある。探して」）:
    P1/P2/P3 は衣装モジュール（スキン）単位。**P2＝ストッキングセット**
    （P2_body_d テクスチャ＝黒ニーハイ＋クリムゾンガーターの生地を目視確認）、
    原作フレーム h0157_20 でも着用確認（つま先のみ露出＝f95の観測と整合）。
    ModelConfigData.bytes に HelenSSR0101 行あり、Item_ClothesMod_HelenSSR01_P1/P2/P3_Body.png 実在
    （`ledger/h0157-skin-content-evidence.json`）。原作メッシュは複数サブメッシュ持ちで境界は
    manifestから復元可能 → `f102` で11分離描画＋連絡シートを作成。
    **武田さんの決定（AskUserQuestion・2026-08-23）: 「P1のまま維持」**。
    ストッキング表示と silkstock ramp適用（G10未反映4枚中2枚）は第1段合格後の展開時に
    改めて計画する。経緯は run-state.json の `plan_conflicts` "G13/DRESS" へ記録済み。
    成果物blend無変更
51. **プローブ出自確認が合格（`f106`・計画v2は独立レビュー通過後）**: 完了条件3点が全合格 —
    ①寮probe切替行が3日(07-25/08-19/08-22)に渡り8件 ②Dormを含むprobe名は
    `06Aimo_Dorm_GFMB_lightProbes` のみで一意 ③Helen以外(Qiongjiu/Cheeta/Harpsy)でも
    寮probe→Bedroom timeline密接隣接(<400文字)が各1例。
    **「寮表示時にこのprobeセットが使われる」を併存証拠の範囲で採用**
    （照明値そのものの証明ではない）。LT-16を新測定値へ同期・LT-20新設。
    `[Lounge] Load Scene Task` 数値idと寝間Form表行id(106701系)の突合は不成立(有効な否定)。
52. **プローブ個別評価（`f107`）**: f103の「baseline」が実は全LIGHT非表示+ほぼ黒世界だったことが判明
    （＝真の現行灯レンダが未存在だったため本物も撮った）。current・mean_sh・probe0..7の
    10候補×2カメラを同一構図でレンダ、輝度正規化済み肌÷髪比+R/Bで比較
    （`logs/f107-compare.json`・`reports/matpreview/f107_contact_sheet.png`）。
    原作32枚の正規化比は平均**3.32**(フレーム差1.82〜5.0)・R/B平均0.97。
    最接近は mean_sh(face 2.94/full 2.11・R/B 0.972)とprobe2/4(2.89/2.3前後・R/B約1.0)、
    どの単一probeも原作比には届かない → **「tetrahedral補間(最大4probe)+直接光欠落」の可能性**
    として報告し、補間候補追加の可否を武田さんに問うて停止（計画v2の定義どおり）。
    成果物blend無変更
53. **較正候補5種とv2候補blend書き出し（`f108`/`f109`・武田さん指示「目標は原作の再現なので
    必要なことをする」）**: 台帳の `m_Tetrahedralization` は**フラグのみで四面体実データは空**
    （独立レビューの記述と異なるため訂正記録）。代わりにprobe座標の構造（偶数index=z下層0.01＝
    暖色系・奇数index=z上層0.18＝暗青系）から層別平均＋原作平均輝度110への較正(k倍)で近似。
    結果(`logs/f108-compare.json`): **cal_low**(下層4probe較正) face 2.81/**full 2.60**・R/B 1.013＝
    全身比が最接近で暖色、**cal_mean_sh** face **2.96**/full 2.08・R/B 0.974＝顔比と色味が最接近
    （原作 3.32/R/B 0.97 対比）。シート `reports/matpreview/f108_contact_sheet.png`。
    `f109` で上位2候補を「Blenderで開けるblend」として書き出し済み
    （`blends/_candidate-sh-lighting/helen-h0157-repro__e0ba175651c20251_sh-candidate-v2-meansh.blend`
    SHA先頭 `84377d2017678db1`／同 `_v2-low.blend` SHA先頭 `5d8464c427ab5f13`。
    ビューポートのシーンライト＋シーンワールドON済みで保存）。
    **ここでセッション死亡（API network_error）→報告のみ次セッションへ持ち越し**。
    tetra真値は scene root 回収まで不可のため本結果は approximation 扱い。成果物blend無変更
54. **f109欠陥の発見と訂正（`f109b`・2026-08-23夜）**: f109が書き出したv2候補blendは既存灯を
    `hide_render=True` のみで `hide_viewport` を残しており、**マテリアルプレビューでシーンライトONの
    とき 既存灯(主600/補200/裏250W)+SH_SUN4本+世界背景の二重照明で白飛びしていた**
    （武田さんの報告「指示通りにしたら白飛び」で判明）。headlessレンダ(f107/f108)は hide_render が
    効くため数値比較は無影響・そのまま有効。**f103候補blendも同じ欠陥を持つ**＝前回「原作感がない」
    の目視は二重照明状態で行われていた可能性が高い。`f109b_candidate_blend_export_fix.py` で
    v2 blend2本を再書き出し（既存灯は両hide済み・実測確認済み）。手元のv2 blendはこの版を使うこと。

---

## 3. 独立 checker（2026-08-19 2回目）の指摘 — **2026-08-20 に全件処理済み**

checker の指摘は正しく、私が誤っていました。**10件すべて処理しました。**
処理の詳細は wiki 正本 `wiki/builds/gf2-helen-repro-v51-run.md` の
「2026-08-20 — 否定を止める門（f72）と、checker 指摘10件の処理」節にあります。

| # | 指摘 | 状態 |
|---|---|---|
| **3-1** | **`f64` の「原作の式と全距離 0.0000画素 一致」は循環検証**。両辺に同じ式を置いており、原作と比べていない。投影も一度もしていない。上限側は8距離すべてで未発動 | **処理済み**。`f75_clamp_independent_verify.py` を新設し、A=成果物を射影して測る / B=原作 MSL の写し（`f51`）の2経路で比べ直した。実際の差は 中央値 0.0063〜0.3560画素・最大 3.7690画素。`OL-20` を書き換え |
| **3-2** | `f63` の手書きqとP22=+1に根拠がない | **解決済み**。対象アプリはUnity 2019.4.40f1・iOS Metal。reversed-ZからP22=near/(far-near)と確定して`f77`へ置換。ただしH0157原作カメラのnear/far実値は未回収 |
| **3-3** | **`f50` の抜け道は塞げていない**。checker が新しい変種を10件作り **9件が通った**（`comparison:"eq"` にすると tolerance 検査が丸ごと飛ぶ／`absence` の探索語をでたらめにする／`shader_anchor` に汎用の字面を並べる 等） | **処理済み**。`eq`／未知の比較語で tolerance が飛ぶ穴、`grep -c`、陽性対照の不在、汎用の字面の anchor、行範囲の水増し、数字どうしの引き算 を塞ぎ **C1〜C10 として再現試験に固定**（22件合格） |
| **3-4** | 台帳内の**直接矛盾**: `OL-1`（「画面の下限1.2画素」）と `OL-21`（「画面上の画素数ではない」）が両方 OBS で両方 PASS | **処理済み**。`OL-1` の③を `OL-21` に合わせて書き換えた |
| **3-5** | wiki 本文の「### 輪郭線の色 — OBS」節が **「基本色 × 0.6」のまま**。黒への訂正は `## 変遷` にしかない | **処理済み**。当該節に訂正の warning を入れた |
| **3-6** | 表示中メッシュ1個の行列へ視点を固定していた | **処理済み**。`f77`で世界座標補助4点を各メッシュがRELATIVE変換で読む構造へ変更。det=+1は生きた実ジオメトリを実在+1 matrixへ載せる静的試験までで、variant自身の見た目は未検証 |
| **3-7** | 常駐スクリプトのカメラビューで `sensor_fit='AUTO'` の扱いが誤り（横長で 1.78倍ずれる）／平行投影カメラで遠近扱いになる／複数回オープンでタイマーが累積 | **処理済み**。`f76` で3件とも修正。旧実装との比が ちょうど 16/9 = 1.7778 であることを別計算で確認 |
| **3-8** | 世界法線押し出しがclip.wを変え、原作との差を作る | **処理済み**。`f77`はclip.xyだけを変更。`f78`のclip.w差は最大7.19e-10 |
| **3-9** | `OL-16` の探索範囲が `f54`/`f56` だけで、現行の `f63` を含まない | **処理済み**。探索範囲に `f63` を追加 |
| **3-10** | `RM-5` の検証器が空（台帳の数字どうしの引き算） | **処理済み**。`f73` で一次データから測り直し。**その過程で旧主張『いずれも真っ黒ではない』が誤りと判明**（`Zuanshi` は 0.0）。`RM-5` を訂正し `RM-5b` / `RM-5c` を追加 |

checker の全文は会話に残っていないので、**上の表がすべて**です。
3-3 の「新しい変種10件」も文面が残っていないため、**コードを読んで自分で10件を組み直し**、
それが全部止まることを再現試験に固定しました（`C1`〜`C10`）。checker が実際に作った10件と
一致している保証はありません。

---

## 4. 武田さんの新しい要望 — **2026-08-20 に実装済み**（`scripts/f72_negative_claim_gate.py`）

> 「照明や輪郭線がないという llm の結論に対して、私が行った指摘は、
> 『その根拠は明確に説明できますか？証明してください』と指摘しただけです。
> わざわざ私が言うまでのことではないので、**監査の時点で修正できるようにして欲しい**です。
> プロジェクトファイルにルールを追加したところで、コンテキスト圧縮で切り捨てられるので、
> **機械的なスクリプトの形式で実現できるよう実装して欲しい**です。」（2026-08-20）

### なぜこの要望が出たか（実例2つ）

どちらも、武田さんが「根拠は？」と一言指摘しただけで**崩れた**。

1. **「輪郭線は原理的に再現できない」**（2026-08-17）
   → 試験記録が1件も無かった。保存済みスクリプト全件でドライバ API の使用が **0件**。
   実際には原作の太さの本体は画面に依存せず、**再現できた**。
2. **「H0157 の照明は特定できない」**（2026-08-19）
   → 「ヘレンのプレハブを参照するバンドル」を探して0件だっただけ。
   **部屋の照明は部屋の側にある**ので参照が無くて当然だった。
   探し直したら **71本・14,204灯**が手元にあった。

### 実装の結果（2026-08-20）

- `scripts/f72_negative_claim_gate.py` — 「未回収」系の抜け道2件を加え、再現試験 **15件すべて合格**（`logs/f72-gate-replay-test.json`）
- 納品条件の2件（「輪郭線は原理的に再現できない」「H0157 の照明は特定できない」）は**不合格になる**
- 証拠一式のそろった正しい否定主張（`OL-4` ほか）は**通る**
- 過去の主張は `ledger/negative-claims-legacy.json` に **109件**を凍結（一覧のみ・宿題）
- 証拠登録簿は `ledger/negative-claims.json`
- 報告の下書きを出す前に通せる: `python3 scripts/f72_negative_claim_gate.py check-text <ファイル>`
- `quality-gate.json` の `gates` 節に登録済み

以下は実装前に決めていた仕様（そのまま実装した）。

### 実装すべきもの（`scripts/f72_negative_claim_gate.py` を想定）

**否定的な結論を、証明なしに置けなくする門。**既存の `f46`〜`f50` と同じ作り
（再現試験つき・`quality-gate.json` の `gates` 節に登録・止められない門は納品しない）。

#### (a) 何を検出するか

台帳（`ledger/fact-ledger.json`）と wiki 正本のテキストから、**否定・不能の主張**を機械的に拾う。
少なくとも次の語形: `無い` `存在しない` `見つからない` `できない` `不能` `原理的に` `特定できない`
`未取得` `0件` `該当なし`。

#### (b) 何を要求するか（**ここが肝**）

強い否定（「無い」「できない」）には、次の**全部**を必須にする。欠けたら監査不合格。

1. **探索範囲**（`searched_scope`）— どこを、どの道具で、いつ調べたか
2. **その場で再実行できる探索コマンド**と、返ってきた件数
3. **陽性対照（positive control）** — **同じ方法で「有るはずのもの」を探して、実際に見つかること。**
   これが今回の2つの事故を両方とも捕まえられた唯一の検査:
   - CAB の逆引きで「45件すべて未登録」と出したとき、**手元にあるバンドル200本で同じ突合をしたら 0/200** で、
     突合そのものが成立していないと分かった
   - 「ヘレンを参照するバンドル0件」は、**部屋の照明という有るはずのものを同じ方法で探せば見つかった**
4. **反証の道**（`falsifier`）— この結論が誤りなら、どこを見れば分かるか

「できない・原理的に無理」という**実現不能の主張**には、上に加えて

5. **実際に試した記録**（どのスクリプトで、何を試して、どう失敗したか）。
   試していないなら「**試していない**」としか書けない（`f47` の入手試行と同じ考え方）。

#### (c) 満たせないときはどうするか

**主張の言い方を弱めさせる。**「無い」ではなく「**この探索範囲では見つからなかった**」へ。
門は、証拠の強さと言葉の強さが釣り合っているかを検査する。

#### (d) 再現試験（納品条件）

次の2件を試験入力にして、**必ず不合格になる**ことを実証する。

- 「輪郭線は原理的に再現できない」（試験記録なし・陽性対照なし）
- 「H0157 の照明は特定できない」（探索範囲が『ヘレンを参照するバンドル』だけ・陽性対照なし）

そのうえで、**現在の台帳にある正しい否定主張**（例 `OL-4`「ヘレン自身の材質は未取得」・
`GL-3`「欠落45件はカタログに登録済み」）が**通ること**も確認する。

#### (e) 私（前のセッション）の判断・既定値

次の点は既定でこう実装してよい。武田さんの指示があれば上書きする。

- **止め方**: 監査を不合格にする（報告を止める）。助言に留めない
- **対象**: 台帳の主張と wiki 正本の本文の両方
- **過去の主張**: いま台帳にある否定主張は、**一覧を出すだけ**にして即時不合格にはしない
  （まず新しい主張から強制する。一覧は宿題として残す）。
  **2026-08-20 に武田さんが「新しい主張から強制」を選択済み。確定。**

---

## 5. 武田さんが決めたこと（2026-08-20・確定）

**過去の主張の扱い**: **新しい主張からだけ強制する。**
いま台帳にある否定的な主張（例「ヘレンの材質は手元に無い」「Directional は0」など）は、
**一覧を出すだけ**にして、即時に監査不合格にはしない。一覧は宿題として残す。

→ 4節(e)の既定どおりに実装してよい。**この点で追加の確認は不要。**

他に曖昧な点はない（止め方＝監査を不合格にする／検出語／要求する証拠＝陽性対照を含む5点／
再現試験の条件は、すべて4節で確定している）。

## 6. 現在の停止点（**2026-08-23 夜更新**・原本LLDB attach 1回はOSに拒否・再試行せず技術的停止）

| 対象 | 状態 | 再開条件 |
|---|---|---|
| **SH照明候補（最優先・ここで止まっている）** | **武田さんの目視判断待ち**。出自確認合格(`f106`)→単一probe評価(`f107`)→較正5候補(`f108`・cal_low=全身比最接近/cal_mean_sh=顔比・色味最接近)→**v2候補blend①②を白飛び訂正版として再書き出し済み(`f109b`)**。前版は既存灯がビューポートで点いたままの二重照明だったため目視無効の可能性（f103候補も同じ欠陥） | 武田さんが `blends/_candidate-sh-lighting/*_v2-meansh.blend` と同 `_v2-low.blend` を見比べた結果で分岐: 採用→本blend反映は別承認を取ってから／調整→第3版作成／現行維持→候補は保存扱い |
| 輪郭線のclip.xy後段 | 世界座標入力へ実装・条件付き機械検証済み | checker指摘を反映済み。Helen固有width・ZBias・H0157 camera・見た目は別検証 |
| q/P22の規約 | Metal reversed-Zから式を確定 | H0157原作カメラのnear/farを回収後、数値を置換 |
| H0157 scene | `conversation_attested; original_lldb_attach_refused; no_retry; no_breakpoint_installed; runtime_gate_failed` | 2026-08-22 14:31に原本PID 30908へLLDB attachを1回試行したが`Not allowed to attach`。計画どおり再試行なし。Developer Mode変更、`sudo`、再署名、注入、追加ツールは本計画外なので行わない。別の明示計画がない限りruntime traceは技術的停止 |
| backup volume 点検 | HDD_02はゲームデータ無し。**HDD_バックアップ系2台は macOS TCC 拒否中**（`ledger/backup-volume-access-20260823.json`） | 武田さんがシステム設定→プライバシーとセキュリティでフルディスクアクセス付与 → 残り2台を再走査 |
| 階調表の材質対応 | `source_recovery_blocked`。prefab root `7648416f…bundle` は **`f100` の3レベル走査でも不検出**（参照すらない）。renderer→material→ramp抽出は入力待ち。silkstock ramp割当ては機械根拠が無く推測禁止（武田さん判断ならDEC）。ストッキング表示(P2)は**第1段合格後の展開時に改めて計画**（G13/DRESS決定済み） | prefab root実データの入手（CDN残りルート/backup volume readable化）。または武田さんの目視判断で割当てを決める |
| 機械ハーネス f46〜f72 | 起動保留ではない。毎回の監査対象 | 継続実行 |

照明リグ流用・白ランプ・body ramp流用・現状維持から選ばせる問題ではない。原作対応が取れるまで適用しない。

---

## 7. 輪郭線の現在の状態

- 仕組み: ジオメトリノード2段。`GFOutline`（骨の前・殻を作る）→ `Armature` → `GFOutlinePostSkin`（骨の後）
- 押し出す向き: **原作の COLOR0.rgb + NORMAL0 + TANGENT0(符号w) から作るなめらか法線**
- 厚み: **`_OutlineWidth × 1.3 / 1920` = 2.0313mm**（`_OutlineWidth=3.0` は INF）
- 倍率: **`COLOR0.w`**
- 色: **黒**（実測。材質514件中289件が (0,0,0,1)。既定値の 0.6 は使われていない）
- 画素クランプ: **f77でclip.xy後段を置換済み**。P22の規約は確定。H0157 camera値は未回収
- **未実装**: 奥行きのずらし（ZBias）。`GlobalCharOutlineZBias` の実値が手元に無く影響も不明（UNK）
- 畳まれた部位（骨スケール0.01）は厚みを揃えない。揃えると畳んだ鞄が 4mm の塊として生える

### 表示追従スクリプト（`GF_OutlineViewport.py`・blend 内テキスト）

**武田さんの Blender では自動で動きません。**実測で `use_scripts_auto_execute = False`、
開くと `scripts disabled … skipping` と出ます。**許可を求めるダイアログも出ません。**
動かない場合は補助オブジェクト `GF_ViewProbe` / `GF_ViewTarget` の既定値
（視点＝2m 手前・1080p・画角55.5度）のままになります。**絵は壊れません。**
この自動実行許可は、機械監査ハーネス`f46`〜`f72`の起動とは別問題。
**「ユーザー指示で保留」という旧記録は誤りとして撤回**。監査ハーネスは毎回実行する。
なお 2026-08-20 に `f76` で中身の誤り3件（カメラの `sensor_fit`・平行投影・タイマー累積）と
座標変換の決め打ちを直した。**動かないままなので、いまの見え方は変わらない。**

---

## 8. H0157 の場面と光（一次資料を分離した現行状態）

### 直接確定したこと

- H0157 clipは`c_HelenSSR0101_Bedroom_0101`。
- 表の連鎖は`DromInteractData 10670101`→formation 106701→Helen 1067→room type 2（`卧室`）。
- 原作GFCharForwardはMain Lightだけでなく、最大32のAdditional Lights、球面調和係数、
  ReflectionProbe、fogを入力に持つ。追加ライト方向でもRampMapを読む。
- よって原作の光を単一ライトへ縮約してはいけない。ただしH0157で実際に有効な灯数・値は未回収。

### Dorm scene候補として回収したもの（H0157で有効とは未証明）

app/cache双方のカタログに唯一のDorm scene `06Aimo_Dorm_GFMB`がある。ただし表のroom type 2から
このscene addressを選ぶruntime joinは回収できていない。`f82` で手元のDorm/Drom表19本と
`SnqxExilium` / `UnityFramework` / `global-metadata.dat` の直接文字列を追加探索したが、
scene literalが0件だったことは実行時joinの不在証明ではない。候補依存から次を回収した。

2026-08-21に調査対象をcache配信とapp同梱のHybridCLR managed assemblyへ拡張した。`f84`が一次ファイル
`Assembly-CSharp.dll.bytes`から両版とも`Dorm_bedroom/weapon/bathroom=12/13/14`（難読化型名は別）と基本path
`Assets/ArtsResource/Scene/Aimo/06Aimo_Dorm/06Aimo_Dorm.unity`を直接回収し、app/cacheカタログは
`06Aimo_Dorm_GFMB.unity`を各1件持つことを確認した。同じmethod body/#US復号器は既知の標準IL assemblyで19件を復号したが、対象Assembly-CSharp両版は0件。
room type 2→enum 12→基本path→GFMB pathの呼出し鎖は回収できていない。併存をjoinの証明へ拡張しない。

2026-08-22には`f87`でAOT側をメタデータから全列挙し、AOT image 124件の名前一致では
`GameAOTMain.dll`と`Unity.Addressables.dll`が各1件、`Assembly-CSharp.dll`が0件であることを直接確認した。
起動コードがhot-update DLLをHybridCLRへ渡す呼出しも逆アセンブルで確認した。関連managed metadataは
分離ロードされる`Assembly-CSharp.dll.bytes`にあるが、ゲーム固有scene選択methodの所在は未確定。
今回のAOT解析ではH0157 joinが出なかった。`Addressables.LoadScene/LoadSceneAsync`のAOT入口6本は
候補観測点であり、実行時監視の準備完了ではない。
LLDB/Frida等の接続手段、ASLR補正、陽性対照breakpoint、managed key復号、出力schema、停止条件が
揃うまで、ゲーム起動・H0157表示をユーザーへ依頼しない。

- `06Aimo_Dorm_GFMB_lightProbes`: 8位置・8組の球面調和係数
- ReflectionProbe cubemap 3件、lightmap 2件
- MB/PC用profileの画面効果24項目
- LUT `test.v103`（1024×32、RGB24）
- 依存はapp 219 / cache 147 / 共通78 / 和集合288。app/cacheに実在186、欠損102。
- 実在186 bundleのLightコンポーネントは0件。ただし欠損にscene rootがあるためscene全体の0件ではない。
- 2026-08-22 15:40時点の保存済みruntime log非空21件（展開後150,468,186 bytes）では、`06Aimo_Dorm_GFMB_lightProbes`とHelen Dormが同じ8MiB ring chunkに出る記録が3件ある。既存2件は`Bedroom_02/03/04_Idle`、新規1件は`Bedroom_0101`と`c_HelenSSR0101_Bedroom_0101`を含む。同じ実行窓の併存までの証拠で、scene load要求、active/唯一scene、照明値の証明ではない。

### 停止理由

- H0157→sceneのruntime joinが未回収。
- `f84`はlocal bundle 13,539本のうち、block tableの予定展開サイズと実サイズが一致した13,536本をscene文字列で走査し対象0件。部分展開0件、対象外3本のraw bytesも0件、陽性対照848件。`f85`は列挙可能な4 rootのPRUNE外でscene root `d128870a…bundle`とHelen prefab root `7648416f…bundle`を完全一致探索し対象0件、陽性対照は検出。app rootとSSD rootは重なるため列挙回数は重複を含む。
- backup volume 2台はrootが`PermissionError`で未走査。不在範囲に含めない。
- したがってASMRリグ、Lobbyの灯、CommandCenterの5灯はいずれもH0157へ適用しない。

台帳: `ledger/h0157-original-lighting-primary.json` / `ledger/h0157-scene-join-search.json` /
`ledger/h0157-managed-scene-primary.json` / `ledger/h0157-missing-primary-source-scan.json` /
`ledger/h0157-runtime-log-scene-evidence.json` /
`ledger/h0157-il2cpp-scene-trace-boundary.json` /
`logs/f81-h0157-primary-verify.json`（40/40 PASS）

---

## 9. 階調表（RampMap）の状態

- 原作の参照 V は **0.125 / 0.375 / 0.625 / 0.875 の4種だけ**。16行は **4本×4行の帯**で、
  帯の中の4行は**完全に同一**（最大差 0.0）→ **Blender へ row0 を入れているのは正しい**
- 帯の対応: 0=`Ramp_Diffuse` / 1=`Ramp_Specular` / 2=`Ramp_Fresnel` / 3=`Ramp_Additional`
- **未反映**: Specular / Fresnel / Additional の3本（Diffuse だけ反映済み）
- **欠陥**: 原作の階調表が見つからなかった材質 **22件**に Blender 既定の黒→白が残り、
  うち **12件が基本色に掛かっている**（表示中では **素肌・顔・スカート・鎖**）。
  現行Blenderの入力は`clamp(dot(法線,(0,0,1)))`で固定。原作はMain Light方向に加え、
  Additional Light方向でも別のRamp帯を読むため、固定方向1本の置換では再現にならない
- 白（無効）にすると全身の **5.66%** が明るくなる（暗くなる画素 0）。
  ただし **肌÷髪 は 1.60 → 1.58** でほぼ動かず、**明暗比の主因ではない**
- **原作では body / face も例外なく ramp を使う**（他キャラ材質 503件で実測）ので、
  **白も原作準拠ではない**
- prefab locationの手元依存21/86本からHelen SSR0101 RampSettingを**少なくとも10件**回収
  （dress 9 + hair 1）。欠損65依存に追加分がある可能性があるため総数は未確定
- renderer→material→RampSetting対応はprefab rootと欠損依存を回収するまで確定しない。推測割り当てはしない

> 訂正済み: OBS40 の「row10 は直線階調」は誤り（Fresnel 帯の急な曲線）。
> OBS-M6 の「`_ramp` は成果物に未反映」も古い。

---

## 10. 触ってはいけないもの

- **既存 `.blend` 25個**（`05_helen-motion-library/.../rest-room-v2.2/blends/`）— 開かない・書かない
- **`raw/`** — read-only
- **GATE の判定条件**（`scripts/e01_gates.py`）— 緩めない。計画の GATE 節が Single Source of Truth
- **ゲームデータ** — 読み取りのみ。`GFL2Data` は `~/.local/bin/gfl2-data-mount.sh`、ゲーム起動中は触らない
- **`blends/_pre-f54-outline/…__2d10e19d3f398472.blend`** — 全部戻すときの戻し先

---

## 11. 機械の門（現在8本）

| 門 | 止めるもの | 再現試験 |
|---|---|---|
| `f46_visual_env_gate.py` | 環境記録と原作フレーム比較の無い**見た目の報告**。**古い画像のまま合格する抜け道も塞いだ** | `logs/f46-gate-replay-test.json`（3件） |
| `f47_obtainability_gate.py` | 入手試行なしの「入手できない」 | `logs/f47-gate-replay-test.json` |
| `f48_claim_ref_check.py` | 撤回済み主張の引用・証拠参照の不在 | `logs/f48-gate-replay-test.json` |
| `f49_question_ticket.py` | 調べた証跡の無い質問 | `logs/f49-gate-replay-test.json` |
| `f50_fact_audit.py` | 検証器の無い主張。**2026-08-20 に抜け道 C1〜C10 を追加で塞いだ**。固定Pythonの実体差し替えと真偽値/文字列の取り違えも拒否 | `logs/f50-gate-replay-test.json`（25件） |
| `f72_negative_claim_gate.py` | **証明の無い否定・不能の結論**（4節）。陽性対照を必須にする | `logs/f72-gate-replay-test.json`（15件） |
| `f97_local_first_gate.py` | **静的な差分・生バイト走査だけでの「手元に無い／欠損／回収不可」の断定**。展開レベル走査（圧縮ブロック内部点検）をしていない主張は弱めた言い方へ、実行時に「欠損ありのまま正常動作した」観測と突き合わせていない強い否定は不合格 | `logs/f97-gate-replay-test.json`（4件・2026-08-22深夜の実際の事故パターン→不合格を含む） |
| `f88_runtime_trace_preflight.py` | 明示同意のある接続方法、セッションHMAC連鎖、対象attach、Mach-O UUIDと同一ASLR slide、陽性対照breakpoint、実機key復号、出力schema、実際のbreakpoint無効化が揃う前のH0157起動依頼。`project_quality_gate.py`からは自動実行されず、実機操作直前に`check-runtime`を直接実行する | `logs/f88-gate-replay-test.json`（改訂版13件） |

台帳: `ledger/fact-ledger.json`（事実**55件**・全件に検証器）／`ledger/negative-claims.json`（証拠登録簿）／
`ledger/negative-claims-legacy.json`（凍結した過去の否定主張109件＝宿題）

実装前の最終再実行（2026-08-22）: `f50` **55/55 PASS**・固定Pythonと真偽値型の検証を含む再現試験25件PASS / `f72` **強制21/21 PASS**・再現試験15件PASS /
`f81` **40/40 PASS** / `f78` 18条件PASS。`quality-gate --phase batch` は欠損入力・原作比較・
ユーザ許容判断が揃っていないため、意図どおりFAIL。

post-attachの最終確認（2026-08-22 15:40）: `f48`正本42件PASS / `f72`今回変更3文PASS・再現試験15件PASS / LT-12（runtime log併存3件）とLT-15（runtime gate `false`）の個別検証PASS。`f72`全量監査は`f87/il2cpp_dumper_py`待機、`f50`全量再監査は`f81`の900秒上限到達後の同じ`f87`待機で技術的停止したため、post-attach全量PASSとは記録しない。

**`f46` だけ不合格のまま**: 成果物を変えたので、`f42`/`f43` が使う確認画像
（`f43_hdri_rot000.png` / `f42_hdri_base.png` / `f42_scene_base.png`）が blend より古い。
**見た目の報告を出す前に撮り直す**。`f45` の原作比較は取り直し済み。

台帳: `ledger/living-claims.json`（正本の生きている主張42件）

---

## 12. このセッションで追加したスクリプト

| # | 何をする |
|---|---|
| `f50_fact_audit.py` | 事実台帳の機械監査（門） |
| `f51_outline_reference.py` | 原作の輪郭線の参照実装と等価性検証 |
| `f52_outline_inputs_probe.py` | 式の入力が成果物に揃っているか |
| `f53_outline_width_by_part.py` | `_OutlineWidth` の部位別分布 |
| `f54_outline_original_formula.py` | 輪郭線を原作の式で作り直す |
| `f55_outline_verify.py` | 姿勢付きで厚み・向き・畳まれた部位を検査 |
| `f56_outline_post_skin.py` | 骨の後で厚みを揃える（畳まれた部位は除く） |
| `f57_outline_color_original.py` | 輪郭線の色（実測の最頻値＝黒） |
| `f58_cab_to_bundle_name.py` | CAB → カタログ登録の突合（陽性対照つき） |
| `f59_who_references_helen.py` | ヘレンのプレハブを参照するバンドル（**問いの立て方が誤っていた例**） |
| `f60_image_diff.py` | 絵の画素差（L4 対策で保存） |
| `f61_outline_color_census.py` | `_OutlineColor` などの実分布 |
| `f62_original_outline_probe.py` | 原作フレームの縁の暗さ |
| `f63_outline_screen_clamp.py` | 画素クランプ＋常駐スクリプト |
| `f64_clamp_verify.py` | クランプの検証（**循環している。3-1**） |
| `f65_ramp_bands.py` | RampMap の帯構造 |
| `f66_ramp_default_leak.py` | 既定の階調表が残っている件 |
| `f67_userampmap_census.py` | `_UseRampMap` の実分布 |
| `f68_find_room_lights.py` | 部屋の照明を持つバンドル探索 |
| `f69_helen_scene_prefabs.py` | ヘレンの場面プレハブ探索 |
| `f70_extract_room_lights.py` | 部屋の照明を階層つきで抽出 |
| `f71_lights_reaching_bed.py` | ベッドに届く灯の絞り込み |
| **`f72_negative_claim_gate.py`** | **証明の無い否定・不能の結論を止める門（陽性対照が中心）** |
| `f73_ramp_sharing_verify.py` | 階調表の共有関係を一次データから測り直す（3-10） |
| `f74_room_evidence_verify.py` | 部屋・照明の主張を手元バンドルのバイト列から測り直す（3-3・陽性対照つき） |
| `f75_clamp_independent_verify.py` | 画素クランプを A=射影の実測 / B=原作の参照実装 の2経路で比べる（3-1/3-2/3-8） |
| `f76_viewport_script_fix.py` | 常駐スクリプトの誤り3件の修正（3-6/3-7）。控えを取ってから差し替える |
| `f77_outline_world_clip.py` | 世界座標補助と原作のclip.xy軸別式へ置換 |
| `f78_outline_world_clip_verify.py` | f51実呼出しと評価済みノードを18条件で比較 |
| `f79_h0157_primary_lighting.py` | H0157表とDorm scene候補の依存・光入力を分離抽出 |
| `f80_h0157_material_ramp_primary.py` | prefab locationの手元依存21本からRampSettingを全走査 |
| `f81_h0157_primary_verify.py` | f79/f80/f82/f83/f84/f85/f86を再実行し40項目を固定。現行blendとf77直前版の照明・材質・World比較も含む |
| `f82_h0157_scene_join_search.py` | Dorm/Drom表19本とIL2CPP入力3本のscene join候補を限定付きで探索 |
| `f83_artifact_shading_verify.py` | f77直前版と現行blendの照明・材質・WorldをBlenderで直接比較 |
| `f84_h0157_managed_scene_primary.py` | HybridCLR managed assemblyのenum・#US文字列・HUDll wrapperとlocal bundle文字列を直接解析 |
| `f85_missing_primary_source_scan.py` | scene/prefab rootを実在mount名で探索し、読めないrootを不在範囲から除外 |
| `f86_runtime_log_scene_evidence.py` | 保存済みgzip/rawゲームログのUTF-16LE文字列からGFMB sceneとHelen Dorm timelineの実行時併存を測る |
| `f87_il2cpp_scene_trace_boundary.py` | IL2CPP AOT image・GameAOTLoader・Addressables scene入口をpinned解析器とARM64呼出しから測り、静的解析と実行時traceの境界を固定 |
| `f88_lldb_scene_trace.py` | LLDB内でAddressables object-key入口のアドレスをASLR解決し、Il2CppString候補をJSONLへ記録する静的scaffold。明示実行までbreakpointは作らない |
| `f88_runtime_trace_preflight.py` | app署名・LLDB・AOT入口・decoder・出力schemaを静的自己試験し、実機証拠が欠けたらruntime gateを不合格にする |
| `f90_runtime_scene_switch_evidence.py` | CJKログ行をUTF-16LEで数え直し、probe切替近接併存（LT-16）とbundle新規ゼロ（LT-17）を台帳化 |
| `f91_cdn_target_catalog.py` | カタログからbundleレコード（cache53,822/app6,612件）をhash・sizeつき抽出 |
| `f92_cdn_single_fetch.py` | CDN候補URLへのRange GET試験（Phase B1・6候補403で停止） |
| `f93_cdn_b1_evidence_verify.py` | B1証拠JSONの再計算整合検査 |
| `f94_outline_zbias_camera_primary.py` | `GlobalCharOutlineZBias` 実値とカメラnear/farの手元全入力探索（陽性対照7入力つき） |
| `f95_p1_feet_dorm_switch.py` | 足元を `_Fight`(ハイヒール)→`_Dorm`(裸足) へ切替（原作フレーム根拠・武田さん承認） |
| `f96_dorm_scene_decompressed_scan.py` | 展開走査の初版。import不備＋速度不足で**無効**（台帳参照禁止） |
| **`f97_local_first_gate.py`** | **静的差分・生バイト走査だけの「無い／欠損」断定を止める門（陽性対照+実行時照合）** |
| `f98_bundle_block_scan.py` | UnityFSブロック直読み+lz4/lzma展開の高速全量走査。GFMB lightProbesのapp同梱所在を確定 |
| `f99_dorm_lightprobes_extract.py` | 寮LightProbes（8プローブ×RGB球面調和27係数・オクルージョン8組）を台帳化 |

## 使わなかったもの・落とした情報

- **対象appの再署名・注入・無断attach**: 署名を変えたデバッグ複製、外部ツールの注入、明示同意前の対象attachは行っていない。そのため手元のゲームapp・ログイン状態・画面は今回変化していない。許可する接続方法と保護条件を武田さんが明示選択した後、原本を保存した別作業として再開できる。
- **照明リグ**: ASMR / Lobby / CommandCenterの候補は新たには適用していない。そのため手元の照明は現行のままで、候補による見た目の変化はない。ただし現行blendには既存のAREA灯3つが残り、`LIGHT_裏`は作成スクリプト上「原作に無い」と明記されている。H0157への直接対応が回収できた場合のみ再適用できる。
- **階調表の推測案**: 白、body系流用、現行既定値を原作値とする扱いは捨てた。今回回収したHelen RampSettingは未適用。ただし現行blendには原作Ramp非対応22材質のBlender既定黒→白が残り、12材質で基本色に影響する。prefabから材質対応を回収後、原作RampSettingを機械適用できる。
- **旧輪郭線式**: `P22=+1`、軸別でない1本のクランプ、`RATIO_MAX=64`、世界法線押し出しを撤去。輪郭線の出方は変わったが見た目比較は未確認。`blends/_pre-f77-world-clip/helen-h0157-repro__be227dbc790bfcaf.blend` から復元可能。

---

## 13. この案件の運用ルール（**破られた実例つき。wiki 正本にも同じものがある**）

短縮版。全文は wiki 正本の「この案件の運用ルール」節。

1. 報告の前に**独立 checker**（結論を書かない指示で）を必ず通す
2. 報告には **成果物と場所 / 原作との差 / 計画との差 / checker の結果** の4点を必ず入れる
3. 中途半端な状態で報告を返さない
4. 「無い」と書く前に保管場所が接続されているか確かめる
5. INF を OBS に格上げしない
6. **数値の指標で見た目を判定しない。**合否は原作の絵との見比べ
7. 一次資料を先に読む。読まずに武田さんへ質問しない
8. 断定には**根拠と分母**を添える
9. 当てはめる前に**原作のシェーダのコード**を読む
10. 武田さんが見ているモード（**マテリアルプレビュー**）で判定する（`f28`）
11. subagent への指示に**自分の結論を書かない**
12. checker に「未完了が完了扱いされていないか」を確認させる
13. 古い未解決項目は、現在も有効かを確かめてから残す
14. **作業のたびに wiki 正本を同期する**
15. 承認は **AskUserQuestion** で取り、承認が出るまで会話を閉じない
16. **ゴールは原作準拠。**見た目の好みやこちらの都合で決めない

### このセッションで新しく分かった失敗の型（次も起きる）

- **問いの立て方が違うと、0件が「無い」に見える。**（`f59` の照明・CAB の名前空間）
  → **陽性対照**（同じ方法で有るはずのものを探す）を必ず付ける。4節の門の中心
- **静的な差分で「無い」と出ても、実行時の観測と突き合わせない限り「無い」は確定しない。**
  （2026-08-23・scene root欠損誤判定 → 圧縮内部に実在）
  → 圧縮内部を含む展開走査と実行時照合を必須にする（`f97` で機械化した）
- **自分の式を両辺に置くと、検証はいつでも 0 になる。**（`f64`）
  → 検証は**原作**か**独立実装**と比べる
- **既定値は「原作の値」ではない。**（輪郭線の色 0.6・階調表の黒→白）
  → シェーダの既定値を採る前に、実際の材質の分布を数える
