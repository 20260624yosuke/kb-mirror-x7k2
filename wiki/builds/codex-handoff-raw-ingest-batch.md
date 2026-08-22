---
type: build
title: "Codex 引き継ぎ: raw/ 残り ingest 一括処理"
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-06-22
---

# Codex 引き継ぎ: raw/ 残り ingest 一括処理(正本)

> **この文書は Codex への作業引き継ぎ用。** 武田さんの指示で、raw/ の未処理分の取り込みを
> Codex が担当する(理由: Codex のトークンを消費したいため)。Claude が 2026-06-22 に作成。
> **正本はこのファイル。** 会話履歴の旧版や写しを正本にしない([[llm-maintainer-handoff-plan]] と同じ運用)。

## 0. まず読むもの(正本の在り処)

Codex は着手前に必ず以下を **その場で読み直す**:

1. `AGENTS.md`(Codex 用の規約。Claude は CLAUDE.md、**別ファイル**)。特に
   **「2次情報の扱いと『質問より宣言』」** 節(2026-06-22 追加)。
2. 共通スキル `~/.claude/skills/llm-wiki/reference.md` の **「X クリップ ingest — サブワークフロー」** 節
   (取り込み手順の正本。Claude/Codex 共通)。
3. `index.md`(既存ページ一覧)と `log.md`(時系列・監査線)。

## 1. このタスクの全体像

- 大目的: `raw/` 配下の **未取り込みソースを wiki 化** する(要約・エンティティ・概念・ミーム・相互リンク・台帳更新)。
- `raw/` は **read-only**。書き換え・削除しない。出力は `wiki/` と `index.md` / `log.md` のみ。
- 言語はすべて日本語。ファイル名は kebab-case(ASCII)。リンクは `[[slug]]`。

## 2. 済んだこと(Claude が 2026-06-21〜22 に実施)

- **パイロット5件**の X クリップを処理(これを形式の手本にしてよい):
  - [[x-naoki-saito-art-style-timing]] — 絵師ウォッチ → 新規 entity [[naoki-saito]]
  - [[x-kazkitashima-elon-monetization-solo-meme]] — 新規 meme [[hitori-meme]] + 既存 [[x-kazkitashima-elon-dm]] と相互リンク
  - [[x-ippandouga-cn-bluearchive-asuna-pv]] — 既存 entity [[asuna-bluearchive]] / [[bluearchive]] を更新
  - [[x-musadosmeme-adulting-adaptation]] — 新規 meme [[adulting-adaptation-meme]]
  - [[x-nekojira-belis-backlight]] — 技術観察。勘を [[hizurume]] の光概念へ **2次情報** リンク
- **恒久ルールを3ファイルへ明文化**: reference.md(取り込み手順)、CLAUDE.md と AGENTS.md(方針)。
- 大量取り込みの **パイロット → レビューは完了済み**。Codex は **本処理(full)** から始めてよい。

## 3. これからやること(残り = この引き継ぎの範囲)

範囲は「残り全部」。**X を主、記事・ログを従**として進める。

### (A) X クリップ ≈ 83 件 ← 主目的(トークン消費の大物)

- `raw/Post by @<handle> on X.md` 形式 **82 件** + `raw/(5) あゆの（@ayunochan_）さんのメディアポスト.md` **1 件**。
- **未取り込みリストは固定で持たず、着手時に下記で再生成**(取りこぼし・二重処理防止):

```bash
cd "<KB root>"
grep -rhoE '^source_path: .*$' wiki/sources/ | sed 's/^source_path: //;s/^"//;s/"$//' | sort -u > /tmp/ingested.txt
# 未取り込みの X ポストを列挙
for f in raw/"Post by @"*.md; do grep -qF "$f" /tmp/ingested.txt || echo "TODO: $f"; done
```

### (B) トップレベル記事 5 件(通常 ingest)

1. `raw/Obsidian Canvasで作る二次元プロット整理システム.md`
2. `raw/【Notion活用術】アイデアを無限に生み出すための具体的な方法.md`
3. `raw/【Obsidian】バックリンクで変わるリンクボード式お絵描き資料管理法とその展望【Canvas】｜希流ハヤ.md`
4. `raw/【SNS攻略の答え】伸びてる”絵描き”の投稿手法全部教えます！！！…【Ixy・いくしー先生Coloso】.md`
5. `raw/ネタが尽きないアイデアの作り方・育て方｜ぼくの考え方・使うツール.md`

→ `wiki/sources/<slug>.md` に日本語要約。登場する概念・人物・ツールは `wiki/concepts/` `wiki/entities/` を新規/更新。

### (C) LLM 対話ログ / Claude Code 記事 4 件

- `raw/_llm_logs/issue_karahajimeyo_yaritori.md`
- `raw/_llm_logs/issue_bottleneck_conversation_log.md`
- `raw/2026_05_19_ingest/inbox/Claude Code実践入門！…解説してみた.md`
- `raw/2026_05_19_ingest/inbox/Claude Codeの便利な機能7選！…解説してみた.md`

→ **対話ログは「外部 LLM の発言」として帰属**し、講座知識・検証済み事実と混ぜない。武田さんの
発言・採用した理解は user-stated として分離(reference.md の「LLM 対話ログ ingest」節 / CLAUDE.md 参照)。

### 対象外(今回はやらない)

- `raw/_MY_ART/*.canvas`(62 件)→ canvas-ingest 専用・自発起動しない・作業フォルダ問題あり。
- `raw/_coloso/` の講座文字起こし(数百)→ /transcript・映像 ingest 専用。講師7名は既に entity 化済み。
- 紛らわしい既処理: `raw/.../イラストレーター {ひづるめ,マーセ,佐々,hide,Nekojira,ye_jji,チャン}.md` は
  既存 entity と重複の可能性大 → **着手前に該当 entity を確認し、重複なら新規作成せず不足分のみ追補**。
- `raw/o07O9n0-2Gnj8UbA.mp4.md` / `raw/無題のフォルダ/無題のファイル.md` は中身を見て判断(空・無意味なら触らない)。

## 4. 守るルール(要点。詳細は §0 の正本)

### X クリップの仕分け = 武田さんのタグ最優先

| `clip_type` タグ | 流し先 |
|---|---|
| `💞トレンド・流行` | source + `wiki/memes/<slug>.md`(ミーム) |
| `💭魅力的な表現/演出のヒント` | source に観察メモ + 関連講座知識へ **2次情報** リンク(Coloso concept に勘を混ぜない) |
| `💭思考メモ` | source + 関連知識へ **2次情報** リンク |
| タグ無し(約2/3) | `＃＃＃` 前の個人メモから 4 分類で推論(reference.md の表) |

- **ミーム判定**: 武田さんがミーム/トレンドとして拾ったら採用。memes は「こういうネタ・空気がある」
  本人の記録。堅い基準で出し惜しみしない(1人が擦り続ける「一人ミーム」も本人命名なら meme 化)。
- **entity 化**: 同じ handle を **2 回以上** 見た絵師のみ。判定は wiki 全文を漁らず `grep` で
  ファイル名 + `author:` 行の出現回数を一発カウント(バッチ開始時に1パスで頻度集計)。1文止まりの
  単発 handle は entity を作らず author 欄止まり。

### 2次情報・正体の扱い(timid に「未確認」で逃げない)

- **広く知られた公的事実**は一次資料に無くても一般知識で確定的に補強。本文に「一般に知られた事実
  として補強(2次情報)」と印を付ける(例: `@_NaokiSaito` = さいとうなおき)。
- **本当に検証できない事柄だけ** `uncertain`(例: X `@Nekojira` が講師 [[nekojira]] と同一人物かは不明)。
- **武田さんの勘・反応**は記録するだけでなく、関連知識へ「2次情報・出典箇所は未特定」と明示リンク。
  孤立させず、講師発言などの確定事実と同じ強さで混ぜない。
- 検証不能な憶測を事実として書くのは禁止。

### 画像

- **画像 vision は既定で使わない**(トークン費用方針)。絵が無いと同定できないミームは
  「不確実 — 要画像確認」と明記し推測で埋めない。

### 進め方(質問より宣言)

- 既定は「ルール通り黙って進める」。質問は、ルールが無い・不可逆・新しい構造判断(新 type/新
  ディレクトリ/新分類体系)に限る。確認が要る時も opt-out 宣言形式(「これでやる、違えば直して」)。
- ユーザー向け説明で横文字・略語・専門用語を未注釈で使わない(直後に日本語の意味を括弧で添える)。

## 5. 各クリップの作業手順(1件ずつ)

1. raw クリップを読む(`＃＃＃` 前の個人メモ + `clip_type` が最重要シグナル)。
2. `wiki/sources/x-<handle>-<topic>.md` を X 専用テンプレ(reference.md)で作成。frontmatter に
   `source_path` / `url` / `author` / `published` / `clipped` / `clip_note`(個人メモ原文) / `ingested`(実施日) /
   `tags` / `status` / `confidence` / `evidence_level` / `last_reviewed`。
3. 仕分け表に従い meme / entity を新規 or 更新。複数該当は source から複数先へリンク。
4. 重複 handle(例: archinoer ×5, NUF_6666 ×2, h4sh1rnoto ×2, ydh2101 ×2, kazkitashima 等)は
   **URL(status ID)で別ポストか確認**。既存 `x-<handle>-*` があれば別 topic の slug にして相互リンク。
5. `index.md` を更新: `### X クリップ` / `## Memes / Trends` / `## Entities` の該当箇所。
6. `log.md` に追記。数十件ごとに小計を残すと追跡しやすい(例: `## [YYYY-MM-DD] ingest | X クリップ 6-30件目`)。

## 6. 落とし穴・注意

- **同時編集**: Claude や別チャットが同じ `index.md` / `log.md` を触ることがある。**編集直前に読み直し、
  小さな Edit、log は末尾追記**。競合したら相手の追記を消さず差分を読んでから足す
  (メモリ `concurrent-chat-no-overwrite` の趣旨。wiki 外のメモリファイルのためリンクではなく名前参照)。
- `raw/` は read-only。Eagle とは連携しない(X クリップに Eagle 参照を張らない)。
- 完了条件: 上記 (A)(B)(C) が `wiki/sources/` に存在し、index/log に反映済み。終わったら本ファイルの
  status を `done` へ更新し、log に完了行を残す。

## 関連

- [[llm-maintainer-handoff-plan]] — 全体の保守引き継ぎ計画(本タスクとは別)
- 手本のパイロット: [[x-naoki-saito-art-style-timing]] / [[x-musadosmeme-adulting-adaptation]] /
  [[x-nekojira-belis-backlight]] / [[x-ippandouga-cn-bluearchive-asuna-pv]] / [[x-kazkitashima-elon-monetization-solo-meme]]

## 完了記録

- 2026-06-22: Codex が handoff の本処理を実行。X クリップ 83 件、通常記事 5 件、Claude Code 記事 2 件を source 化し、既存取り込み済みだった LLM 対話ログ 2 件は `source_path` で確認して重複作成しなかった。`index.md` と `log.md` を更新済み。
- 2026-06-22: 完了後監査で `raw/o07O9n0-2Gnj8UbA.mp4.md` が `source_path` 未一致として残っていることを確認。理由は通常抽出対象だった `Post by @... on X.md` 形式ではなく、handoff でも「中身を見て判断」として保留されていたため。中身は `clip_type: 💭魅力的な表現/演出のヒント` のX動画クリップだったため、[[x-video-bust-softness-bias]] として source 化した。`raw/無題のフォルダ/無題のファイル.md` は 0 bytes のため取り込み対象外。
