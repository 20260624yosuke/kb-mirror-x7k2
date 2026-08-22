---
type: build
title: Xアイデア作業台
created: 2026-06-25
sources:
  - issue-framework-review-for-x-eagle-project-2026-06-23
  - eagle-review-view-plugin-design-2026-06-24
  - x-eagle-project-current-state-interference-audit-2026-06-24
  - x-eagle-idea-workbench-prototype-plan-review-2026-06-25
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-06-25
---

# Xアイデア作業台

## 現在の統合見解

`Xアイデア作業台` は、Eagle 内の X 由来保存を読み取り専用で見返し、武田さん自身が事実カードを束ねて創作方向を作るための Window Plugin（Eagle 内で別ウィンドウを開く形式のプラグイン）。

目的は、AI に創作成果物を作らせることではない。X 由来の作者・投稿本文・反応数・保存先・保存日時を材料にして、武田さんが「題材」「文脈」「周期性」「見せ場」「準備行動」を言語化しやすくすることである。

## 実装状態

2026-06-25 時点で、初回プロトタイプを `tools/eagle-x-idea-workbench/` に作成した。

- `manifest.json`: Eagle Window Plugin 設定。
- `index.html`: 3列画面。
- `styles.css`: 画面スタイル。
- `app.js`: Eagle Web API V2 読込、カード追加、メモ保存、Markdown コピー。
- `workbench-core.js`: X注釈解析、FactCard 化、Markdown 生成。
- `README.md`: Eagle で開く方法と確認観点。
- `tools/tests/test_eagle_x_idea_workbench.js`: 注釈解析とMarkdown生成の自動試験。

## 検証状態

- 実装済み: はい。
- 自動試験済み: はい。`node --check` と `node tools/tests/test_eagle_x_idea_workbench.js` を確認。
- Eagle API 実データ読込確認: はい。`source: x` 検索で 20 件取得し、20 件をカード化できた。
- 画面のモック確認: はい。Google Chrome のヘッドレス実行でカード追加、メモ入力、Markdown生成を確認。スクリーンショットは `tools/eagle-x-idea-workbench/verification-mock.png`。
- Eagle 内 Window Plugin としての実機確認: 未確認。
- 運用開始可能: 未判定。Eagle 内で開き、実データで 5〜10 枚を束ねる使用感確認が必要。

## 非対象

初回プロトタイプでは以下を行わない。

- Eagle 項目、注釈、タグ、フォルダの書き換え。
- AI によるアイデア自動生成。
- 自動分類。
- Wiki への自動書き戻し。
- 長期保存用の外部データベース化。

## 使用感の要点

1. 左列 `事実プール` で X 由来カードを見る。
2. 気になるカードを中央の `組み合わせボード` に送る。
3. 右列 `仮説メモ` で「題材」「文脈」「周期性」「見せ場」「準備行動」を書く。
4. `LLM用コピー` で、選んだ束だけを Markdown としてコピーする。
5. LLM は最初から結論を作る役ではなく、ユーザーが選んだ束への壁打ち相手になる。

## 矛盾・未確定

- Eagle のローカル HTML 画面では CORS（ブラウザが別由来の通信を止める制限）により、Eagle Web API V2 を直接読めない可能性がある。Eagle Plugin API は CORS の影響を受けないため、最終確認は Eagle 内 Window Plugin として行う。
- `localStorage`（プラグイン画面内の簡易保存領域）はプロトタイプ用の仮保存であり、長期保存先としては扱わない。
- サムネイルは注釈内 `image_url` を優先する。`image_url` が無い項目や動画はプレースホルダー表示になる。

## 関連リンク

- [[issue-framework-review-for-x-eagle-project-2026-06-23]]
- [[eagle-review-view-plugin-design-2026-06-24]]
- [[x-eagle-project-current-state-interference-audit-2026-06-24]]
- [[x-eagle-idea-workbench-prototype-plan-review-2026-06-25]]
