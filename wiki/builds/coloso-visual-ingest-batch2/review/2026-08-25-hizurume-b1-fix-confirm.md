---
type: analysis
title: "ひづるめ B1 群 修正再確認報告・PASS(承認確定)"
date: 2026-08-25
reviewer: 独立レビュアー(修正再確認・別セッション・opencode)
targets: [coloso-hizurume-ch09-light-shadow-color]
verdict: approve
status: active
confidence: high
evidence_level: source-backed
sources: [coloso-hizurume-ch09-light-shadow-color]
---

# ひづるめ B1 群 修正再確認報告(2026-08-25)

## 総合判定: **PASS(B1 群の承認確定)**

- 対象は前回修正確認レビューの要修正 **1 件のみ**: ch09 ev-091 のツール名「油彩**獣**毛 鶴」→「油彩**狸**毛 鶴」の訂正が、実フレームどおりに適用されたかの再確認。
- 修正指示 A〜C の適用実体と B1 群 4 章の観測内容は前回修正確認レビューまでに検証済みであり、本再確認で新たな問題は発見されなかった(追加修正指示 **0 件**)。

## 検証方法(レビュアー自身の実行による)

1. **source 確認**: `wiki/sources/coloso-hizurume-ch09-light-shadow-color.md` の ev-091 行(155 行目)のツール名が「油彩狸毛 鶴」(青いストロークプレビュー)・ブラシサイズ 50.0px・不透明度 98% になっていることを確認。
2. **実フレームとの突合**: 指示どおり `ffmpeg -ss 01:46 -t 8 -i "raw/_coloso/2026_05_31_ひづるめ/_attachments/09_02.mp4" -vf "tmix=frames=40,crop=180:36:1632:142,scale=iw*6:-1:flags=lanczos" -frames:v 1 /tmp/confirm-toolname.png` を自実行。40 フレーム平均化+6 倍拡大クロップの字形は、3 文字目が⺨(けものへん)+里=狸の細幅左右分割・4 文字目が毛・空白を挟み鶴で、source 記載どおり。保存済みフレーム `hizurume-ch09-02-01m46s.png` の同一座標クロップも自前で作成し比較 → 両者同一画像内容で一致(「獣」の幅広密集字形はどこにもない)。
3. **機械検証**: `python3 tools/video_ingest_gate.py check --manifest wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/manifest.json --source wiki/sources/coloso-hizurume-ch09-light-shadow-color.md --snapshot wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/snapshot.json --phase complete --index index.md --log log.md` を自実行 → **PASS (complete)**。警告「retrofit 実行のため本文非破壊は節の存在確認のみ」は既知の retrofit 由来で FAIL ではない(前回レビューも同様)。
4. **記録の同期確認**:
   - manifest `ev-091` observation が source 行と同一文言で同期(「油彩狸毛 鶴」含む)。
   - manifest recheck の 01m46s エントリ(verdict: confirmed)の note に訂正記録あり: 「ツール名の『獣』は誤読と判明 → 『油彩狸毛 鶴』へ訂正(実フレームの 3 時刻クロップ+40 フレーム平均化+同フォント同サイズの『狸/獣』参照レンダリング比較で確定)。source・manifest 観測文とも修正済み」。
   - `log.md` に訂正適用エントリ「## [2026-08-25] ingest | coloso ひづるめ B1 修正確認レビュー指摘の訂正適用(ch09 ev-091 ツール名 1 字)」あり。旧表記「油彩獣毛 鶴」は append-only のため直前の適用エントリ履歴(9816 行)に残るが、訂正エントリ内で明示されており履歴として正しい扱い。

## 判定

- 要修正 1 件(ツール名 1 字)は **実フレームどおりに適用済み**。source・manifest・recheck note・log の 4 か所が整合。
- 追加の修正指示は **0 件**。これをもってひづるめ B1 群(ch06/07/09/13/14)の修正確認は完了し、**承認確定**とする。
- 本判定と同時に、`wiki/builds/coloso-visual-ingest-batch2/quality-gate.json` の hizurume-b1-theory family へ承認確定記録を追記した(証拠=本ファイル)。

## 検証証跡

- 自前抽出クロップ: `/tmp/confirm-toolname.png`(動画から直接抽出・セッションの一時領域)
- 保存済みフレームの同一座標クロップ: `/tmp/confirm-saved-crop.png`
- ゲート出力: `video_ingest_gate: PASS (complete)`(retrofit 警告 1 行のみ)
