# ixy-2024 代表比較・受入証拠(2026-08-25・バッチ中断時点)

## 状態(重要)

- intake 骨格 82本・検収承認済み。バッチ文字起こしは **12/82(ok 8 + nospeech 4)でユーザー判断により中断**(GPU並列競合で所要約18時間超過見込みのため)。`batch_safe: false`。
- 本レポートは**中断時点**の受入証拠。残りの再開は最下部の手順どおりに行う。

## 比較方法(パイロット [[coloso-intake-design]] A0-A6 準拠)

- 監査: `tools/coloso_intake_audit.py`。結果の正本: `reports/2026-08-25-ixy-2024-audit.md`(全講座 PASS・不合格0)。
- 逐語照合(A6): ページ本文ブロックを `coloso_transcribe.build_verbatim_body` で `.json/.vtt` から**再計算**し完全一致を機械判定。
- 元動画との対応: `_attachments/NN<ext>` は HDD 実体への symlink。`mapping.json`(size+mtime記録)照合で差し替え検知を含む(A3)。
- NN対応表の正本: `reports/2026-08-25-ixy-2024-dryrun.md`(name ソート・カード承認済み)。

## 代表入力・出力

- 代表逐語: `_attachments/80.mov`(← HDD `/21/21_編集.mov`, 68MB, 処理183.6秒) → `coloso_ixy_80 (未確認).md`。総括講義の自然な逐語73行。
- 無音候補サンプル: `01.MP4`(6分間・全30秒窓が幻聴フィラーのみ→ページ追記なし=未完扱い)、`04.MP4` `10.MP4` `12.MP4`(nospeech判定)。
- バッチ処理済み ok 8本: NN02, 03, 05, 06, 07, 08, 09, 11。

## ユーザー受入(すべて 2026-08-25 の承認カード回答)

1. 講師名正綴凍結: 「**ixy**(小文字)」選択。
2. ソートキー確定: 「name を承認」+ 上記 dry-run 対応表。
3. **Obsidian symlink 動画再生: 「再生できた」**(coloso_ixy_80 ページでの実再生確認・N1実証)。
4. 検収承認: 「承認・残り全本へ」(NN対応表・逐語目視・監査PASS を含む総合承認)。
5. バッチ中断: 「中断していいや。wikiにここまでの経緯を記録して、いつでも再開できるようにしておいて」。
- 承認範囲: 2024_04_22_ixy 講座のみ。他講座への流用不可(M3)。

## 受容済みの限界

1. 公式カリキュラム章題未確定(`title_source: unconfirmed`・ページ名 `(未確認)`)。確定源(Coloso公式 or 武田さん入力)を得た時点で R3 に従い更新。
2. nospeech = Whisper が発話ゼロと判定しただけ。無音の確定証拠ではない。該当ページには transcript 節を付けず未完扱い(R7)。必要なら個別に人手確認。
3. **残り 70本未処理**(`batch_safe: false`)。再開後は必ず監査を通し、代表承認の範囲を超えた判断が必要になったら停止して報告する。

## 再開手順

1. `/Volumes/HDD_02` がマウントされていることを確認。
2. `python3 tools/coloso_intake_audit.py` → 全講座 PASS を確認。
3. `python3 wiki/builds/coloso-intake/reports/ixy-2024-batch-runner.py` を KB ルートで実行(state は同ディレクトリの jsonl を読み書き・ok/nospeech 済み NN は自動スキップ)。
4. 完了後: `tools/coloso_intake_audit.py` 全項目 PASS → 本レポートの「状態」と `quality-gate.json` の `ixy-2024` ブロック(batch_safe 等)を更新 → log.md 追記。
