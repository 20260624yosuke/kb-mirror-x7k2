# ch15 進捗(2026-08-26)

- snapshot: 済み(3本の動画 SHA 記録済み・wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/snapshot.json)
- **p1 (15_01.mp4, 904.5s): 完走**(以下の状態は初回時)。抽出46枚→盲検46枚(バッチA-D2、サーバエラー3回再実行で回収)→保存24枚→
  再確認4枚=max(3,10%)全て confirmed(02m20s のレイヤー番号は不一致のため観測文から除外)→
  `gate check --phase staging` **PASS**。manifest status: complete / visual_ingested は未付与(全パート完了後)
- p2 (15_02.mp4, 906.1s): 完走(保存15枚)
- p3 (15_03.mp4, 1005.9s): 完走(保存15枚)
- 全パート完走後: 観測節を置換更新 → `check --phase complete` PASS → visual_ingested 付与 → index/log 更新

## 退避ファイル
- read-p1-a.md (00m00s-03m40s) / read-p1-b.md (04m00s-07m40s) / read-p1-c.md (08m00s-10m40s) /
  read-p1-d1.md (11m00s-12m40s) / read-p1-d2.md (13m00s-15m00s)

## 運用メモ(サーバエラー対策)
- バッチ失敗時は同じプロンプトで再実行すればよい(読取は冪等)。連続失敗時はバッチを半分に割る
- 各バッチ返却後すぐこのフォルダへ退避済み。セッションが死んでもここから再開できる

## 完了記録 (2026-08-26)
- p2: バッチA-F・抽出46枚→保存15枚。再確認4枚 confirmed(ツール名・フォルダ番号の読取揺れは観測文から除外)。
  `gate check --phase staging` PASS。
- p3: バッチA-G・抽出51枚→保存15枚。再確認4枚でキャンバス名「kaotikokakata」→「kaonokakikata*」を corrected 確定。
  `gate check --phase complete` **PASS** → `visual_ingested: 2026-08-26` 付与、index/log 更新済み。
- ch15 完了。次は ch16 首(mp4 1本・約20分)から。
