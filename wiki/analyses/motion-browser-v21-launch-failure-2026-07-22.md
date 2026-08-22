---
type: analysis
title: Motion Browser v2.1 が開かない原因調査
created: 2026-07-22
prompted_by: Motion Browser.app が開かない理由の精査
sources:
  - gf2-helen-starlit-waltz-3d-reference-build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-22
---

# Motion Browser v2.1 が開かない原因調査

## 問い

`library-v2-fidelity/retarget-fix-pilot/Motion Browser.app` をダブルクリックしても開かない理由は何か。
初回調査では修正せず、Wiki の実装記録、現物、macOS の起動記録を照合した。
同日 2026-07-22 に、アプリ入口の修正と LaunchServices 経由の検証まで実施した。

## 回答（要約）

直接原因は、アプリ内の起動スクリプトが、アプリの外にある同階層の
`Open Motion Browser.command` を読み込んで実行しようとする構成である。macOS はアプリ本体の起動には
成功したが、システム方針により外部 `.command` の `file-read-data`（ファイル内容の読み取り）を拒否した。
その結果、`bash` は終了コード 126（実行できない）で終了し、ブラウザーもサーバーも起動しない。

内部の `browser/server.py` と配信データは壊れていない。Python からサーバーだけを起動する分離試験では、
版情報 API、トップページ、動画の部分配信がすべて正常だった。

## 根拠

### 1. 2026-07-22 の実際の起動記録

macOS 統合ログでは、13:20:35、13:20:45 前後、13:21:03 に
`local.helen.motionbrowser.v21` の起動要求が通り、`MotionBrowser` プロセスが生成されている。
したがって「Finder がアプリを認識できない」「実行権限が無い」「外付け SSD が `noexec`」は直接原因ではない。

各起動の直後に、カーネルが次を記録している。

```text
(Sandbox) System Policy: bash(...) deny(1) file-read-data
/.../retarget-fix-pilot/Open Motion Browser.command
```

その直後にアプリは終了し、launchd の終了状態は `32256`。シェル終了値へ戻すと `32256 / 256 = 126` で、
「コマンドを実行できない」に対応する。

### 2. 現物の呼び出し構造

アプリの `Contents/MacOS/MotionBrowser` は 106 バイトのシェルスクリプトで、次の外部ファイルを `exec` する。

```bash
APP_DIR="$(cd "$(dirname "$0")/../../.." && pwd)"
exec "$APP_DIR/Open Motion Browser.command"
```

`../../..` は `.app` の外側の `retarget-fix-pilot/` を指す。この構成が、macOS ログで拒否されたパスと一致する。

### 3. バックエンドの分離試験

`/usr/bin/python3 browser/server.py` を直接起動し、次を確認した。

- `GET /api/version`: HTTP 200。`dataset_id = helen-v2.1-corrected-pilot-20260720`
- `GET /`: HTTP 200、`text/html`
- `GET /videos/H0052.mp4`（先頭 1024 バイト）: HTTP 206、1024 バイト返却

試験後は `Ctrl-C` でサーバーを終了した。修正や常駐化は行っていない。

## 副次的な問題

- アプリはコード署名されておらず、`spctl`（macOS の実行可否審査）は `no usable signature` として拒否する。
  ただし今回の実ログでは、macOS はアプリ本体を実際に生成・実行した後、外部 `.command` の読み取りで止めている。
  よって未署名は配布・将来安定性の問題ではあるが、今回観測した即時終了の直接原因ではない。
- `LSUIElement = true` のため、起動途中のアプリは Dock に通常表示されない。さらにランチャーの標準エラーは
  Finder から見えず、外部 `.command` に到達する前に落ちるので専用ログも更新されない。利用者には「無反応」に見える。
- `reports/motion-browser-v2.1.log` は 2026-07-20 の作成時試験でサーバー起動と版情報取得を記録しているが、
  2026-07-22 の失敗時には更新されていない。これは失敗位置がログを書き始める外部 `.command` より前であることと一致する。

## 実装経緯との照合

- 既存 Wiki の [[gf2-helen-starlit-waltz-3d-reference-build]] は、2026-07-16 までの 3D 資料化と v2 物理資料を記録している。
- 問題の v2.1 アプリは 2026-07-20 作成で、アプリ固有の設計・起動試験・現在の失敗経路は、調査開始時点の
  `index.md` / `wiki/` / `log.md` には未収載だった。
- v2.1 の実装内容とユーザーレビュー手順は、プロジェクト側の `IMPLEMENTATION-STATUS.md` と
  `USER-REVIEW.md` に保存されていた。ただし両文書は「`Motion Browser.app` をダブルクリックする」とするだけで、
  アプリ外 `.command` 読み取りが macOS に拒否される可能性は検証項目に含めていなかった。
- 一つ前の v2 アプリはサーバースクリプトを `.app/Contents/Resources/` 内に置いている。v2.1 で外部ランチャーへ
  委譲する構成に変わったことが、今回の回帰（以前は成立した入口が新しい版で壊れたこと）に対応する。

## 現在の判定

- 原因特定: 完了（確信度 high）
- アプリ修正: 実施済み
- 自動試験: 直接起動と LaunchServices 経由の API / トップページ / 動画 Range 配信が成功
- Finder からの改善確認: LaunchServices 経由では成功。前面ブラウザ表示そのものは、画面占有を避けるため `HELEN_BROWSER_NO_OPEN=1` で抑止して検証した。
- 運用開始可能: Motion Browser 入口は起動可能。Blender 実機での見た目確認と貫通監査の解釈は別件として未完了。

## 2026-07-22 修正結果

修正では、`.app` 起動時にアプリ外の `.command`、ログ、ブラウザー用カタログ、動画を読みに行く構造をなくした。

- `Contents/MacOS/MotionBrowser` を ASCII の薄い bash ランチャーに変更した。
- 実体のサーバーを `Contents/Resources/server.py` に配置した。
- `browser/index.html`、`browser/catalog.json`、`previews/videos/` をそれぞれ `.app/Contents/Resources/` 配下へ同梱した。
- 起動ログはプロジェクト配下 `reports/` ではなく `/tmp/helen-motion-browser-v2.1.log` へ出すようにした。LaunchServices 経由のプロセスは、プロジェクト配下への起動時書き込みも System Policy に拒否されたため。
- アドホック署名は外した。旧 v2 と同じ未署名ローカルアプリとして扱う。

検証結果:

- `bash -n` と `python3 -m py_compile`: 成功。
- 直接起動: `GET /api/version` が HTTP 200、`GET /` が HTTP 200、`GET /videos/H0052.mp4` の Range 読み込みが HTTP 206 / 1024 バイト。
- LaunchServices 経由起動: 同じく `GET /api/version` が HTTP 200、`GET /` が HTTP 200、動画 Range 読み込みが HTTP 206 / 1024 バイト。
- `HELEN_BROWSER_TEST_MODE=1` で `POST /api/open-blender` に `{"view_action_id":"H0052","frame":0}` を送信し、HTTP 200 / `{"status":"launched"}` を確認した。
- 2026-07-22 14:03:24 以降の System Policy ログでは、`retarget-fix-pilot` に対する拒否は確認されなかった。

## 関連リンク

- [[gf2-helen-starlit-waltz-3d-reference-build]]
