---
type: build
name: CodexBar
aliases: [codexbar]
sources: []
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-07-14
---

# CodexBar

## 現在の統合見解

CodexBar は、Claude と Codex の使用量やリセット時刻を macOS のメニューバーで見るために導入した常駐アプリ。
2026-07-14 に Homebrew cask で `0.42.1` をインストールし、表示対象を `Codex` と `Claude` の2つへ絞った。

現在の構成:

- アプリ本体: `/Applications/CodexBar.app`
- CLI: `/opt/homebrew/bin/codexbar`
- 設定ファイル: `~/.config/codexbar/config.json`
- 有効 provider: `Codex` / `Claude`

Claude 側はメニューバーから表示確認済み。Codex 側は provider として有効だが、確認時点では
`OpenAI is unavailable in the current environment.` と表示され、使用量表示は未成立。

## 根拠

- `brew info --cask codexbar` で `codexbar 0.42.1` の導入を確認。
- `codexbar config providers --json` で `codex` と `claude` のみ有効を確認。
- `codexbar config validate` は `Config: OK`。
- `pgrep -fl /Applications/CodexBar.app/Contents/MacOS/CodexBar` で常駐プロセス起動を確認。
- 武田さんがメニューバー上で `Codex` と `Claude` の2つを表示したと報告。

## 導入手順

1. `brew install --cask codexbar`
2. `codexbar config enable --provider claude`
3. `codexbar config enable --provider codex`
4. `open -gj -a CodexBar`

## 状態

- `実装済み`: CodexBar 導入、設定作成、有効 provider の絞り込み
- `自動確認済み`: CLI 存在、provider 設定、設定検証、プロセス起動
- `実機確認済み`: メニューバー上で `Codex` と `Claude` の2つが表示されること
- `運用開始可能`: Claude 側は開始可能。Codex 側は認証または環境側の未解決あり

## 矛盾・未確定

- `Codex` provider は有効だが、メニュー上では `OpenAI is unavailable in the current environment.` と表示された。認証情報、セッション読取、または CodexBar 側の対応条件のどれが原因かは未切り分け。
- `Codex` 側の使用量が実際に取れるところまでは、まだ実機確認していない。

## 変遷

- 2026-07-14: X クリップ経由で CodexBar を調査。
- 2026-07-14: Homebrew cask で `codexbar 0.42.1` を導入。
- 2026-07-14: provider を `Codex` / `Claude` の2つへ絞り、常駐起動を確認。
- 2026-07-14: 武田さんがメニューバー上で `Codex` と `Claude` の2つを表示。Claude は表示確認、Codex は `OpenAI is unavailable in the current environment.` で未解決。

## 関連リンク

- [[claude-code]]
