---
type: analysis
sources:
  - coloso-ye-jji-illustration-course
  - coloso-nekojira-ch01-orientation
  - coloso-chan-02-sec01-opening-info
  - coloso-hizurume-illustration-course
  - coloso-hide-human-drawing-course
  - coloso-marse-illustration-course
  - coloso-sasa-illustration-course
status: active
confidence: medium
evidence_level: ai-hypothesis
last_reviewed: 2026-06-12
---

# 統合候補マップ(2026-06)

> [[llm-maintainer-handoff-plan]](Fable 5 引き継ぎパッケージ計画)の Phase 1 成果物。
> 「どのテーマを講座横断で統合すると wiki の回答精度が上がるか」の優先順位付きバックログ。
> **優先順位は AI の判断(ai-hypothesis)であり、武田さんの承認で確定する。**
> 模範テーマ(Phase 2)は武田さんが決定済み(後述)。承認後は、後継 AI がこの順で
> 1 テーマずつ統合作業(調査表付き、計画正本の Phase 2 形式)を行う。

## 再計測(2026-06-11 23:56 実測)

| 項目 | 件数 |
|---|---|
| wiki 全 Markdown | 661 |
| sources | 225 |
| entities | 17 |
| concepts | 388 |
| memes | 9 |
| builds | 9 |
| analyses | 13 |
| `status:` あり | 430(無し = 231 = legacy) |
| index.md 行数 | 864 |

- 計測後に本パッケージの新規ページ(計画正本・本マップ)が加わるため、以後のセッションで
  件数を判断に使う場合は再計測すること。

## 対象 7 講座の固定(Phase 2 講座調査表の対象)

| 講座(講師) | entity | source 接頭辞 | 代表 source | source 数 | concept 数 |
|---|---|---|---|---|---|
| Ye Jji | [[ye-jji]] | `coloso-ye-jji-` | [[coloso-ye-jji-illustration-course]] | 24 | 86 |
| Nekojira | [[nekojira]] | `coloso-nekojira-` | [[coloso-nekojira-ch01-orientation]] | 26 | 26 |
| chan | [[chan]] | `coloso-chan-` | [[coloso-chan-02-sec01-opening-info]] | 20 | 140 |
| ひづるめ | [[hizurume]] | `coloso-hizurume-` | [[coloso-hizurume-illustration-course]] | 27 | 59 |
| hide | [[hide-animator]] | `coloso-hide-` | [[coloso-hide-human-drawing-course]] | 28 | 47 |
| マーセ | [[marse]] | `coloso-marse-` | [[coloso-marse-illustration-course]] | 23 | 27 |
| 佐々 | [[sasa]] | `coloso-sasa-` | [[coloso-sasa-illustration-course]] | 37 | 9 |

- concept 数 = frontmatter `sources:` の接頭辞から機械集計(2026-06-11)。
- 佐々は source 37 に対し concept 9 と概念化が最も浅い(統合時に source 直接参照が必要)。

## 機械走査の結果(方法と限界)

- **方法**: 全 388 concept の frontmatter `sources:` を講座接頭辞へマッピング + index.md の
  カテゴリ・1 行サマリで束ね。**この段階では講座本文を調査していない**(計画の段階化に従う)。
- **結果**: 388 concept 中 **380 が単一講座のみ**。複数講座を統合済みの concept は 6 つだけ:
  - [[shi-sen-yu-dou]](4 講座: ye_jji・ひづるめ・マーセ・佐々)
  - [[focus-first-composition]](マーセ・佐々)/ [[jiko-tensaku]](ひづるめ・佐々)/
    [[line-as-shadow-deformation]](ひづるめ・ye_jji)/ [[mitsudo-management]](ひづるめ・ye_jji)/
    [[texture]](マーセ・ye_jji)
- **観察**: ye_jji 系ページに「Nekojira 視点」節が追記される形の**部分統合**が複数存在
  ([[shadow-area-via-occlusion-and-reflection]]、[[saido-no-3-points]])。講座横断の知識が
  特定講座名のページ内に埋まっており、検索・帰属の観点で統合ハブ化の価値が高い。
- **限界**: slug・索引・frontmatter ベースの束ねのため、名前が違うが内容が重なるテーマを
  見落とし得る。本文確認したページは `## 変遷` に記録(高優先度・模範候補のみ)。

## 統合候補テーマ(優先順位付き)

優先度の根拠 = 関わる講座数 × 矛盾・分岐の裁定価値 × 武田さんの制作判断への直結度。
**「本文確認済み」のみ順位確定。「要調査」「見込み」は着手時に本文確認してから確定する。**

### 高優先度 1: 影の設計(面積配分・シェイプ・暗部の描き方)— ★模範テーマ(武田さん決定)

- **状態**: **模範統合ページ [[shadow-design]] 作成済み(2026-06-12、Phase 2 停止点の
  レビュー待ち)**。以下は作成時の調査記録として保持。
- **確認状態**: 本文確認済み(2026-06-12: [[shadow-shape-design-4-principles]]
  [[six-four-shadow-ratio]] [[shadow-area-via-occlusion-and-reflection]]
  [[coloso-sasa-ch10-light-impression]])
- **関連がありそうな講座**: ye_jji・Nekojira・chan・佐々(本文確認済み)+ ひづるめ
  ([[hikari-kage-2-direction]] [[value-25-rule]] — index 確認)・マーセ
  ([[coloso-marse-ch15-shadow]] — index 確認)。
- **関連ページ**: 上記 + [[occlusion-shadow]] [[form-shadow]] [[cast-shadow]]
  [[reflected-light]] [[hikari-kage-2-direction]] [[coloso-marse-ch15-shadow]]
- **矛盾・分岐候補**:
  - **Nekojira 影比率 7:3/8:2(均等回避)vs chan 影 6:4(キャラ中心では意図的に拡大)** —
    chan 側が「通常は 7:3」と認めた上で破る条件付き分岐。裁定価値が高い。
  - ye_jji の物理ベース(オクルージョン + 反射光)vs Nekojira のシェイプデザイン論 —
    既に「補完関係」とメモ済みだが、ハブ不在で他講座と繋がっていない。
  - 佐々 ch10 はフォームシャドウ・落ち影・環境光・反射光・オクルージョンを区別する
    講座内モデルを提示(本文確認済み)— ye_jji の 7 要素との対応付けが統合の論点。
- **統合形態**: **新規の横断ハブ concept(武田さん決定 2026-06-12)**。中立 slug
  (既存 2 ページは ye_jji / Nekojira の講座名を冠した内容のため、横断ハブの置き場として不適)。
- **優先度の根拠**: 6 講座に分布 + 数値が直接ぶつかる分岐(7:3 vs 6:4)+ 影は塗り工程の
  中核で制作判断への直結度が高い。模範テーマとしての決定理由は「模範テーマの決定」節を参照。
- **legacy 追補対象**: [[shadow-shape-design-4-principles]] と
  [[shadow-area-via-occlusion-and-reflection]] は `status:` 無し — 統合で触る際に追補。

### 高優先度 2: 視線誘導の統合完成

- **確認状態**: 本文確認済み(2026-06-12: [[shi-sen-yu-dou]] [[e-no-chikara-ba]]
  [[gaze-water-flow-model]])
- **関連がありそうな講座**: ye_jji・ひづるめ・マーセ・佐々(統合済み)+ **chan(未統合)**。
  Nekojira は間接候補([[silhouette-readability]] 等)、hide は関連薄い見込み — 着手時の
  調査表で正式判定。
- **関連ページ・現状**: 既存ハブ [[shi-sen-yu-dou]] が 4 講座まで統合済み・frontmatter 完備・
  講師間の射程差も記録済み。**chan の視線誘導体系が丸ごと未統合**:
  [[gaze-water-flow-model]] / [[shudai-fukudai-sub]] / [[main-gaze-vs-sub-gaze]] /
  [[contrast-nesting]] / [[hidden-color-line-device]] / [[six-four-shadow-ratio]]。
  このうちハブへ片方向リンクがあるのは [[gaze-water-flow-model]]・[[main-gaze-vs-sub-gaze]]・
  [[hidden-color-line-device]] の 3 ページのみ(grep 確認 2026-06-12)。残り 3 ページは
  リンクも無く、ハブ側に chan 節も無い。
- **矛盾・分岐候補**: 「視線誘導」の定義域が講師ごとに異なる(ye_jji = 構図を除外 /
  ひづるめ = 構図・遠近まで含み比率式に批判的 / マーセ = フェチ部位の伝達 / chan = 主題・
  副題・サブ + 水流 / 佐々 = 面・線・点 + 回帰)。「同名異射程」として記録済みで、
  「現在の統合見解」への昇華が残り。
- **統合形態の提案**: 既存 [[shi-sen-yu-dou]] の拡張(推奨構造への再編 + chan 節追加 +
  講座調査表)。新規ページ不要。
- **優先度の根拠**: 完成度の 2 本柱の 1 つ(ye_jji 定義)で制作判断への直結度が最大。
  既存ハブがあるため残作業が最少 — 模範テーマ(影の設計)の次に着手する筆頭候補。

### 高優先度 3: 高彩度の置き場所(SSS・明暗境界・影の縁)

- **確認状態**: 本文確認済み(2026-06-12: [[saido-no-3-points]] [[mei-an-kyoukai-saido]]
  [[sss-and-surface-scattering]] [[coloso-sasa-ch10-light-impression]])
- **関連がありそうな講座**: ye_jji・Nekojira・ひづるめ・佐々(本文確認済み)。chan は**要調査**:
  [[border-color-saturation-injection]] は「髪と肌の接点」など物と物の境界への高彩度注入で、
  明暗境界・影の縁と同一主張かは本文から確認できない(同ページは [[mei-an-kyoukai-saido]] を
  「理論側」として参照しており、同族の可能性はあるが未確定)。
- **関連ページ**: 上記 + [[touka-hikari]] [[shadow-edge-high-saturation]]
  [[grey-plus-local-high-saturation]] [[bowtie-mei-do-otoshi-wasure]]
- **共通点(本文確認済み)**: 4 講座とも**高彩度を画面全面ではなく限定的な場所に置く**
  (低〜中彩度の地に対する局所的な彩度配置として扱う)。「4 講座の合意」はここまで。
- **矛盾・分岐候補(重要 — 置き場所の「幅」が講師間で対立)**:
  - **ye_jji = 「中間トーン帯全体」**: [[mei-an-kyoukai-saido]] は「境界ラインだけ高彩度に
    するのは理論的に正しくない描写(個人デフォルメ)。理論上は中間トーン帯全体が高彩度」と
    明言(ch07 本文 + PDF p4)。
  - **ひづるめ・Nekojira = 「境界線・影の縁」への限定アクセント**:
    [[sss-and-surface-scattering]]「明暗境界線に高彩度を入れるだけでクオリティ UP、SSS が
    起きない場所でも『嘘』で OK」/ [[shadow-edge-high-saturation]](影の縁 1 箇所への集約)。
    ye_jji の基準ではこれは「個人デフォルメ」側に分類され得る — **帯全体 vs 線・縁の対立**として
    裁定価値が高い。
  - 佐々は「明暗境界や肌の表面下散乱に高彩度」(帯か線かは本文から未判定 — 統合時に確認)。
  - その他: chan の「接点境界」が同族技法か別技法かの判定。
- **統合形態の提案**: 新規ハブ or [[saido-no-3-points]] 拡張(同ページに既に Nekojira 節が
  あるため拡張でも成立。ただし slug が ye_jji 用語のままになる)。
- **優先度の根拠**: 「限定的な場所に置く」という共通方針の下で、**帯全体(ye_jji)か
  線・縁(ひづるめ・Nekojira)か**という実技に直結する違いが本文確認済みで存在する。
  合意の確認ではなく**この違いの整理にこそ統合価値がある**ため高を維持。

### 中優先度(要調査 — 着手時に本文確認してから順位確定)

#### 4. カラーラフ工程

- 関連ページ: [[color-rough-3-stages]](本文確認済み)/ [[coloso-ye-jji-ch12-color-rough]]
  / [[coloso-sasa-ch16-fantasy-color-rough-1]] [[coloso-sasa-ch17-fantasy-color-rough-2]] /
  [[coloso-marse-ch11-rough]] / [[rough-most-brain]] / [[far-view-completion-criterion]] /
  [[color-rough-quality-over-finish-quality]]
- 関連がありそうな講座: chan(確認済み)・ye_jji・佐々・マーセ・ひづるめ(見込み)
- 矛盾候補: 工程の切り方(chan 3 段階 vs ye_jji 4 工程)/ ラフの完成基準(遠目完成 vs
  色ラフ能力 > 完成能力)— いずれも要調査
- 統合形態の提案: 新規ハブ(ye_jji 側はラフ概念が concept 化されておらず既存拡張先が無い)
- 優先度の根拠: 5 講座見込みで制作工程の要だが、本文確認が chan のみのため中。
  確認が済めば高 3 と同等の価値見込み

#### 5. トーン・明度管理

- 関連ページ: [[tone-as-foundation]] [[tone-6-step-limit]] [[tone-prediction-practice]] /
  [[three-tone-simplification]] [[micro-tone-variation]] / [[value-25-rule]]
  [[saido-up-contrast-truth]] / [[mitsudo-management]](ye_jji 階調削減と接続)
- 関連がありそうな講座: chan・Nekojira・ひづるめ・ye_jji(見込み)
- 矛盾候補: トーン段数の統制値(chan「6 段階上限」vs Nekojira「3 トーン + 微差 2-3%」)—
  数値が直接ぶつかる可能性(要調査: 適用工程が違う可能性もある)
- 統合形態の提案: 新規ハブ(見込み)
- 優先度の根拠: 4 講座見込みの基盤テーマだが、高 1〜3(影・彩度)と範囲が重なるため、
  上位テーマの統合後に範囲を再定義してから着手

#### 6. 線画の役割

- 関連ページ: [[line-as-shadow-deformation]](2 講座統合済み・核候補)/
  [[lineart-3-principles-nekojira]] [[shizenbutu-vs-jinkoubutu]] / [[whole-arm-line-control]]
  [[stroke-count-line-planning]] / [[coloso-sasa-ch18-fantasy-lineart-1]] /
  [[coloso-marse-ch13-lineart]](metadata-only 注意)/ [[coloso-hizurume-ch21-painting-work-2]]
- 関連がありそうな講座: ye_jji・Nekojira・hide・ひづるめ・佐々(見込み)+ マーセ
  (資料薄・要調査)。佐々 ch10 に「線画 = 変形したオクルージョンシャドウ」の発言を本文確認済み
  (2026-06-12)— 核候補ページとの統合相性が良い
- 矛盾候補: 線画の存在意義(ye_jji 最小限派 / Nekojira 3 原則 / hide アニメーター流の
  線そのもの / ひづるめ「線画の黒が寒色化の正体」)— 要調査
- 統合形態の提案: 既存 [[line-as-shadow-deformation]] 拡張 or 新規ハブ — 着手時判断
- 優先度の根拠: 6 講座見込みと分布最大級だが、マーセ線画章が metadata-only など資料の
  薄い講座を含むため中

#### 7. 模写・資料の使い方

- 関連ページ: [[photo-mosha-6-benefits]] [[mosha-4-rules]] / [[mosha-4-categories]]
  [[color-mosha-eye-training]] / [[color-training]] [[meiga-bunseki]]
  [[high-quality-photo-tracing]] / [[observation-via-abstraction]] /
  [[reference-grouping-for-fetish]] / [[coloso-sasa-ch05-observation]]
- 関連がありそうな講座: ひづるめ・chan・ye_jji・Nekojira・マーセ・佐々(見込み、ほぼ全講座)
- 矛盾候補: 模写の目的分類が講座ごとに別体系(6 効果 / 4 分類 / 観察→抽象化)+
  実施ルールの差(補正レイヤー禁止等)— 要調査
- 統合形態の提案: 新規ハブ「模写・資料の使い方」1 本で開始し、肥大したら分割(着手時判断)
- 優先度の根拠: 講座数は最大級だが、技法より学習行動寄りで制作判断への直結度は技法系に劣る

#### 8. 自己添削・振り返り

- 関連ページ: [[jiko-tensaku]](2 講座統合済み・核候補)/ [[one-day-smartphone-review]] /
  [[insight-memo]] / [[reanalysis-as-growth-metric]] / [[coloso-ye-jji-ch11-mistake-note]] /
  [[coloso-sasa-ch13-past-work-feedback]]
- 関連がありそうな講座: ひづるめ・佐々(統合済み)+ マーセ・chan・ye_jji(見込み)
- 矛盾候補: 大きな対立は見込み無し(1 日置く / スマホ確認 / チェックリスト / メモは
  手法差として並ぶ見込み)— 要調査
- 統合形態の提案: 既存 [[jiko-tensaku]] 拡張(有力)— 着手時判断
- 優先度の根拠: 統合済みの核があり作業は小さいが、制作の直接判断より学習行動寄りのため中

#### 9. SNS 運用・バズ

- 関連ページ: [[buzz-first-impression-and-structure]] [[zero-to-2man-followers]]
  [[8man-iine-recipe]] [[sns-ng-actions]] [[ai-era-fame-followers]] / [[sns-growth-cycle]]
  [[coloso-sasa-ch09-sns-growth]] / [[portfolio-target-first]]
- 関連がありそうな講座: ひづるめ・佐々(直接)+ ye_jji(ポートフォリオ・キャリア論)。
  他 4 講座は関連薄い見込み(無理に含めない)
- 矛盾候補: ye_jji「ポートフォリオは目標会社ファースト(フォロワー数ではない)」vs
  ひづるめ・佐々「フォロワー・人気が鍵」— 目的軸(就職 vs SNS)の違いとして裁定価値あり
- 統合形態の提案: 新規ハブ
- 優先度の根拠: 武田さんの X 中心活動への直結度は高いが、関わる講座が 3 つのため中

### 低優先度・保留

#### 10. 仕上げ・補正

- 関連ページ: [[layer-effects-by-intensity]] [[vignette-3-side-rule]] [[photo-finish-3-set]]
  / [[blur-overlay-finishing]] / [[monochrome-silhouette-check]] /
  [[coloso-nekojira-ch24-final-finishing]] [[coloso-hizurume-ch19-finishing-3]]
  [[coloso-sasa-ch34-swimsuit-finishing-1]]
- 関連がありそうな講座: 6 講座(hide 以外)見込み
- 矛盾候補: 明確な対立は未検出。手順差が大きく「統合見解」より「手法カタログ」になる見込み
- 統合形態の提案: 要調査(ハブ型かカタログ型かを着手時に判断)
- 優先度の根拠: 統合見解型に向かない見込みのため低

#### 11. 成長戦略・学習配分

- 関連ページ: [[knowledge-conversion-loop]](核候補)[[theory-sense-quantity-quality]] /
  [[seventy-thirty-comfort-challenge]] / [[tokugi-yusen]] [[shitsu-ryou-shitsu]] /
  [[weakness-driven-study-prioritization]] / [[interest-driven-learning]]
- 関連がありそうな講座: 佐々・Nekojira・ひづるめ・chan・ye_jji(見込み)
- 矛盾候補: 練習配分の体系差(70:30 / 理論感覚量質 / 質→量→質)+ 「特技優先(ひづるめ)」と
  「強み先→弱点後(chan)」が同方向か別主張か — 要調査
- 統合形態の提案: 新規ハブ or [[knowledge-conversion-loop]] 拡張 — 着手時判断
- 優先度の根拠: 矛盾は多いが、制作の直接判断より遠い哲学レイヤーのため低

#### 12. 肌・質感

- 関連ページ: [[texture]](2 講座統合済み・核候補)[[texture-types]] /
  [[material-three-patterns]] [[sweat-heat-mood]] / [[skin-shittori-water-drop-highlight]]
  [[skin-tanpaku-painting]] / [[wet-skin-2-step]] /
  [[coloso-sasa-ch31-swimsuit-character-rendering-1]]
- 関連がありそうな講座: ye_jji・マーセ・chan・Nekojira・佐々・ひづるめ(見込み)
- 矛盾候補: 肌の彩度・密度方針(chan「淡白に塗る」vs マーセ「血色・汗・しっとり強調」)—
  スタイル分岐として裁定価値あり(要調査)
- 統合形態の提案: 既存 [[texture]] 拡張 or 肌特化の新規ハブ — 要調査
- 優先度の根拠: 高 3(彩度)と SSS 周りで重複するため、彩度統合後に範囲確定してから

#### 13. 人体・構図の単独テーマ群(保留)

- 関連ページ: [[three-mass-body-blocking]] [[box-proportion-method]] / [[s-curve-waist-pose]]
  / [[weight-axis-balance]] [[nagare-rhythm]] / [[kouzu-1-to-2]]
- 関連がありそうな講座: 人体 = hide 中心 + Nekojira・マーセ(部分)。構図 = ひづるめ・
  マーセ・佐々(ただし視線誘導と大幅重複)
- 矛盾候補: 未検出(hide はほぼ単独峰で統合相手が限定的)
- 統合形態の提案: 保留(構図は高 2 視線誘導の統合後に残余を判断)
- 優先度の根拠: 統合価値(複数講座の裁定)が現時点で小さいため保留

## 模範テーマの決定(2026-06-12 武田さん)

- **模範テーマ(Phase 2)= 高優先度 1「影の設計」、形式 = 新規の横断ハブ concept**。
- 決定基準(武田さん): 「すぐ使える完成度」よりも**「後継 AI が今後行う典型作業の手本」を優先**。
  既存ハブの拡張ではなく、複数講座の根拠調査 → 共通点・条件分岐・未確定の整理 → 新規統合、
  という一連の作業を模範として残す。
- 経緯: Fable の当初推奨は「視線誘導(既存 [[shi-sen-yu-dou]] 拡張)= 完成度・確実性優先」
  だったが、上記の決定基準により影の設計が模範テーマに確定。視線誘導はバックログ最上位群の
  まま(着手順の最終確認は Phase 1 承認時)。

## このマップの使い方(後継 AI 向け)

1. 着手前に [[llm-maintainer-handoff-plan]] と(Phase 3 完成後は)メンテナー指南書を読む。
2. 武田さんが承認した優先順で 1 テーマずつ。テーマ着手時に全 7 講座の調査表
   (計画正本の Phase 2 形式)を作る。「要調査」テーマは本文確認してから順位を確定する。
3. 統合で触った legacy ページには status / confidence / evidence_level を追補する
   (触った分だけ。一括変換はしない)。
4. 本マップの優先順位は ai-hypothesis。実態とズレたら本マップを更新し、`## 変遷` に残す。

## 変遷

- 2026-06-12: 初版作成(Fable 5、Phase 1)。再計測 2026-06-11 23:56、本文確認 8 ページ
  ([[shi-sen-yu-dou]] [[e-no-chikara-ba]] [[gaze-water-flow-model]]
  [[shadow-shape-design-4-principles]] [[six-four-shadow-ratio]]
  [[shadow-area-via-occlusion-and-reflection]] [[saido-no-3-points]] [[color-rough-3-stages]])。
  ※初版の変遷には「6 ページ」と誤記(正しくは 8)。
- 2026-06-12: Codex の Phase 1 レビューを反映して修正。①「高彩度の置き場所」の根拠を是正
  (誤根拠 intermediate-color-weakness を削除、chan の border-color-saturation-injection は
  「接点境界」のため要調査へ、ひづるめ [[sss-and-surface-scattering]] と佐々
  [[coloso-sasa-ch10-light-impression]] を本文確認して根拠に追加 — 計 3 ページ追加確認、
  累計 11 ページ)②候補 4〜13 に計画既定の 5 項目(関連ページ / 講座 / 矛盾候補 / 統合形態 /
  優先度根拠)を追補 ③ chan 6 ページのハブへのリンク状況を grep 実測に修正(リンク有は 3)
  ④武田さん決定(模範テーマ = 影の設計・新規横断ハブ)を反映し、優先度 1 と 2 を入替。
- 2026-06-12: Codex 再指摘を反映し、高優先度 3 の「4 講座合意確認型」を**撤回**。
  [[mei-an-kyoukai-saido]] の本文確認(+1、累計 12 ページ)により、ye_jji は「中間トーン帯全体」
  (境界ラインだけは誤りと明言)で、ひづるめ・Nekojira の「境界線・影の縁」と置き場所の幅が
  対立すると判明。共通点を「高彩度を限定的な場所に置く」へ縮小し、帯全体 vs 線・縁を
  重要な分岐として記録。優先度は高 3 のまま(違いの整理に統合価値)。

## 関連リンク

- [[llm-maintainer-handoff-plan]] — 引き継ぎパッケージ計画の正本
- [[shi-sen-yu-dou]] — 視線誘導の既存ハブ(高優先度 2)
- [[llm-wiki-ai-precision-schema]] — 本マップが従う AI 精度優先スキーマ
- [[feedback-granularity-ai-precision]] — 粒度判断の原則(統合形態の判断基準)
