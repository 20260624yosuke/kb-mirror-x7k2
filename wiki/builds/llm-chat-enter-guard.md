---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-18
---

# LLM チャット Enter 誤送信ガード

## 目的

LLM チャットアプリで Enter を押したときにメッセージが送信されてしまう事故を防ぐ。
日本語入力中に英数モードだと気づかず Enter を押す、文章途中で Enter を押す等。

対象アプリ: ChatGPT / Claude / Codex(デスクトップアプリ版)+ VS Code(Kimi Code 用、2026-08-18 追加)

## 完成イメージ

| 操作 | 挙動 |
|---|---|
| Enter | 改行（送信しない） |
| Cmd+Enter | 送信 |
| 日本語変換中の Enter | 変換確定（改行も送信もしない） |

ターミナル・ブラウザ等には一切影響しない。

## 現在の状態: 運用中(2026-08-18 に VS Code へ拡大)

### 2026-08-18: VS Code(Kimi Code)を対象に追加・採用

- 背景: Kimi Code(VS Code 上で動く TUI アプリ)の入力欄は Enter=送信の仕様で、誤送信が
  発生した。送信キーを変更する公式設定は存在しない(Kimi 公式 docs の keyboard /
  interaction ページで確認)。
- 事前検証: Kimi 入力欄で Shift+Enter=改行が効くことをユーザーが実機確認し、
  既存ルールへの対象追加で成立すると判断(ターミナルで Shift+Enter が区別されない
  場合の代替として `Enter→Ctrl+J` の別ルール案も用意したが不採用)。
- 変更: 既存ルール `LLM Chat: Enter→改行, Cmd+Enter→送信` の 2 つの manipulator
  両方の `bundle_identifiers` に `^com\.microsoft\.VSCode$` を追加(計 2 行)。
  バックアップ: `~/.config/karabiner/karabiner.json.bak-20260818`。
- 実機確認(ユーザー実施・全合格): Enter=改行 / Cmd+Enter=送信 / 日本語 IME の
  変換確定は正常。
- **例外(仕様上回避不可)**: Kimi の AskUserQuestion カードの自由入力欄は、
  置き換え後の Enter(=Shift+Enter)でも送信されてしまう。長文はカードを閉じて
  メイン入力欄に書く運用とする。
- 副作用(ユーザー許容済み): VS Code 上のすべての Enter 確定操作(ファイル名変更・
  Cmd+P でのファイル選択等)が Cmd+Enter 化。コード編集はしない運用のため実害小。
- 復元: `cp ~/.config/karabiner/karabiner.json.bak-20260818 ~/.config/karabiner/karabiner.json`
  (Karabiner が自動で再読込する)
- 既存 3 アプリでの実動作テスト(対照実験)は未実施のまま。効いていないことに
  気づいた場合は別件として調査する。

### 2026-06-20 時点の記録(一部はその後解決)

- Karabiner-Elements 16.0.0 への更新とルール追加は完了済み。BetterTouchTool は断念済み(理由は後述)。
- 「Mac 再起動待ちで保留」としていた件は、その後再起動されており、2026-08-18 に
  プロセス稼働を確認済み(`Karabiner-Core-Service` / `karabiner_console_user_server` /
  VirtualHIDDevice Daemon)。
- 旧未完了リストのうち「Claude/ChatGPT での Enter 挙動テスト」は記録上いまだ未実施。
- 「日本語変換確定が壊れた場合は `input_source_unless` 条件で日本語入力中は Enter を
  素通しにする」は、VS Code では不要だったが、既知のフォールバックとして残す。
- 「BTT に残っている Codex 用トリガー 2 つの削除」は実施済みか未確認。

## 設定ファイルと対象アプリ

設定ファイル: `~/.config/karabiner/karabiner.json`
ルール名: `LLM Chat: Enter→改行, Cmd+Enter→送信`

対象アプリの bundle ID:

| アプリ | bundle ID |
|---|---|
| ChatGPT | `com.openai.chat` |
| Codex | `com.openai.codex` |
| Claude | `com.anthropic.claudefordesktop` |
| VS Code(Kimi Code 用に追加 2026-08-18) | `com.microsoft.VSCode` |

アプリを追加したい場合は `bundle_identifiers` 配列に正規表現で追加する。

## 元に戻す方法

Karabiner-Elements の設定画面 → Complex Modifications →
「LLM Chat: Enter→改行, Cmd+Enter→送信」を削除する。

## 試したこと・判断の経緯

### BetterTouchTool（断念）

BTT の Keyboard Shortcut トリガーで Enter → Shift+Enter に置換する方法を最初に試した。

- **結果**: Enter は送信されなくなったが、**日本語 IME の変換確定も壊れた**。
  BTT は CGEvent レベルでキーを横取りするため、IME が変換中かどうかを区別できない。
- Cmd+Enter → Enter の設定も、送信先が `(null)` になり動作しなかった。
- **判断**: BTT では「Enter 入れ替え」と「IME 変換確定」を両立できないため断念。

### Karabiner-Elements 15.3.0（バージョン不適合）

BTT の代わりに Karabiner-Elements を使う方針に切り替えた。
既にインストール済みの 15.3.0 で設定を追加したが、キー入れ替えが効かなかった。

- **原因**: macOS Tahoe 26.4.1 に対して Karabiner 15.3.0 が古すぎた。
  `karabiner_grabber`（キー入力を横取りする本体プロセス）が起動直後に終了していた。
  ドライバ（VirtualHIDDevice 1.8.0）は読み込まれていたが、grabber との通信路が
  確立できない状態だった。
- **判断**: 16.0.0 にアップデートして対応。

### Karabiner-Elements 16.0.0（現在）

16.0.0 をインストール済み。アーキテクチャが変わり、grabber → Core Service に移行。
VirtualHIDDevice Manager も 5.0.0 → 6.14.0 に更新された。
ただし古いドライバ（1.8.0）がカーネルに残っているため、Mac 再起動が必要。

### IME 変換確定について（未検証）

Karabiner は BTT より低いレベル（HID デバイスレベル）でキーを書き換える。
macOS の日本語 IME が Shift+Enter でも変換確定するかどうかは未検証。

壊れた場合の代替案:
- ルールに `input_source_unless` 条件を追加し、日本語入力ソースが有効な間は
  Enter をそのまま通す。英数モードでのみ Enter → Shift+Enter に変換する。
- これで「英数モードだと気づかず Enter で送信」は防げる。日本語モードでは
  変換確定後の Enter で送信されるが、本人が日本語で打っている自覚がある状態なので
  誤送信リスクは低い。

## 関連ファイル

- 元の検討メモ: `raw/` 配下（ChatGPT との会話で作成した BetterTouchTool 設定引き継ぎメモ）
- Karabiner 設定: `~/.config/karabiner/karabiner.json`
