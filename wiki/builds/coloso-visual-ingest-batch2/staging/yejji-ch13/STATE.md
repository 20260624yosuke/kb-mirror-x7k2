# ye_jji ch13 線画の活用 — 進捗STATE

- 対象: 13_1(1064s)/13_2(1066s)/13_3(905s)/13_4(678s) = 4動画・62分
- 設計: video-visual-ingest-design v2.4(vault 内永続 staging・バッチ即時書き込み)
- source: wiki/sources/coloso-ye-jji-ch13-lineart.md

## 手順と状態
- [ ] 文字起こし読み(--at 誘導時刻の抽出)
- [ ] dry-run
- [ ] snapshot(抽出前)
- [ ] 抽出 → staging/p1..p4
- [ ] 盲検読取(バッチごとに observations_pN_batchX.txt へ書き込み)
- [ ] recheck
- [ ] 保存・manifest
- [ ] source 反映
- [ ] index/log/inbox
- [ ] gate complete PASS → visual_ingested
- [ ] staging 削除

## 再開点
- 現在: 開始直後(文字起こし読みから)
