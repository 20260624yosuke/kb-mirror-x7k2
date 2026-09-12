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
（Enter=改行・Cmd+Enter=送信）にそろえたが、実装手段は Karabiner ではなく
OpenCode 公式の TUI 設定＋VS Code キーバインドで行った。

- 素の Enter は送信から外し、改行に割り当てた。
- 送信は中継キー `ctrl+y` に割り当て、VS Code 側で
  `cmd+enter`（`terminalFocus` 時に限定）を捕捉して `ctrl+y` 相当の制御文字
 （`U+0019`）をターミナルへ送る。
- 2026-09-12 時点で設定ファイルの作成と静的検証まで完了。
  実機の打鍵確認は未実施。さらに机上検証で、稼働中の Karabiner 既存ルールと
  干渉する疑いが濃厚になった（下の「矛盾・未確定」）。運用開始前に要解決。

## 正本ファイル

- グローバル TUI 設定（新規作成・2026-09-12）:
  `/Users/takedayousuke/.config/opencode/tui.json`
  - `keybinds.input_submit`: `ctrl+y`（既定 `return` から変更）
  - `keybinds.input_newline`:
    `return,shift+return,ctrl+return,alt+return,ctrl+j`
    （既定の改行群に `return` を追加した形で、既存の改行操作は維持）
- VS Code キーバインド（新規作成・2026-09-12）:
  `/Users/takedayousuke/Library/Application Support/Code/User/keybindings.json`
  - `key: cmd+enter`、`command: workbench.action.terminal.sendSequence`、
    `args.text: U+0019`、`when: terminalFocus`
  - 作成前は同ファイル自体が存在せず、競合なしを確認済み。
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

- 実装済み: 上記 2 ファイルの作成。
- 自動試験済み: 両ファイルの JSON 有効性、`tui.json` のキー名が公式の
  バインド ID と一致すること、`opencode debug config` の正常起動（終了 0）。
- 実機確認済み: なし。要求された 8 項目（IME 変換確定・改行・送信・
  英数字時・エディタ側無影響・IME 変換中の Cmd+Enter）は未実施。
- 運用開始可能: いいえ。下の Karabiner 干渉の解決と実機確認が先。

反映には OpenCode の再起動（TUI 設定は起動時読込）と
VS Code ウィンドウのリロードが必要。

## 矛盾・未確定

> [!warning] Karabiner 既存ルールとの干渉の疑い（2026-09-12・机上検証・実機未確認）

- 稼働確認済み（2026-09-12 にプロセス実在を確認）: Karabiner-Elements の
  Core Service・VirtualHID デーモン・console_user_server が起動中。
  設定 `/Users/takedayousuke/.config/karabiner/karabiner.json`
  （mtime 2026-08-18）の `LLM Chat: Enter→改行, Cmd+Enter→送信` ルールは
  `^com\.microsoft\.VSCode$` を対象に含み、条件はアプリ単位のみ
  （ターミナルフォーカスでは区別しない）。
- 当該ルールは HID 層で `cmd+enter → 素の enter` へ剥がし、
  `素の enter → shift+enter` へ変換する。VS Code より下層で起きるため、
  統合ターミナル上でも適用される。
- よって机上では次の通りになる見込み（推論・未実測）:
  - Enter 打鍵 → Karabiner で shift+enter → OpenCode は改行として処理。
    Enter=改行の目的は達成される。
  - Cmd+Enter 打鍵 → Karabiner で素の enter に剥がされる →
    VS Code の `cmd+enter` バインドは発火しない →
    OpenCode は素の enter（=改行）を受け取る。**送信されない。**
- すなわち現状のままでは送信経路が成立しない可能性が高い。
  実機で Cmd+Enter が送信されなければ、この干渉が第一容疑者。
- 候補と代償（いずれも未実施・判断待ち）:
  - Karabiner ルールから VS Code を外す: Kimi Code（公式の送信キー変更手段が
    無い）が誤送信に戻る代償あり。両立には別案が要る。
  - VS Code 側の送信トリガーを Karabiner が剥がさないキーへ変える:
    「送信は必ず Cmd+Enter」の統一操作要件と衝突する。
  - Karabiner 側でターミナル時だけ除外する: bundle ID 条件しか無いため不可。
  - OpenCode 側の submit を Karabiner 通過後のキーへ割当てる:
    通過後の enter/shift+enter は改行と共有のため単純割当ては不可。
    別の中継キー案の再設計が要る。

## 使わなかったもの・落とした情報

- 該当なし。既存の shift+enter 等の改行操作は維持し、削っていない。
  プロジェクト固有の `tui.json` を作る案は、全プロジェクト適用と
  保管庫への個人設定混入回避のため不採用（捨てた場合の手元の変化なし）。

## 関連リンク

- [[llm-chat-enter-guard]] — 同一操作体系の先行実装（Karabiner 方式）。
  本件は OpenCode TUI への拡張だが手段が異なり、上記の干渉が未解決。
- [[azookey-symbol-input-customization]] — 日本語入力層の別件カスタム。
  直接の依存は無い。
