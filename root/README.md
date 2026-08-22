# LLM Knowledge Base

LLM が継続的に育てるパーソナル知識ベース(LLM Wiki パターン)。

## 構成

- `raw/` — 不変のソース置き場。記事・論文・画像・データなど。**LLM は読み込み専用**。
- `wiki/` — LLM が生成・更新するマークダウン Wiki。
  - `wiki/sources/` — 各ソースの要約
  - `wiki/entities/` — 人物・組織・製品など
  - `wiki/concepts/` — 概念・テーマ
  - `wiki/analyses/` — 複数ソース横断の分析・質問への回答
- `index.md` — 内容カタログ。Wiki 全ページの 1 行サマリ。
- `log.md` — 時系列ログ。ingest / query / lint の履歴。
- `CLAUDE.md` — Claude Code 用のスキーマ document(LLM 向け規約)。
- `AGENTS.md` — Codex と、その他の LLM が参照できる共通入口規約。

## 使い方

1. `raw/` にソース(マークダウン化した記事、PDF、画像)を置く。
2. Claude Code または Codex を開き、`/llm-wiki ingest raw/<file>` で取り込み。
3. 普通に質問するか `/llm-wiki query <質問>` で wiki に問い合わせ。
4. ときどき `/llm-wiki lint` で健康診断。

## LLM 向け入口

- Claude Code は `CLAUDE.md` を最初に読む。
- Codex は `AGENTS.md` を最初に読む。
- Kimi Code は `KIMI.md` を最初に読む（`KIMI.md` 内で `AGENTS.md` の共通規則を参照する）。
- その他の LLM は、参照可能な `AGENTS.md` または `CLAUDE.md` を最初に読む。
- 旧形式ページを強い根拠にする前の確認ルールは、両方の規約に同じ内容で置いている。

## 推奨ツール

- **Obsidian** で `wiki/` を開くとグラフビューと `[[link]]` ナビゲーションが快適。
- wiki ページを直接開くときは `python3 tools/open_in_obsidian.py <wikiの相対パス> --markdown` で直開きリンクを作れる。LLM の回答にも同じ `Obsidianで開く` リンクを付ける。
- **Obsidian Web Clipper** で記事を Markdown 化して `raw/` に投入。
- 画像を含む記事は Obsidian の「Download attachments for current file」(`raw/assets/` に保存) を併用。

## 詳細

LLM Wiki パターンの設計思想と詳細は、利用中の LLM 環境で実際に使える
`llm-wiki` スキルの `SKILL.md` と `reference.md` を参照。
