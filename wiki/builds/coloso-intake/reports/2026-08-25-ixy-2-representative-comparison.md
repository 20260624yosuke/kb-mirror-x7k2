# ixy-2 パイロット 原作比較・受入証拠(2026-08-25)

## 比較方法

- 監査: `tools/coloso_intake_audit.py`(設計 [[coloso-intake-design]] の A0-A6)。結果の正本: `reports/2026-08-25-ixy-2-final-audit.txt`(全講座 PASS)。
- 逐語照合(A6): 各ページの本文ブロックを `coloso_transcribe.build_verbatim_body` で `.json/.vtt` から**再計算**し、ページ内と完全一致を機械判定(23/23 本)。
- 元動画との対応: `_attachments/NN.mov` は HDD 実体への symlink であり、`mapping.json` の size+mtime 照合で差し替え検知を含む(A3)。
- 代表入力: `_attachments/01.mov`(処理824.7秒)・`23.mov`(243.8秒)。代表出力: `coloso_ixy_01 (未確認).md`・`coloso_ixy_23 (未確認).md`。

## ユーザー受入

- 検収カード回答(2026-08-25): 「問題なさそうだね。タスクを進めていいです。」— NN対応表・Obsidian再生・逐語目視の3点を提示したうえでの総合承認。
- 承認範囲: 2025_09_27_ixy_2 講座(23本)の intake 骨格+逐語文字起こし品質のみ。他講座への流用不可(M3)。

## 受容済みの限界

1. Obsidian での symlink 動画再生について、再生画面そのものの明示報告は未取得(総合承認に含む)。再生不能が判明したら本 family を再オープンする。
2. 公式カリキュラムの章題は未確定(`title_source: unconfirmed`、ページ名に `(未確認)`)。本 family のスコープは逐語移植までで章題は含まない。確定源(Coloso公式 or 武田さん入力)を得た時点で R3 に従い更新する。
