---
type: analysis
title: Raycast v2 移行 — 無音点検の結果と残タスク(再開手順)
created: 2026-08-22
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-22
---

# Raycast v2 移行 — 無音点検の結果と残タスク(再開手順)

## 経緯

2026-08-22、武田さんが X で Raycast v2 正式リリースを知りダウンロード。「旧版が開いてるらしくて置き換えられない」と相談。使用感への影響を質問したため、まずユーザー操作不要の「無音確認」(スクリプト本体・依存・移行痕跡の機械確認)を実施した。実行テスト(画面にウィンドウが出るもの)と Raycast UI からの起動テストは残タスクとして本ページに記録。

## 実機確認結果(2026-08-22・無音確認)

### 重要な発見: v2 は既に入って稼働している

- `/Applications/Raycast.app` の `CFBundleShortVersionString` = **2.0.5.0(v2)**、プロセス稼働中。
- macOS 26.5.1(Tahoe 系)で v2 の要件(Apple Silicon + Tahoe)を満たす。
- `defaults read com.raycast.macos` に `fallbackSearches_didMigrateScriptCommands = 1`(v2 側マイグレーション処理の実行痕跡)と `database_lastValidAppVersion = 1.104.25`(v1 最終版)が残る。
- → 「旧版が開いている」ように見えたのは常駐ランチャーが動いていたため。**入れ替え作業自体は済んでいる**。

### Script Commands 9 本(`~/.config/raycast-scripts/`)の点検

| スクリプト | 権限 | shebang | メタコメント | bash -n |
|---|---|---|---|---|
| google_tasks_quickadd.sh | OK | OK | schemaVersion 1 / title / mode / argument1 / needsConfirmation | OK |
| llm_wiki_open.sh | OK | OK | 同上 + packageName/icon | OK |
| multi_site_image_search.sh | OK | OK | schemaVersion 1 / title / mode / argument1 | OK |
| pick_window_layout_version.sh | OK | OK | OK | OK |
| restore_after_mac_reboot.sh | OK | OK | OK | OK |
| restore_after_obsidian_restart.sh | OK | OK | OK | OK |
| restore_obsidian_layout.sh | OK | OK | OK | OK |
| save_current_window_layout.sh | OK | OK | OK | OK |
| save_current_window_layout_force.sh | OK | OK | OK | OK |

### 依存関係(すべて存在確認)

- `~/.config/google-tasks-quickadd/`: `venv/bin/python3` → `/opt/anaconda3/bin/python3` (→ python3.11) シムリンク OK、`google_tasks_quickadd.py` 構文 OK、OAuth 用 `token.json` / `client_secret.json` 存在。
- `tools/open_in_obsidian.py` 構文 OK(llm_wiki_open.sh の依存)。`command -v python3` = `/opt/anaconda3/bin/python3`。
- KB tools 正本 4 本(pick_window_layout_version / restore_after_mac_reboot / restore_after_obsidian_restart / save_current_window_layout)存在。
- Google Chrome 存在(multi_site_image_search.sh の依存)。
- `~/Library/Application Support/com.raycast.macos/NodeJS/runtime/22.22.2` 存在(sign-firefox-xpi.sh が参照するパスは v2 下でも生存)。
- `MSIS_DRYRUN=1` でのドライラン実行成功: Chrome を開かず日本語ワードの URL エンコード(通常/アンダースコア/Instagram タグの3形式)まで正常出力。

## Raycast v2 公式情報の要点(web 出典: raycast.com/new, changelog 2026-08-19, blog)

- UI 全面刷新(Tahoe 向け)、Root Search へファイル検索統合(Rust 製独自インデクサ)、ホットキーレコーダ改善(fn 単体・左右修飾キー区別・国際キーボード対応)。
- AI 強化: Agents(旧 Presets)/ Memory / Skills / Screen Awareness / チャットフォルダ・分岐。
- **AI Chat と Dictation(音声入力)は正式版から Pro(有料)**。ベータ中は無料。サインインで 7 日トライアル。
- オンボーディングで v1 セットアップをインポート。一部権限の再許可が必要な場合あり。
- 既知回帰: フォールバック起動 + 必須引数のコマンドが「Value is missing in argument」で失敗(GitHub raycast/extensions #28986)。フォールバック登録していないコマンドには無関係。

## 未検証・残タスク(再開ポイント)

1. **Raycast UI での起動テスト(ユーザー操作・数秒)**: ⌥Space → コマンド名入力 → Enter。特に argument1 + needsConfirmation の 2 本(タスク追加 / Obsidianページを開く)と画像検索、layout 系 4 本。
2. Settings → Extensions で `~/.config/raycast-scripts` フォルダ登録が引き継がれているか目視確認(v2 の DB は独自形式で機械読取不可だったため未確認)。
3. AI Chat / Dictation の Pro 化の扱いを武田さんが決める(使うなら Pro 登録、使わないなら放置)。
4. 権限再許可の要否確認(ホットキーやウィンドウ操作が効かなくなったら許可を見直す)。
5. layout 復元系 4 本の実行テストは画面配置が変わるため、作業時間帯を避けて実施。

## 使わなかったもの・落とした情報

なし(今回の確認で収集した情報はすべて本ページへ記載)。

## 関連リンク

- [[multi-site-image-search]]
- [[google-tasks-quickadd]]
- [[obsidian-direct-open-entrypoint]]
- [[window-layout-restore]]
