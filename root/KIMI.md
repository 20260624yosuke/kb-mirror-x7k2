# LLM Wiki — Schema for Kimi Code

このディレクトリは **LLM Wiki** 形式のパーソナル知識ベースです。
あなた（Kimi Code）はこのナレッジベースの maintainer として、まず `AGENTS.md` の共通規則を読み、続いて本ファイルの Kimi 独自補足を適用してください。

## Kimi 独自補足: 成果物の場所を明示する

### ルール

1. **ファイル・ページ・設定・スクリプトなどを作成・更新した場合、説明や感想より先に場所を書く。**
2. **Wiki ページ**を作成・更新した場合:
   - `wiki/...` の相対パスを必ず書く。
   - 可能なら `obsidian://open?vault=...&file=...` の **Obsidianで開く** リンクを併記する。
   - URI は `python3 tools/open_in_obsidian.py <相対パス> --markdown` で生成する。
3. **Wiki 以外のファイル**（コード、設定、Blend、画像、レポートなど）を作成・更新した場合:
   - プロジェクトルートからの相対パスを必ず書く。
   - 必要に応じて絶対パスも併記する。
4. **成果物が複数ある場合は箇条書きで一覧にする。** 主たる成果物と補助ファイルを分けてもよい。
5. **成果物が無い純粋な説明・回答・相談の場合は、「本回答に新規/更新ファイルはありません」と書く。**
6. **「作成しました」「更新しました」だけで場所を省略しない。** ファイルパスまたは `[[slug]]` が無い報告は未完成とする。

### 報告の形式例

#### Wiki ページを更新した場合

```
更新したページ: wiki/sources/coloso-ye-jji-ch02-contrast.md
Obsidianで開く: obsidian://open?vault=LLM+Knowledge+Base+_01&file=wiki%2Fsources%2Fcoloso-ye-jji-ch02-contrast
```

#### スクリプトを作成した場合

```
作成したファイル:
- tools/generate_report.py （プロジェクトルート相対）
- /Volumes/SSD_M.2/05_claude/.../tools/generate_report.py （絶対パス）
```

## Kimi 独自補足: 成果物 Inbox への申告

場所の明示(上のルール)に加えて、成果物を作成・更新した場合は inbox への申告も必須。
チャット内リンクはハーネス依存で押せないことがあるため、申告した記録が確認導線の正になる。

```
python3 "<KBルート>/tools/inbox.py" add "<絶対パス>" --origin kimi --task "<依頼の要約>" --note "<見て判断すべき点>"
```

- ユーザーの入口: Raycast「成果物Inboxを開く」/「成果物Inbox処理済み」。CLI は `llm-wiki-inbox`。
- 共通規則は `AGENTS.md`「成果物 Inbox(全ハーネス共通の機械導線)」節。正本は [[deliverable-inbox]]。

### 関連

- `AGENTS.md` — 共通規則
- [[kimi-code-artifact-location]] — 本ルールの詳細・経緯
