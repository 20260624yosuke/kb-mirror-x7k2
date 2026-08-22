---
type: concept
name: 影の設計(講座横断ハブ)
aliases: [shadow design, 影の描き方(横断), 影設計ハブ]
tags: [shadow, cross-course, hub, synthesis]
sources:
  - coloso-ye-jji-ch04-volume
  - coloso-ye-jji-ch14-coloring-process
  - coloso-ye-jji-ch15-shadow-area
  - coloso-ye-jji-ch17-shadow-detail
  - coloso-nekojira-ch20-light-shadow
  - coloso-nekojira-ch21-color-value
  - coloso-nekojira-ch24-final-finishing
  - coloso-chan-02-sec08-character-focused
  - coloso-chan-02-sec11-coloring-focused
  - coloso-hizurume-ch09-light-shadow-color
  - coloso-marse-ch15-shadow
  - coloso-sasa-ch10-light-impression
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-06-12
---

# 影の設計(講座横断ハブ)

> 7 講座を横断する統合ハブ。影の語彙・面積配分・暗部の描き方・明度規律・視線誘導利用を、
> 講師別の根拠付きで統合する。[[llm-maintainer-handoff-plan]] Phase 2 の模範統合ページ
> (2026-06-12)。バックログ上の位置: [[synthesis-backlog-2026-06]] 高優先度 1。
> **帰属原則**: 以下はすべて「講師が該当講座でそう述べた」であり、講師の制作実態や
> 一般理論としての断定ではない。

## 現在の統合見解

影の設計は、調査した 7 講座のうち根拠を採用した 6 講座(hide は判断保留)で、次の 5 層に
整理できる。

> **根拠レベルの区別**: この節の層分け・「共通」「条件分岐」などの裁定は **AI の整理
> (inferred)**。講師ごとの発言・数値そのものは「根拠(講師別)」節と各出典リンクから
> **source-backed**(元資料で直接確認できる)として確認できる。ページ全体の
> `evidence_level` は inferred(統合に AI の判断を含むため)。

### 1. 影の語彙 — 5 講座が同じ区別を使用(確度高)

- **[[form-shadow|フォームシャドウ]]**(物体自身の立体由来)/
  **[[cast-shadow|キャストシャドウ = 落ち影]]**(物体が他へ落とす)/
  **[[occlusion-shadow|オクルージョンシャドウ]]**(接触部の最暗部)/
  **[[reflected-light|反射光]]**(暗部を弱く持ち上げる)— この区別が共通言語。
- ye_jji が最も体系的(量感 7 要素 [[ryou-kan]]、ch04)。chan は sec11 で同じ 2 分法を定義
  ([[form-shadow-vs-cast-shadow-definition]])。マーセは ch15 で「胸自体の立体影」と
  「胸が腕へ落とす影」を分けて実演。佐々は ch10 でフォーム・落ち影・環境光・反射光・
  オクルージョンを区別し理由づけて適用。Nekojira はオクルージョンを線画にまで拡張
  ([[lineart-3-principles-nekojira]])。
- → wiki の回答では、この区別を影の標準語彙として使ってよい。

### 2. 面積配分 — 「均等を避ける」は共通、具体比率は目的で分岐

- Nekojira(ch20): **7:3 / 8:2**。「5:5 は効果が薄れる」「形を均等に分割したくない」
  ([[shadow-shape-design-4-principles]])。
- chan(sec08): 通常は 7:3(目に心地よい比率)と認めた上で、**キャラクター中心では
  6:4 へ影を意図的に拡大**し視線を圧縮・滞留させる([[six-four-shadow-ratio]])。
- **統合**: 「影と光の面積を均等に割らない」が共通原則。7:3 か 6:4 かは対立ではなく
  **目的による条件分岐** — 画面の心地よさ・動き優先 = 7:3 系(Nekojira)、キャラへ視線を
  集中 = 6:4(chan)。
- 付随する調整(Nekojira): シンプルな服 → 影多め / 複雑な柄 → 影控えめ。比率が均等に
  寄ったらキャストシャドウの追加で 7:3 へ戻す。

### 3. 暗部の中の描き方 — オクルージョン + 反射光(2 講座一致)

- ye_jji(ch14 要約 PDF p4): 暗部ではミッドトーン・キャスト・フォームの明度差が機能しない
  ため、**オクルージョン(さらに暗く)+ 反射光(明るく抜く)だけで形を読ませる**
  ([[shadow-area-via-occlusion-and-reflection]])。オクルージョン 5 原則(ch17)、
  反射光は主光より弱く。
- Nekojira も同運用: 影内へもう 1 トーン足す「バウンスライト」(ch21/24)、仕上げでの
  オクルージョン点の反復配置(ch24)。
- ye_jji(ch04 PDF p5): **影は重ねない** — 同一光源では影同士が重なっても光量は負に
  ならない([[shadow-do-not-overlap]])。オクルージョンのみ光方向非依存の例外で、
  「重ねる」のではなく「最暗部として 1 段暗くする」扱い。

### 4. 明度・色の規律と工程

- ひづるめ(ch09): 光と影の**バリュー値(白黒明度)差は 25 以上**、**彩度 0 の影は不可**、
  影の色相は光の色相とずらす([[value-25-rule]])。明るい絵は例外になりやすいと本人明言。
- 塗り方向 2 種(ひづるめ ch09): **光から影**(王道・カジュアル向き・速い)/
  **影から光**(現実的・情報量を上げやすい・視線誘導の塗りと関わる)
  ([[hikari-kage-2-direction]])。
- 実装工程の例: ye_jji は色塗り 6 工程の中で「影領域の設定(ch15)→ 明部(ch16)→
  暗部 = オクルージョン + 反射光(ch17)」と独立工程化。マーセ(ch15)は乗算レイヤー +
  グレーで 1 影(光源決定)→ 細かい影 → ソフトライトで色味 → 明部はスクリーン
  ([[marse-atsunuri-workflow]])。

### 5. 影は視線誘導の道具でもある

- chan: 影 6:4 拡大 = 視線の圧縮・滞留(sec08、「ノイズマーケティング」とも)。
- マーセ: パラソルの**落ち影で主題部位(胸)を際立たせる**、目の周りを暗くして目の立体感
  (ch15)。
- ひづるめ: 「影から光」の塗りは視線誘導と関わる(ch09)。
- → 視線誘導側の統合は [[shi-sen-yu-dou]](バックログ高優先度 2)で行い、本ページは
  影側の根拠置き場とする。

## 根拠(講師別)

- **ye_jji**(該当講座でそう述べた): 暗部 = オクルージョン + 反射光(ch14 起点、ch15/17
  実演)/ オクルージョン 5 原則(ch17)/ 影は重ねない(ch04)—
  [[shadow-area-via-occlusion-and-reflection]] / [[shadow-do-not-overlap]]
- **Nekojira**: 影シェイプ 4 原則 = 大中小・点線面・シンプル&複雑・7:3(ch20)/
  「正解はない、効果的な戦略の使い方が重要」(ch20 12:59)/ バウンスライト・オクルージョン点
  (ch21/24)— [[shadow-shape-design-4-principles]]
- **chan**: 影 6:4 への意図的拡大(sec08)/ フォーム vs キャストの定義、フォームシャドウの
  書き込み量とコントラストが質感の確信(sec11)— [[six-four-shadow-ratio]] /
  [[form-shadow-vs-cast-shadow-definition]]
- **ひづるめ**: バリュー値 25 以上・彩度 0 不可・色相ずらし / 光から影・影から光(ch09)—
  [[value-25-rule]] / [[hikari-kage-2-direction]]
- **マーセ**: 乗算 1 影 → 細影 → ソフトライト色味の工程 / 立体影と落ち影の区別 / 落ち影に
  よる主題強調(ch15)— [[coloso-marse-ch15-shadow]]
- **佐々**: 主光源の強さ・色・方向を先に決め、影 5 種を理由づけて適用 / 明暗境界・SSS の
  高彩度で視線を集める(ch10)— [[coloso-sasa-ch10-light-impression]]

## 講座調査表(全 7 講座)

| 講座 | 確認したページ | 関連性 | 根拠への採用 | 理由 |
|---|---|---|---|---|
| Ye Jji | [[shadow-area-via-occlusion-and-reflection]]、[[shadow-do-not-overlap]]、[[saido-no-3-points]] | 直接関連 | 採用 | 暗部描写の代替原則・オクルージョン 5 原則・影は重ねない等、影設計の中核主張が揃う |
| Nekojira | [[shadow-shape-design-4-principles]]、[[shadow-area-via-occlusion-and-reflection]] 内の Nekojira 視点節(ch14/18/21/24) | 直接関連 | 採用 | シェイプ 4 原則(影をデザインとして扱う独自体系)+ 暗部運用が ye_jji と一致 |
| chan | [[six-four-shadow-ratio]]、[[form-shadow-vs-cast-shadow-definition]] | 直接関連 | 採用 | 面積配分の条件分岐(通常 7:3 → キャラ中心 6:4)と影語彙の定義 |
| ひづるめ | [[hikari-kage-2-direction]]、[[value-25-rule]] | 直接関連 | 採用 | 明度規律(バリュー 25・彩度 0 不可)と塗り方向 2 種 |
| hide | 全 28 source を「影/シャドウ」で grep 走査し、言及 6 件の文脈を確認([[coloso-hide-ch12-three-mass-blocking]] 影面での奥行き確認、[[coloso-hide-ch14-figure-perspective]] 接地影、[[coloso-hide-ch27-character-illustration]] 影付け) | 判断保留 | 不採用 | 収載分の言及は作画補助の付随的記述のみで、影の設計に関する主張は無い。ただし ch27 は章説明上「影付け」を扱うが transcript 不在(metadata-only)のため内容を判定できない |
| マーセ | [[coloso-marse-ch15-shadow]] | 直接関連 | 採用 | 乗算ベースの影工程、立体影 / 落ち影の区別、落ち影による主題強調 |
| 佐々 | [[coloso-sasa-ch10-light-impression]] | 直接関連 | 採用 | 影 5 種の区別と理由づけ適用(理論章)。ch21-22 / ch30-33 の実演章は未調査(追加調査候補) |

## 矛盾・未確定

- **7:3 vs 6:4 は対立ではなく条件分岐**(chan 自身が「通常は 7:3」と認めた上で目的により
  破る)— 本ページの統合見解 2 で整理済み。
- **影シェイプ 4 原則は Nekojira 単独の体系**で、他講座の裏取りは無い(マーセ
  [[same-area-color-variation]] と方向は整合するが同一主張ではない)。
- chan のフォーム / キャスト定義は ye_jji と**用語レベルで一致**するが、エッジ品質や濃度
  設計など運用の細部が同じかは未調査。
- 佐々 ch10 は Whisper 文字起こし由来で**用語の誤変換リスク**あり(source ページ注記)。
  実演章(ch21-22 / ch30-33)は未調査。
- hide は ch27(影付けを扱う章)の transcript 不在により**判断保留**(調査表参照)。
- ひづるめ「影から光」とマーセの乗算ワークフローの関係(併用可能か別系統か)は未調査。
- 「影は重ねない」は単一主光源が暗黙の前提(複数光源の限界は [[shadow-do-not-overlap]] の
  解釈の揺れ参照)。

## 変遷

- 2026-06-12: 初版作成(Fable 5、引き継ぎパッケージ Phase 2 の模範統合ページ)。
  全 7 講座を調査(講座調査表参照)、根拠採用 6 講座、hide は判断保留。
- 2026-06-12: `evidence_level` を source-backed → **inferred** へ訂正(武田さん確定方針)。
  講師発言そのものは source-backed だが、層分け・条件分岐の裁定という AI の判断を含む
  統合ページのため。本文に根拠レベルの区別注記を追加。

## 関連リンク

- [[synthesis-backlog-2026-06]] — 統合候補マップ(本テーマ = 高優先度 1)
- [[llm-maintainer-handoff-plan]] — 引き継ぎパッケージ計画の正本
- [[shi-sen-yu-dou]] — 視線誘導ハブ(影の視線誘導利用の統合先、高優先度 2)
- [[ryou-kan]] — ye_jji 量感 7 要素(影語彙の体系元)
- [[mitsudo-management]] — 密度管理(影の情報量配分と関連)
