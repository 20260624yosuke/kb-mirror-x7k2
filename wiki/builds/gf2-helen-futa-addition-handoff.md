---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
sources: []
---

# 引き継ぎ資料 — ヘレン陰部追加（07_futa-helen・2026-08-23 作成）

> [!info] このページの役割
> セッションが頻繁に空中分解する（§3の履歴）ため、**別セッションで作業を再開できる状態を常にここに保つ**。
> 作業の各段階でこのページとプロジェクト側台帳を更新してから次へ進む。
> 実測値の正本はプロジェクト側 `reports/` の台帳群。衝突したらプロジェクト側が新しい。

## 0. プロジェクトの所在と目的

- **プロジェクト**: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/07_futa-helen/`
- **目的**: ヘレン（v51 blend の複製）へ「えっぐいチンポ」を移植する。物理（揺れ）込み。
- **1A 分類**: **approximation**（原作に存在しないパーツ追加のため原作比較は存在しない。
  合否の正本は武田さんの目。忠実再現版とは分離して扱う）
- **Blender**: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app`（4.5.11 LTS・headless 実績あり）

## 1. 承認済みの決定（ユーザー承認済み・再確認不要）

| 決定 | 根拠 |
|---|---|
| 追加部は物理（揺れ）込みで作る | 2026-08-22 22:46 カード回答 |
| 素材は既成品探しから | 2026-08-22 22:55 カード回答 |
| Kemono（海賊サイト）からの収集代行はしない | 2026-08-23 00:14 回答済み（方針確定） |
| 勃起系は正規チャネル探索・萎え系は別セッション分離 | 2026-08-23 00:18 カード回答 |
| **素材 = #7 Gracyanne Barbosa Futa2.blend を採用** | 2026-08-23 08:36 カード回答（#1 makehuman／DAEMON待ちより選択） |

## 2. 現在位置（2026-08-23 10時時点）

**採用確定 → 直後の移植下準備（①②）でセッション死亡。①②未実施。**

次の一手（前セッション承認済みの方針そのまま）:

1. **①陰部クラスタを独立blendに書き出す**: Futa2.blend（読み取り専用ソース）から
   Shaft+Glans+Testicles スロット面のみを独立blend化 → `07_futa-helen/blends/` へ保存
2. **②ヘレン体blendを実測する**: `06_repro-v51/blends/helen-h0157-repro.blend`
   （SHA `e0ba175651c20251…`・**触らない・複製して使う**）から身長・股位置・スケール・肌材質名を実測
3. **③移植計画書を出す** → 武田さん承認 → 実装（造形ループ）

## 3. セッション死亡履歴（なぜこのページがあるか）

| セッション | 時間帯 | 死に方 |
|---|---|---|
| quick-tiger（ses_fd64bd…） | 08-22 22:41 → 08-23 03:08 | プレビュー抽出成功を報告（02:37）→ 空ステップ約100本 → 最終メッセージ未完了のまま死亡 |
| brave-wolf（ses_fd5568…） | 08-23 03:09 → 10:02 | 調査を兼ねて継続。#7採用カード成立（08:36）→ 移植下準備の ls/find 直後（09:01:57）→ 空ステップ727本 → 死亡 |
| （本ページ管理中のセッション） | 08-23 10時〜 | **作業のたびにこのページを更新すること** |

共通パターン: 実質的な最終出力の直後にトークン0の空ステップが連続し、最後のメッセージが
`completed=None` のまま終わる。**「直前に成功報告を書いた」状態が一番危険**。成功報告を書いたら
即このページを更新してから次の作業に移る。

## 4. 主要ファイル（絶対パス）

| 何 | パス |
|---|---|
| ソース blend（読み取り専用） | `/Users/takedayousuke/Downloads/Gracyanne Barbosa/Gracyanne Barbosa - Futa2.blend`（SHA-256 `d27a7d77…c44be`） |
| 候補台帳（正本） | 同プロジェクト `07_futa-helen/reports/SOURCE-CANDIDATES-2026-08-22.md` |
| 資産評価 | 同 `07_futa-helen/reports/ASSET-EVAL-gracyanne-futa2-2026-08-23.md` |
| プレビュー画像 | 同 `07_futa-helen/reports/previews/`（`gracy_shaft_cluster_viewA/B/C.png` が最新版） |
| ヘレン本体 blend（触らない） | 同 `06_repro-v51/blends/helen-h0157-repro.blend` |

## 5. 触ってはいけないもの

- **`~/Downloads/Gracyanne Barbosa/` 配下** — 読み取り専用ソース
- **`06_repro-v51/blends/helen-h0157-repro.blend`** — v5.1 再現の成果物。移植には必ず複製を使う
- **既存 `.blend` 25個**（`05_helen-motion-library/.../rest-room-v2.2/blends/`）
- **`raw/`** — read-only

## 6. ライセンス上の残課題

#7 は Patreon 有料投稿物で**購入証拠・ライセンス表記が未確認**（台帳 #7 行参照）。
技術作業は進めるが、確定は武田さん確認事項として記録に残し続けること。

## 7. 変更履歴

- 2026-08-23: ページ新規作成（brave-wolf 死亡後の再開地点を記録）。①②未実施。
