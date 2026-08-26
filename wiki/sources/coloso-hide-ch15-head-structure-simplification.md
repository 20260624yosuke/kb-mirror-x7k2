---
type: source
title: "coloso_hide_15 頭部の構造と単純化"
authors: [hide]
date: 2026-05-31
source_path: raw/_coloso/2026_05_31_hide_01/coloso_hide_15 頭部の構造と単純化.md
ingested: 2026-06-01
tags: [coloso, 人体ドローイング, 頭部, 解剖, 単純化, hide]
status: active
confidence: high
evidence_level: source-backed
visual_ingested: 2026-08-26
last_reviewed: 2026-06-01
---

# coloso hide ch15 ― 頭部の構造と単純化

> Section 06「骨格と筋肉の単純化 1: 胴体」の開始章。元 md は Whisper 逐語で、解剖用語に誤変換が多い。本要約では文脈から明らかな範囲で **脳頭蓋 / 顔面頭蓋 / 眼窩 / 鼻腔 / 側頭稜 / 眼窩上隆起 / 頬骨 / 外耳道 / 下顎骨 / 乳様突起** などへ補正している。

## 要約

頭部を描くために必要な骨格知識を、名称暗記ではなく **描画上のランドマーク** として整理する章。hide は解剖学を「キャラクターを自由に描くための道具」と位置づけ、全名称の暗記より、表面に現れる出っ張り・くぼみ・面の切り替わりを把握することを重視する。頭部は大きく **脳頭蓋** と **顔面頭蓋** に分け、脳頭蓋を球体、顔面頭蓋をシールド / ブロックとして単純化する。後半では頭部をさまざまな角度から描き、顔の凹凸・E ライン・眼窩・頬骨・唇・顎などを面として理解する。

## ソース内の主要主張

- キャラクターイラストでも、骨や筋肉を理解しているかどうかで絵の説得力は変わる。
- ただし、骨名・筋名をすべて詳細に覚える必要はない。解剖学は目的ではなく **描きたいキャラクターを自由に描くための道具**。
- 頭は大きく **脳頭蓋** と **顔面頭蓋** の 2 パーツに分けられる。正面では脳頭蓋を球体、顔面頭蓋をホームベース型 / シールド型として扱う。
- 顔のランドマークとして、眼窩、鼻腔、側頭稜、眼窩上隆起、鼻骨、上顎骨、頬骨、下顎骨、外耳道、乳様突起などを確認する。
- 重要なのは名称ではなく、目の彫りを作る出っ張り、頬の出っ張り、下顎角、首筋がつく乳様突起など、表面に効く部分を押さえること。
- 頭部練習では、正面・横・斜め・真上・後ろ・真下・あおり・俯瞰など、複数角度から頭蓋骨を描いて立体を把握する。
- 頭部単純化では、脳頭蓋を球体として描き、側面を切り落とし、顔面頭蓋を角張った前面・側面ブロックとしてはめる。
- 比率の基準は、頭頂・髪の生え際・眉間・鼻の付け根・顎。これはキャラクターによって変わるため、絶対値ではなく基準として使う。
- 顔の凹凸では、横顔を基準に E ライン、鼻、上唇、下唇、顎、眼窩、頬骨、ほうれい線、耳の傾きを確認する。
- 斜め顔では、眉間と鼻筋の立体に眼球を交わすように置き、その上にまぶたをかぶせる。顔をブロック化してから凹凸を足すと理解しやすい。

## 抽出されたエンティティ

- [[hide-animator]] — 講師

## 抽出された概念

- [[anatomy-as-drawing-tool]] — 解剖学はキャラクターを描くための道具
- [[head-two-part-simplification]] — 脳頭蓋 + 顔面頭蓋の 2 パーツ単純化
- [[face-landmarks-and-planes]] — 顔のランドマークと面
- [[turning-edge-plane-awareness]] — 側頭稜・頬骨ラインを面の切り替わりとして読む
- [[box-proportion-method]] — 既存 Nekojira の頭部構築法と比較可能
- [[jintai-anki]] — 既存 hizurume の人体暗記観と比較可能

## 不確実・要確認

- 原 transcript の「側頭領」は文脈上 **側頭稜** と補正した。
- 原 transcript の「胸骨 / 胸骨球」は文脈上 **頬骨 / 頬骨弓** と補正した。
- 原 transcript の「眼下」は文脈上 **眼窩** と補正した。
- ch15 末尾のデフォルメキャラの顔の凹凸パートは、現パイロットでは章全体の要約に留め、詳細 concept 化は保留。

## 映像観測(フレーム由来)

- 抽出日: 2026-08-26 / 元動画: [[_attachments/15_01.mp4]] + [[_attachments/15_02.mp4]] + [[_attachments/15_03.mp4]](分割 3 本)
- 元動画 SHA-256: `56d170a12dd1a89df7f92d63e27a09b5a27d980cb1c21b30fd8507da217ccb87`(15_01) / `5adcd66de4e24c5456e1d6861707ea693a9c437d4fa56067349763f34455fe4b`(15_02) / `4a685456fbdd19924b824407cd2ceb2d33fa956e6eb313ee0ad61a04dc241b96`(15_03)
- 方式: 20秒間隔抽出 / p1 抽出46枚・保存24枚 + p2 抽出46枚・保存15枚 + p3 抽出51枚・保存15枚(読取143枚・計54枚)(バッチ退避方式: 読取結果を wiki/builds/coloso-visual-ingest-batch2/hide-batch3/ch15/ へ逐次退避)
- 設計版: video-visual-ingest-design v2.3 / 読取モデル: opencode/x-preview-f-free (ox-alpha)(盲検読取はサブエージェント分割回し、第2読者4枚=max(3,10%切り上げ))
- 凡例: 画面上で確認できた事実のみ。判読できない文字は「判読不能」と記載。時刻は各動画内の時刻。冒頭は頭蓋骨写真素材への色分け注記パート、後半は多角度スケッチのデモ。アプリ UI に薄い透かし状文字列が出るが読みが安定しないため「判読不能」扱い。

| evidence_id | 動画 | 時刻 | frame | 確信度 | 画面の観測(事実のみ) |
|---|---|---|---|---|---|
| ev-001 | 15_01.mp4 | 0:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-00m20s.png]] | high | CLIP STUDIO PAINT EX の白キャンバス中央に黒文字で「第15講」「頭部の構造と単純化」のタイトル。消しゴムツール選択(Brush Size 80.0)、拡大率36.6%。 |
| ev-002 | 15_01.mp4 | 0:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-00m40s.png]] | high | キャンバス上部に2行テキスト。黒字「人物画を描く →中身(骨格・筋肉)の把握は大切だが…」、赤字「骨や筋肉の名前を全て詳細に知っている必要はない」。 |
| ev-003 | 15_01.mp4 | 1:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-01m20s.png]] | high | 正面・側面の頭蓋骨写真が白キャンバスに2枚並ぶ。スポイトツール選択。「フォルダー 1」内に「Multiply レイヤー 2」「IMG_1867 のコピー 2」等のレイヤー群。 |
| ev-004 | 15_01.mp4 | 1:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-01m40s.png]] | high | 頭蓋骨2枚が薄い水色に着色され、正面像上に黒の細線で円(楕円)+縦横の直線ガイド+顔輪郭風の曲線を重ね描き。粗い鉛筆(30.0/大理石)、「100 % Normal レイヤー 2」選択。 |
| ev-005 | 15_01.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-02m00s.png]] | high | 頭蓋骨写真が消え、簡略化スケッチ2つ(正面=卵形+顎部、側面=楕円+縦基準線)。正面は上部薄緑・頬〜顎ピンク、側面も薄緑で塗り分け。塗りつぶし(Tolerance 5.0)、「Multiply Layer 1」選択。 |
| ev-006 | 15_01.mp4 | 2:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-02m20s.png]] | high | 頭蓋骨写真に戻り、正面像の両眼窩を赤で塗り「眼窩」と赤字注記。塗りつぶし(Tolerance 5.0)、描画色赤、Folder 2 内 Multiply レイヤー選択。 |
| ev-007 | 15_01.mp4 | 2:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-02m40s.png]] | high | 正面像の鼻腔を赤で塗り「鼻腔」注記追加。側面像の頭頂部沿いに赤い曲線+「側頭稜」ラベル。Gペン(80.0)へ切替。 |
| ev-008 | 15_01.mp4 | 3:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-03m00s.png]] | high | 正面像の眉弓に淡い紫塗り+「眼窩上隆起」、鼻骨に緑塗り+「鼻骨」、上顎周辺に黄緑輪郭+「上顎骨」と色分け注記が増える。 |
| ev-009 | 15_01.mp4 | 3:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-03m20s.png]] | high | 正面像の頬骨・上顎部を黄緑と黄色で範囲塗り+「頬骨」注記。側面像の頬骨弓をオレンジ/山吹色で塗り「頬骨弓」ラベル。 |
| ev-010 | 15_01.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-03m40s.png]] | high | 正面像の下顎全体を青紫(藍色)で塗り「下顎骨」。側面像も下顎同色、頬骨弓後端に赤丸+「外耳道」ラベル。 |
| ev-011 | 15_01.mp4 | 4:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-04m00s.png]] | high | 色分け済み頭蓋骨2面の全景。赤注記「眼窩上隆起」「頬骨」「下顎角」と、側面頭蓋の頭頂から後頭へ回り込む赤い線。Gペン80.0、Multiply レイヤー選択。 |
| ev-012 | 15_01.mp4 | 4:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-04m40s.png]] | high | キャンバス白紙になり、淡いグレーの円(頭蓋土台)+縦横中心補助線+円上下端の短い横線を描いた直後。粗い鉛筆40.0、新規レイヤー選択。 |
| ev-013 | 15_01.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-05m20s.png]] | high | 両眼窩相当の大きな楕円2つ+眉弓部の横線+口位置の横線が追加され、頭蓋正面の主要パーツが揃う。粗い鉛筆40.0。 |
| ev-014 | 15_01.mp4 | 6:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-06m00s.png]] | high | 正面スケッチ完成し薄グレー塗り(眼窩・鼻腔等)、左へ縮小配置。右側に新しい円(側面用土台)+下方向補助線の描写開始。作業レイヤー切替。 |
| ev-015 | 15_01.mp4 | 7:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-07m00s.png]] | high | 表示縮小。完成した正面(左)+側面(中央)+右に3つ目の土台として傾いた円+縦の長い中心線+横補助線の描写開始。ナビゲーターに頭部サムネイル3つ。 |
| ev-016 | 15_01.mp4 | 8:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-08m40s.png]] | high | 傾き顔に歯列(グリッド状)+頬骨弓+眼窩の灰色塗りが描き込まれペン入れ調に。Gペン(90.0)へ戻り新規レイヤー追加。 |
| ev-017 | 15_01.mp4 | 9:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-09m20s.png]] | high | キャンバス左下に4つ目=真上から見た頭蓋(上面図)出現。灰色卵形シルエット+中央正中線+左右対称ガイド円。選択範囲系ツール表示、ナビゲーター4つ。 |
| ev-018 | 15_01.mp4 | 10:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-10m00s.png]] | high | 下段中央に5つ目=後ろから見た頭蓋(背面図)出現。頭頂部ギザギザの縫合線+灰色塗り+首・顎のブロック状補助形状。Gペン、ナビゲーター5つ。 |
| ev-019 | 15_01.mp4 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-10m40s.png]] | high | 下段右に6つ目=下から見た頭蓋(下面図)の作画開始。外側円形輪郭+中央小円+上端波形線。粗い鉛筆40.0、ナビゲーター6つ。 |
| ev-020 | 15_01.mp4 | 11:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-11m00s.png]] | high | 頭蓋スケッチ6つが2行3列(正面・側面・斜め/上面・下面・背面寄り)で並び、それぞれ灰色塗り+黒線画の完成状態。消しゴム「硬め」70.0。 |
| ev-021 | 15_01.mp4 | 12:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-12m40s.png]] | high | 頭蓋スケッチ周囲に変換用バウンディングボックス+「OK/Cancel」ボタン=拡大縮小操作中。Scale ratio W65%/H65%、縦横比保持、Interpolation: Hard edges。 |
| ev-022 | 15_01.mp4 | 13:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-13m00s.png]] | high | キャンバス中央に大きな円形頭部下描き(縦横補助線+顔中心線+横ガイド線)。左上に完成済み頭蓋スケッチ(灰陰影)を参照配置。粗い鉛筆40.0、36.6%。 |
| ev-023 | 15_01.mp4 | 14:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-14m20s.png]] | high | 中央に新しい頭部下描き(円+前方に張り出す箱形ガイド・傾いた構図)を黒鉛筆で開始。左列に完成済み頭蓋スケッチ上下2つ。 |
| ev-024 | 15_01.mp4 | 15:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-01-15m00s.png]] | high | 歯列・上顎の塊、顎輪郭、側頭部の穴(耳孔位置と思える楕円)などの線を追加し、頭蓋の立体構造が詳細化。 |
| ev-025 | 15_02.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-02m00s.png]] | high | キャンバス右上に5つ目の頭蓋下描き新規追加(球体+十字+斜め補助線)。4つ目は灰色塗り範囲拡大。粗い鉛筆40.0。 |
| ev-026 | 15_02.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-03m40s.png]] | high | ズームアウト(33.3%)。キャンバス右下に6つ目の頭蓋スケッチ開始(楕円+顔面ブロック補助線+眼窩・鼻孔・上顎あたりの粗い線)。陰影なし。 |
| ev-027 | 15_02.mp4 | 4:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-04m40s.png]] | high | キャンバス白紙化。中央に大きな楕円(球)+左下に接する箱型ブロックの細い補助線のみ。 |
| ev-028 | 15_02.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-05m20s.png]] | high | 白キャンバス中央に大きな円(球)+内部に縦楕円(側面補助線)1本、右上に描き始めの短い線。鉛筆系40.0(大理石)。 |
| ev-029 | 15_02.mp4 | 6:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-06m20s.png]] | high | 球の下側に左右垂直線+底辺横線からなる台形輪郭(顎・フェイスライン下書き)を描き足し。 |
| ev-030 | 15_02.mp4 | 7:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-07m20s.png]] | high | 頭部下書き全体(球+フェイス部分)を薄グレーで塗りつぶし。Gペン(90.0)へ切替。 |
| ev-031 | 15_02.mp4 | 7:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-07m40s.png]] | high | キャンバスほぼ白紙に戻り、中央に新しい円(球)+横切る緩い曲線のみ(新下書き開始)。別レイヤー群へ切替。 |
| ev-032 | 15_02.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-08m00s.png]] | high | 頭部フォーム(円+顎輪郭)に顔側楕円部分の灰色塗り。黒線で十字中心線+目の高さ補助線+小さな三角マーカー+下方へ延びる複数縦線。塗りつぶし(他レイヤーを参照)。 |
| ev-033 | 15_02.mp4 | 9:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-09m20s.png]] | high | 4つ目頭部が描き込まれ球+多面体稜線(黒直線)+灰色塗り面が完成に近い。十字線と顔側楕円面の塗り分け。ナビゲーター5つ。 |
| ev-034 | 15_02.mp4 | 10:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-10m20s.png]] | high | 右側に6つ目の頭部フォーム(大きな円+灰色塗り+中心線+あご輪郭)+その左に小さめ頭部(円+縦線)追加。ナビゲーター8つ。 |
| ev-035 | 15_02.mp4 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-10m40s.png]] | high | 球+十字ガイド+多面体稜線+灰色塗りの頭部フォームが10個並び、右列中段に線画のみの新頭部(球+十字+あご方向稜線)描き始め。消しゴム(80)。 |
| ev-036 | 15_02.mp4 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-12m20s.png]] | high | 表示が新しい領域へ移動。左に耳のような楕円2つを伴う頭部フォーム(右側灰塗り)、中央に大きな円+縦横十字ガイドの新しい頭部。 |
| ev-037 | 15_02.mp4 | 12:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-12m40s.png]] | high | 中央の円に薄い水色ガイド線(円を貫く縦線+複数横線+円下方向への線)を描き足し。 |
| ev-038 | 15_02.mp4 | 13:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-13m20s.png]] | high | 左に灰色面塗り+楕円形耳2つの頭部立体スケッチ。中央右に大きな薄い円+水色十字ガイド+黒直線ポリゴンで眉・鼻・頬の稜線ブロック。 |
| ev-039 | 15_02.mp4 | 14:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-02-14m20s.png]] | high | 左に精緻化された頭部(耳+稜線増)、中央に新しい頭部下描き(大きな円+水色ガイド+右側に水色楕円=側頭面塗り+縦直線群)。 |
| ev-040 | 15_03.mp4 | 0:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-00m20s.png]] | high | キャンバス左に完成済み多面体構造ヘッド(灰色面塗り+黒線)、右に正面頭部の作成途中(青い補助線=楕円・十字ガイド、顔右半分に薄い水色の面)。粗い鉛筆40.0。 |
| ev-041 | 15_03.mp4 | 1:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-01m20s.png]] | high | 右頭部から青補助線と水色の面が消え黒線のみの清書状態。耳の輪郭追加。複製レイヤー上で作業。 |
| ev-042 | 15_03.mp4 | 2:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-02m40s.png]] | high | 別ドキュメント「241003-Birthday-Pack-10.png*」。白いタンクトップの女性がスツールに座る写真素材を白抜け表示。下部にクレジット表記(FREE TO USE…/PATREON.COM/JOOKPUBSTOCK)。 |
| ev-043 | 15_03.mp4 | 3:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-03m20s.png]] | high | 女性写真の頭部に赤鉛筆で頭蓋楕円・目線・鼻・口・顎の補助線。粗い鉛筆90.0、Layer 4 選択、拡大率90%。 |
| ev-044 | 15_03.mp4 | 4:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-04m00s.png]] | high | 写真レイヤー非表示、白地に赤〜ピンクの線画のみ(面構成・耳・首・肩)。Gペン70.0、Layer 3(35%)選択。 |
| ev-045 | 15_03.mp4 | 5:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-05m00s.png]] | high | 別ドキュメント(DSC_0305…jpg)。両手を頭の後ろに回した男性写真(フェード済み)の頭部に赤い楕円+顔中心縦線の描写開始。 |
| ev-046 | 15_03.mp4 | 6:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-06m20s.png]] | high | 顎〜首〜鎖骨まわりまで赤い輪郭線を拡大(首左右の線+鎖骨寄りの斜線)。 |
| ev-047 | 15_03.mp4 | 6:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-06m40s.png]] | high | 写真非表示で白地+線画のみ。眼窩と鼻の三角帯に薄ピンク塗り。新規 Layer 4(35%)選択、Gペン70.0へ切替。 |
| ev-048 | 15_03.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-08m00s.png]] | high | 別キャンバス(タイトルバーは「kaonokakikata*」=顔の描き方 と読める)に白背景+青い線のみの頭部下書き2つ。粗い鉛筆25.0、Layer 73 選択。 |
| ev-049 | 15_03.mp4 | 9:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-09m00s.png]] | high | 左頭部で鼻・唇・顎の凸凹を含む側面輪郭をほぼ通しで描写、頭頂に大きな弧を追加。 |
| ev-050 | 15_03.mp4 | 11:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-11m00s.png]] | high | 左頭部内部を明るいグレーで塗り分け(首まで含む)。消しゴムへ切替、新規 Layer 74 選択。 |
| ev-051 | 15_03.mp4 | 11:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-11m40s.png]] | high | 濃グレー塗りが拡大し頭蓋〜あご・首の影まで大部分を塗り分け。塗りつぶし(Tolerance 5.0)、Layer 75。 |
| ev-052 | 15_03.mp4 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-12m20s.png]] | high | 右正面頭部に水平ガイド線数本(眉・目・鼻・口高さ)+縦中心線を描き足し。粗い鉛筆へ戻り Folder 37 内 Layer 76。 |
| ev-053 | 15_03.mp4 | 14:40 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-14m40s.png]] | high | 正面頭部全体を白〜薄グレーで塗り、ペン入れ風の滑らかな黒線へ置換(目・鼻・口・耳・首まで整う)。Gペン70.0、新規 Layer 78。 |
| ev-054 | 15_03.mp4 | 15:00 | ![[wiki/assets/frames/coloso-hide-ch15-head-structure-simplification/hide-ch15-03-15m00s.png]] | high | 左右両頭部とも黒線線画+灰色塗りが完成。目・鼻・口・耳・首の線入り。Gペン70.0、Layer 78 選択。 |
## 関連リンク

- [[coloso-hide-human-drawing-course]] — 講座メタ
- [[coloso-hide-ch11-3d-figure-tips]] — 面・稜線・ラッピングラインなど、頭部単純化の前提
- [[coloso-nekojira-ch09-box-proportion]] / [[coloso-nekojira-ch13-head-application]] — 既存の頭部構築講座
