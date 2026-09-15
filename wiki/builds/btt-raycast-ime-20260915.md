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
- 英数入力: `com.apple.inputmethod.Kotoeri.RomajiTyping.Roman` (TIS current source を英数状態で実測。前回の「ことえり Romaji」回答と一致)
- BTT: Shift+C / ⌘Space / Hyper 系トリガーは未使用 (btt_data_store 直接クエリ)。fn+英字 トリガー多数 (window snapping 系) → backend は fn を使わない
- Karabiner-Elements 稼働中: 外部KB (vid=9610) で left_option→英数、right_option→かな。Apple 内蔵KB は esc→fn, tab→英数。BTT 合成イベントは物理KB経由でないため影響なしと想定 (未検証)
- Raycast の db は暗号化 (SQLCipher) のため既存 Hotkey 自体はDBから読めないが、ユーザー確認済み: Root = `⌘Space` / Clipboard History = `⇧C`
- Raycast defaults の `enforcedInputSourceIDOnOpen = com.apple.inputmethod.Kotoeri.RomajiTyping.Roman` は、Gate 2のA/BでRoot・Clipboardとも監視中のTIS source切替を観測せず、Rootもたつきとの因果も支持されなかった。恒久削除せず元の値を維持。
- BTT アクション: Change Input Source = type 420 (`BTTActionChangeInputSource`), Send Keyboard Shortcut = type 264 (`BTTShortcutToSend` "59,58,56,55,キー" 形式)。標準trigger action列は `BTTActionsToExecute` 配列。今回のartifactはJSON配列のため、importは `btt://jsonimport` またはBTTのJSON importを使い、`add_new_trigger`へ配列全体を渡さない。

## 決定事項
- backend Hotkey 案: Root = ⌃⌥⌘⇧Space ("59,58,56,55,49"), Clipboard = ⌃⌥⌘⇧C ("59,58,56,55,8") — 監査上の既存割当と競合なし
- Gate 1 PASS: 英数Input Sourceを `com.apple.inputmethod.Kotoeri.RomajiTyping.Roman` に確定。日本語は `dev.ensan.inputmethod.azooKeyMac.Japanese`。
- Gate 2完了: `enforcedInputSourceIDOnOpen` はA/BいずれでもRoot・Clipboardの監視中TIS sourceを切り替えなかった。一時無効化後、元の値へ復元済み。
- Gate 3 artifact生成: `wiki/builds/btt-raycast-ime-20260915-gate3-artifact.json` を生成したが、BTT/Raycast/macOSへは未適用。SHA-256: `c99e6bbbc229ac71e534cfa9dc41bdf56db85a71ff03935e1bdd6e221f12990a`
- Gate 3.1 hardening: disabled import用artifactを生成。`BTTEnabled = 0`、Root/Clipboardの識別名、modifier混入防止を明示。BTT/Raycast/macOSへは未適用。

## Gate 2 A/B結果
- A（hidden prefあり）Root: 起動前・起動直後・終了時とも `dev.ensan.inputmethod.azooKeyMac.Japanese`。起動直後のもたつきは再現あり。
- A（hidden prefあり）Clipboard: 起動前・起動直後・終了時とも `dev.ensan.inputmethod.azooKeyMac.Japanese`。日本語入力と最初のSpace変換は正常。
- B（hidden pref一時無効）Root: 起動前・起動直後・終了時とも `dev.ensan.inputmethod.azooKeyMac.Japanese`。入力時のもたつきは継続。
- B（hidden pref一時無効）Clipboard: 起動前・起動直後・終了時とも `dev.ensan.inputmethod.azooKeyMac.Japanese`。日本語入力は正常で、Aとの差は体感上なし。
- 結論: この試験条件ではhidden prefがRoot/Clipboardのどちらかに実効的に作用した証拠は得られず、Rootのもたつき原因とも確認できない。
- 推奨: hidden prefは恒久削除せず、元の値を維持する。非公式設定の恒久削除による別の挙動変化を避けるため。
- Gate 3: live設定変更なしのimport artifact生成へ進行可能。ただしRootのもたつきは未解決であり、Gate 3通過を完成・改善の証拠とは扱わない。

## 運用確定
- Raycast側のbackend Hotkeyは、手順書に従いユーザーが手動設定する。
- Gate 4 import手順: disabled artifactをJSON配列としてimport → BTT read-backで2triggerを識別名・`BTTEnabled=0`・Global / All Apps scopeとして確認 → Raycast backend Hotkeyを手動設定 → backend受け口をread-back確認 → 最後に2triggerだけenableする。app scopeが限定されていた場合はenableしない。

## 未検証
- BTT synthetic shortcut がKarabinerを確実に迂回するか

## 検証計画
実装後、通常Raycast / Clipboard をそれぞれ 20 回ずつ切替、IME の追随と race の有無を確認。

## 関連リンク
- [[obsidian-direct-open-entrypoint]]

## Gate 3.2 履歴 (2026-09-15)
- Gate 3.1 の `BTTActionsToExecute` 形式は、BTT 4.204 の `add_new_trigger` ingest で action が欠落し、key trigger のみが残った。
- Gate 3.2 は、BTT 4.204 の実機 `add_new_trigger` / `get_trigger` / `get_triggers` で保持を確認した `BTTAdditionalActions` 形式へ移行した compatibility artifact であり、Gate 3.1 を supersede (置き換え) する。
- Gate 3.2 artifact: `wiki/builds/btt-raycast-ime-20260915-gate3.2-artifact.json`。Root / Clipboard とも `BTTEnabled=0`、action順は 420 → shortcut-send。
