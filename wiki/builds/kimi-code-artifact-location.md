---
type: build
title: Kimi Code 成果物場所明示ルール
created: 2026-08-19
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-19
---

# Kimi Code 成果物場所明示ルール

## 目的

Kimi Code が「作業した」と報告しても、ファイルの場所が分からないとユーザーが探す手間がかかる。
Kimi Code 専用に、成果物の場所を報告の先頭に確実に書くルールを定める。

## ルール

1. **場所を最初に書く**: ファイル・ページ・設定・スクリプトなどを作成/更新した場合、説明や感想の前に場所を示す。
2. **Wiki ページ**: `wiki/...` の相対パスに加え、可能なら `obsidian://open` リンクを併記する。
3. **Wiki 以外のファイル**: プロジェクトルートからの相対パスと、必要に応じて絶対パスを併記する。
4. **複数ファイルは一覧**: 箇条書きで列挙し、主成果物と補助ファイルを分ける。
5. **成果物が無い場合も宣言**: 「新規/更新ファイルはありません」と書く。
6. **場所なき「作成しました」は禁止**: パスまたは Wikilink が無い報告は未完成とする。

## 例

### Wiki ページの更新

```
更新したページ: wiki/sources/coloso-ye-jji-ch02-contrast.md
Obsidianで開く: obsidian://open?vault=LLM+Knowledge+Base+_01&file=wiki%2Fsources%2Fcoloso-ye-jji-ch02-contrast
```

### 新規スクリプト

```
作成したファイル:
- tools/generate_report.py
- /Volumes/SSD_M.2/05_claude/.../tools/generate_report.py
```

## 経緯

- 2026-08-19: Kimi 使用時に「報告だけで場所が不明」という不満が出たため、Kimi Code 専用ルールとして追加。

## 関連

- [[obsidian-direct-open-entrypoint]]
- [[llm-maintainer-handbook]]
