---
type: source
title: ヘレン「星夜のワルツ」3D資料化 導入計画
authors: [Codex]
date: 2026-07-15
source_path: /Users/takedayousuke/llm-uploads/20260715-191331--ヘレン-星夜のワルツ-3D資料化-導入計画.md
ingested: 2026-07-15
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-15
tags: [gf2, helen, starlit-waltz, plan, blender, mmd, eagle]
---

# ヘレン「星夜のワルツ」3D資料化 導入計画

## 要約

ヘレン「星夜のワルツ」を**MMD 動画ではなく 3D 作画資料として使う**ための導入計画。
公式配布 PMX を Blender 4.5 LTS + MMD Tools へ読み込み、中立状態と観察ポーズを分けて保存し、
カメラとレンダーをスクリプト化する計画として整理されている。

## ソース内の主要主張

- 公式配布ページは `AplayBox` の `海伦（星夜华尔兹）` を採用する。
- 私的な作画資料としてのみ使い、商用利用・再配布・部品流用はしない。
- 作業場所は一時導入として `~/Downloads/gf2-helen-starlit-waltz-pilot/` を想定していた。
- Blender は 4.5 LTS Apple Silicon 版、MMD Tools は v4 系を想定。
- 中立状態を `helen-neutral.blend`、観察ポーズ状態を別名保存し、中立を壊さない。
- `helen-reference-capture.py` を作り、正面・斜め前・真横・斜め後ろ・俯瞰・下から・胸部アップ・ドレス全体を自動生成する。
- 観察ポーズは左右上腕 30 度、肘 10 度を想定し、腕・肩・胸の干渉が見えることを完成条件に置く。
- 最低 8 種類の角度資料と、中立/観察ポーズ比較画像の生成を検証項目に含める。

## 抽出されたエンティティ

- `AplayBox`
- `Blender 4.5 LTS`
- `MMD Tools`
- `GirlsFrontline HelenSSR0101.pmx`
- `Eagle`

## 抽出された概念

- 中立状態を保持した別保存
- カメラ自動生成
- 観察ポーズの固定化
- 角度別レンダー自動化
- 3D 資料化の撤退ライン設定

## 不確実・要確認

- この段階では、配布物の実際の材質再現、透過、骨名、ポーズの自然さは未検証。
- 当初想定の一時ディレクトリ運用は、実装後の最終配置とは一致しない可能性がある。

## 関連リンク

- [[gf2-helen-starlit-waltz-mmd-quickstart-investigation-2026-07-15]]
- [[gf2-helen-starlit-waltz-project-artifacts-2026-07-15]]
- [[gf2-helen-starlit-waltz-3d-reference-build]]
