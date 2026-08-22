---
type: build
title: Canvas ingest 指南書 — Eagleフィードバック目的の優先順位と手順
created: 2026-07-05
sources:
  - eagle-personalize-workflow-redesign-2026-07-03
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-07
---

# Canvas ingest 指南書(Eagleフィードバック目的)

## 目的(取り違え防止)

このingestは**Canvasをwiki化するためではない**。目的は、Canvasに残っている
「武田さんが実際にどの画像をどう使ったか」という一次情報を抽出し、**Eagleへ返す**こと:

1. **実戦_使用済みタグ**: Canvasで実際に使われた画像(=保存のゴールに到達した画像)をEagleで
   引けるようにする。武田さんの定義「保存のゴールはCanvasで使われること」に直結する成果物
2. **答え合わせデータ**: CLIP検索・タグの品質検証(実際に使った画像が上位に出るか)
3. **語彙の種**: used-for/noteに武田さんが書いた言葉=検索語彙辞書の最良の材料

抽出主軸の選択は不要 — `tools/canvas_ingest.py` は1回の実行で sha256確定リンク・used-for・
note・グループを全部同時に出す。**最大化すべきは sha256確定リンク数**(=Eagleに返せる量)。

## 対象の全体像(2026-07-05 実測)

- raw/_MY_ART に Canvas 79個。ingest済み2個:
  - `2026_05_30_アスナxアイドル衣装.canvas` → [[art-canvas-9a22d71d38cd]](sha256確定194件)
  - `2026_05_29_長乳xOLxアスナ_01.canvas` → 旧形式パイロット(中身ほぼ空。**再ingest対象**)
- 未取り込み約77個 = 名前付き作品Canvas約10個 + 「無題のファイル」系約60個 + __light複製

## 優先順位(3段)

### 優先1: 現行制作テーマ(2026_07の3個)— 最初のバッチ

| Canvas | 理由 |
|---|---|
| `raw/_MY_ART/2026_07/2026_07_04_この夏、どんな水着イラストを描くか計画.canvas` | 今まさに進行中の計画ボード(30KB) |
| `raw/_MY_ART/2026_07/2026_07_03_最近の水着キャラ調査_メモ.canvas` | 同テーマの調査メモ |
| `raw/_MY_ART/2026_07/2026_07_03_ニッケ水着キャラx金ビキニ.canvas` | 同テーマの派生 |

今使っている資料に実戦フラグが付く=創作への還元が最速。水着はCLIP棚(clip候補_水着)も
既にあるので、「CLIP候補」×「実戦使用済み」の重なりが最初の答え合わせになる。

### 優先2: 名前付き作品Canvas(約8個)

`2026_05_31_バストアップx寝る_01` / `2026_05_30_お尻xポーズ_01` /
`2026_05_29_アスナxカリン_バストアップ_01` / `2026_05_29_長乳xOLxアスナ_01(再)` /
`オタクシルエットxアスナx運動ウェア` / `2026_07_04_描いた事があるやつじゃないと…(仮説)` など。
画像ノードが多くリンク収量が大きい。1バッチ2〜3個ずつ。

### 優先3(当面対象外): 「無題のファイル」系 約60個

テキスト中心のアイデアの種で、Eagleリンク収量が少ない。ギャラリー
([[myart-canvas-gallery]])で見られるため放置しても失われない。Eagleフィードバック目的では
後回しでよい。将来「アイデアの変遷」を扱う段階で再検討。

### 除外

- `*__light.canvas` は**取り込まない**(正本の軽量複製。二重取り込み禁止)
- raw/ は読み取り専用。Canvasと添付画像の変更・削除は一切しない

## 手順(1 Canvas あたり)

既存の canvas-ingest skill をそのまま使う(正本: `skills/canvas-ingest/SKILL.md`、
実装: `tools/canvas_ingest.py`、設計: [[art-canvas-ingest-design]])。

1. **dry-run**: `--dry-run` で confirmed / candidate / unmatched 件数を確認
2. **本処理**: `--update-index --update-log` 付きで実行 →
   `wiki/sources/<slug>.md` + `<slug>.usage.json`(sidecar)が生成される
3. **Eagleフィードバック変換(本指南書の新設ステップ)**:
   - sidecar の `assets[].eagle_matches[]` から `status == "confirmed"` の item_id を全件収集
   - 書き込み前にログJSON保存(`tools/eagle_sort_data/clip_full/tag_writeback_log_*.json` 書式)
   - Eagleへ **`実戦_使用済み`** タグを付与(Claude=MCP `item_add_tags` /
     Codex=`node .claude/skills/eagle/scripts/eagle-api-cli.js call item_add_tags --json ...`)
   - **candidate(寸法一致どまり)には付与しない**(sha256確定のみ。誤フラグ防止)
   - 検証: タグ検索で件数一致を確認
4. **記録**: log.md にエントリ(Canvas名・confirmed件数・タグ付与件数・ログファイル名)

## 安全規則

- raw/ 読み取り専用 / Eagleへはタグ追加のみ / 書き込みは事前ログ+事後検証 /
  `実戦_使用済み` タグは1種類に統一(Canvas別のタグ乱立をしない。どのCanvasで使われたかは
  sidecar が正本として持っている)
- used-for等の解釈は一次観測(source-backed)とAI推論(inferred)を分離(canvas-ingest既存設計)
- 通常は画像visionを使わない(Canvas JSON・ファイル名・Eagle metadataで足りる。既存設計踏襲)

## 実行環境とモデル

- 手順が文書化済みの反復作業=ミドル級(Sonnet 5 / Codex GPT-5.4、推論低〜中)で実行可。
  **機械的な実行手順(実コマンド・停止条件・ゲート手順)は [[canvas-ingest-model-runbook]] が正本**(2026-07-07 新設)
- Codexで実行する場合の書き込み手段は `.claude/skills/eagle` のCLI(動作確認済み 2026-07-05)
- 初回バッチ(優先1の3個)の結果は上位級(Fable)が抜き取り確認してから優先2へ進む
  → **2026-07-07 実施・合格**(log.md「C1 優先1 抜き取り確認(ゲート判定・合格)」)
- 以後のゲート担当(2026-07-07 武田さん決定): その時点のハイエンド推論モデル
  (現状 Opus 4.8 か GPT-5.5。Fable が定額利用可になれば Fable)。頻度は毎バッチ後

## 実績(2026-07-07 追記)

- 優先1: 3個 ingest+タグ50件(2026-07-06)+ゲート合格(2026-07-07)
- 台帳事故と修復: 2026-07-06 の3個**並行実行**で `wiki/canvas-registry.json` の2エントリが
  lost-update 消失 → 2026-07-07 に `--canvas-id` 明示の直列再実行で修復。
  以後 **canvas_ingest は直列実行のみ**(ランブック絶対規則1)
- 優先2 バッチ1: [[art-canvas-2e58fb8820ad]](バストアップx寝る)/
  [[art-canvas-951230dbd7ee]](お尻xポーズ)/ [[art-canvas-ba1d6b4e50ac]](アスナxカリン)
  ingest+新規23件へタグ付与(2026-07-07)。優先2の残りは約5個

## 期待される成果物(武田さんに残るもの)

- Eagleに `実戦_使用済み` の棚(スマートフォルダ化は武田さん手動・数クリック)
- 「clip候補_水着 かつ 実戦_使用済み」のような掛け合わせ検索(Eagleのタグ複合検索で可能)
- wiki側: Canvas source ページ+sidecar(将来の資料シート・傾向分析の材料)

## 関連リンク

- [[canvas-ingest-model-runbook]](廉価モデル向け実行ランブック・お手本付き)
- [[art-canvas-ingest-design]](canvas_ingest.py の設計正本)
- [[eagle-clip-tag-runbook]](CLIP側の棚化runbook)
- [[eagle-personalize-workflow-redesign-2026-07-03]](プロジェクト全体の設計)
- [[canvas-idea-cultivation-workflow]](無題Canvas群=アイデア育成の背景)
