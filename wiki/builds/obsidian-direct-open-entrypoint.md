---
type: build
title: Obsidian直開き共通入口
created: 2026-07-12
sources: []
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-08-22
---

# Obsidian直開き共通入口

## 目的

LLM が返した wiki ページを、Finder で探してファイル名をコピーし、Obsidian の検索へ貼り付ける手順をなくす。Claude、Codex、その他の LLM が同じ形式の `obsidian://` リンクを返せるようにし、必要に応じて CLI と Raycast からも同じ入口を使って開けるようにする。

## 使い方

### LLM の回答から開く

回答中の **Obsidianで開く** リンクをクリックする。macOS が Obsidian にページを渡し、そのページを開く。

### LLM / ターミナルからリンクを作る

```bash
python3 tools/open_in_obsidian.py wiki/sources/coloso-ye-jji-ch02-contrast.md --markdown
```

ファイルを実際に開く場合だけ `--open` を付ける。

```bash
python3 tools/open_in_obsidian.py wiki/sources/coloso-ye-jji-ch02-contrast.md --open
```

保管庫の外のパスは受け付けない。リンクは保管庫名と保管庫内相対パスから作るため、長い絶対パスを検索欄へ貼る必要がない。

### CLI / Raycast から直接開く

2026-07-12 に、Google Tasks クイック追加と同じ「Raycast を薄い入口、本体は独立 CLI」方針で拡張した。

- 本体: `tools/open_in_obsidian.py`
- Raycast 正本: `tools/llm_wiki_open.sh`
- 配置スクリプト: `tools/install_llm_wiki_open.sh`
- CLI 実体: `~/.local/bin/llm-wiki-open`
- Raycast 実体: `~/.config/raycast-scripts/llm_wiki_open.sh`

入力として以下を受け付ける。

- `wiki/...md` / `raw/...md` の相対パス
- 保管庫内の絶対パス
- `obsidian://open?...file=...`
- `[[page-slug]]` / `[[page-slug|表示名]]`
- ページ名 / slug の完全一致

部分一致や曖昧一致では開かない。候補だけを返し、誤って別ページを開かないことを優先する。

例:

```bash
llm-wiki-open "[[coloso-ye-jji-ch02-contrast]]"
```

```bash
llm-wiki-open "wiki/sources/coloso-ye-jji-ch02-contrast.md"
```

## LLM 側の運用

- 作成・更新した wiki ページを報告するとき、可能なら `Obsidianで開く` リンクを添える。
- 絶対ファイルリンクは検証用、`obsidian://` リンクは人間が読むための入口として併記できる。
- GUI を勝手に前面操作するのではなく、通常はクリック可能なリンクを返す。
- ユーザーが「開いて」と明示した場合だけ、`tools/open_in_obsidian.py --open` を実行する。
- Raycast / CLI からの前面表示は、ユーザー本人が明示実行した入口として扱う。

## 経緯

- 2026-07-12 時点で、LLM 回答中の `Obsidianで開く` リンクは使えるようになった。
- ただし、手元の任意ページを開くときはまだ `Finder -> ファイル名コピー -> Obsidian検索` が残っていた。
- そこで、Google Tasks クイック追加 [[google-tasks-quickadd]] と同じく、Raycast は入口だけにして、本体の解決ロジックは独立 CLI に寄せる方針を採用した。
- ページ名解決は誤爆が危険なので、部分一致で勝手に開く設計は採らず、「一意なら開く / 曖昧なら候補表示で止まる」に固定した。
- **2026-08-22 実測**: opencode CLI のチャット面では、回答中の `obsidian://` リンクを押しても何も起きないと武田さんが報告(生成ページ5枚の確認時に発生)。作業ディレクトリ内ページのフォールバックとしては**相対パスの Markdown リンク**(例: `[無題のファイル](wiki/sources/art-canvas-9f97839670d1.md)`)が動作した。グローバル CLAUDE.md の 2026-08-03 実測(一般ファイルでの `obsidian://` 無反応)と同じ現象が wiki 内ページでも再現。LLM 側は「どのインターフェースで会話しているか」でリンク形式を使い分ける必要がある。URI 生成ツール自体(`tools/open_in_obsidian.py`)の故障ではなく、チャットUI側のリンク扱いの問題。

## 状態

- `実装済み`: `tools/open_in_obsidian.py` にページ解決ロジックを追加。
- `自動試験済み`: `tools/tests/test_open_in_obsidian.py` で 9 件通過。
- `実機確認済み`: 2026-07-12、武田さんが Raycast から確認し「問題ない」と報告。

## 完成条件

- 保管庫内の相対パスから正しい `obsidian://open` URI を生成できる。
- 空白・日本語を含むパスを URL エンコードできる。
- 保管庫外のパスを拒否する。
- 対象 source ページの先頭からも直開きできる。
- `wiki/` / `raw/` の Markdown ページを、相対パス・絶対パス・Obsidianリンク・Wikilink・完全一致ページ名から解決できる。
- 曖昧な候補を誤って開かない。
- Raycast から 1 回入力して Obsidian 前面へ開ける。

## 関連

- [[obsidian-ui-improvement-roadmap]]
- [[llm-maintainer-handbook]]
- [[google-tasks-quickadd]]
