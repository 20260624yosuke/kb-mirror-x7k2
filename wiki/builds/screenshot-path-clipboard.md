---
type: build
name: screenshot-path-clipboard（スクショパス自動コピー）
aliases: [スクショパス自動コピー, screenshot-path-clipboard]
tags: [macOS, screenshot, clipboard, llm, automation, launchagent]
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-05
---

# screenshot-path-clipboard（スクショパス自動コピー）

## 現在の統合見解

スクリーンショットを保存した直後に、その画像ファイルの絶対パスをクリップボードへ入れる
Mac 用の小さな自動化。LLM との会話で、スクショ画像をファイルパスとして渡す作業が増えたため、
「撮る → Finder で探す → パスをコピーする」という手順を省く。

保存先は macOS のスクリーンショット設定を読む。2026-07-05 時点では
`~/Library/Mobile Documents/com~apple~CloudDocs/ダウンロード/02_スクショ保存`、
保存形式は `jpg`。保存先を macOS 側で変更した場合も、スクリプトは次回実行時に設定を読み直す。

## 実装

- 本体: `~/.local/bin/screenshot-path-clipboard`
- 自動起動: `~/Library/LaunchAgents/com.takedayousuke.screenshot-path-clipboard.plist`
- 監視方式: LaunchAgent の `WatchPaths` でスクショ保存先フォルダの変更を監視する。
- 状態ファイル: `~/Library/Application Support/ScreenshotPathClipboard/last-copied.tsv`
- ログ: `~/Library/Logs/screenshot-path-clipboard.log`

動作は、フォルダ更新を検知 → 1 秒待機 → 保存先直下の最新画像
(`jpg/jpeg/png/heic/tif/tiff`)を選択 → 前回処理済みと違えば `pbcopy` で絶対パスを
クリップボードへ入れる、という流れ。

## 検証状態

- 実装済み。
- `bash -n ~/.local/bin/screenshot-path-clipboard` 成功。
- LaunchAgent plist は `plutil -lint` 成功。
- `launchctl print gui/$UID/com.takedayousuke.screenshot-path-clipboard` で `WatchPaths` が
  スクショ保存先を監視中であることを確認。
- 一時ディレクトリを使った自動試験で、最新画像の絶対パスが `pbcopy` 経由でクリップボードへ
  入ることを確認。

## 矛盾・未確定

- 実際の `Cmd+Shift+4` などの手操作スクショでの改善確認は未実施。LaunchAgent とコピー処理は
  確認済みだが、ユーザーの通常操作での体感確認は次回スクショ時に行う。
- スクショを「クリップボードへコピー」設定で撮った場合は、保存ファイルが作られないため
  ファイルパスは取得できない。このツールの対象は「ファイルとして保存されるスクショ」。

## 変遷

- 2026-07-05: 初版実装。既存最新スクショを `--prime` で処理済みにしてから LaunchAgent を
  読み込み、以後の新規スクショだけをコピー対象にした。

## 関連リンク

- [[clip2md]] — 長文を `.md` 化し、保存後にファイルパスをクリップボードへ戻す既存ツール。
