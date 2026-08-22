---
type: analysis
title: X→Eagle保存拡張の現状と干渉リスク監査
created: 2026-06-24
sources:
  - x-eagle-free-save-pilot
  - eagle-review-view-plugin-design-2026-06-24
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-24
---

# X→Eagle保存拡張の現状と干渉リスク監査

## 確認時点

2026-06-24、武田さんから「このプロジェクトの存在を忘れていた。何を実装したのか、他プロジェクトと干渉していないか」と質問があったため、Wiki記録とローカル実環境を確認した。

## 現在の実装状態

現行コードは、拡張機能 `tools/x-eagle-save-extension/manifest.json` が v0.5.18、動画補助処理 `tools/x-eagle-video-helper/server.js` が v0.5.15。

主な実装済み内容:

- X画像を Eagle へ保存する Firefox / Chrome 共通拡張。
- Eagleフォルダ検索、最近フォルダ、新規フォルダ作成、保存候補チェック除外、保存成功後自動クローズ。
- X動画を `yt-dlp` 補助処理で取得して Eagle へ保存する経路。
- クリップボード上の TikTok など外部動画URLを Eagle へ保存する経路。
- Eagleメモ欄を `【見る用】` と `【LLM用】` に分ける注釈形式。
- Firefox通常版へ入れるための署名済み `.xpi` 生成。
- Firefox自動更新用の `update_url`、`updates.json`、GitHub Pages向け配布補助。
- v0.5.18 では、重複画像で既存項目を使った場合に、既存項目を1件だけ特定できるときのみ新しいXメタデータ注釈を既存メモ上部へ追記する。

未実装:

- Eagle の Window Plugin（クリックすると別画面を開く形式）によるレビュー専用ビュー。
- Eagle の Inspector（右側詳細欄への追加表示）プラグイン。

## 現在の稼働状態

- Eagle API: `localhost:41595` で応答あり。Eagle 4.0.0 / build 20260401。
- 動画補助処理: `127.0.0.1:41795` で起動中。`/health` は v0.5.15 を返す。
- LaunchAgent: `com.takedayousuke.x-eagle-video-helper` は `running`。ログは `~/Library/Logs/XEagleVideoHelper/`。
- Firefox自動更新URL: `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` はHTTP 200で応答。ただし公開中の更新情報は v0.5.17。ローカルの v0.5.18 配布ファイルは作成済みだが、GitHub push 認証がなく公開反映は未完了と記録されている。

## 干渉リスク

現時点で確認できる範囲では、他プロジェクトとの直接衝突は見つからない。

理由:

- 使用ポートは分離されている。Eagle本体は `41595`、X/Eagle動画補助処理は `41795`、PureRef中継は `17777`。E-Hentai/Notion clipper 候補の `41885` は待受なし。
- X/Eagle拡張は、通常はブラウザ拡張ボタンや右クリック保存を使ったときだけ Eagle に書き込む。勝手に既存Eagle項目を巡回する処理はない。
- ビュー専用 Eagle Plugin は未実装なので、Eagle本体の表示や右側パネルに常駐するものはない。

注意が必要な点:

- v0.5.18 では、重複保存時に既存項目を1件だけ特定できる場合、既存Eagle項目のメモ上部へ新しいXメタデータ注釈を追記する。これはユーザー操作を起点にした書き込みだが、「既存項目は絶対に触らない」状態ではなくなっている。
- 動画補助処理は LaunchAgent で常駐している。外付けSSD未マウント時の起動挙動は未確認。
- Firefox自動更新の公開先は現在 v0.5.17 を配っている。ローカルコードは v0.5.18 なので、インストール済みFirefox拡張がどの版かは別途Firefox画面で確認が必要。
- 外部動画保存は `yt-dlp` とサイト側仕様に依存する。ログイン必須、DRM（コピー防止の仕組み）、サイト側制限の動画は失敗しうる。

## 判断

現状は、他プロジェクトへ強く干渉している状態ではない。

ただし、このプロジェクトは「完全に眠っている」わけでもない。動画補助処理は常駐中で、Firefoxに固定ID版の拡張が入っていれば、ユーザー操作に応じてEagleへ保存・既存項目への注釈追記・Firefox自動更新確認が起きる。

したがって、放置するなら次のどちらかを選ぶのが安全。

- 運用継続: そのまま。必要時だけ使う。干渉は小さい。
- 凍結: Firefox拡張を無効化し、`com.takedayousuke.x-eagle-video-helper` LaunchAgent を止める。Eagle本体や既存データはそのまま。
