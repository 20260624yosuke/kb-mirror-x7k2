# ye_jji ch13 線画の活用 — 進捗STATE

- 対象: 13_1(1064.9s)/13_2(1066.3s)/13_3(905.2s)/13_4(678.3s) = 4動画・62分
- 設計: video-visual-ingest-design v2.4(vault 内永続 staging・バッチ即時書き込み)
- source: wiki/sources/coloso-ye-jji-ch13-lineart.md
- frames dir: wiki/assets/frames/coloso-ye-jji-ch13-lineart/(snapshot.json 作成済み)

## 手順と状態
- [x] 文字起こし読み(--at 誘導時刻の抽出) ※transcript は _01 のみ。p1 誘導33箇所
- [x] dry-run(54+54+46+34=188枚+誘導33)
- [x] snapshot(抽出前) → `wiki/assets/frames/coloso-ye-jji-ch13-lineart/snapshot.json`
- [x] 抽出 → staging/p1(84枚)/p2(54枚)/p3(46枚)/p4(34枚) = **218枚・完了**
- [ ] 盲検読取(13バッチ・各バッチが observations_pN_bX.txt へ自己書き込み) ← **次の再開点はここ**
- [ ] recheck(max(3, ceil(保存数×10%))・新規コンテキスト読取者)
- [ ] 保存・manifest(videos[]+extraction[]+observations+recheck・status complete)
- [ ] source 反映(映像観測節 6列表+visual_ingested)
- [ ] index/log/inbox
- [ ] gate complete PASS(retrofit snapshot は完了後に撮り直し)
- [ ] staging 削除

## 盲検読取バッチ定義(13バッチ・結果ファイルは staging 直下)
- p1-b1(17): 00m00s〜03m00s → observations_p1_b1.txt
- p1-b2(17): 03m20s〜06m00s → observations_p1_b2.txt
- p1-b3(17): 06m09s〜09m20s → observations_p1_b3.txt
- p1-b4(17): 09m40s〜13m23s → observations_p1_b4.txt
- p1-b5(16): 13m40s〜17m40s → observations_p1_b5.txt
- p2-b1(18): 00m00s〜05m40s → observations_p2_b1.txt
- p2-b2(18): 06m00s〜11m40s → observations_p2_b2.txt
- p2-b3(18): 12m00s〜17m40s → observations_p2_b3.txt
- p3-b1(16): 00m00s〜05m00s → observations_p3_b1.txt
- p3-b2(16): 05m20s〜10m20s → observations_p3_b2.txt
- p3-b3(14): 10m40s〜15m00s → observations_p3_b3.txt
- p4-b1(17): 00m00s〜05m20s → observations_p4_b1.txt
- p4-b2(17): 05m40s〜11m00s → observations_p4_b2.txt

## 読取エージェントへの指示テンプレ(再開時にそのまま使う)
- 盲検: 指定画像以外読まない。画面上の事実のみ日本語で。アプリ名/タイトルバー/キャンバス進行/ツール/Tool property 数値/レイヤー構成/焼き込み字幕逐語/透かし。推測禁止・読めない文字は「判読不能」。直前フレーム同一なら「前フレーム(XX)と同一画面。」+変化点。確信度 high/medium/low。
- 出力: `FILENAME || confidence || 観測文` を指定の observations ファイルへ Write し、返信は行数のみ。

## 再開点
- 現在: **抽出まで完了・盲検読取は 0/13 バッチ**(2026-08-26、サブエージェント用エンドポイントが
  「Upstream request failed: Endpoint is unavailable」で停止中・4回再試行すべて失敗のため中断)。
- 再開方法: 新規セッションで「yejji-ch13 の STATE.md を読んで映像ingestの続きをして」と依頼すれば、
  このファイルのバッチ定義どおり p1-b1 から再開できる。フレームは vault 内 staging のため揮発しない。
- 注意: 読取はサブエージェント(盲検)で行う。エンドポイントが混んでいる場合は同時実行数を絞る(1〜2体)。
