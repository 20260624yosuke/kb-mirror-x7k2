---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-25
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

- 許可リスト(default deny)。除外: 隠しディレクトリ/.git/__pycache__/backups等。テキスト5MB(2026-08-22に1MBから引き上げ)・画像10MB上限
- 秘密スキャン: **専用パターン11種(openai/anthropic/github×2/aws/google/hf/slack/秘密鍵/JWT/bearer)+ keyword=valueルール(16字以上英数字混在)**。全行適用
- 方針変更(2026-08-22): 汎用エントロピー総当たり検出は廃止。本KBの正当な長ID(Blenderクリップ名`BED-ACT_…`、Google画像検索のURL内部トークン等)に連続誤爆し、fail-closedが形骸化する恐れが実測されたため。未知形式への第二防御として GitHub Push Protection を想定(要設定確認)
- unpublish原則: 一度pushしたら取り下げにはrepo削除→再作成が必要。「撤去不能前提」でmanifest選定する

## 実測済み検証(2026-08-22)

TEST1a〜10合格(逐字一致/削除反映/検知停止/quarantine非公開/日本語パス/再構築/launchd発火)。公開範囲はAPI tree権威判定で1,111ファイル(wiki 1,098+root 11+index+.gitignore)。非対象(AGENTS.md/log以外の直下json等)は404。

## 既知の注意

- CDNキャッシュ(max-age=300)により更新・削除の反映は最大約5分。新規は即時。`?v=`によるキャッシュバストは**実測で無効**
- root/log.md(作業ログ全文)は承認のもと公開中。外す場合はmanifest該当行を削除
- fine-grained PATは漏洩経歴あり(チャット露出)。差し替えを推奨(未実施)

## 2026-08-25 の障害(通知連発)と修正

### 何が起きたか(実測)

- 2026-08-24 17:42 の同期を最後に失敗が続き、macOS通知「連続3回失敗」が5分ごとに発火(failure_count=209まで)。
- 原因: git_phase の削除適用(index は即時変更される)の途中で PC が落ち、コミットされない半適用状態が固定。以後毎回、index に存在しないパスへの `git rm --cached` が exit 128 で落ちる(冪等でない設計)。
- 副作用: 一時 ingest ステージング `_staging_batch_resume_20260824/` 配下**311件**(marse-visual 74 / sasa-batch 37 / yejji-ch05 174 / yejji-ch05-recovered 26・日本語名のタスクログtxt含む)が HEAD に含まれ、約17時間公開ミラー上に生きたままだった。

### 修正

- **git_phase を `git add -A` 1本に置換**(変化判定→add→diff --cached ガード→commit→push)。行単位再生の quoting/リネーム/空コミット/冪等性の4問題が構造的に消えた。なお旧方式への `--ignore-unmatch` 追加だけでは不十分: core.quotepath 既定の八進エスケープにより日本語パス26件が「成功扱いだが消えない」silent no-op になり、約15分で失敗ループが再燃することをレビューのスクラッチrepo実験で確認済み。
- **`_staging*` ディレクトリを同期対象外**(expand_scope の parts チェック+明示行の二重防御)。ingest 作業用一時フォルダが公開ミラーに出ることを根絶。除外すると rebuild_staging が worktree からも自動除去する。
- **連敗通知を1時間に1回に間引き**(stale 通知と同じ方式)。障害時は209連敗で毎回通知していた。

### 適用順序の注意

対象外フィルタを**先**に入れ、git_phase 修正を**後**に入れた。逆順だと、フィルタ無しのまま削除楔が解けた瞬間、worktree 待機中の別の一時フォルダ555枚(`_staging_batch2_20260825` 等)が一気に新規公開されうる。300秒周期の launchd が常時動いているため、中間状態を作らない手順が必要。

### 検証(2026-08-25 実機)

- 修正後の最初の launchd 同期(12:07:03)で commit+push 完了(390 files changed・追加更新と311件削除の両経路が同一コミットで通過=回帰確認)。手動実行はロックで正しくスキップ(二重実行防護を実測)。
- git status 0件・index/HEAD に `_staging` 0件・raw URL 404化を確認。
- 15分経過後の静穏確認: 下記「未確認」参照。

### 公開リスクの残存(選択Aの範囲・ユーザー了承済み)

- 削除後も GitHub の git 履歴には作業フレーム311件が残る。**SHA-pinned raw URL は無期限に取得可能**。fork/clone 済みの拡散は把握不能。完全消去は repo 削除→再作成のみ(未実施)。
- **2026-08-25 決定(武田さん承認)**: 履歴は**現状維持**(repo再作成は見送し)。理由: 残存内容は公開予定素材の前段階が大半で実害が小さいこと、repo再作成は PAT 再許可の可能性と ChatGPT 読み取りの一時中断を伴うこと。将来消したくなったら PAT 差し替え(Wiki 既載の推奨事項)とセットで実施する。

## 変遷

- 2026-08-22: v1計画→独立監査2体(Major8+Minor12)→v2→実装(パイロット4本)→同日中に案B承認でナレッジ全文共有へ拡大
- 2026-08-22: テキスト同期上限を1MB→5MBへ引き上げ(log.md肥大対策。フル同期継続方針を武田さん確認済み。分割・recent派生ビューは見送り)
- 2026-08-25: git_phase を add -A 方式へ置換・`_staging*` 同期対象外・連敗通知の間引き。半適用楔による通知連発(209連敗)と一時フォルダ311件の公開を回復(詳細は「2026-08-25 の障害」節)

## 関連リンク

[[obsidian-ui-improvement-roadmap]] / [[llm-wiki]](運用規約) / 正本計画書はllm-uploads参照
