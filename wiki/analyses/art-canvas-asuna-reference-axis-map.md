---
type: analysis
sources: [art-canvas-9a22d71d38cd]
status: active
confidence: medium
evidence_level: inferred
last_reviewed: 2026-06-15
related:
  - "[[art-canvas-9a22d71d38cd]]"
  - "[[hair-flow-design]]"
  - "[[texture]]"
  - "[[leg-silhouette-rhythm]]"
  - "[[eagle-folder-prediction-pilot-2026-06-14]]"
---

# アスナ×アイドル衣装 Canvas — 参考軸マップ(深掘り抽出パイロット)

[[art-canvas-9a22d71d38cd]] の Canvas メモ（382 note / ユニーク 185）を **武田さんの参考軸**ごとに
整理し、**既存の講座由来 concept** と **Eagle フォルダ軸**に接続した抽出パイロット。
目的は、深掘り抽出（note / candidate / provenance）を「自動化のテンプレート」にできるかの実証。

> [!note] このページの evidence_level
> Canvas メモの引用は **source-backed**（本人がボードに書いた一次テキスト）。
> 軸への分類と concept への接続は **inferred**（LLM の解釈）。

## 抽出で確認した構造（重要）

- Canvas の note は実際には3種類が混在する:
  1. **参考観察** — なぜ参考にするか・どの軸か（例「なびく後ろ髪の情報量参考になるな」）← **抽出対象**。
  2. **制作判断** — この作品でどうするか（例「アスナの後ろ髪の動きを考える必要ある」）← アスナ作品固有。再利用概念にしない。
  3. **反応/自問** — 「あ〜これ」「いいな」← 抽出価値低。
- 参考軸の語彙は **新規作成不要**。すでに **389 の concept（講座由来）に存在**し、Canvas メモはその**適用例**。
  つまり高価値の抽出は「新ページ量産」ではなく「**実践（Canvas）↔ 講座知識（concept）↔ Eagle 軸**の接続」。

## 参考軸 → 既存 concept → Eagle フォルダ

### 髪
- 参考観察（source-backed）:「なびく後ろ髪の情報量参考になるな」「後ろ髪がうねってるのいいな」
  「髪のシルエットとか束の描き方の資料になるな」「毛束の毛先のシルエットの参考にできるかも？」
- 接続 concept（inferred）: [[hair-flow-design]] / [[hair-tuft-strong-weak]] / [[silhouette-check]]
- Eagle 軸: `03_髪_髪デフォルメ参考`
- 制作判断（作品固有）:「アスナの後ろ髪の動きを考える必要ある」「後ろ髪とサイドテールで別方向にするのどう？」

### 肌・質感
- 参考観察:「腹部の質感って大事だな」「脚質感参考」「肌にハイライト入れてるなこれ」「むちっとしてる」
  「原作のmx2jの質感」「改めて質感えぐいわ」
- 接続 concept: [[texture]] / [[texture-types]] / [[abdominal-muscle-simplification]] /
  [[skin-shittori-water-drop-highlight]] / [[wet-skin-2-step]]（むっちり→[[mx2j]] の作画質感）
- Eagle 軸: `03_人体03_肌_質感/むっちり感` / `03_人体01_腹部周り`

### 脚・足
- 参考観察:「脚質感参考」「ふとももの質感エロいな」「脚のポーズのシルエットパクった」「この絵の脚みて書いてたわ…」
- 接続 concept: [[leg-silhouette-rhythm]] / [[texture]]
- Eagle 軸: `03_人体02_脚`
- 制作判断:「いやそもそも膝上ぐらいでもいいかもな」「これは膝上だもんな」

### 手
- 参考観察:「手首と人差し指」「手の角度が想定と違ったな」「実際に手を見たら確かに」「お手本と照らし合わせて調整」
- 接続 concept: [[hand-type-silhouette]]
- Eagle 軸: `03_人体01_手`

### 服・衣装
- 参考観察:「服も当たり前に形参考になるわ」「スカート参考」「シュシュはいつもの形状のタイプだな」
  「肩の部分の服の構造を考えたい」「エレンジョーの服と構造似てるのかも？」
- 接続 concept: 該当が薄い（[[paint-first-clothing-hair]] / [[clothing-skin-gap-fetish]] は技法寄り）。
  **「服の構造」概念は未収載の候補**。
- Eagle 軸: `05_服装_*`（ファッションイラスト / フリル 等）

### 光・影
- 参考観察:「肌にハイライト入れてるなこれ」「デコルテに頭の落ち影が落ちてるのいいな」「え…ていうかこれ逆光やん」
- 接続 concept: [[silhouette-of-light-edge]] / [[ambient-vs-dramatic-light]]（**逆光**は専用 concept 要確認）
- Eagle 軸: `06_明暗_逆光` / `06_明暗_照明参考`

### 構図・シルエット
- 参考観察:「厚みのシルエット参考」「この2枚を基本のシルエットとして参考にした」「全身構図だから」
- 接続 concept: [[silhouette-check]] / [[monochrome-silhouette-check]] / [[silhouette-readability]]
- Eagle 軸: `04_構図_*` / `04_ポーズ`

## 登場プロヴェナンス（作者・キャラ・作品）

- **作者（抽出済み entity）**: [[mx2j]] / [[anyak]] / [[lufi-ays]] / [[yutokamizu]]
- **作者（未抽出・review_candidates の artist-signal 4件）**: Kronii 系イラストの `@archinoer` / `@DotTheBot18` / `@rasec_asdjh`
- **キャラ（未 entity 化）**: アスナ・セリカ・ハスミ・ユウカ（ブルアカ）/ ゴールデングロー（アークナイツ）/
  クロニー（ホロライブ）/ モダニア・ラピ・アニス・ノイズ（NIKKE）/ マシュ（FGO）/ エレン・ジョー（ゼンゼロ）
- **作品**: ブルアカ / アークナイツ / NIKKE / ホロライブ / FGO / ゼンゼロ
  - いずれも Eagle に対応フォルダ（`07_キャラ_*` / `07_作品_*`）が既存。

## 未抽出・要判断

- **review_candidates 150 内訳**: fragment 82 / memo-propagation-deferred 29 / question系 32 /
  artist-signal 4 / unnamed-group 3。価値が高いのは artist-signal 4（Kronii 系）。残りは低価値 or tool 必要。
- candidate→relation への昇格は **sidecar（機械正本）の書換**になるため手編集しない（tool 経由が筋）。

## このパイロットで見えた「自動化の形」

入力: Canvas note（雑多）→ **①分類**（参考観察 / 制作判断 / 反応）→ **②参考観察を既存 concept と Eagle 軸へ接続**
→ **③provenance（作者/キャラ/作品）を entity 化** → **④曖昧は candidate に退避**。
これは決定論ツール（taskB2）が出す骨格の **先にある LLM 判断層**そのもの。
[[eagle-folder-prediction-pilot-2026-06-14]] の二軸（視覚テーマ / 出自）とも一致する。

## 関連リンク

- [[art-canvas-9a22d71d38cd]]
- [[eagle-folder-prediction-pilot-2026-06-14]]
- [[hair-flow-design]] / [[texture]] / [[leg-silhouette-rhythm]] / [[silhouette-check]]
