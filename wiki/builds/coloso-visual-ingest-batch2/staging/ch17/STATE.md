# ch17 ingest STATE(再開点)

- 開始: 2026-08-26 / 統括: opencode セッション(完全自律・approval_delegation 方針)
- 対象: raw/_coloso/2026_05_31_ひづるめ/coloso_ひづるめ_17 【実技1-1】暗い絵を描く.md
- 動画: 17_01.mp4(905.9秒・46枚)/ 17_02.mp4(799.7秒・40枚) / 20秒間隔 / 計86枚
- snapshot: wiki/assets/frames/coloso-hizurume-ch17-dark-painting-1/snapshot.json 作成済み
- staging 抽出済み: p1/(46枚)・p2/(40枚)

## 進捗

- [x] dry-run / snapshot / 抽出
- [ ] 盲検読取: p1 batch1(batch1: 00m00s〜05m40s) → observations_p1_batch1.txt
- [ ] 盲検読取: p1 batch2(06m00s〜12m00s) → observations_p1_batch2.txt
- [ ] 盲検読取: p1 batch3(12m20s〜15m00s) → observations_p1_batch3.txt
- [ ] 盲検読取: p2 batch1(00m00s〜06m40s) → observations_p2_batch1.txt
- [ ] 盲検読取: p2 batch2(07m00s〜13m00s) → observations_p2_batch2.txt
- [ ] recheck(サブエージェント・9枚)
- [ ] 本保存+manifest+source 節+index/log
- [ ] gate check PASS → visual_ingested
EOF
