---
type: entity
name: Obsidian
aliases: []
category: product
sources: [obsidian-canvas-two-dimensional-plot, obsidian-backlink-link-board-art-reference]
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-07-12
---

# Obsidian

## 現在の統合見解

Markdown ベースのノートアプリ。Canvas、バックリンク、ローカルファイル管理を使った創作資料・プロット整理の文脈で登場する。

2026-07-12 のローカル調査では、Obsidian 1.12.7 が通常 Quit の終了承認後も本体プロセスを残す症状を確認した。`canvas-reference-tools` 無効、全コミュニティプラグイン無効、空 vault でも再現したため、この症状は特定プラグインや vault 内容より Obsidian / Electron / macOS 側に寄った問題として扱う。運用対策は [[canvas-reference-tools]] 側の終了ガードとして実装済み。

## 根拠

- [[obsidian-canvas-two-dimensional-plot]] — 関連 source。
- [[obsidian-backlink-link-board-art-reference]] — 関連 source。

## 矛盾・未確定

- 本ページは今回取り込んだ source に基づく最小限の統合。実名・経歴など、source に無い情報は推測で補っていない。
- 2026-07-12 の終了停止調査はローカル環境の観測に基づく。Obsidian / Electron / macOS 側に寄った問題という判定は切り分け結果からの推定であり、Obsidian 本体の根本原因までは未特定。

## 変遷

- 2026-06-22: raw 残り ingest で作成。
- 2026-07-12: Obsidian 終了承認後に本体プロセスが残るローカル症状と、[[canvas-reference-tools]] 側の終了ガード対策へのリンクを追記。

## 関連リンク

- [[obsidian-canvas-two-dimensional-plot]]
- [[obsidian-backlink-link-board-art-reference]]
- [[canvas-idea-cultivation-workflow]] — Canvas を資料ビュー兼アイデア育成の作業台として、窓を大量に常時開く運用とその理由。
- [[canvas-reference-tools]] — Obsidian 終了承認後の本体プロセス残存に対する、この vault 向けの終了ガード実装記録。
