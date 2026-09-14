---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-15
sources: []
---

# BTT + Raycast IME 自動切替ハブ (2026-09-15)

## 目的
Raycast をユーザー向けランチャーの唯一のハブとして維持し、BetterTouchTool (BTT) が裏方として IME 切替を自動化する。ユーザー操作 (通常Raycastホットキー / Shift+C) は変更しない。

## 設計
1. Raycast にユーザーが直接押さない backend 用 Hotkey を 2 つ設定 (Root Search / Clipboard History)。
2. BTT がユーザー Hotkey を受け、Input Source 切替 (ネイティブアクション 420) → backend Hotkey 送信 (アクション 264) を順に実行。Delay は入れない (race 実測時のみ最小限追加)。
3. Raycast 側のユーザー向け Hotkey 割当は外し、二重監視を除去。

## 監査済み事実 (2026-09-15)
- 日本語入力: `dev.ensan.inputmethod.azooKeyMac.Japanese` (azooKey、現在選択中。TIS で実測)
- 英数候補: `dev.ensan.inputmethod.azooKeyMac.Roman` (azooKey English) / `com.apple.inputmethod.Kotoeri.RomajiTyping.Roman` (ことえり、Raycast の隠し設定 `enforcedInputSourceIDOnOpen` が指す方)
- BTT: Shift+C / ⌘Space / Hyper 系トリガーは未使用 (btt_data_store 直接クエリ)。fn+英字 トリガー多数 (window snapping 系) → backend は fn を使わない
- Karabiner-Elements 稼働中: 外部KB (vid=9610) で left_option→英数、right_option→かな。Apple 内蔵KB は esc→fn, tab→英数。BTT 合成イベントは物理KB経由でないため影響なしと想定 (未検証)
- Raycast の db は暗号化 (SQLCipher) のため既存 Hotkey は読めず、ユーザー確認が必要
- Raycast defaults に `enforcedInputSourceIDOnOpen = com.apple.inputmethod.Kotoeri.RomajiTyping.Roman` あり (非公式、実効性未検証。有効なら Clipboard 日本語化と競合しうる)
- BTT アクション: Change Input Source = type 420 (`BTTActionChangeInputSource`), Send Keyboard Shortcut = type 264 (`BTTShortcutToSend` "59,58,56,55,キー" 形式)。アクション列は `BTTAdditionalActions` 配列。追加は `btt://add_new_trigger/?json=...` / `btt://jsonimport/<base64>`

## 決定事項
- backend Hotkey 案: Root = ⌃⌥⌘⇧Space ("59,58,56,55,49"), Clipboard = ⌃⌥⌘⇧C ("59,58,56,55,8") — 監査上の既存割当と競合なし

## 未決定
- 通常Raycast の現在 Hotkey (ユーザー回答待ち → BTT トリガー化の入力)
- 英数の実体 (ユーザー回答待ち)
- Raycast 側の適用手法 (手動/自動/共同)
- `enforcedInputSourceIDOnOpen` の扱い

## 検証計画
実装後、通常Raycast / Clipboard をそれぞれ 20 回ずつ切替、IME の追随と race の有無を確認。

## 関連リンク
- [[obsidian-direct-open-entrypoint]]
