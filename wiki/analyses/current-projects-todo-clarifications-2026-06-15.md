---
type: analysis
sources: []
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-06-15
---

# 現行プロジェクトとToDoの状態訂正(2026-06-15)

> 進行状況の最新一覧は [[projects-dashboard]] を参照。本ページは 2026-06-15 時点の
> 意図・経緯の記録として残す。

## 問い

現在進行中のプロジェクトと ToDo を整理した後、武田さんが確認済み事項・保留・曖昧だった
ToDo の意図を訂正した。

## 確定した状態(user-stated)

- [[llm-maintainer-handoff-plan]]: Fable 5 が現在使用不可のため、Phase 3 は保留。
- [[multi-site-image-search]]: Raycast からの実呼び出しを含め、画像検索ツールは実機確認済み。
- [[canvas-reference-tools]]: Canvas 画像をクリスタへ貼り付ける操作は実機確認済みで、使用感も良好。
- [[pureref-session-restore]]: 実装済みなのは PureRef のファイル集合の再起動後復元であり、
  Mac 全体のウィンドウ位置・サイズ・並びの復元ではない。

## Canvas画像コピーの認識訂正

実装確認の結果、現在の Canvas 画像コピーは Obsidian とクリスタを直接接続する専用処理ではない。
macOS の共通クリップボードへ PNG 画像を書き込む方式であり、画像クリップボードを読める
他アプリにも貼り付けられる。したがって「クリップボード方式へ変更する」大規模修正は不要。

現在は Canvas 内部への貼り付けも壊さないため、画像と同時にマーカー付き JSON テキストを
クリップボードへ保持している。将来、テキストアプリへ貼った際の JSON 表示が実害になった場合のみ、
内部貼り付け情報を独自形式へ分離する改善を検討する。

## 汎用ウィンドウ配置復元

「ウィンドウ固定ソフトが欲しい」「再起動後にウィンドウがごちゃつく」は、同じ問題意識を持つ
ToDo だが、まだプロジェクト化されていない。[[pureref-session-restore]] は PureRef ファイルを
再度開くだけで、画面上の位置は復元しない。汎用の配置復元を始める場合は、対象アプリと
「位置・サイズ・デスクトップ・表示モニター」のどこまで復元するかを先に決める必要がある。

## Eagle保存時のX反応数スナップショット案

### 武田さんの意図(user-stated)

Eagle へ X の画像を保存する時点で、画像情報と一緒に、そのポストのいいね数・リポスト数を
取得して残したい。後から最新値を追跡するのではなく、**保存時点の数値を記録できればよい**。

### 実現性(inferred)

- X API は公開ポストの `public_metrics` として、いいね・リポスト・返信・引用等を取得できる。
- Eagle Plugin API は URL からの項目追加時に `website`・`tags`・`annotation` を設定でき、
  既存項目も Item API の `save()` で更新できる。
- したがって「X のポスト URL / post ID を取得 → 保存時の `public_metrics` を取得 →
  `取得日時 + 数値` を Eagle の注釈へ保存」は技術的に実現可能。
- 数値は変動するため、タグよりも注釈へ
  `2026-06-15 保存時: likes=... / reposts=...` のように時刻付きで残す方が適する。
- X API は現在、使用量に応じた課金方式。API を使わず画面表示から数値を読む方法は、
  X の画面変更で壊れやすいため、安定運用には向かない。

### 現在の状態

アイデアの意図と実現可能な経路を記録した段階。実装方式・保存形式・費用上限は未決で、
まだ build として着手していない。

> 追記(2026-06-21): この目的(保存時点の反応数を Eagle へ残す)は X→Eagle 保存拡張
> [[x-eagle-free-save-pilot]] で**実装済み**(画面表示の反応数を注釈へ記録、v0.5.12 実機OK)。
> 画面読み取り方式を採用したため、本節の「X API 方式・費用上限」の検討は不要になった。
> 本案は同拡張の一機能であり、独立した未着手プロジェクトではない(混同しないこと)。

### 公式情報

- Eagle Plugin API Item: https://developer.eagle.cool/plugin-api/api/item
- X API Metrics: https://docs.x.com/x-api/fundamentals/metrics
- X API Pricing: https://docs.x.com/x-api/getting-started/pricing

## 関連リンク

- [[llm-maintainer-handoff-plan]]
- [[multi-site-image-search]]
- [[canvas-reference-tools]]
- [[pureref-session-restore]]
