---
type: build
name: takeda 版 PureRef フォーク構想(画像意図データ層)
aliases: [pureref-personal-fork, pureref-fork, image-intent-data-layer, personalize-data-layer]
tags: [tooling, personalize, pureref, eagle, image-management, planning]
sources: []
status: uncertain
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-06-22
related: [[pureref-session-restore]]
---

# takeda 版 PureRef フォーク構想(画像意図データ層)

## ステータス

- **構想段階(2026-05-19 起票)**。実装は未着手
- 計画書: `~/.claude/plans/eagle-llm-velvet-pond.md` の Phase B
- 関連実装済み: [[pureref-session-restore]](autosave 復元スクリプト)

## なぜ作るか

`.md` 指示書(Eagle 自動整理用)だけでは捕まえきれない暗黙知 ―― **「なぜこの画像を保存したか」「どう使う / 使った か」** ―― を構造化するため、別軸のパーソナライズデータ層を作りたい、という takeda さんの構想。

> 単なる `.md` からの推論だけじゃなくて、別視点のパーソナライズされたデータも作れればなって思っている。(2026-05-19 takeda さん発言)

これは、Eagle のタグ・メモ・レーティングを LLM が機械的に付けるだけでは到達できない「制作プロセス上の文脈」を、人間(takeda さん)の **配置行為そのもの** から拾い上げる試み。PureRef で画像を「どこに置いたか」「何の隣に置いたか」「何の階層に入れたか」は、それ自体が暗黙のラベリングである、という発想。

## 元発話(takeda さん 2026-05-19)

> 「PureRef を改良して、基本的な操作は同じだが、取り込んだ画像データを **読み取れる形** にする。
> PureRef は無限キャンバスとは別軸の階層分けもでき、UI ではフォルダ分けに見える。
> フォルダに画像・テキストデータを入れられる。
> そういう情報から、画像を **何の意図で使ったか** のデータを取り込み、画像自体に **評価指標やデータの意味づけ** を行う。
> 単なる `.md` からの推論だけでなく、**別視点のパーソナライズデータ** を作れればという大計画。」

## 構成要素

| 要素 | 内容 | 現状の壁 |
|---|---|---|
| **入力UI** | PureRef 互換の操作感(画像投げ込み・無限キャンバス・階層フォルダ) | PureRef 純正は独自バイナリ `.pur`、外部からデータが読めない |
| **データ層** | 画像ごとに「配置座標 / 所属フォルダ / 隣接画像 / 添えテキスト / 使用意図 / 評価」を構造化保持 | 標準 PureRef にこの機能なし |
| **エクスポート** | JSON / SQLite で他ツール(Eagle / LLM)から読める形 | 自作 or 別ツール乗り換えが必要 |
| **意味づけ取得** | 「なぜここに置いたか」を入力時 or 事後に LLM がインタビュー形式で吸い上げ | UX 設計が未定 |
| **Eagle 連携** | PureRef 側で生まれたメタを Eagle のタグ/メモ/レーティングに同期 | 双方向 API 設計が必要 |

## 実装ルート候補(評価未了)

| ルート | 内容 | 難度 | 自由度 |
|---|---|---|---|
| **(a) `.pur` リバースエンジニアリング + ラッパー自作** | 純正 PureRef を維持しつつ外部から読む(ライセンス・規約要確認) | 高 | 中 |
| **(b) 既存類似ツールへの乗り換え** | Milanote / Heptabase / Scrapbox / Obsidian Canvas / Excalidraw など、データが構造化されている代替に移行 | 低-中 | 中 |
| **(c) Eagle ボード機能 + 拡張** | Eagle のスマートフォルダ・コメント欄を PureRef 代替に育てる(MCP で既に操作可能) | 低 | 低-中 |
| **(d) 自作キャンバスアプリ** | Electron / Tauri で軽量に。最大自由・最大工数 | 高 | 高 |

→ 次の具体タスク: **(a)〜(d) の比較検討表を本ページに追記**。takeda さんと選定後に MVP 設計へ。

## Eagle 自動整理(Phase A)との接続点

- Phase B のデータ層が出力する **「使用意図ラベル」** は、Phase A 指示書のメモ欄ルールと**語彙を揃える**こと
- Phase B が定める **「画像評価指標」** で、Phase A のレーティング基準を**上書き**する
- 双方向同期: PureRef フォーク → Eagle、Eagle → PureRef フォークの両方向で齟齬が出ない設計が要

## 関連リンク

- 計画書: `~/.claude/plans/eagle-llm-velvet-pond.md`(Phase A / B 一体)
- 関連実装: [[pureref-session-restore]] — 純正 PureRef autosave からセッション復元する既存スクリプト群。`.pur` 解析の足がかりになりうる
- オーナー文脈: メモリ `user_takeda_yohsuke_profile.md`
- 連携評価: [[canvas-eagle-connection-strength]] — この構想の Eagle 双方向連携の達成度評価(読む向きは Canvas Ingest で実現／書き戻しは未達)と「使用意図＝一次情報」方針 (2026-06-07)

## 不確実性メモ

- (a) ルートの法的・契約的可否(PureRef ライセンス全文未確認)
- (b) ルートで採用しうるツール各々の API / エクスポート機能(個別調査未了)
- 「意味づけ取得」UX を **入力時に聞く** か **事後にバッチで聞く** かの分岐は未決
- そもそも PureRef 互換でなくてもよいかもしれない(takeda さんの操作感への愛着度合いを再確認したい)
