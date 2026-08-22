---
type: source
title: ヘレン SSR0101 ショートパンツ衣装化 計画
authors: [Codex]
date: 2026-07-27
source_path: /Users/takedayousuke/llm-uploads/20260727-001858--ヘレン-SSR0101-ショートパンツ衣装化.md
ingested: 2026-07-27
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-27
tags: [gf2, helen, ssr0101, short-outfit, blender, mmd, plan]
---

# ヘレン SSR0101 ショートパンツ衣装化 計画

## 要約

`ヘレン_SSR0101_礼服_930.blend` を元に、参照画像に近いショートパンツ丈の衣装を
**作画資料として360度観察できる別 Blend**へ派生させる実装前計画。
上半身・顔・髪・コート・靴などは維持し、Welrod のパンツ構造を候補に使って、
元 Blend を上書きせずに新規成果物を作る方針と撤退ラインが整理されている。

## ソース内の主要主張

- 目的は、参照画像に近いショートパンツ丈の衣装を作画資料として観察できる状態にすること。
- 上半身、髪、顔、手袋、脚ベルト、靴、肩掛けコートは維持する。
- PMX への再出力、再配布用モデル化、17モーション全件保証は対象外。
- 第一候補は `Welrod_デフォルト.blend` の `Cth1-Pants` を使う。
- Welrod 側のウェイトは使わず、ヘレンの `P1-Cloth-Underwear` と `BodySkin` から骨の影響を移し直す。
- 元の `P1-Cloth-Skirt` は腰接続部だけ残し、長い部分を除去して新パンツと継ぐ。
- 再生成用スクリプト `07-build-helen-short-outfit.py` を追加し、入力元・候補・出力先・検査レポート先を引数化する。
- 最終成果物は `01_blend/ドールズフロントライン2/ヘレン_SSR0101_ショートパンツ参考.blend` とする。
- 検証は中立5方向レンダーと、代表モーション `H0157` / `H0176` / `H0705` で行う。

## 抽出されたエンティティ

- `ヘレン_SSR0101_礼服_930.blend`
- `Welrod_デフォルト.blend`
- `Cth1-Pants`
- `P1-Cloth-Skirt`
- `P1-Cloth-Underwear`

## 抽出された概念

- 元 Blend 非破壊の派生成果物
- ドナー衣装の移植
- ウェイト転送のやり直し
- 中立姿勢と代表モーションでの段階検証
- 撤退ラインを先に固定した試行管理

## 不確実・要確認

- この段階では、見た目の自然さと動作中の露出・貫通は未検証。
- 背面の不明部分は既存衣装から控えめに補う前提で、完全再現は目標にしていない。
- 計画末尾でも「実装はまだ開始していない」と明示されている。

## 関連リンク

- [[gf2-helen-ssr0101-short-outfit-reference-build]]
- [[mmd-library-blender-import]]
