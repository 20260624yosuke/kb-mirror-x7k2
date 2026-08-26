---
type: build
title: "マーセ ch12 読取結果の救出記録"
status: active
confidence: high
evidence_level: source-backed
sources: [coloso-marse-ch12-underdrawing-perspective]
created: 2026-08-26
last_reviewed: 2026-08-26
---

# マーセ ch12 読取結果の救出（2026-08-26）

## 経緯

- 2026-08-25 セッション（`ses_fc81a7f55ffezofYvTJIkrq61b`）で ch11 完走後、ch12 の盲検・sweep 読取を進めたがセッション死亡。
- 2026-08-26 セッション（`ses_fc75e6212ffefJZOELYoDUS1uL`）で DB からの回収を試みたが再度死亡（二重中断）。
- 本日、opencode.db の task tool 出力から直接救出した。

## 救出物

`recovered-readings.json`（29バッチ中18バッチに出力あり・計63,189文字）。subagent の観測テキストをバッチ単位でそのまま保存。

### ch12-01（12_01-sweep・91枚）— 読取としては完走していた

- 盲検読取 57枚： 5バッチ全部成功（00m00s〜12m40s 台）
- sweep読取 91枚： 4バッチ全部成功（sweep-001〜091）

### ch12-02（12_02-sweep・120枚）— 盲検のみ完走

- 盲検読取 71枚： 6バッチ全部成功（00m00s〜16m40s 台）
- sweep読取： **未完**。sweep-001〜024 は出力空（失敗）、以降も記録なし

### 同梱

- ch11 分（スイープ前半25＋後半37＋recheck 6）＝完走済み分の控え

## 失われていて再実行が必要なもの

1. ch12-02 の sweep 120枚分の読取
2. フレーム PNG 本体の本保存（`wiki/assets/frames/coloso-marse-ch12-underdrawing-perspective/`）— システム一時領域は PC 再起動で消滅済みのため再抽出が必要
3. manifest.json 作成／第2読者 recheck／source ページへの映像観測節反映／gate complete PASS／`visual_ingested` 付与／index・log 更新

## 再開手順

1. `tools/video_frames.py --dry-run` からやり直し（staging 再構築）
2. ch12-01 は救出観測を流用できるが、設計 v2.3 の機械検査（表行数＝manifest 等）を通すため、source 反映時に本ファイルの観測を転記して整合させる
3. ch12-02 の sweep を再実行
4. 以降は [[video-visual-ingest-design]] v2.3 の手順どおり
