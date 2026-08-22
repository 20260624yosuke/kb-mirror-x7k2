---
type: build
title: "Coloso raw ingest 対応表"
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-07
---

# Coloso raw ingest 対応表

## 目的

`raw/_coloso/` 配下の Markdown(マークダウン形式の文章)ページが、既存の `wiki/sources/coloso-*.md`
に対応済みかを棚卸しする。重い再 ingest(再取り込み)を始める前に、重複・空ファイル・
映像 ingest(映像読み取り)対象を分け、次に触るべき raw だけを残す。

## 対象と非対象

- 対象: `raw/_coloso/**/*.md` の 299 件。
- 非対象: 動画・画像・PDF(印刷向け文書形式)などの添付ファイル 452 件。これらは source ページの
  `supplementary`(補助資料)や映像 ingest の対象として必要時に扱う。
- 今回やらないこと: raw の編集、講座内容の再要約、新規 concept(概念)作成、映像 ingest の実行。

## 判定方法

既存 source の `source_path` と `supplementary`(補助資料)を読み、`+ _02.md`、
`＋ 02.md`、`01–03.md` のような省略表記を実在する raw パスへ展開した。
ファイル名の濁点表記ゆれ(NFC/NFD。Unicode 正規化形式で、濁点を一体化するか分離するかの違い)も
正規化して照合した。

判定カテゴリ:

- `対応済み`: 既存 source の `source_path` または `supplementary` から対応が確認できる。
- `既存sourceへ追補必要`: 内容は既存 source に関係するが、監査線としての
  `source_path` / `supplementary` が不足している。
- `低情報なので保留`: 空ファイルや添付メモで、現時点では取り込む本文がない。
- `映像 ingest対象`: 文字情報ではなく動画参照が本体。Codex では実行しない。
- `重複`: 講座商品ページ・講師紹介ページなど、既存の講座メタ source / entity と重なる。
  新規 source 化しない。

## 集計

このページでは、対応済み 290 件を 1 行ずつ貼らず、判定方法と講座別集計へ圧縮する。
次に作業が発生する可能性がある 9 件だけを raw ごとに全列挙する。対応済み 290 件の
個別対応先は、各 source ページの `source_path` / `supplementary` を正本として辿る。

| 判定 | 件数 |
|---|---:|
| 対応済み | 290 |
| 既存sourceへ追補必要 | 1 |
| 低情報なので保留 | 3 |
| 映像 ingest対象 | 1 |
| 重複 | 4 |
| 合計 | 299 |

### 講座別

| rawフォルダ | 合計 | 対応済み | 追補必要 | 低情報保留 | 映像 | 重複 |
|---|---:|---:|---:|---:|---:|---:|
| `01_coloso_ye_jji` | 75 | 74 | 0 | 1 | 0 | 0 |
| `2026_05_30_マーセ` | 19 | 19 | 0 | 0 | 0 | 0 |
| `2026_05_31_hide_01` | 29 | 27 | 0 | 1 | 0 | 1 |
| `2026_05_31_ひづるめ` | 27 | 26 | 0 | 0 | 0 | 1 |
| `2026_05_31_佐々` | 37 | 36 | 0 | 0 | 0 | 1 |
| `coloso_chan_02` | 41 | 41 | 0 | 0 | 0 | 0 |
| `coloso_nekojira` | 71 | 67 | 1 | 1 | 1 | 1 |

## 先に見るべき例外

| raw | 判定 | 対応先 | メモ |
|---|---|---|---|
| `raw/_coloso/01_coloso_ye_jji/_attachments/Pasted image 20260512151206.png.md` | 低情報なので保留 |  | 0 bytes。取り込み不要 |
| `raw/_coloso/2026_05_31_hide_01/フリーアニメーター hide 1.md` | 重複 | [[coloso-hide-human-drawing-course]] / [[hide-animator]] | 講座商品/講師紹介ページ。新規 source 化せず、必要時だけ既存ページへ不足確認 |
| `raw/_coloso/2026_05_31_hide_01/無題のファイル.md` | 低情報なので保留 |  | 0 bytes。取り込み不要 |
| `raw/_coloso/2026_05_31_ひづるめ/イラストレーター ひづるめ 1.md` | 重複 | [[coloso-hizurume-illustration-course]] / [[hizurume]] | 講座商品/講師紹介ページ。新規 source 化せず、必要時だけ既存ページへ不足確認 |
| `raw/_coloso/2026_05_31_佐々/イラストレーター 佐々 1.md` | 重複 | [[coloso-sasa-illustration-course]] / [[sasa]] | 講座商品/講師紹介ページ。新規 source 化せず、必要時だけ既存ページへ不足確認 |
| `raw/_coloso/coloso_nekojira/chapter3/_tools/inventory.md` | 既存sourceへ追補必要 | [[coloso-nekojira-ch03-shape-intro]] | PSD(Photoshop形式の画像ファイル) inventory(中身一覧)。source 本文には反映済みだが、`supplementary` などの監査線へ追補候補 |
| `raw/_coloso/coloso_nekojira/coloso_Nekojira_03 形態入門_資料（動画）.md` | 映像 ingest対象 | [[coloso-nekojira-ch03-shape-intro]] | 動画 URL(リンク先アドレス) / ローカル動画参照メモ。Codex では映像読み取りを実行しない |
| `raw/_coloso/coloso_nekojira/inbox_01/Claudian.md` | 低情報なので保留 |  | 0 bytes。取り込み不要 |
| `raw/_coloso/coloso_nekojira/イラストレーター Nekojira.md` | 重複 | [[coloso-nekojira-ch01-orientation]] / [[nekojira]] | 講座商品/講師紹介ページ。新規 source 化せず、必要時だけ既存ページへ不足確認 |

## 確認したこと

- `raw/_coloso/2026_05_31_佐々/coloso_佐々_07 理論vs感覚, 量vs質.md` はファイル名に
  カンマがあるため単純なカンマ分割では未対応に見えるが、実際は
  [[coloso-sasa-ch07-theory-sense-quantity-quality]] の `source_path` と一致している。
- `raw/_coloso/coloso_nekojira/Nekojira_09 ... 01–03.md` や
  `raw/_coloso/coloso_nekojira/coloso_Nekojira_04 ... 01–03.md` のような範囲表記は、
  既存 source の省略記法として実ファイルへ展開済み。
- `raw/_coloso/coloso_nekojira/coloso_Nekojira_22 ... 01.md ＋ 02.md + 03.md + 04.md + 05.md`
  の全角プラスも展開済み。
- `ye_jji_13. 線画の活用_02.md` から `_04.md` は transcript(文字起こし)が薄いが、
  [[coloso-ye-jji-ch13-lineart]] 側で「ライブドローイング、transcript ほぼ空」として対応済み。

## 次の処理方針

1. 大量再 ingest は不要。299 件中 290 件は既存 source に対応済み。
2. product page(講座商品/講師紹介ページ)の重複 4 件は、新規 source を作らず、
   講座メタ source と entity に不足がある場合だけ追補する。
3. `chapter3/_tools/inventory.md` は [[coloso-nekojira-ch03-shape-intro]] に
   `supplementary` として監査線を足す候補。
4. `coloso_Nekojira_03 形態入門_資料（動画）.md` は映像 ingest 側の入口候補。
   Codex は実行しない。
5. 0 bytes の 3 件は保留でよい。

## 2026-06-22 判断: 残りは実施しない

武田さん確認を受けて、残り 9 件は追加 ingest しない方針にする。

- 追補候補 1 件: `chapter3/_tools/inventory.md` は PSD(Photoshop形式の画像ファイル)の中身一覧で、
  [[coloso-nekojira-ch03-shape-intro]] 本文には内容がすでに反映されている。足すとしても
  `supplementary`(補助資料)の監査線補強だけで、知識量はほぼ増えない。
- 映像 ingest 対象 1 件: `coloso_Nekojira_03 形態入門_資料（動画）.md` は動画参照メモ。
  該当 source はすでに厚く、Codex は映像読み取りを実行しないルールのため、今回対象外。
- 重複 4 件: 講座商品ページ・講師紹介ページで、既存の講座メタ source / entity と役割が重なる。
  新規 source 化すると重複が増える。
- 低情報保留 3 件: 0 bytes の空ファイルで、取り込む情報がない。

結論: 現時点では追加作業しない。将来、監査線を完全にしたい場合だけ
`chapter3/_tools/inventory.md` の `supplementary` 追記を検討する。

## 2026-07-07 追記: inbox の商品ページ 2 件を source 化(全体監査時)

全体監査(lint)で、本表の対象外だった `raw/2026_05_19_ingest/inbox/` に講座商品ページが
3 件残っていたことを確認した。

- `イラストレーター ye_jji.md` → **source 化した**: [[coloso-ye-jji-course-product-page]]。
  講師の所属(Fevercell アカデミー)が文字起こし由来の旧表記「Pーゼルアカデミー」と矛盾しており、
  矛盾解消と 2024 年の経歴事実(鳴潮・崩壊・アズールレーン等)の監査線を確保する価値があったため。
- `イラストレーター チャン.md` → **source 化した**: [[coloso-chan-02-course-product-page]]。
  チャン2=PART 2 という講座系譜の書面根拠を確保するため。
- `イラストレーター Nekojira.md` → **source 化しない**(従来方針のまま)。
  `raw/_coloso/coloso_nekojira/イラストレーター Nekojira.md` と同内容の重複クリップで、
  既存ページとの矛盾も新規事実も確認できなかったため。

注: 上記 2 件の source 化は「商品ページは新規 source 化しない」という 2026-06-22 方針の例外。
矛盾解消・新規事実がある場合のみ source 化する、に方針を精緻化した(それ以外は従来どおり重複扱い)。
