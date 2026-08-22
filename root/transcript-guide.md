# `/transcript` 使い方メモ

この KB の動画文字起こしスキルです。指定した Obsidian ページ内の、指定した動画リンクだけを
文字起こしして、同じページに逐語文字起こしを追記します。要約や解説はしません。

## 依頼の仕方

この KB を **vault ルート**で開き、`/transcript` で起動するか、自然文で頼みます。

```text
raw/_coloso/2026_05_30_マーセ/coloso_マーセ_08 どこを見せたいのか最初に決めておく.md の [[08.mp4]] を文字起こしして
```

- ページ名・動画名にスペースや日本語が入るので、手で叩くときは必ずクオートします。
- 動画ファイル自体は通常ページ隣の `_attachments/`（例 `_attachments/08.mp4`）にあり、
  スクリプトが自動で見つけます。自分で場所を探す必要はありません。

## 何が起きるか

指定動画（例 `08.mp4`）について、同じ `_attachments/` フォルダに

- `08.txt` / `08.vtt` / `08.srt` / `08.json` / `08.tsv`

が生成され、指定ページの末尾に `## 文字起こし: [[08.mp4]]` セクションが追記されます。
**指定した 1 本だけ**を処理し、同フォルダの他の動画（`09.mp4` など）には触れません。

## 無音バグの自動除去について

Coloso は無音の作画パートが長く、その区間で Whisper が誤作動します（同じ行を何十回も
ループ／「ご視聴ありがとうございました」「チャンネル登録をお願いします」を連発）。
スクリプトはこれを機械的に除去します（`--condition-on-previous-text False` ＋ 連続重複行の
間引き ＋ 既知フィラーの削除）。これは「喋っていない文字列」の除去であって、要約や整文では
ありません。実際の発話は言い換えず、そのまま残します。

## 何をしないか

- 要約・解説・キーワード抽出・チャプター分けをしない（無音バグの機械除去は除く）
- `/llm-wiki ingest` / `/llm-wiki query` をしない
- `wiki/`, `index.md`, `log.md` を触らない
- ページ内の全動画を勝手に処理しない

## 既に文字起こしがある場合

同じ動画の transcript ファイルや文字起こしセクションが既にある場合、デフォルトでは停止します。
作り直したいときだけ「上書きしてよい（`--overwrite`）」と明示して依頼してください。

## スラッシュコマンドが出ないとき

`/transcript` は `.claude/skills/transcript/SKILL.md` として登録されています。追加直後は
補完に出ないことがあるので、その場合は Claude Code のセッションを再起動してください。

## 初回セットアップ（別マシン / venv を消した場合）

`mlx_whisper` と `ffmpeg` が必要です。未導入なら以下を実行します。

```bash
brew install ffmpeg
python3 -m venv ~/.local/share/coloso-transcribe-venv
~/.local/share/coloso-transcribe-venv/bin/pip install mlx-whisper
```

内部では次を使います。

- `.claude/skills/transcript/SKILL.md`
- `tools/coloso_transcribe.py`
