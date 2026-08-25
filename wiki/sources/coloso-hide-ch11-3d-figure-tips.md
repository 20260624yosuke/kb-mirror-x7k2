---
type: source
title: "coloso_hide_11 人物を立体的に描くためのコツ"
authors: [hide]
date: 2026-05-31
source_path: raw/_coloso/2026_05_31_hide_01/coloso_hide_11 人物を立体的に描くためのコツ.md
ingested: 2026-06-01
tags: [coloso, 人体ドローイング, 立体, パース, hide]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-01
visual_ingested: 2026-08-25
---

# coloso hide ch11 ― 人物を立体的に描くためのコツ

> Section 05「立体を意識した人物の描き方」の中核章。元 md は Whisper 逐語。本要約では明らかな誤変換を補正している(例: 両線→稜線、胸角→胸郭、気化形態→幾何形態)。

## 要約

人物に立体感を出すための 4 つのポイントとして、**面の意識 / ターニングエッジ**、**ラッピングライン**、**短縮法**、**オーバーラップとタンジェント** を扱う。共通する狙いは、人体をシルエットや輪郭だけで捉えず、箱・円柱・球体などの単純形に置き換え、面の向き・方向性・前後関係を線で読み取れるようにすること。ch12 以降の「人物を単純な形に落とし込む」実践への橋渡しになっている。

## ソース内の主要主張

- **ターニングエッジ / 稜線** は面と面の境目。四角い箱だけでなく、顔・首・胸郭・腰・手足などでも、前面と側面の切り替わりを探す。
- 丸い人体でも、円柱を箱に置き換えて考えると稜線を見つけやすい。円柱では明暗グラデーションの中央付近が稜線として扱える。
- **手** は輪郭だけをなぞると平面的になりやすく、手のひらを板、指の付け根を球体、指をやや四角い円柱として面分けすると立体感が出る。
- **ラッピングライン** は物体を包み込む線。シルエットに立体感と方向性を与え、同じシルエットでも線の回り込みで向きが反転する。
- ラッピングラインはアイレベルから離れるほどカーブが強くなる。服の袖・模様・シワもラッピングラインとして扱える。
- **短縮法** は画面に対して斜めに置かれたものを見た目上短く描くことで奥行きを示す方法。カメラに向かうほど円柱の見た目の長さは短くなり、楕円面は円に近づく。
- 人体では腕や剣などに短縮法が出やすい。先に短縮された見た目の長さを決め、その中に円柱を当てはめると間延びしにくい。
- **オーバーラップ** は T 字の重なりで前後関係を示す。**タンジェント** は輪郭が接して X 字的につながる状態で、奥行きが曖昧になりやすい。
- 同じシルエットでも、首・胸・腕・股・膝・足首などのオーバーラップ線の入れ方で、前向き / 後ろ向きや手前 / 奥の印象を操作できる。

## 抽出されたエンティティ

- [[hide-animator]] — 講師

## 抽出された概念

- [[turning-edge-plane-awareness]] — 面の意識 / ターニングエッジ
- [[wrapping-line]] — ラッピングライン
- [[foreshortening-drawing]] — 短縮法
- [[overlap-and-tangent]] — オーバーラップとタンジェント
- [[keitai-ryoku]] — 面の向きと立体理解の既存概念
- [[perspective-eye-level-method]] — アイレベルを簡略的に使う既存概念

## 不確実・要確認

- 「ターニングエッジ」と「稜線」は本章ではほぼ同義に扱われている。ただしデッサン一般では、物理的な角と、丸い形の面転換線を分けて扱う場合がある。
- 「タンジェント」は本章では奥行きを失わせる接線事故として扱われる。デザイン上あえて使う可能性までは本章では扱わない。

## 映像観測(フレーム由来)

- 抽出日: 2026-08-25 / 元動画: [[_attachments/11_01.mp4]] + [[_attachments/11_02.mp4]](分割 2 本)
- 元動画 SHA-256: `c1a9072b2febb2ecc27eb8a55f85cbadad3666df4f52416f442bc1976ca7456d`(11_01) / `0ac9865d18d7990fe2d2b4ef25d95a1733761e00506dcb672529f56f7d0593ac`(11_02)
- 方式: 20秒間隔抽出 + 完成前の 10秒間隔全帯域スイーブ点検 / 抽出47+27枚・読取74枚・保存79枚(スイープで発見した未観測画面 11_01 00:10/00:30/13:10・11_02 00:10/02:10 の5枚を追加分として保存)
- 設計版: video-visual-ingest-design v2.3(分割動画対応・動画列付き 6 列表) / 読取モデル: opencode/x-preview-f-free (ox-alpha)(盲検読取はサブエージェント分割回し、第2読者8枚=max(3,10%切り上げ)、不一致確定のため原寸クロップ再読を実施)
- 凡例: 画面上で確認できた事実のみ。判読できない文字は「判読不能」と記載。時刻は各動画内の時刻。ev-023 は初観測の選択レイヤー番号誤り(Layer 4→Layer 3)を原寸クロップで訂正済み(manifest recheck 参照)。両パートとも冒頭 00:00 は黒画面。11_01 は CSP 実演タイムラプスで描き込みが連続的に進むため、隣接フレームとの差分は「同一画面。変化点: 」の形で書く。アプリ UI に薄い透かし状文字列が繰り返し出現するが読みが安定しないため「判読不能」扱い。写真素材は JookpubStock 等のフリー素材で、画面内のクレジット帯表記は原文どおり写す。

| evidence_id | 動画 | 時刻 | frame | 確信度 | 画面の観測(事実のみ) |
|---|---|---|---|---|---|
| ev-001 | 11_01.mp4 | 0:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-00m00s.png]] | high | 全面がほぼ黒で、右上寄りに淡い青白の点が1つあるのみ。文字・UI・人物なし。 |
| ev-002 | 11_01.mp4 | 0:10 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-00m10s.png]] | high | 節タイトルカード。白キャンバス中央に黒の明朝風文字2行「第11講」「人物を立体的に描くためのコツ」。消しゴムツール選択(サブツール「さらい」・Brush Size 80.0)。レイヤー Layer(選択中)/Layer 3/Layer 2/Layer 1/Paper。10秒スイープで発見した未観測画面。 |
| ev-003 | 11_01.mp4 | 0:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-00m20s.png]] | high | スライド「このchapterで学べること」。箇条書き4項目「面の意識(ターニングエッジ)について」「ラッピングライン」「短縮法」「オーバーラップとタンジェント」。右パネルは消しゴム系サブツール一覧(一部判読不能)、レイヤー Layer 4 選択。 |
| ev-004 | 11_01.mp4 | 0:30 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-00m30s.png]] | high | スライド「面の意識(ターニングエッジ)について」がキャンバス中央に大きく黒文字表示。テキストツール選択(サブツール「テキスト」・Size 34.3・フォント名判読不能)。レイヤー Layer 1/Paper のみ。10秒スイープで発見した未観測画面。 |
| ev-005 | 11_01.mp4 | 0:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-00m40s.png]] | high | スライド「ターニングエッジとは?」+下向き矢印+「面と面の境目の線」。塗りつぶし系サブツール一覧とツールプロパティ「他レイヤーを参照」(Tolerance 0.0・Area scaling 2)。Layer 2 選択。 |
| ev-006 | 11_01.mp4 | 1:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-01m00s.png]] | high | キャンバス中央に角の丸い灰色立方体1つ(陰影付き・線なし)。ペン系サブツール(Gペン選択・Brush Size 125.0)。Layer 3 選択。 |
| ev-007 | 11_01.mp4 | 1:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-01m20s.png]] | medium | 同立方体の上稜と正面の縦稜に赤い線が描き込まれ、上に赤い手書き文字「ターニングエッジ(陵線)」(括弧内2文字は判読に若干の不確かさ)。鉛筆系「粗い鉛筆」Brush Size 40.0。Layer 4 選択。 |
| ev-008 | 11_01.mp4 | 1:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-01m40s.png]] | high | 画面構成が変化。左に赤注記付き立方体(縮小表示)、右に新たに線画の顔(禿頭の輪郭・目・眉・笑った口・耳)。消しゴム系サブツール Brush Size 35.0。Folder 2 内 Layer 5 選択。 |
| ev-009 | 11_01.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-02m00s.png]] | high | 顔の右半分にグレーの陰影が塗られ、陰影と地の境目(額〜目元・頬〜顎)に赤い線。左下に線画のみの斜め円柱が追加。鉛筆「粗い鉛筆」Brush Size 40.0。Folder 3 内 Layer 7 選択。 |
| ev-010 | 11_01.mp4 | 2:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-02m20s.png]] | high | 円柱に陰影と上稜の赤線、右に矢印と長い直方体(グレー塗り+上稜・正面稜に赤線)。円柱の下に手書きの「?」。塗りつぶし系サブツール(Tolerance 0.0)。Folder 3 内 Layer 8 選択。 |
| ev-011 | 11_01.mp4 | 2:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-02m40s.png]] | high | キャンバス右側に立ち姿の女性全身線画(未着色・直立)が追加。既存の立方体・顔・円柱・直方体はそのまま。鉛筆「粗い鉛筆」Brush Size 40.0。Folder 4 内 Layer 9 選択。 |
| ev-012 | 11_01.mp4 | 3:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-03m00s.png]] | high | 全身線画に赤線追加: 頭部中央を縦に通る線と、胴体(脇腹〜腰〜太もも)に沿う線。Folder 4 内 Layer 10 選択。描画色が赤。 |
| ev-013 | 11_01.mp4 | 3:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-03m20s.png]] | high | 全身図の右腕・体側・脚の一部にグレーの陰影が付き、輪郭の一部(右腕の外側・肩〜腕・脚のライン)が赤線でなぞられる。Gペン Brush Size 125.0。Folder 4 内 Layer 11 選択。 |
| ev-014 | 11_01.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-03m40s.png]] | high | 陰影が全身に広がり、赤線は腕・脚など所々に残る。キャンバス下部中央に薄い透かし風文字(判読不能)。消しゴム系サブツール Brush Size 45.0。Layer 11 選択のまま。 |
| ev-015 | 11_01.mp4 | 4:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-04m00s.png]] | high | 別ドキュメント「FANDOMS_KINGDOMHEARTS_023-copy.jpg」(1519 x 2125px・表示170.0%)。白のスポーツブラとショーツの女性が黄色い鍵型プロップを両手で構える写真の上に、赤いラフ描き込み2箇所(顔の横顔ラインに沿う赤線・頭の右上に赤い立方体スケッチ)。Layer 2 選択/Layer 1(48%)/写真レイヤー。粗い鉛筆 Brush Size 40.0。 |
| ev-016 | 11_01.mp4 | 4:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-04m20s.png]] | high | 同写真画面。赤ラフが増加: 髪の輪郭・顔の中央線・肩から腕・鍵の柄に沿う線。頭の右上に赤い箱形スケッチ2つと格子入り円筒スケッチ1つ。腕部分に薄い透かし文字(判読不能)。粗い鉛筆 Brush Size 40.0・Texture 大理石。 |
| ev-017 | 11_01.mp4 | 4:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-04m40s.png]] | high | 同一画面(170.0%)。表示位置がやや右へパンされ、赤い箱スケッチは右端に一部のみ。赤ラフの状態は 04:20 と同様。UI・レイヤー構成も同一。 |
| ev-018 | 11_01.mp4 | 5:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-05m00s.png]] | high | 表示倍率が 100.0% になり全身が見える。赤ラフが増加: 体側面の縦線・両脚の輪郭線。画面右側に赤いスケッチ群(格子模様入り兜状の形・箱形・脚の形らしき輪郭)。画像最下部に帯状クレジット「FREE TO USE AS REFERENCE FOR PERSONAL AND COMMERCIAL WORK」「JENNIFER GÜNTHEL JOOKPUBSTOCK.COM」。 |
| ev-019 | 11_01.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-05m20s.png]] | high | 同一画面(100.0%・全身)。赤い構図スケッチがさらに増加: 左脚の左側に縦長円筒・箱形が複数、右脚の右側に楕円口の円筒、左足元に箱形、脚自体にも赤線追加。クレジット帯は同じ。 |
| ev-020 | 11_01.mp4 | 5:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-05m40s.png]] | high | 別タブ「Every-Day-Is-Hand-Day-05-scaled.jpg」(2560 x 1681px・96dpi・100.0%)がアクティブ。黒背景の写真素材: 手前に向かって指を曲げた手のクローズアップ。描き込みなし。最下部に同じクレジット帯。レイヤーは写真レイヤーのみ。粗い鉛筆 Brush Size 40.0。 |
| ev-021 | 11_01.mp4 | 6:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-06m00s.png]] | high | 同手のファイル。写真が明るく飛んだ状態(背景薄グレー・手は白っぽい)になり、黒の線画が追加: 指全体・手・手首の輪郭線。描画色が黒。レイヤー Layer 2(選択中)/Layer 1(64%)/写真。クレジット帯は薄グレーで残る。 |
| ev-022 | 11_01.mp4 | 6:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-06m20s.png]] | high | 同ファイル。写真レイヤーの表示がオフになり白背景+黒線画のみ。指の付け根〜手の甲に灰色の円形・帯状シェイプが重なる。レイヤー5枚(Layer 2/Layer 4 選択中/Layer 3/Layer 1(70%)/写真非表示)。 |
| ev-023 | 11_01.mp4 | 6:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-06m40s.png]] | high | 同ファイル。写真レイヤーが再び表示(明るい状態)されクレジット帯も復活。黒線画に加え、指を箱形に区分する直線(関節位置の区切り線)と灰色のシェイプが追加され、指が立体のブロック状に見える構図。選択レイヤーは Layer 3(初回読取「Layer 4」を原寸クロップで訂正)。 |
| ev-024 | 11_01.mp4 | 7:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-07m00s.png]] | high | 同ファイル。写真は再び非表示(白背景+線画)。サブツール[塗りつぶし]「他レイヤーを参照」選択(Tolerance 0.00・Area scaling 2・Refer multiple)。線画の一部セグメント(人差し指の先端など)が灰色で塗りつぶされ始める。Layer 4 選択。 |
| ev-025 | 11_01.mp4 | 7:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-07m20s.png]] | high | 同一画面。灰色の塗りが拡大: 複数の指セグメント・掌側の面・手首〜前腕に渡る長い帯状の領域が灰色で塗られる。 |
| ev-026 | 11_01.mp4 | 7:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-07m40s.png]] | high | 別ドキュメント「Illustration*」(A4 7016 x 4961px・600dpi・36.5%)。白キャンバスに黒文字のスライド「ラッピングラインとは?」+下向き矢印+「モノを包み込むように描く線」。レイヤー Folder 6/Layer 13(選択中)/フォルダー1つ/Paper。鉛筆 Brush Size 35.0。 |
| ev-027 | 11_01.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-08m00s.png]] | high | 同ドキュメント。文字は消え、白キャンバス左側に灰色のカプセル型(斜め円柱状)が2つ(右上向きと右下向きに1個ずつ)。レイヤーに「Layer 13 Copy」が追加され選択中。Navigator にも2つのカプセル。 |
| ev-028 | 11_01.mp4 | 8:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-08m20s.png]] | high | 同一画面。2つのカプセル型に、円柱を横に区切る湾曲した線が複数本描き込まれ円筒のセグメント状に。画面上部中央に薄い透かし文字(判読不能)。UI・レイヤー構成は同一。 |
| ev-029 | 11_01.mp4 | 8:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-08m40s.png]] | high | 同ドキュメント。白キャンバスに4つの練習図形: 左上に傾いた円柱(グレー塗り+鉛筆の輪郭線+表面に横帯線)、左下にもう1本の傾いた円柱(同様)、中央上にS字カーブのチューブ(鉛筆輪郭+上側に帯線・塗り白)、中央下にT字チューブ(鉛筆輪郭のみ未塗り)。粗い鉛筆 Brush Size 35.0。Folder 6 内 Layer 13 Copy 選択。 |
| ev-030 | 11_01.mp4 | 9:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-09m00s.png]] | high | 同一画面(図形構成は同じ)。S字チューブとT字チューブがグレー塗りになり両面に帯線。サブツール[ペン]「Gペン」選択 Brush Size 35.0。Folder 6 内に新規「Layer 14」追加・選択。 |
| ev-031 | 11_01.mp4 | 9:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-09m20s.png]] | high | 同一画面(円柱×2・S字・T字)。右側に前腕+手(指を開いた手)のシルエットが新規追加されグレー塗り・帯線なし。サブツール[塗りつぶし](「編集レイヤーのみ参照」・Tolerance 5.0・Area scaling 2)。Folder 6 内に新規「Layer 15」追加・選択。 |
| ev-032 | 11_01.mp4 | 9:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-09m40s.png]] | high | 同一画面。腕が2本になり(上の腕は 09:20 のもの)、両腕とも表面に帯線(肘・手首方向の区切り線)が鉛筆で描き込まれる。サブツールは[鉛筆]「粗い鉛筆」に戻る。Folder 6 内に「Layer 15 Copy」追加・選択。 |
| ev-033 | 11_01.mp4 | 10:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-10m00s.png]] | high | 画面切替。キャンバスは全身シルエット2体(女性型・立ちポーズ・腰に手)が左右に並ぶ。グレー塗り+濃い輪郭線で帯線はまだなし。サブツール[ペン]「Gペン」選択 Brush Size 200.0。レイヤーパネルは Folder 9 配下に Folder 5(Layer 16・Layer 17)/Folder 7(Layer 16 Copy・Layer 18)・Layer 20 選択表示、Layer 19 ほか旧図形レイヤー群と Paper。カラーサークルの選択色が赤系。 |
| ev-034 | 11_01.mp4 | 10:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-10m20s.png]] | high | 同一画面(2体のシルエット)。左図形の胸の高さに赤い水平線が引き伸ばされ、右端に赤手書き「EL」。図形間に薄いグレーの垂直ガイド線2本。上部中央に薄い透かし状の英数字(判読不能)。サブツール[鉛筆]「粗い鉛筆」。Folder 7 内 Layer 19 ハイライト・Layer 20 に編集中アイコン。 |
| ev-035 | 11_01.mp4 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-10m40s.png]] | high | 同一画面(2体+赤EL線+垂直ガイド)。2体の間に縦長円柱が描き加えられ(細い輪郭線)、高さ方向の数か所に赤い楕円のリング。右図形の腰付近に半透明の濃いグレー円形カーソル(ブラシサイズ表示)。サブツール[鉛筆]「粗い鉛筆」。 |
| ev-036 | 11_01.mp4 | 11:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-11m00s.png]] | high | 同一画面(2体+円柱+赤リング)。左図形の全身に帯線が描き込まれる(顔の目の高さ・胸・腹・腰・太もも・膝・ふくらはぎの水平区切り線)。円柱と赤リングは変化なし。Folder 9 内に新規「Layer 21」追加・選択。 |
| ev-037 | 11_01.mp4 | 11:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-11m20s.png]] | high | 同一画面(左図形帯線入り・円柱+赤リング)。左図形の足元に薄いグレーの四辺形(地面の平面)追加。キャンバス右下・右図形の足元高さに赤い水平線と赤手書き「EL」。円柱の右に細い垂直線がもう1本。サブツール[図形]「直線」選択 Brush Size 6.0。Folder 9 内「Layer 22」選択。 |
| ev-038 | 11_01.mp4 | 11:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-11m40s.png]] | high | 同一画面。最初の円柱の右に2本目の円柱が描き加えられ(頂部に大きな赤楕円・高さ数か所に赤リング)2本並ぶ。サブツールは[鉛筆]「粗い鉛筆」に戻る。Folder 7 内「Layer 20」ハイライト(編集中アイコン)。 |
| ev-039 | 11_01.mp4 | 12:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-12m00s.png]] | high | 同一画面(2体+2本の円柱+赤EL線)。右図形にも左と同様の帯線が描き込まれ(顔・胸・腹・腰・太もも・膝・ふくらはぎ)、右図形の足元にも薄いグレーの四辺形(地面)追加。Folder 9 内に Layer 23・Layer 24 が増え「Layer 24」選択。 |
| ev-040 | 11_01.mp4 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-12m20s.png]] | high | 同一画面(両図形とも帯線入り・2本の円柱+赤リング・EL線・地面の四辺形)。画面上の追加変化は確認できない。レイヤーも Layer 24 選択のまま。サブツール[鉛筆]「粗い鉛筆」。 |
| ev-041 | 11_01.mp4 | 12:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-12m40s.png]] | high | 同一画面。左図形の両腕に帯線が描き加えられ(上腕・前臂に斜めの区切り線)、右図形は変化なし。下部中央に薄い透かし状の英数字(判読不能)。Folder 7 内「Layer 23」ハイライト(編集中アイコン)。 |
| ev-042 | 11_01.mp4 | 13:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-13m00s.png]] | high | グレー塗りの人体マネキン2体(頭は輪郭線のみ・胴手足はグレー塗り+黒の区切り線)が左右に並び立つ。両者とも両肘を外側に曲げ両手を腰に当てたポーズ。左マネキンの足元にパースの四辺形(地面)、右マネキンの足元にも小さな三角のガイド線。2体の間に円柱2本分の縦線が立ち、各段に赤い楕円の作図線が上下に複数。キャンバス上部に左端から中央まで赤い水平線、右下に赤い手書き「EL」。粗い鉛筆 Brush Size 35.0。Layer 24 選択。ズーム 36.5。 |
| ev-043 | 11_01.mp4 | 13:10 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-13m10s.png]] | high | スライド。白キャンバス中央に黒の明朝風大きめ文字「短縮法とは何か?」、その下に淡いグレーの極小文字1行(判読不能)。ペンツール「Gペン」選択 Brush Size 17.5。レイヤー Layer 1(選択中)/Paper のみ。10秒スイープで発見した未観測画面。 |
| ev-044 | 11_01.mp4 | 13:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-13m20s.png]] | high | 別ドキュメント「Illustration3*」(36.6%)に切替。キャンバスほぼ白紙で、左寄り上部にかすれた縦の鉛筆線1本、左下に小さな直方体のスケッチ(上にすぼまった台形の上面+下に矩形)。レイヤー「Layer 1」(選択中)と「Paper」のみ。粗い鉛筆 Brush Size 45.0。 |
| ev-045 | 11_01.mp4 | 13:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-13m40s.png]] | high | 同一画面(Illustration3*・36.6%)。右上寄りに大きな長方形が鉛筆の荒い線で描かれ、その左端から長い縦線が画面中央を縦断して下まで伸びる。長方形の右横に十字形のブラシカーソル。左中央に小さな箱のスケッチ(矩形+斜めに傾いた四辺形の板が重なる)が現れ、その左端に赤/ピンク色の短い縦線2本。レイヤーは Layer 4(選択中)/Layer 3/Layer 2 Copy/Layer 2/Layer 1/Paper。サブツールリスト最下部に「テクスチャ鉛筆」と読める項目。右上に薄いグレーの半透明文字列(判読不能)。 |
| ev-046 | 11_01.mp4 | 14:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-14m00s.png]] | high | 同一画面。キャンバスに鉛筆風ラフ2群: 右寄りに細い赤の補助線を伴って、上に横長の直方体(正面の長方形・わずかに歪む)、その下に横倒しの円柱(左端に楕円形の口・グレーのベタ塗り)。左寄りに斜めに重なる四辺形(板状)数枚と、そこから下へ長い縦線1本、下端に小さな箱形。左のグループは線のみでグレー塗りなし。サブツール[鉛筆]「粗い鉛筆」選択 Brush Size 45.0。レイヤー Layer 2 Copy 2 選択中。ズーム 36.6。 |
| ev-047 | 11_01.mp4 | 14:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-14m20s.png]] | high | 同一画面。キャンバス右側の円柱がもう1本増え縦に2本並ぶ(いずれも左端に楕円の口・グレー塗り・周囲に赤いガイド線)。上の直方体と左側のラフ群は同一。サブツールパネルが[塗りつぶし]に切替(「塗りつぶし」選択中)、ツールプロパティは「編集レイヤーのみ参照」(Tolerance 5.0・Area scaling)。レイヤー選択が「Layer 2 Copy 2」から「Layer 5」へ。ナビゲータに円柱2本が反映。 |
| ev-048 | 11_01.mp4 | 14:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-14m40s.png]] | high | 同一画面。キャンバス右下に新たな描きかけのラフ: 大きな縦長の楕円(円)と、そこから右下へ長く伸ぶ斜めの直線2本、下端を横切る直線、楕円中心を通る縦線。上の円柱から赤い縦ガイド線が下まで伸びる。左側のラフ群と直方体・円柱2本は同一。サブツールパネルが[鉛筆]に戻り「粗い鉛筆」選択(一覧に「シャーペン」「デザイン鉛筆」等)。レイヤーは最上部に「Layer 7」追加・選択中、「Layer 2 Copy 3」も新たに見える。最下部中央やや右に薄いグレーの小さな文字(判読不能)。 |
| ev-049 | 11_01.mp4 | 15:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-15m00s.png]] | high | 同ファイル(Illustration3*)継続。前フレームで描きかけだった3つ目の円柱が灰色塗りで完成。左側の縦線に沿って斜めの直方体がさらに追加されジグザグに4〜5個連なる。キャンバス下部に薄い斜め線1本。ナビゲーターのサムネイルに3つの灰色図形。レイヤー Layer 8(選択中)/Layer 7/Layer 6/Layer 5/Layer 4/Layer 3/Layer 2 Copy 2/Layer 2 Copy/Layer 2 Copy 4/Layer 2 Copy 3/Layer 2/Layer 1/Paper。ツール設定(鉛筆・Brush Size 45.0)は同一。 |
| ev-050 | 11_01.mp4 | 15:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-01-15m20s.png]] | high | 同ドキュメント「Illustration3*」継続。変化点: 4本目のグレー塗り円柱が下端に追加(楕円断面を赤い縦線が貫く)。各形を縦につなぐ赤いジグザグの折れ線が描かれ、左側では矩形の左下角から各円柱の左端(楕円の左縁)を順に結び、右側では矩形の右端から各円柱の右端を結ぶ赤線が一列に通る。左上の小スケッチ(斜め平行四辺形4本+縦線+左下の小箱)は 15:00 と同一。レイヤーパネルは Layer 9(選択中)/Layer 8/7/6/5/4/3/2 Copy 2/2 Copy/2 Copy 4/2 Copy 3/2/1/Paper。ナビゲーターに矩形と円柱4本。Brush Size 45.0・サブツール[鉛筆]のまま。 |
| ev-051 | 11_02.mp4 | 0:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-00m00s.png]] | high | 画面全体がほぼ完全な黒。右上寄りにごく小さな淡い青白色の点が1つあるのみ。文字・スライド・人物なし。 |
| ev-052 | 11_02.mp4 | 0:10 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-00m10s.png]] | high | キャンバス右側に灰色塗りの円柱が縦に5つ積まれ、赤い垂直ガイド線2本と赤い斜め接続線、黒の水平補助線が走る。各円柱の左端に楕円断面と赤い中心線。左側には細い鉛筆線で描かれた傾いて積まれた箱形ブロック5〜6個の小スケッチとその下に小さな箱1つ、赤い垂直線が貫く。上部円柱の左上付近に赤い十字印。中央に極淡の文字列(判読不能)。サブツール[鉛筆]「粗い鉛筆」Brush Size 45.0。レイヤー Layer 5(選択中)。10秒スイープで発見した未観測画面。 |
| ev-053 | 11_02.mp4 | 0:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-00m20s.png]] | high | CLIP STUDIO PAINT EX。タイトルバー「Illustration3*」(A4 7016 x 4961px・600dpi・36.6%)。白いキャンバスに横向き(側面)の人体を箱の組み合わせで描いた下書き: 頭部の四角・胸の灰色の箱・腰まわりの灰色の箱2〜3個・腕は水色で塗り分けた細長いパーツ数節。透視図のガイド線(パース定規)がキャンバス全体に走る。サブツール[鉛筆]「粗い鉛筆」選択(一覧に「リアル鉛筆」「濃い鉛筆」「アニメ原画鉛筆R」「鉛筆」「シャーペン」等)。Brush Size 45.0・Texture 大理石・Stabilization 20。レイヤー Folder 2・Layer 12(選択中)・Layer 11・Layer 10・Folder 1・Paper。 |
| ev-054 | 11_02.mp4 | 0:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-00m40s.png]] | high | 画面が切替わり別ドキュメント「Viper-JookpubStock-04.jpg」(3179 x 4295px・240dpi・42.3%)。白のスポーツブラとショーツ姿で刀を肩に担いだ女性のストック写真が白っぽく薄く表示。写真下端にクレジット帯「FREE TO USE AS REFERENCE FOR PERSONAL AND COMMERCIAL WORK / FREE TO USE IN PHOTO MANIPULATION FOR PERSONAL WORK」「JENNIFER GUNTHER JOOKPUBSTOCK.COM」「FULL SET AVAILABLE ON PATREON.COM/JOOKPUBSTOCK | KO-FI.COM/JOOKPUBSTOCK」。サブツール[塗りつぶし]「編集レイヤーのみ参照」(Apply to connected pixels only・Close gap・Tolerance 5.0・Area scaling 3)。レイヤー Layer 2(選択中)/Layer 1(66%)/写真。ドキュメントタブに「Illustration3*」と併存。 |
| ev-055 | 11_02.mp4 | 1:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-01m00s.png]] | high | 同写真ドキュメント。写真の女性の上に赤い鉛筆線で頭部の直方体・胸・腹・腰・太ももの箱状アタリ線と腕の線が描き込まれる。サブツール[鉛筆]「粗い鉛筆」Brush Size 45.0。レイヤー構成変化なし(Layer 2 選択中・Layer 1 66%・写真)。ナビゲーターにも赤線が反映。 |
| ev-056 | 11_02.mp4 | 1:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-01m20s.png]] | high | 同一画面。赤い箱アタリがさらに進み、両腕(上腕・前腕を円関節でつないだ筒)と両脚(太もも・膝・すね・足の箱)まで赤線で描き込まれる。UI・レイヤー・クレジット表記は変化なし。 |
| ev-057 | 11_02.mp4 | 1:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-01m40s.png]] | high | 同一画面。刀身に沿って右へ長く伸びる赤い直方体のアタリ線が描き加えられ、上げた側の腕にも円筒を横断する短い線が数本入る。UI・レイヤー・クレジット表記は変化なし。 |
| ev-058 | 11_02.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-02m00s.png]] | high | 同ドキュメント。新たに青い線が描き加えられる: 肩の高さを横断する水平線とその両端の縦線・刀の下側を囲む平行四辺形の箱・体の右側の縦線。レイヤーパネルに新しい「Layer 3」が追加され選択中。描画色が青に変化(カラーサークルも青系)。ナビゲーターにも青線が反映。サブツールは鉛筆/粗い鉛筆のまま。 |
| ev-059 | 11_02.mp4 | 2:10 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-02m10s.png]] | high | 同写真ドキュメント(Viper-JookpubStock-04.jpg・42.3%)。写真の上に赤ピンクの関節人形風デッサンが半透明で重なる: 箱型の頭・分割された胴体・腕・脚(筒状の節)・右肩の剣と左手に構えた長い灰色の刃。写真の顔は赤い箱頭で覆われる。サブツール[ペン]「Gペン」選択 Brush Size 150.0。レイヤー Layer 5・Layer 2・Layer 4(各100%)/Layer 1(57%・選択中)/写真。キャンバス右下に極淡の文字列(判読不能)。10秒スイープで発見した未観測画面。 |
| ev-060 | 11_02.mp4 | 2:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-02m20s.png]] | high | 画面が切替わり別ドキュメント「Illustration4*」(A4 7016 x 4961px・600dpi・36.6%)。白いキャンバスに鉛筆で描いた楕円(輪)が2つ並び、左の楕円の下に短い線1本。サブツール[鉛筆]「粗い鉛筆」選択(一覧に「デッサン鉛筆」まで確認)。Brush Size 45.0。レイヤー Layer 2(選択中)/Layer 1/Paper。 |
| ev-061 | 11_02.mp4 | 2:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-02m40s.png]] | high | 同ドキュメント(Illustration4*)。目(縦線2本)と口(曲線)を描いた顔付きの円が3つ、左から小・中・大の順に並ぶ。左の小さい白い円が変形ハンドルで選択中で下に「OK / Cancel」ボタン。右側に「Editing: Transformation settings」パネル(Mode: Scale/Rotate・Scale ratio W 100/H 100・Keep aspect ratio・Interpolation method: Hard edges)。サブツールパレット[移動]。レイヤー Layer 1(選択中)/Layer 2/Layer 3/Paper。中と右の円は薄いグレーで塗られる。 |
| ev-062 | 11_02.mp4 | 3:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-03m00s.png]] | high | 同ドキュメント(Illustration4*)。3つの顔付き円が互いに接するように並べ替えられ、下に地面を示す水平線1本。円と円の接する部分に赤い鉛筆の線。変形UIは消えサブツール[鉛筆]「粗い鉛筆」に戻る。レイヤー Layer 4(選択中)/Layer 3/Layer 2/Layer 1/Paper と増加。 |
| ev-063 | 11_02.mp4 | 3:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-03m20s.png]] | high | 同ドキュメント(Illustration4*)。3つの顔付き円は互いに離れて並び、それぞれの下に短い地面線(03:00 の赤い接触線は見えない)。キャンバス下部に新たに、右奥から手前左へ大きくなる円を斜めに7個ほど連ねた描きかけの図。レイヤー Layer 4(選択中)/Layer 1/Layer 2/Layer 3/Paper。サブツール[鉛筆]「粗い鉛筆」。 |
| ev-064 | 11_02.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-03m40s.png]] | high | 同ドキュメントだがキャンバスの内容が切替わり、同じ立ち姿(片腕を横に伸ばした人体のシルエット線画)が左右に2つ並んで描かれる。先ほどの円の図は見えない。レイヤーパネルは Folder 2・Folder 4(中に Layer 7・Layer 6 Copy が選択中)・Folder 3(中に Layer 6)・Folder 1・Paper のフォルダ構成。ナビゲーターにも2体のシルエット。サブツール[鉛筆]「粗い鉛筆」。 |
| ev-065 | 11_02.mp4 | 4:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-04m00s.png]] | high | 同一画面。左右2体の人体シルエット線画はほぼ同様に表示。変化点はレイヤーパネルで、選択レイヤーが「Layer 6 Copy」から「Layer 7」に変わっている(Folder 4 内の Layer 7 がハイライト)。UI のその他の部分は変化なし。 |
| ev-066 | 11_02.mp4 | 4:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-04m20s.png]] | high | 同ドキュメント(Illustration4*)。白キャンバスに全身の人体線画(顔のない木偶人形風)が2体並ぶ。左は後ろ姿で片腕を横に伸ばし左足に薄いグレーの塗り、右は正面寄りで片腕を横に伸ばす。サブツール[鉛筆]「粗い鉛筆」Brush Size 45.0(一覧に「デザイン鉛筆」まで確認)。レイヤー Folder 2 内 Folder 4(Layer 7 選択中/Layer 6 Copy)・Folder 3(Layer 5/Layer 6)・Folder 1・Paper。 |
| ev-067 | 11_02.mp4 | 4:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-04m40s.png]] | high | 同一画面。右側人体の伸ばした腕に肘〜前腕の二重輪郭線が追加。レイヤー選択が Folder 3 内「Layer 5」に移動(ハイライト)。右図の右手先付近に薄いグレーの小さな文字列が出現(ほぼ判読不能)。 |
| ev-068 | 11_02.mp4 | 5:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-05m00s.png]] | high | 同一画面。右側人体に赤いラフ線が追加(顔に2点の赤ドット・首・胸・肩・腕・腰・太もも・すね)。左側人体の左足〜すねに青い線が追加。レイヤー「Layer 7」に赤いレイヤーカラーアイコンが付き再選択、「Layer 5」にも赤アイコン。右側のレイヤープロパティに「Layer color」の青スウォッチ表示。 |
| ev-069 | 11_02.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-05m20s.png]] | high | 画面が切替わりタブ2枚構成。左タブ「231201-Birthday-Pack-05.jpg」: グレーの写真素材。スポーツブラとショーツ姿の女性が直立し右手に細い棒を持ち両腕を広げるポーズ。写真下部に白文字クレジット「FREE TO USE AS REFERENCE FOR PERSONAL AND COMMERCIAL WORK」「JENNIFER GUNTHEL JOOKPUBSTOCK.COM」。右タブ「Illustration5*」(A4 4961 x 7016px・600dpi・25.9%): 白キャンバスに下書き段階のラフ(頭の円+十字ガイド・背骨のS字線・両腕の線・胴・腰・脚の薄い線)。レイヤー Layer 1(選択中)/Paper のみ。 |
| ev-070 | 11_02.mp4 | 5:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-05m40s.png]] | high | 同一画面。右キャンバスのラフが進行: 頭部に顔の輪郭線・首・肩・背中の輪郭・両腕と手・胴・腰(ショーツ線)・脚の線が増筆。レイヤーに「Layer 2」が追加され選択中。ナビゲーターサムネイル更新。 |
| ev-071 | 11_02.mp4 | 6:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-06m00s.png]] | high | 同一画面。肩・胸・上腕に濃い黒の線が追加され、ブラトップのラインと胴体の脇線が描かれる。 |
| ev-072 | 11_02.mp4 | 6:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-06m20s.png]] | high | 同一画面。右上に伸ばした側の手に指の線まで描き込まれ、前腕・肘の線が濃くなる。顔の線が整理される。 |
| ev-073 | 11_02.mp4 | 6:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-06m40s.png]] | high | 同一画面。左側(画面左)の腕が伸ばした形で描き込まれ両腕が揃う。キャンバス右下寄りに薄い文字列(判読不能)。 |
| ev-074 | 11_02.mp4 | 7:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-07m00s.png]] | high | 同一画面。左手から斜め上へ長い棒(写真のモデルが持つスティック)の直線が追加。ブラのバンド・ウエスト・脇の線が濃く整理され、腰〜ショーツの輪郭も濃くなる。 |
| ev-075 | 11_02.mp4 | 7:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-07m20s.png]] | high | 同一画面。ショーツ〜腰の線がさらに濃くなり、右脚(画面向かって右)の太もも〜膝の輪郭を追加。キャンバス中央に薄いグレーの文字列(判読不能・透かし様)。 |
| ev-076 | 11_02.mp4 | 7:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-07m40s.png]] | high | 同一画面。両脚が描き込まれる(膝・ふくらはぎ・足首・組んだ脚の交差線)。左側の写真の足元付近に薄いグレーの文字列が出現(数文字らしきものは判読不能)。 |
| ev-077 | 11_02.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-08m00s.png]] | high | 同一画面。両足のライン(つま先含む)まで描かれ、全身のラフ線画が一通り完了した状態。パネル構成に変化なし。 |
| ev-078 | 11_02.mp4 | 8:20 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-08m20s.png]] | high | 同一キャンバス。サブツール選択が「デザイン鉛筆」へ、ツールプロパティが[テクニック鉛筆](Brush Size 70.0・Opacity 100・Thickness 100・Angle 0.0・Brush density 75・Texture 動画用紙・Texture mode Subtract・Stabilization 2)に変化。レイヤーは最上位に「Layer 3」が追加・選択され Layer 1 の表示チェックが外れる(非表示)。キャンバスは線画が整理され、頭部・顔の平面・胴・腕に薄いグレーの陰影が塗り始められる。 |
| ev-079 | 11_02.mp4 | 8:40 | ![[wiki/assets/frames/coloso-hide-ch11-3d-figure-tips/hide-ch11-02-08m40s.png]] | high | 同一キャンバス。サブツールパネルが[色混ぜ]カテゴリに切替わり「ぼかし」を選択(他に色混ぜ/指先/筆なじませ等・一部判読不能)。ツールプロパティ[ぼかし](Brush Size 300.0・Intensity of blur スライダー・Brush density 100)。キャンバスは脚・胴・腕へグレーの陰影が広がり、左膝付近に丸いブラシカーソルが表示。キャンバス右下に薄い文字列(判読不能)。レイヤーは Layer 3 選択のまま。 |

## 関連リンク

- [[coloso-hide-human-drawing-course]] — 講座メタ
- [[coloso-hide-ch15-head-structure-simplification]] — 頭部の面分け・骨格単純化へ接続
- [[observation-via-abstraction]] — 複雑な対象を単純形へ落とす既存概念
