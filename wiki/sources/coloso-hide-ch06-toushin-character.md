---
type: source
title: "coloso_hide_06 頭身ごとのキャラクターの特徴と描き分け"
authors: [hide]
date: 2026-05-31
source_path: "raw/_coloso/2026_05_31_hide_01/coloso_hide_06 頭身ごとのキャラクターの特徴と描き分け.md"
ingested: 2026-06-01
tags: [Coloso, 人体ドローイング, 頭身, キャラクターデザイン]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-01
---

# coloso hide ch06 ― 頭身ごとのキャラクターの特徴と描き分け

## 要約

頭身の違いによるキャラクター比率を扱う章。アニメ作画では顔の似せ方だけでなく、頭身・プロポーションが合っているかが非常に重要だとする。8 頭身を基準に、7 頭身から 2 頭身まで、頭部の丸さ、胴体と手足の長さ、年齢感、デフォルメ感がどう変わるかを示す。

## ソース内の主要主張

- 頭身は「身長が頭何個分か」の基準で、キャラクターの年齢感・デフォルメ度・印象に直結する。
- アニメ現場では、顔の完全一致より、頭身・比率の一致がリテイク理由になりやすい。
- 頭身が下がるほど、胴体に対して手足が短くなり、頭部は丸くなる。
- 8 頭身は成人理想体型、7 頭身はアニメでよくある体型、6 頭身は幼年寄り、5 頭身以下はよりデフォルメが強い。
- 4 頭身から 2 頭身では、リアルな年齢比率よりも、キャラクターとしての可愛さやデザインを優先する。
- スーパーヒロイン、スポーツ漫画、強い敵キャラなどでは、脚長・極端な高頭身・肩幅や手の大きさの誇張が使われる。

## 抽出されたエンティティ

- [[hide-animator]] — 講師。

## 抽出された概念

- [[toushin-character-proportion]] — 頭身別のキャラクター比率。
- [[proportion-exaggeration-character-design]] — 脚長・高頭身・パワー型などの比率誇張。

## 不確実・要確認

- 年齢対応は厳密な人体統計ではなく、作画上の印象づけの説明。
## 映像観測(フレーム由来)

- 抽出日: 2026-08-25 / 元動画: [[06.mp4]] / 元動画 SHA-256: 6b70194e847b8d9363ceda45783dc17bec1b2db7ed419516c6a8f7a7cf76f28f
- 方式: 20秒間隔抽出 + 完成前の 10秒間隔全帯域スイーブ点検 / 抽出47枚・読取47枚・保存51枚(スイープで発見した未観測画面 00:10/01:10/01:30/13:10 の4枚を追加分として保存)
- 設計版: video-visual-ingest-design v2.3 / 読取モデル: opencode/x-preview-f-free (ox-alpha)(盲検読取はサブエージェント4体、第2読者5枚、不一致確定のため原寸クロップ再読を実施)
- 凡例: 画面上で確認できた事実のみ。判読できない文字は「判読不能」と記載。ev-023/ev-036 は初観測のレイヤーフォルダ番号誤りを原寸クロップで訂正済み(manifest recheck 参照)。ev-048〜ev-051 は 10秒スイープで発見した未観測画面(ffmpeg 直接抽出+盲検読取)。冒頭 00:00 は黒画面、末尾 15:20 は「Coloso.」ロゴのアウトロで知識情報なし。アプリ UI に薄い透かし状文字列が複数回出現するが読みが安定しないため「判読不能」扱い。

| evidence_id | 時刻 | frame | 確信度 | 画面の観測(事実のみ) |
|---|---|---|---|---|
| ev-001 | 00:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-00m00s.png]] | high | 全面がほぼ黒で、右上寄りに青みのある小さな点が1つあるのみ。文字・UI・人物なし。 |
| ev-002 | 00:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-00m20s.png]] | high | CLIP STUDIO PAINT EX の画面(アクティブタブ「Illustration*」)。白キャンバスにグレー塗りの立ち姿デッサン2体(左は線が薄く未完、中央は輪郭が濃い)。中央の人物の右側に赤い楕円を縦に約7つ連ねた列と、それを貫く青い縦線、水平ガイド線。キャンバス上部に赤い大きな文字「頭が何個分か？」。右パネルに鉛筆系サブツール一覧・レイヤーパネル(Folder 3 Copy 配下に複数レイヤー)、Navigator に人物サムネイル。 |
| ev-003 | 00:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-00m40s.png]] | high | 同一画面。赤文字「頭が何個分か？」が消え、人物が3体に増加(左=頭身の高い成人男性型、中央=体格の中間で胸の膨らみと腰の線のある女性型、右=頭身の低い子供型)。各人物の右側に赤い楕円の縦列(左約8個・中央約7個・右約6個)と水平ガイド線。Navigator も3体分、レイヤー数も増加(Folder 3 Copy 配下 Layer 9 Copy〜Layer 12 Copy 等)。右部に薄い透かし状文字列(判読不能)。 |
| ev-004 | 01:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-01m00s.png]] | high | 白地のテキストスライドに切替。黒い明朝体で「ここまで人物画の基礎知識として」、その下に「①人体のパーツ分けと比率」「②男性と女性の描き方の特徴」の2行。レイヤーパネルは Folder 3 Copy/Folder 2 Copy/Folder 1 Copy/Layer 1/Paper。Navigator はほぼ白。 |
| ev-005 | 01:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-01m20s.png]] | high | 図解画面に切替。グレー塗りの成人男性立位デッサン1体(中央やや左)+その右側に赤い楕円の縦列(約8個)、青い縦線、水平ガイド線。文字なし。レイヤー Folder 1 Copy 選択。Navigator に男性1体。 |
| ev-006 | 01:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-01m40s.png]] | high | テキストスライド「このchapterで学べること」。箇条書き「・7〜2頭身の描き方」「・各頭身の比率について」。レイヤー構成は 01m20s と同じく Folder 1 Copy 選択。 |
| ev-007 | 02:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-02m00s.png]] | high | 新規ドキュメント「Illustration2*」(A4 7016x4961px 600dpi 36.6%)に切替。ほぼ白紙のキャンバスに薄い鉛筆線(上部の横線、中央を縦に通る長い中心線、途中の短い横線2本、下部の横線)。レイヤー Layer 1 選択。Navigator はほぼ白。 |
| ev-008 | 02:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-02m20s.png]] | high | 同一画面。描画進行: 上部に頭の楕円と十字ガイド、顎・胸・腰・腰下あたりの横ガイド線が増加、肩から脇への曲線、腰の位置にV字の線(股ガイド)。レイヤー Layer 2 追加選択。Navigator に薄い縦線。 |
| ev-009 | 02:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-02m40s.png]] | high | 同一画面。進行: 胸郭を示す左右の曲線、肩の丸(円)、腰の左右に股関節の小さな円、膝位置の円、足首から先の足の形、腰の横線。頭の楕円+十字ガイドは維持。Layer 2 のまま。 |
| ev-010 | 03:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-03m00s.png]] | high | 同一画面。進行: 两腕と体側に下げた手が追加され、全身の立ちポーズ骨格スケッチが出揃う。Layer 2 のまま。 |
| ev-011 | 03:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-03m20s.png]] | high | 同一画面。清書進行: 輪郭が整い、胸・腰・股・膝に青い水平ガイド線、顔に十字ガイド、手の形が明確化。レイヤー Layer 3 追加選択(Layer 2/Layer 1 は下)。Navigator に薄い全身像。 |
| ev-012 | 03:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-03m40s.png]] | high | 同一画面。脚部の清書が進行(太もも〜膝〜すねの輪郭、膝の形、つま先の分かる足)。青い水平ガイド線は残存。Layer 3 のまま。 |
| ev-013 | 04:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-04m00s.png]] | high | キャンバスに2体並び: 左=完成したグレー塗りの男性立位(胸・腰・股・脚に青ガイド残る)、右=次の作画開始直後(頭の楕円+青い縦中心線、体側に等間隔の短い横線のみ)。レイヤー Folder 2 内 Layer 6 選択(Layer 5/Folder 1/Paper)。ブラシサブツール一覧末尾に「デジタル鉛筆」。Navigator に左の灰色人物+右上に小さな円。 |
| ev-014 | 04:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-04m20s.png]] | high | 同一画面。右の線画が進行(頭部楕円+十字ガイド、胴体・腰あたりまでの輪郭、青い中心線と水平ガイド線。脚は線なし)。右パネル: 鉛筆サブツール一覧(リアル鉛筆/粗い鉛筆(選択)/濃い鉛筆/アニメ原画鉛筆/アニメ原画鉛筆R/鉛筆 ほか)、Brush size パレット(40 が選択色)、ツールプロパティ(Brush Size 40.0/Brush density 100/Texture 大理石/Texture density 20/Stabilization 20/Adjust by speed チェック)。Layer 6 選択のまま。 |
| ev-015 | 04:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-04m40s.png]] | high | 同一画面。右の線画が進行し肩から両腕(手の輪郭まで)、腰から両脚・膝(膝の丸ガイド)・足元まで描き込まれた。キャンバス上部に薄い透かし状文字列(判読不能)。左の参照デッサン・パネル類に変化なし。 |
| ev-016 | 05:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-05m00s.png]] | high | 同一画面。透かし文字は消滅。レイヤーに新規 Layer 7 追加選択(Folder 2 内 Layer 7/Layer 6/Layer 5)。右の線画は全身の輪郭が整理され細身のプロポーションに描き直されている(脚の外側ラインなど追加)。 |
| ev-017 | 05:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-05m20s.png]] | high | 同一画面から大きな変化: 右側の人体は全身輪郭が完成し、左と同様にグレーで塗り分けられた状態。ツールが[選択範囲]グループの「隙間閉鎖(囲って塗り)ツール」に切替(矩形選択/長方形選択/楕円選択/投げなわ選択/折れ線選択/選択ブラシ ほかが並ぶ)。ツールプロパティ: Target color「All enclosed ar…」、「Close gap」チェック、Area scaling 0、Refer multiple、Stabilization 5。レイヤー Folder 2/Folder 1 折りたたみ Folder 2 選択。カーソルは右足元付近。Navigator に男女2体。 |
| ev-018 | 05:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-05m40s.png]] | high | 同一画面。右側(女性型)の人体に変形用バウンディングボックス(四隅・辺中央ハンドル)が表示され拡大縮小操作中。キャンバス下部に「OK」「Cancel」。右パネルに「Editing Transformation settings」(Mode Scale/Rotate、Reference point Center、Scale ratio W 99/H 99、Keep aspect ratio チェック、Rotation angle 0.0、Adjust position Free position、Interpolation method Hard edge)。サブツールは鉛筆「粗い鉛筆」に戻る。レイヤー新規 Folder 3 内 Layer 9 選択(Folder 2、Folder 1 表示)。キャンバス左下に薄い透かし状文字列(判読不能)。 |
| ev-019 | 06:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-06m00s.png]] | high | 変形ボックスと OK/Cancel 消滅。キャンバス右側に3体目の作画開始(頭部の楕円+十字ガイド、胴体・腰あたりの輪郭、中心線と水平ガイド線。脚はガイド線のみ)。レイヤー Folder 3 内 Layer 10(選択)/Layer 9。Navigator に2体+右端薄い3体目。 |
| ev-020 | 06:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-06m20s.png]] | high | 同一画面。3体目(頭が大きく低身長の子供風プロポーション)の線画進行(両腕・両脚・足の輪郭と膝のガイド)。腰の高さに赤い水平線、左右股関節位置・肩付近に赤い円ガイド。レイヤー変化なし(Layer 10 選択)。左2体変化なし。 |
| ev-021 | 06:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-06m40s.png]] | high | 同一画面。3体目の赤いガイド線は消え、全身の線画(頭・胴・腕・脚)が薄い鉛筆線で整えられる。レイヤー新規 Layer 11 追加選択(Folder 3 内)。キャンバス中央に薄い透かし状文字列(判読不能)。 |
| ev-022 | 07:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-07m00s.png]] | high | 同一画面。3体目(子供)は全身の線画が完成し左2体と同じグレー塗りに。ツールが[消しゴム]の「さくら」に切替(硬め/柔らかめ/練り消しゴム/ベクター用/スタンプ消しゴム等)。ツールプロパティ: Brush Size 30.0/Opacity 100/Anti-erasing(ドット選択)/Vector eraser/Stabilization 4。レイヤー Folder 3/2/1 折りたたみ Folder 3 選択。カーソルは子供の足元付近。Navigator に3体。 |
| ev-023 | 07:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-07m20s.png]] | high | 同一画面。左端の男性の頭部に赤い長方形枠(半透明の赤塗り)と赤文字注釈「横幅0.7」「縦1」が重ねて表示。キャンバス右側に4体目の作画開始(楕円の頭部+十字ガイド、下へ伸びる縦線1本のみ)。レイヤー Folder 4 展開内に Layer 14(選択)/Layer 13、下位に折りたたみ Folder 3/Folder 2/Folder 1/Paper(初観測の「Folder 3 内」は原寸クロップ再読で Folder 4 に訂正)。サブツール鉛筆「粗い鉛筆」選択。Navigator は3体+右端の描きかけ。 |
| ev-024 | 07:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-07m40s.png]] | high | 同一画面。男性頭部の赤い枠と「横幅0.7」「縦1」注釈は消滅。4体目は頭部楕円+十字ガイド、中心線、あご下付近の短い水平線の状態(胴体未作画)。キャンバス左下に薄い透かし状文字列(判読不能)。パネル類は 07m20s と同じ。 |
| ev-025 | 08:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-08m00s.png]] | high | 同一画面。4体目の線画進行(肩の丸ガイド、胴体・腰 V字ライン、両腕・両脚(膝丸ガイド)・足元まで。頭部は楕円のまま)。透かし消滅。Navigator 右端に4体目。Layer 14 選択のまま。 |
| ev-026 | 08:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-08m20s.png]] | high | 同一画面。4体目は薄い青みのある下描き風の線でほぼ描き上がり(頭十字ガイド・胴・両腕・両脚・足)。レイヤー新規 Layer 15 追加選択(Folder 4 内)。キャンバス右中央に薄い透かし状文字列(判読不能)。Navigator 右端に4体目。 |
| ev-027 | 08:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-08m40s.png]] | high | 同一画面。キャンバスに4体の全身像が並ぶ(高身長成人男性→女性→青年→子供体格の順に低くなる)。各体はグレー塗り+黒の輪郭線、顔に十字ガイド、体中央に青い中心線と横ガイド線。4体目(最右端)は頭の輪郭のみで体は未描画。サブツール[選択範囲]の一覧で「投げなわ選択」がハイライト。レイヤー Layer 15 選択中(上位にフォルダ、下位 Layer 14/Layer 13/Folder 3〜Folder 1/Paper)。1体目の胸元に薄い透かし状文字列(判読不能)。 |
| ev-028 | 09:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-09m00s.png]] | high | 同一画面。4体目(幼児型)に頭・胴・四肢の輪郭が描き進められ、グレー塗りまで完了(4体とも同仕上がり)。サブツール[鉛筆]系に切替。ツールプロパティ Brush Size 40.0/density 100/Texture 大理石/Stabilization 20。レイヤー Layer 17 選択(上位に Folder 4 が見える)。 |
| ev-029 | 09:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-09m20s.png]] | high | 同一画面。キャンバス右端に5体目の作画開始(楕円の頭の輪郭と垂直ガイド線1本)。レイヤー新規 Layer 18 追加選択(Layer 17 の上)。キャンバス下部に薄い透かし状文字列(判読不能)。 |
| ev-030 | 09:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-09m40s.png]] | high | 同一画面。5体目に頭・胴・腕・脚の輪郭線が描き進められる(グレー塗りなし、線画のみ)。Layer 18 のまま。画面上部中央に薄い透かし状文字列(判読不能)。 |
| ev-031 | 10:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-10m00s.png]] | high | 同一画面。5体目の全身輪郭(頭・胴・腕・脚・足)がほぼ描き終わる(まだ塗りなし)。レイヤー新規 Layer 19 追加選択。Navigator に5体反映。 |
| ev-032 | 10:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-10m20s.png]] | high | 同一画面。5体目にグレー塗り完了、腕をやや開いた姿勢(他4体と同様の仕上がり)。レイヤー Layer 21 選択、Folder 5 新たに見える(Layer 21/Folder 5/Folder 4〜Folder 1/Paper)。中央左寄りに薄い透かし状文字列(判読不能)。 |
| ev-033 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-10m40s.png]] | high | 同一画面。キャンバス右端に6体目の作画開始(楕円の頭の輪郭と垂直ガイド線、足元に短い横線)。レイヤー新規 Layer 22 追加選択。サブツール一覧最下部に「デッサン鉛筆」が見える。右端に薄い透かし状文字列(判読不能)。 |
| ev-034 | 11:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-11m00s.png]] | high | 同一画面。6体目に頭+丸みのある胴体・腕・脚の輪郭が描き進められる(線画のみ)。Layer 22 のまま。 |
| ev-035 | 11:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-11m20s.png]] | high | 同一画面。6体目に頭部の十字ガイド・青い中心線・四肢の輪郭が加わり細部進行。レイヤー新規 Layer 23 追加選択(Layer 23/Layer 22/Layer 21/Folder 5〜Folder 1/Paper)。下部右寄りに薄い透かし状文字列(判読不能)。 |
| ev-036 | 11:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-11m40s.png]] | high | 画面が大きく変化。グレー塗り済み6体が背の高い順に整列し直される(成人男性→女性→少年型→子供型→子供型→幼児型、体格差が段階的)。7体目の作画開始(乳児型の右側に楕円の頭の輪郭と垂直ガイド線、足元に横線。輪郭未描画)。レイヤー Folder 7 展開内に Layer 25(選択)、下位に折りたたみ Folder 6〜Folder 1/Paper(初観測の「Folder 6 が新たに見える」は原寸クロップ再読で Folder 7 に訂正)。右部に薄い透かし状文字列(判読不能)。Navigator に6体が階段状に表示。 |
| ev-037 | 12:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-12m00s.png]] | high | 同一画面。7体目(最右端・最小の乳児型)にもグレー塗り完了し、7体全体が左から身長の高い順に整列。ツール[選択範囲]の「隙間閉鎖<囲って塗る>ツール」がハイライト(ツールプロパティ: Target color「All enclosed area」/Close gap チェック/Area scaling 0/Refer multiple/Stabilization 5)。レイヤー Layer 27(合成モード Multiply)選択、上位に Layer 28(Normal)、下位に Layer 25/Folder 6〜Folder 1/Paper。Navigator に7体反映。 |
| ev-038 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-12m20s.png]] | high | 新しい白紙キャンバスに切替(タイトルバーは「Illustration2*」表記のまま)。キャンバスはほぼ白紙で上部に鉛筆の曲線ストローク1本のみ。レイヤー Layer 1/Paper のみ(Layer 1 選択)。ツールは鉛筆系に戻りツールプロパティ Brush Size 40.0/density 100/Texture 大理石/Stabilization 20。Navigator 白紙。中央右寄りに薄い透かし状文字列(判読不能)。(ffmpeg シーン変化検出で 12m13s に切替点を検出) |
| ev-039 | 12:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-12m40s.png]] | high | 同一画面。立ち姿の人体構図が作画中: 楕円の頭+顔の十字ガイド、胴体の輪郭とS字状の中心線、腰・股のガイド線、青の垂直中心線と複数の横ガイド線、左脚の長い輪郭線が足先まで伸びる(線画のみ、塗りなし)。レイヤー Layer 2 選択(Layer 1/Paper 下位)。Navigator に薄い人物シルエット。左上寄りに薄い透かし状文字列(判読不能)。 |
| ev-040 | 13:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-13m00s.png]] | high | 同一ドキュメント。キャンバス中央に正面立ち姿の女性の全身ラフ線画(頭部の楕円、胴体・胸・腰のラフ線、腕は体のそば、裾が広がるロング状の輪郭を太いグレーの線で描写。体の中心に青い縦中心線)。レイヤー上から Layer 3(Multiply、選択中)/Layer 2/Layer 1/Paper。ツールはペン系(Gペン等の一覧)、ツールプロパティ Brush Size 125.0/Opacity 100/Stabilization 20/Adjust by speed チェック。Navigator に立ち姿サムネイル。画面下部にカラーセット・カラーサークル、アニメーションセル関連パネル(数値 50)。 |
| ev-041 | 13:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-13m20s.png]] | high | タイトルバー・倍率は同一だがキャンバス内容が別の絵に変化: 左側に長いツインテールの少女の全身シルエット(グレー塗り、髪の房が両側に大きく弧を描いて垂れ先端がゆるく巻かれる)、キャンバス右寄りに細い鉛筆の縦線1本(描きかけ)。レイヤー Folder 2 内 Layer 4 選択、Folder 1/Paper。サブツール鉛筆系「粗い鉛筆」選択(Brush Size 40.0/density 100/Texture 大理石/Stabilization 20)。Navigator にシルエットのサムネイル。 |
| ev-042 | 13:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-13m40s.png]] | high | 同一画面。右側の縦線の位置に立ち姿の人物ラフが進行(頭部の楕円、肩から腕の丸、胴体、腰、脚(膝の丸)、足先まで細い鉛筆線と青い中心線で骨組み。シルエットと同じく脚を開いた立ちポーズ)。レイヤー Folder 2 内新規 Layer 5 追加選択(Layer 4 下位)。Navigator にシルエットと右側のラフ。 |
| ev-043 | 14:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-14m00s.png]] | high | 同一画面。右の人物ラフがさらに進行(頭部に髪の毛の描写、首・肩、胴体に服の開きを示すV字の線、両腕は体側に垂らして手まで、脚は開いて足先まで。輪郭に太めの濃いグレー線)。ツールはペンの「Gペン」(Brush Size 150.0/Opacity 100/Stabilization 20)。レイヤー Folder 2 内 Layer 6(Multiply、選択)/Layer 5/Layer 4、下位 Folder 1/Paper。 |
| ev-044 | 14:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-14m20s.png]] | high | キャンバス左側のシルエットが2体に(既存ツインテール少女+右に短髪男性の全身シルエット(Tシャツとズボン、腕を体側に垂らした正面立ち、グレー塗り)追加)。右側では新しい下描き開始(角の丸い頭部の形+下へ細い縦線、途中に等間隔の薄い青みのある横ガイド線が数本交差)。ツールは鉛筆「粗い鉛筆」。レイヤー新規 Folder 3 作成、内に Layer 8(選択)/Layer 7、下位 Folder 2/Folder 1/Paper。Navigator に2体のシルエット+右端の頭部スケッチ。右下に極薄のグレー文字列(判読不能)。 |
| ev-045 | 14:40 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-14m40s.png]] | high | 同一画面構成。右の新しい下描きが大きく進行(角丸頭部から太いグレーの線で幅広い肩と両腕(手まで)、胴に大きな楕円形の胸の形、腰から箱形の下半身と脚、足先まで。肩回り・腕一部は濃い太線、胴内部は細い鉛筆線と青い中心線)。ツール Gペン(Brush Size 150.0/Opacity 100/Stabilization 20)。レイヤー Folder 3 内 Layer 9(Multiply、選択)/Layer 8/Layer 7。Navigator に2体シルエット+右端に大きな人物の輪郭。 |
| ev-046 | 15:00 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-15m00s.png]] | high | タイトルバーとドキュメントタブが「例外*」に変化(A4 7016x4961px 600dpi 36.6%)。キャンバスにグレー塗りシルエット3体: ①ツインテールの少女(細腰・広腰、長いドリル状のツインテールが左右に大きく弧を描く)②短い跳ねた髪の細身男性③極端に筋肉質な巨体型(頭が小さく肩と腕が非常に幅広、拳が大きく脚も太い)。3体は同程度の高さ〜右がやや高く、右の体幅は他2体より格段に広い。レイヤー Folder 3(選択・展開)内 Layer 9(Multiply)/Layer 8/Layer 7、下位 Folder 2/Folder 1/Paper。サブツール[鉛筆]一覧で「シャーペン」行が青反転(一覧: リアル鉛筆/粗い鉛筆/濃い鉛筆/アニメ原画鉛筆/アニメ原画鉛筆R/鉛筆/シャーペン/デザイン鉛筆、原寸クロップで確定)。Navigator に3体のシルエット。 |
| ev-047 | 15:20 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-15m20s.png]] | high | CLIP STUDIO 画面から切替、全面黒。中央に白いロゴ文字「Coloso.」(ピリオドはオレンジ色)。最下部に細い赤い横線、右上隅にごく小さな青紫色の点1つ。その他要素なし。 |
| ev-048 | 00:10 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-00m10s.png]] | high | 白地キャンバス中央やや左に黒い大きな日本語テキスト2行「人物画の基礎知識その③」「頭身ごとのキャラクターの特徴と描き分け」。人物・ガイド線なし。タイトルバー「Illustration*」(A4 7016x4961px 600dpi 36.6%)、Sub Tool[テキスト]の「テキスト」サブツール選択、Tool property[テキスト](Font「07あおずもポップ」風表記(末尾判読不能)/Size 34.3/Edge 10 ほか)、レイヤー Folder 3 Copy 選択。(10秒スイープで発見・補完) |
| ev-049 | 01:10 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-01m10s.png]] | high | 図解画面。グレー塗り成人男性立位1体+右側に赤い楕円の縦列(約7〜8個、重なりがあり正確な数は数えにくい)、中心に青い縦線、最上部と最下部に薄い横ガイド線。楕円列の右に大きな黒文字「理想の体型」。脚部付近に極めて薄い透かし状文字列(判読不能)。レイヤー Folder 1 Copy 選択(展開)。(10秒スイープで発見・補完) |
| ev-050 | 01:30 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-01m30s.png]] | high | 図解画面。グレー塗りマネキン2体(左=背の高い男性型、右=明らかに背の低い女性型、体格差が明瞭)+右側に赤い楕円の縦1列(約8個、重なり合い正確な数は数えにくい)、中心に青い縦線、上下に薄い横ガイド線。キャンバス上のテキスト文字はこの時点で見当たらない。レイヤー Folder 2 Copy 選択。右端に Animation cells パネル(数値 50)。(10秒スイープで発見・補完) |
| ev-051 | 13:10 | ![[wiki/assets/frames/coloso-hide-ch06-toushin-character/hide-ch06-13m10s.png]] | high | タブ「Illustration2*」のまま、キャンバスはツインテール少女のシルエット1体のみのグレー塗り表示(両側に長いツインテール(お団子+垂れた長髪)、直立・やや開脚。輪郭線・ガイド線は非表示)。レイヤーはフラット構造 Layer 3(Multiply、選択中)/Layer 2/Layer 1/Paper、ツールはペン系「Gペン」選択。(10秒スイープで発見・補完) |

## 関連リンク

- [[coloso-hide-ch04-body-basics]]
- [[coloso-hide-ch05-male-female-proportion]]
- [[coloso-hide-human-drawing-course]]

