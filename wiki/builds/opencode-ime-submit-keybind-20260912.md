---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-12
sources: []
---

# OpenCode TUI の IME 誤送信ガード（Enter=改行・Cmd+Enter=送信）

## 現在の統合見解

VS Code 統合ターミナル上の OpenCode（TUI）で、日本語 IME の変換確定 Enter による
誤送信を防ぐためのキー割り当て変更。操作体系は [[llm-chat-enter-guard]] と同じ
（Enter=改行・Cmd+Enter=送信）にそろえた。

2026-09-12 の武田さん判断で案B（通過後キーに整合）を採用した。Karabiner が
VS Code 上の Enter 系キーを HID 層で変換した後のキーに OpenCode 側を合わせる
方式で、VS Code 側のキーバインド追加は不要になった（発火不能なため除去済み）。
Kimi Code 側の Karabiner ガードには手を付けていない。

- 物理 Enter → Karabiner で shift+enter → OpenCode は改行として処理。
- 物理 Cmd+Enter → Karabiner で素の enter → OpenCode は送信として処理。
- IME 変換中の物理 Enter → shift+enter → IME が変換確定（Kimi 側の
  2026-08-18 実機確認と同型式）。送信されない見込み。
- 2026-09-12 時点で設定変更と静的検証まで完了。実機の打鍵確認は未実施。

## 正本ファイル

- グローバル TUI 設定（2026-09-12 作成・同日案Bへ変更）:
  `/Users/takedayousuke/.config/opencode/tui.json`
  - `keybinds.input_submit`: `return`（=既定値。Karabiner が Cmd+Enter を
    剥がした後の素の enter を送信に使う）
  - `keybinds.input_newline`:
    `shift+return,ctrl+return,alt+return,ctrl+j`（=既定値。Karabiner が
    素の Enter を変換した後の shift+enter を改行に使う）
  - 値だけ見れば既定と同一だが、Karabiner 通過後キーへの依存を明示するため
    ファイルは残す。
- VS Code キーバインド: 2026-09-12 に `keybindings.json` を新規作成
  （`cmd+enter`＋`terminalFocus`→`sendSequence` U+0019）したが、同日中に除去
  した。Karabiner が Cmd を剥がすため VS Code に Cmd+Enter が届かず発火不能
  （死に設定）だったため。作成前と同じく同ファイルは存在しない状態へ戻した。
- 採用しなかった中継キー `ctrl+y` の検証記録:
  OpenCode 入力欄で直接 Ctrl+Y を押すと正常に送信できた（2026-09-12 武田さん
  実機確認）。`input_submit = ctrl+y`・`tui.json` 読込・OpenCode 側送信処理は
  正常と確定。この結果で障害箇所を VS Code 側中継に限定できた。
- 触っていない既存設定:
  `/Users/takedayousuke/.config/opencode/opencode.jsonc`（権限のみ）、
  プロジェクトの `opencode.json`（skills・instructions のみ）、
  VS Code の `settings.json`。

## 経緯

- 2026-09-12、武田さんの依頼で作業開始。要求は Enter=送信しない・改行、
  Cmd+Enter=送信への変更。macOS の Command は TUI へ修飾キーとして渡せない
  可能性があるため、VS Code 側で捕捉して未使用の制御キーへ変換する推奨構成が
  指定された。
- 調査で確定した事実（いずれも直接確認）:
  - 導入済み OpenCode は 1.18.30。キーバインドの正式な設定方式は `tui.json`
    （`opencode.json` のスキーマにキーバインド項目は無い）。
    グローバルは `~/.config/opencode/tui.json`、プロジェクト固有は
    プロジェクト直下の `tui.json`。どちらも存在しなかったため、
    全プロジェクトに効くグローバル側を新規作成した。
  - 既定値（公式ドキュメント）: `input_submit` は `return`、
    `input_newline` は `shift+return,ctrl+return,alt+return,ctrl+j`。
  - 中継キー `ctrl+y` は既定バインド一覧に単独使用が無い
    （`ctrl+alt+y` 等のみ）ことを確認して採用。
  - VS Code の `sendSequence` は `U+0000` 形式の文字コード指定が公式の流儀で、
    `U+0019`（10進 25）が `ctrl+y` に相当することを確認。
  - `keybindings.json` 作成時、書き込み内容の制御文字が生バイトで混入して
    JSON 不正になったため、書き直して `U+0019` のエスケープ表記で有効な
    JSON であることを機械的に確認した。
- wiki 記録作業中の 2026-09-12、既存の [[llm-chat-enter-guard]] との
  干渉を点検し、下の未確定事項を発見した。

## 検証状態

- 実装済み: グローバル `tui.json` の案B値への変更と、
  発火不能だった VS Code `keybindings.json` の除去。
- 自動試験済み: `tui.json` の JSON 有効性・キー名が公式バインド ID と一致すること、
  `opencode debug config` の正常起動（終了 0）、除去後の User ディレクトリに
  `keybindings.json` が残っていないこと。
- 実機確認済み: Ctrl+Y 直接押下での送信のみ（2026-09-12 武田さん実施）。
  案Bの 8 項目（IME 変換確定・改行・送信・英数字時・エディタ側無影響・
  IME 変換中の Cmd+Enter）は未実施。
- 運用開始可能: いいえ。実機確認が先。

反映には OpenCode の再起動（TUI 設定は起動時読込）が必要。
VS Code 側は死に設定の除去のみのため再読込は必須ではないが、
ウィンドウのリロードを推奨。

## 矛盾・未確定

### 解決済み: Karabiner 干渉の原因特定と案B採用（2026-09-12）

- 原因は VS Code ではなく、選択中プロファイルで稼働する Karabiner
  `LLM Chat` ルールが HID 層で VS Code 上の `cmd+enter` を素の enter へ
  剥がすことだった（デーモン稼働・選択プロファイル・条件内容を直接確認、
  武田さんの Ctrl+Y 直接送信テストとも整合）。
- VS Code 1.137.0 の送信系 2 設定は既定のままで支障なし、
  Cmd+Enter の実効競合なし（拡張機能側はエディタ条件付きのみ）、
  既知の VS Code 側不具合群は条件不一致を確認済み。
- VS Code 側だけの安全な修正は不存在と判断（素の Enter 受信時送信は
  Karabiner 停止時に全ターミナルを破壊するため不採用）。
  武田さん判断で案Bを採用し、案A（Karabiner から VS Code を除外）は不採用。
  Karabiner 側は無改変のため Kimi Code のガードに影響なし。

### 残存する既知の制約（いずれも未実測・実機確認待ち）

- Karabiner 依存: Karabiner 停止中は物理 Enter が素の enter のまま届き
  送信に戻る（現 Kimi と同級の露出）。
- VS Code 外のターミナルで OpenCode を使うと、素の Enter が送信になる。
  本設定は VS Code＋Karabiner 前提。
- IME 変換中の物理 Cmd+Enter → 素の enter → IME が変換確定する見込みだが、
  確定後に Enter が漏れて送信されるかは不明。他サービスと同型の境界として
  実機確認項目に入れる。

## 使わなかったもの・落とした情報

- 該当なし。既存の shift+enter 等の改行操作は維持し、削っていない。
  プロジェクト固有の `tui.json` を作る案は、全プロジェクト適用と
  保管庫への個人設定混入回避のため不採用（捨てた場合の手元の変化なし）。

## 関連リンク

- [[llm-chat-enter-guard]] — 同一操作体系の先行実装（Karabiner 方式）。
  本件は OpenCode TUI への拡張だが手段が異なり、上記の干渉が未解決。
- [[azookey-symbol-input-customization]] — 日本語入力層の別件カスタム。
  直接の依存は無い。
