---
type: build
name: Google Tasks クイック追加
aliases: [google-tasks-quickadd, タスク追加ショートカット]
tags: [macOS, raycast, google-tasks, oauth, python, capture, automation]
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-06-21
---

# Google Tasks クイック追加

## 現在の統合見解

「思いついたタスクを Google Tasks の既定リスト(マイタスク)へ一瞬で放り込む」ための Mac 用クイック
キャプチャ。入力経路は **Raycast Script Command → ローカル Python → Google Tasks API**。
全部「既定のマイタスク(`@default`)」に入るので、後で 1 日 1 回まとめて整理する運用に合わせる。

- 認証は OAuth(クライアント種別 = Desktop app)。Google 同意画面を **本番公開(In production)** に
  することでリフレッシュトークンが失効せず、半永久的に使い続けられる。
- スクリプト実体・venv・トークンはすべて `~/.config/google-tasks-quickadd/` 配下に置き、**外付け SSD を
  外していても動く**(SSD 非依存)。正本は wiki の `tools/`。
- 失敗(オフライン等)しても結果を 1 行で通知し、取りこぼし(追加できていないのに気づかない)を防ぐ。

## アーキテクチャ

```text
Raycast Script Command「タスク追加」
  -> ~/.config/raycast-scripts/google_tasks_quickadd.sh
       -> 引数(タスク名)を Python スクリプトへ渡す
  -> google_tasks_quickadd.py
       -> token.json で認証(失効時は自動更新)
       -> tasks.insert(tasklist="@default", {"title": ...})
  -> Google Tasks 既定リスト(マイタスク)
  -> Raycast が結果を HUD 通知(silent mode)
```

- 当初は Apple ショートカット経由を計画していたが、Raycast Script Command で直接呼ぶ方が
  シンプルなため変更(2026-06-21)。確認ダイアログ付き(`needsConfirmation`)で誤送信を防止。
- iPhone/iPad は公式 Google Tasks アプリ/ウィジェットで追加(同じ既定リストに入る)。

## 実装

- 正本スクリプト: `tools/google_tasks_quickadd.py`
  - `auth` モード: 初回のみ。ブラウザ認証(`access_type=offline` + `prompt=consent` で
    リフレッシュトークンを確実取得)→ `token.json` 保存(chmod 600)。
  - 追加モード: タスク名を **argv 優先 / 無ければ stdin** で受け、`@default` に insert。
    argv がある時は stdin を読まない(固まり防止)。結果は成功・失敗とも **stdout に1行 + exit 0**。
- Raycast Script Command: `~/.config/raycast-scripts/google_tasks_quickadd.sh`
  - Raycast の引数（タスク名）を Python スクリプトの argv へ渡す。silent mode + 確認ダイアログ付き。
- インストーラ: `tools/install_google_tasks_quickadd.sh`
  - 専用 venv 作成 + 依存導入(`google-auth` / `google-auth-oauthlib` / `google-api-python-client`)。
  - 正本 → 実体(`~/.config/`)へコピー。設定ディレクトリ 700・秘密ファイル 600。
  - **正本を編集したら再実行して実体へ反映する**(正本=tools/、実体=~/.config/)。
- 秘密ファイル: `~/.config/google-tasks-quickadd/client_secret.json` / `token.json`
  (wiki 外・git 外・chmod 600)。`token.json` は Google Tasks を読み書きできる鍵そのもの。

## セットアップ手順

### Google Cloud Console(武田さん本人のみ可能)

1. console.cloud.google.com でプロジェクト作成。
2. 「Google Tasks API」を有効化。
3. OAuth 同意画面: User type = External、アプリ名・サポートメール入力、**公開状態を In production に**。
4. 認証情報 → OAuth クライアント ID 作成 → 種別 **Desktop app** → JSON をダウンロード →
   `~/.config/google-tasks-quickadd/client_secret.json` として保存。

### Mac 側

1. `bash tools/install_google_tasks_quickadd.sh`(venv・実体コピー・権限設定）。
2. 初回認証（ブラウザで「許可」。初回だけ「確認されていないアプリ」警告→「詳細」→「移動」→「続行」）:
   `"~/.config/google-tasks-quickadd/venv/bin/python3" "~/.config/google-tasks-quickadd/google_tasks_quickadd.py" auth`
3. 動作確認: 上記 python で `... "テスト"` を実行し、Google Tasks に「テスト」が出るか確認。
4. Raycast で「タスク追加」を検索 → 表示されれば完了（Script Command は自動検出される）。

## 運用

- 思いついたら Raycast → 「タスク追加」→ タスク名入力 → Enter → 確認ダイアログで実行。
- Esc でキャンセル。
- 溜まったタスクは 1 日 1 回まとめて整理（別運用）。

## 状態

- `実装済み`（2026-06-07）: Python スクリプト・インストーラ作成、venv 構築、依存導入、
  未認証・空入力時のグレースフル動作を確認。
- `実機確認済み`（2026-06-21）: Google Cloud Console 設定（OAuth 同意画面を本番公開）、
  OAuth 初回認証、CLI からのテストタスク追加（「テスト：Claude から追加」）に成功。
  Raycast Script Command（`~/.config/raycast-scripts/google_tasks_quickadd.sh`）を配置済み。
- `実機確認済み`（2026-06-21）: Raycast からの実呼び出し、動作良好。確認ダイアログによる誤送信防止も確認。

## 将来候補(今回やらない)

- Mac+iPhone 共用の単一ショートカット(「URL の内容を取得」で API を直接叩く、Python/venv/SSD 不要)。
  認証更新をショートカット内に組む必要があり作業量が増えるため別件扱い。
- 締切設定・複数リスト振り分け・1 日 1 回の自動整理。

## 関連リンク

- [[diary-quick-capture]] — 同系統のクイックキャプチャ(殴り書きメモ)
- [[shortcut-efficiency]] — 「描いていない工程を省く」効率化観
