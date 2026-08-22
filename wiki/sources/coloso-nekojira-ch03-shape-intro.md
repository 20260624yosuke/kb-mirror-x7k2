---
type: source
title: Nekojira 03. 形態入門 — 形態の定義 / シルエットとネガティブシェイプ
authors: [Nekojira]
date: 2026-02-15
source_path: raw/_coloso/coloso_nekojira/coloso_Nekojira_03 形態入門.md
source_psd: raw/_coloso/coloso_nekojira/chapter3.psd
source_video: https://www.youtube.com/watch?v=iilllgYITik
source_video_local: 03.形態入門.mp4
psd_extracted_to: raw/_coloso/coloso_nekojira/chapter3/
video_frames_dir: raw/_coloso/coloso_nekojira/chapter3/auto/
video_duration: "08:57"
video_frames_count: 27
video_frames_interval_sec: 20
ingested: 2026-05-16
ingested_video: 2026-05-17
re_ingested: 2026-05-17
tags: [coloso, character-design, shape, silhouette, negative-shape, fundamentals, nekojira]
---

# Nekojira 03. 形態入門

## 要旨

[[nekojira]] によるキャラクターデザイン Section 02 の最初の章。**「形」は視覚言語である**という命題を起点に、3 基本形(○ / □ / △)と性格・感情の対応マッピングを与え、これをキャラクター造形に応用する道具立てを示す。後半は「[[silhouette-readability|良いシルエット]] = clean/comprehensive/easy to read」と、その実現手段としての [[negative-shape|ネガティブシェイプ]] に展開する。

## キーポイント

- **形には意味がある**: ○ = Cute/Softness/**Friendly**、□ = **Stable**/Balance/Protection、△ = **Energetic**/Offensive/Direction([[shape-personality-map]])
- **同じキャラでも形要素の差し替えで性格が変わる**: 章 03 の PSD には 6 段階の女性キャラバリエーション(`Harmless` → `square eyes` → `Calm, obedience` → `Back to harmless` → `Energetic` → `Energetic, offensive`)が controlled experiment として並置されており、目・髪型・輪郭の形を入れ替えるだけで「親しみやすい/落ち着いた/活発/攻撃的」が段階的に切り替わる
- **動物の例**: ヤギに三角の角を足すと攻撃的に、猫を四角化すると頑丈に、スズメに三角を足すと脅威的に見える(ワシ ≒ 三角の塊だから圧的に見える)
- **形を反復しすぎないバランスが必要**(3:28 警告): 単に三角を増やせば力強くなるわけではない
- **良いシルエットの 1 条件 = "clean"**(輪郭が明確で読み取りやすい)。シルエットだけ見て対象が分かるか(4:09 のスズメ NG 例)が判定基準
- **正面ポーズは部位が重なりシルエットが弱くなる**。横向きの方が明確だが、正面でも **ポーズ調整 + ネガティブシェイプ**で救える(4:46〜5:07)
- **ネガティブシェイプの 2 階層**: (1) シルエットレベルで重要部位の周囲を空ける、(2) 内部レベルでも適用可(Rubin's vase 型の二重認識)
- **描く前に「物の形」より「その周りの空間の形」を観察する**癖が、絵の流れと自然さを生む(7:29〜8:06 — 著者自身の学習史的失敗談として開陳)

## PSD 構造の把握(取り込み 2 回目で判明)

`chapter3.psd`(3893×3896, 92 ノード, 16 グループ, 75 ピクセル + 1 テキスト)は **講師ライブ実演用** に設計されており、講義中に **トップレベル 3 グループ(`Shape` / `Silhouette` / `Negative shape`)を 1 つずつトグル ON** + 各セクション内で個別レイヤーを切り替える運用。

初期可視状態は `Shape` のみで、他 2 つは hidden。`sips` フラット PNG 化や全レイヤー強制可視化では **どちらも実際の講義スライド状態を再現できない**。各「講義タイミング」の正解状態は `raw/_coloso/coloso_nekojira/chapter3/canonical/` に **39 枚の canonical PNG として書き出し済み**(レイヤー組み合わせ単独 render 方式)。

### 重要訂正(初回取り込みの誤り)
- 初回 preview 画像「猫・消火器・女性・男性・蝶・カラスのシルエット並列」は**実在しない**。全レイヤー強制可視化で hidden オブジェクトが重なってできた幻
- 実際は `Silhouette/Character` グループ = **3 体のキャラポーズ + 赤線ネガティブシェイプ修正例**(canonical [[b0_silhouette_default.png|b0]])
- 個別物体(消化器・人物・カラス等)は別々の hidden レイヤーに格納されていて、講師は順次トグルで見せていた

## 時系列ダイジェスト × canonical 画像対応表

| 時間 | 節 | 主張 | canonical 画像 | 元レイヤー |
|---|---|---|---|---|
| 0:05–0:22 | Shape | 形とは何か。基本 3 形に絞る | [[a0_shape_overview_3basics.png\|a0]] | `Shape/1/Three basic shape` |
| 0:22–0:47 | Shape | ○=friendly / □=stable / △=energetic の性格マッピング。動物に投影 | a0(同上、上段の 3 形 + 性格ラベル) | 同上 |
| 0:48–1:18 | Shape | **ヤギ + △角 = Attack up**、**猫 → □化 = Defense up** | a0(下段 2 体 + 赤ラベル) | 同上(`Layer 14` `Layer 15` `Layer 6` `Layer 2`) |
| 1:18–1:30 | Shape | スズメに △強調 → 脅威化。ワシは △ の塊 | a0(右列) | 同上 |
| 1:44–2:01 | Shape | バリエーション 1: **Harmless**(基準・○ 全部) | [[a1_variant_01_harmless.png\|a1]] | `Shape/2/Harmless` 4 layers |
| 2:02–2:20 | Shape | バリエーション 3: **Calm, obedience**(落ち着いた大人) | [[a2_variant_02_calm_obedience.png\|a2]] | `Shape/2/Calm, obedience` 4 layers |
| 2:21–2:26 | Shape | バリエーション 2: **square eyes**(目を □ + メガネ追加 → 知的・静謐) | [[a3_variant_03_square_eyes.png\|a3]] | `Shape/2/square eyes` 5 layers |
| (対照) | Shape | バリエーション 4: **Back to harmless**(○ 復帰) | [[a4_variant_04_back_to_harmless.png\|a4]] | `Shape/2/Back to harmless` 4 layers |
| 2:27–2:50 | Shape | バリエーション 5: **Energetic**(△ 増加・活発) | [[a5_variant_05_energetic.png\|a5]] | `Shape/2/Energetic` 3 layers |
| 2:51–3:05 | Shape | バリエーション 6: **Energetic, offensive**(目も △ → 攻撃的) | [[a6_variant_06_energetic_offensive.png\|a6]] | `Shape/2/Energetic , offensive` 3 layers |
| 3:11–3:28 | Shape | 「形には力がある」「ただし反復しすぎ NG」のまとめ | a10 大型コンポジット [[a10_shape2_offensive_composite.png\|a10]] | `Shape/2/Energetic , offensive Copy`(大型 3312×2069) |
| 3:38–4:00 | Silhouette | シルエット節導入 | [[b1_silhouette_intro_long_text.png\|b1]] 長文導入版 | `Silhouette/Silhouette is a quickest way to communicate...` |
| 3:43 | Silhouette | 良いシルエットの条件 = "comprehensive, simple, clean" | [[b2_silhouette_principle_short.png\|b2]] 短文要約版 | `Silhouette/Silhouette need to be comprehensive, simple and clean` |
| 4:00–4:09 | Silhouette | 個別物体 = 内部詳細なしで判別可能(猫・消化器など) | [[b4_standalone_Layer_5_Copy_8.png\|b4-1]] 男女シルエット + [[b4_standalone_Layer_5_Copy_9.png\|b4-2]] 消化器 + [[b4_standalone_Layer_5_Copy_10.png\|b4-3]] | `Silhouette/Layer 5 Copy 8/9/10` hidden 層 |
| 4:09 | Silhouette | スズメ単独 = 判別不能例 | [[b4_standalone_Layer_11_Copy.png\|b4-4]] | `Silhouette/Layer 11 Copy`(652,2778, 1131×1034) |
| 4:21–4:46 | Silhouette | 正面 vs 横向き比較 | b4-1(同男女シルエット) | 同上 |
| 4:46–5:07 | Silhouette | **ポーズ修正実演 + 赤線でネガティブシェイプ指示**(本節の核心) | [[b0_silhouette_default.png\|b0]] 主要スライド | `Silhouette/Character` 9 layers(3 体 = A・B・C のペア) |
| 5:07–5:30 | Silhouette → Negative | 教訓 2 項目(comprehensive + negative shape) 提示 | b0 内テキスト | `Silhouette/1.Silhouette need to be comprehensive... Copy` |
| 5:30–5:50 | Negative shape | **ネガティブシェイプ定義**: Rubin's vase + Escher 鳥魚 | [[c0_negative_shape_default.png\|c0]] 主要スライド | `Negative shape /Layer 17` + `Layer 22` |
| 5:50 | Negative shape | タイトル + 定義文の同時表示 | [[c1_negative_shape_with_text.png\|c1]] | c0 + `Negative space word` group |
| 5:50–7:09 | Negative shape | **実演 Sample 1**: 形 → 曲線追加 → お尻と認識 | [[c4_Sample_1_Layer_1_Copy.png\|c4-1a]] → [[c4_Sample_1_Layer_1_Copy_2.png\|c4-1b]] → [[c4_Sample_1_Layer_1_Copy_3.png\|c4-1c]] | `Negative shape /Sample 1/Layer 1 Copy` `Copy 2` `Copy 3` の段階 |
| 7:09–7:53 | Negative shape | **Sample 2 サキュバスメイド完成作 + ネガティブシェイプ解析**(参照: Nekojira 本人の完成イラスト) | [[c4_sample2_Layer_1_Copy_6.png\|c4-2c]] フルカラー完成版 → [[c4_sample2_Layer_1_Copy_5.png\|c4-2b]] → [[c4_sample2_Layer_1_Copy_4.png\|c4-2a]] 解析版 | `Negative shape /sample2/Layer 1 Copy 4` `Copy 5` `Copy 6` |
| 7:53–8:35 | Negative shape | 著者学習史: 解剖学固執 → ネガティブスペース観察への転換 | テキストのみ(画像補助なし) | (transcript) |

### 参考画像

- 6 バリエーション重ね合わせ確認用(本来は講師がトグル切替): [[a7_variant_all_6_compare.png|a7]]
- Shape/2 動物ペア配置: [[a9_shape2_animal_pair.png|a9]]
- 個別キャラポーズ(Character グループの 8 サブレイヤーを 1 枚ずつ): `b3_character_only_*.png` 8 枚

## 講義動画 frame 対応(20 秒等間隔抽出, 全 27 枚)

2026-05-17 追加。`raw/_coloso/coloso_nekojira/chapter3/auto/MMmSSs.png`(27 枚, 7.1MB, 1280px 縮小済)。
**canonical と相補**: canonical = 静的スライドの正解状態 / video = 講師の **時間進行 + live 注釈追加** の証拠。

### Shape 節(0:00–3:28)

| 時刻 | frame | 状態 |
|---|---|---|
| 0:00 | ![[00m00s.png]] | 講義開始、白板に "Shape" タイトルのみ。Clip Studio Paint (iPad) で進行 |
| 0:20 | ![[00m20s.png]] | ○ □ △ + 性格英語ラベル(`Cute,soft,friendly` / `Stability,serious,artificial` / `Energetic,Movement,danger`) |
| 0:40 | ![[00m40s.png]] | 動物 3 体追加(猫・ヤギ・スズメ、いずれも未改変・素朴な状態) |
| 1:00 | ![[01m00s.png]] | ヤギに **赤い △ 角 + "Attack up" ラベル** を live 加筆 |
| 1:20 | ![[01m20s.png]] | 猫に □ 化 + "Defense up"、スズメに △ 強調、ワシ(△ の塊)を追加描画 |
| 1:40 | ![[01m40s.png]] | バリエーション節へ移行、○ ベースの可愛い女性キャラ(harmless) line drawing |
| 2:00 | ![[02m00s.png]] | 同キャラに **□ 髪型 + メガネ** を赤線下書きで追加(calm, obedience 進行中) |
| 2:20 | ![[02m20s.png]] | **□ 目に変更**(square eyes 完成) |
| 2:40 | ![[02m40s.png]] | 別キャラ: **△ 髪型 + 上向きベクトル**(energetic)、画面左に "Ctrl+Z" 連打のオンスクリーン表示 |
| 3:00 | ![[03m00s.png]] | 全 5 バリエーション並置(対照): harmless / calm / square-eyes 上段 + energetic / offensive 下段 |
| 3:20 | ![[03m20s.png]] | 節終わり: スズメ単独 + 字幕「単に三角形やその他の要素を多く加えれば、自動的にキャラクターが力強くなるというわけではありません」 |

### Silhouette 節(3:38–5:30)

| 時刻 | frame | 状態 |
|---|---|---|
| 3:40 | ![[03m40s.png]] | 新規 "Silhouette" ページ: 黒シルエット 5 体(猫・消火器・女性・男性・蝶) — どれも内部詳細無しで判別可能 |
| 4:00 | ![[04m00s.png]] | 同シルエット + **蝶と消火器に赤線注釈** — 「これらは輪郭がクリーン」の指し示し |
| 4:20 | ![[04m20s.png]] | テキストのみ "1.The silhouette should be easy to understand"(原則登場) |
| 4:40 | ![[04m40s.png]] | A(座位、部位重なり多 = 問題例) / B(改善版) の対比キャラポーズ登場 |
| 5:00 | ![[05m00s.png]] | C(さらに改善版)追加 → A→B→C の 3 段階改善列 |
| 5:20 | ![[05m20s.png]] | **3 体に赤線でネガティブシェイプ箇所を指示**(腕周辺の空間を空ける指示) |

### Negative shape 節(5:30–8:35)

| 時刻 | frame | 状態 |
|---|---|---|
| 5:40 | ![[05m40s.png]] | Rubin's vase + Escher 鳥魚 — 「黒/白を交互に主役として認識する脳の仕組み」例 |
| 6:00 | ![[06m00s.png]] | **実演 Sample 1 開始**: ランダムな曲線(意味不明) |
| 6:20 | ![[06m20s.png]] | 2 つの曲線を追加 → 楕円対が浮かび上がる |
| 6:40 | ![[06m40s.png]] | 楕円対をクリーンアップ |
| 7:00 | ![[07m00s.png]] | 画面を回転 → 「お尻」として認識される、字幕「ただ形から始めてから終わらせただけです」 |
| 7:20 | ![[07m20s.png]] | 胴体(Y 字型)追加。著者学習史パート開始(「解剖学に固執して硬い絵になった」) |
| 7:40 | ![[07m40s.png]] | 胴体仕上げ、字幕「下着のネガティブスペースに注目し始めます」 |
| 8:00 | ![[08m00s.png]] | 同上継続(語り続行) |
| 8:20 | ![[08m20s.png]] | **まとめスライド**: 2 大教訓テキストを Silhouette ページに追加 — `1. The silhouette should be easy to understand` / `2. Instead of overly focus on the object itself, consider the negative shape of the important part could be a solution.` |
| 8:40 | ![[08m40s.png]] | 講義終了画面(まとめスライド継続) |

### Video → canonical 対応 (frame ↔ PSD)

| video frame | 対応 canonical | 補足 |
|---|---|---|
| 0:20 | [[a0_shape_overview_3basics.png\|a0]] 上段 | 基本 3 形 + ラベル |
| 0:40–1:20 | a0 下段 | 動物加筆段階を video のみで観察可 |
| 1:40 | [[a1_variant_01_harmless.png\|a1]] | harmless |
| 2:00 | [[a2_variant_02_calm_obedience.png\|a2]] | calm, obedience |
| 2:20 | [[a3_variant_03_square_eyes.png\|a3]] | square eyes |
| 2:40 | [[a5_variant_05_energetic.png\|a5]] | energetic |
| 3:00 | [[a7_variant_all_6_compare.png\|a7]] | 全並置(canonical では 6 枚、video では 5 枚で表示) |
| 3:40, 4:00 | [[b4_standalone_Layer_5_Copy_8.png\|b4-1]] 系 | 個別物体シルエット群 |
| 4:40–5:20 | [[b0_silhouette_default.png\|b0]] | A/B/C キャラ + 赤線(video では描き加わる過程が見える) |
| 5:40, 8:20 | [[c0_negative_shape_default.png\|c0]] / [[c1_negative_shape_with_text.png\|c1]] | Rubin's vase + Escher |
| 6:00–7:00 | c4 Sample 1 系 | canonical 3 stage に対し video は 60 秒の進行を 5 frame で表現 |
| 7:20–8:00 | (canonical なし — Sample 2 サキュバスメイドではなく、こちらは Sample 1 の続編としての胴体追加) | video のみで観察可 |

### Video が追加で与える情報(canonical で取れない)

1. **ツール特定**: 講師は **Clip Studio Paint on iPad** で実演(右ペインにブラシ・カラーホイール・レイヤパネル)。受講者が同じツールで追随しやすい
2. **live 加筆の時間感覚**: c4 Sample 1(ランダム → お尻)は約 **60 秒**で描かれる。「短時間スケッチでネガティブスペースから入る」の実例として時間感も学習要素
3. **赤線注釈のタイミング**: どのテキスト発話と同期して赤線が引かれるか(canonical では完成形のみ)
4. **動物 / バリエーション例の加筆順序**: 「素朴 → 修飾追加」の段階。"Attack up" "Defense up" のラベルが講師の手書き由来であることが分かる(canonical では既に書き込まれた状態)
5. **Ctrl+Z オーバーレイ**: 2:40 で講師が試行錯誤を実演で見せている(完成形だけ提示する canonical との差)

## 主要登場人物・概念

- [[nekojira]] — 講師
- [[shape-personality-map]] — ○ / □ / △ × 性格マッピング(章 03 の中心概念)
- [[silhouette-readability]] — Nekojira 流のシルエット可読性概念
- [[negative-shape]] — ネガティブシェイプ
- [[silhouette-check]] — 隣接概念([[coloso-ye-jji-ch03-silhouette]] 由来のシルエット複雑化 6 原則)。可読性と複雑化はトレードオフ関係にあり、Nekojira 側は可読性優先

## 引用に値する一節

> 形とは視覚的な言語です。私たちが馴染みのある形を元に意味を伝えるための言語なのです。(3:11)

> 同じ形を繰り返しすぎると不自然に見えてしまってバランスが重要です。(3:28)

> シルエットはキャラクターや物体を伝える最も素早い方法です。輪郭を見るだけで内部の詳細がなくても何かを理解できます。(3:38)

> 重要な部分の周りに意図的に空白スペースを残すことです。これで輪郭をより興味深く理解しやすくします。(5:07)

> 対象物自体より重要な部分のネガティブスペースを考えることが解決策になる。(8:06)

> (筆者の学習史)解剖学的な正確さに集中しすぎて絵が硬くて不自然になる問題があった。ネガティブスペースを理解してからは周りの空間の形を観察して描くようになった。(7:29–7:48)

## 文字起こしノイズの注記

[[coloso-nekojira-ch03-shape-intro]] の元 transcript には音声認識由来の誤変換が多数ある(取り込み時の確認用にメモ):

- 「資格的な言語」→ **視覚的な言語**
- 「輪郭音」→ **輪郭を**
- 「中小化」→ **抽象化**
- 「延金法」→ **遠近法**
- 「企画付形」→ **幾何学的形(または規格的形)**
- 「霊として」→ **例として**
- 「無外に」→ **無害に**
- 「証明視点」→ **正面視点**
- 「とて気に」→ **意図的に**
- 「便を示し」→ **盃を示し**(Rubin's vase の文脈)
- 「ガ長」→ **画長**(?要再確認)
- 「お尻り」→ **お尻**
- 「Y ジ型」→ **Y 字型**

Wiki ページの引用では訂正版を採用している。

## Batch 4 ingest 後の文脈位置付け(2026-05-17 追記)

本章はシリーズの **Section 02 形態** の入り口であり、後続章との関係:

- [[coloso-nekojira-ch04-observation-abstraction]] — 本章の「形 = 視覚言語」を実装する **CSI ストローク**(C/S/I 線への自己制限)を導入。ネガティブシェイプ観察も実装
- [[coloso-nekojira-ch05-figure-practice-1]] / [[coloso-nekojira-ch06-figure-practice-2]] / [[coloso-nekojira-ch07-figure-practice-3]] — 人体練習で本章の「観察 → 抽象化」を全身に適用
- [[coloso-nekojira-ch08-shape-rhythm]] — 本章の「形 = 視覚言語」を **コントラスト 3 種(直線×曲線/大小/密度)** に拡張(Section 03 開幕)
- [[coloso-nekojira-ch09-box-proportion]] / [[coloso-nekojira-ch10-face-drawing]] / [[coloso-nekojira-ch13-head-application]] — 顔・頭部に本章の概念(○□△ × 性格)を **目の 6 セグメント分割**等で具体適用

→ 本章は **3 基本形** = 講座全体の「アルファベット」、後続章は **そのアルファベットを使った単語・文** に相当する

## 個人的な所感 / 残った問い

- Nekojira の「○ □ △ × 性格」は 예지 ([[ye-jji]]) 講座の [[silhouette-check|シルエット複雑化 6 原則]] と直接の競合ではなく **直交軸**。前者 = メッセージ設計、後者 = 視覚的興味の設計。両方ともキャラ絵の前段階で同時に走る
- [[silhouette-check]] の「三角形を活用」原則は Nekojira の「△ = energetic/offensive」と意味付けで重なる。横断的に「三角形要素 = アクセント + 攻撃性/活発さ」と覚えてよさそう
- 6 段階女性キャラバリエーションは [[takeda-yohsuke]] の作画キャラ性格設定の事前チェックに使える可能性が高い → [[nekojira-feedback-checklist]] に組み込み
- PSD レイヤーは英語名で残されている → 講師自身が「教えるための注釈」を埋め込んでいる。後続章の PSD でも同様の分類辞書を期待できる
- 残った問い: 「形を反復しすぎる」の閾値は? Nekojira は警告だけで定量化していない。次章以降の課題で「アクセントは全体の 10〜20%」のような基準が出るか要観察
