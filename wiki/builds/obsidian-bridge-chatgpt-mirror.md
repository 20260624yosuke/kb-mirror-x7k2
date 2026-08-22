---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-22
sources: []
---

# ObsidianBridge(ChatGPT読み取りブリッジ)

## 要約

ChatGPTはローカルObsidian Vaultを直接読めないため、**許可リスト内のファイルだけをGitHub公開ミラーへ自動同期し、raw URL経由でChatGPTに読ませる**仕組み。2026-08-22に計画(独立監査2体)→実装→実測検証→ナレッジ全文共有化まで完了。正本計画書: `/Users/takedayousuke/llm-uploads/20260822-obsidian-chatgpt-bridge-plan.md`(v2)。

## 構成

```text
[Obsidian Vault] --launchd 300秒--> [bridge_sync.py]
  manifest読込 → 入力検証(fail-closed) → staging構築
  → 秘密スキャン(検知時は隔離+通知+中止) → index.md生成
  → 変更ゼロなら停止 → git commit/push
[github.com/20260624yosuke/kb-mirror-x7k2] --raw URL--> [ChatGPT]
```

| コンポーネント | 場所 |
|---|---|
| 同期スクリプト | `~/Library/Application Support/ObsidianBridge/bridge_sync.py`(/opt/homebrew/bin/python3・標準ライブラリのみ) |
| 許可リスト | 同dir `sync_manifest.tsv`(明示行=`src⇥mirror`、`@dir⇥dir⇥接頭辞[⇥拡張子csv]`) |
| staging/quarantine | 同dir 配下(Vault外・repo外。`.git`内部は保護) |
| launchd | `~/Library/LaunchAgents/com.takedayousuke.obsidian-bridge.plist`(StartInterval=300) |
| ログ | `~/Library/Logs/ObsidianBridge/sync.log`(サイズ閾値ローテ7世代) |
| ミラー | 公開repo kb-mirror-x7k2(PATはmacOSキーチェーン保存) |

## 運用

- 対象追加: `sync_manifest.tsv` に行追加(または@dir行)。新規ノートはwiki/配下なら自動反映
- ChatGPTへの入口URL: `https://raw.githubusercontent.com/20260624yosuke/kb-mirror-x7k2/main/index.md`
- 通知: macOS通知が出たら隔離発生 or 3回連続失敗 or 6時間成功なし。ログ確認→quarantine内容確認
- 停止: `launchctl bootout gui/$UID/com.takedayousuke.obsidian-bridge`

## セキュリティ設計(と変更履歴)

- 許可リスト(default deny)。除外: 隠しディレクトリ/.git/__pycache__/backups等。テキスト1MB・画像10MB上限
- 秘密スキャン: **専用パターン11種(openai/anthropic/github×2/aws/google/hf/slack/秘密鍵/JWT/bearer)+ keyword=valueルール(16字以上英数字混在)**。全行適用
- 方針変更(2026-08-22): 汎用エントロピー総当たり検出は廃止。本KBの正当な長ID(Blenderクリップ名`BED-ACT_…`、Google画像検索のURL内部トークン等)に連続誤爆し、fail-closedが形骸化する恐れが実測されたため。未知形式への第二防御として GitHub Push Protection を想定(要設定確認)
- unpublish原則: 一度pushしたら取り下げにはrepo削除→再作成が必要。「撤去不能前提」でmanifest選定する

## 実測済み検証(2026-08-22)

TEST1a〜10合格(逐字一致/削除反映/検知停止/quarantine非公開/日本語パス/再構築/launchd発火)。公開範囲はAPI tree権威判定で1,111ファイル(wiki 1,098+root 11+index+.gitignore)。非対象(AGENTS.md/log以外の直下json等)は404。

## 既知の注意

- CDNキャッシュ(max-age=300)により更新・削除の反映は最大約5分。新規は即時。`?v=`によるキャッシュバストは**実測で無効**
- root/log.md(作業ログ全文)は承認のもと公開中。外す場合はmanifest該当行を削除
- fine-grained PATは漏洩経歴あり(チャット露出)。差し替えを推奨(未実施)

## 変遷

- 2026-08-22: v1計画→独立監査2体(Major8+Minor12)→v2→実装(パイロット4本)→同日中に案B承認でナレッジ全文共有へ拡大

## 関連リンク

[[obsidian-ui-improvement-roadmap]] / [[llm-wiki]](運用規約) / 正本計画書はllm-uploads参照
