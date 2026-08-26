---
type: source
title: "coloso_hide_14 立体を意識して人物を描く③"
authors: [hide]
date: 2026-05-31
source_path: "raw/_coloso/2026_05_31_hide_01/coloso_hide_14 立体を意識して人物を描く③.md"
ingested: 2026-06-01
tags: [Coloso, 人体ドローイング, パース, 複数人物]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-01
---

# coloso hide ch14 ― 立体を意識して人物を描く③

## 要約

人物をパース空間に置く章。8 頭身・2 頭幅程度の箱をパースに乗せ、各断面の楕円や足の接地をアイレベルと地面タイルで確認する。複数人物では、同じ身長の人物のアイレベル位置や、頭頂・踵から消失点へ向かう線を使って、空間内の人物サイズを合わせる。章末では、低アングル・広角・魚眼的な歪みも、必要に応じて三点透視的に扱うと説明する。

## ソース内の主要主張

- まず 8 頭身・2 頭幅程度の箱をパース内に置き、人物全体の空間占有を確認する。
- アイレベルから離れるほど、奥行き面の楕円や面の見え方が強くなる。
- 足の接地は地面タイルや接地影で確認すると説得力が増す。
- 同じ身長の複数人物は、アイレベルが体のどの高さを通るか、または頭頂・踵から消失点へ伸びる線で大きさを合わせる。
- 子供や高身長キャラも、同じ空間内の基準線を使って身長差を決める。
- 人物・車・建物・箱などを同じ空間内の単純形として扱う。
- 広角・あおり・魚眼的な絵では、垂直方向にも収束を入れて三点透視的に扱う場合がある。

## 抽出されたエンティティ

- [[hide-animator]] — 講師。

## 抽出された概念

- [[figure-perspective-box]] — 人物をパース箱に入れて扱う。
- [[multi-figure-perspective-placement]] — 同一空間に複数人物を配置する。
- [[perspective-eye-level-method]] — アイレベル基準の配置。
- [[wrapping-line]] — 断面楕円とアイレベルの関係。

## 不確実・要確認

- chapter 14 は前半基礎の締めであり、人体細部の解剖学は ch15 以降へ移る。

## 映像観測(フレーム由来)

- 抽出日: 2026-08-26 / 元動画: [[_attachments/14_01.mp4]] + [[_attachments/14_02.mp4]](分割 2 本)
- 元動画 SHA-256: `4b55bf954cb876b73c4dbb9d75e81022e9581519dde3108d81935fe87e302e1f`(14_01) / `1f50cca4dcadf4f33bc748dd0d7a132392d70a1d6367ddec82a79d40703e44f9`(14_02)
- 方式: 20秒間隔抽出 / 抽出47+54枚・読取101枚・保存53枚
- 設計版: video-visual-ingest-design v2.3(分割動画対応・動画列付き 6 列表) / 読取モデル: opencode/x-preview-f-free (ox-alpha)(盲検読取はサブエージェント6分割回し、第2読者8枚=max(3,10%切り上げ))
- 凡例: 画面上で確認できた事実のみ。判読できない文字は「判読不能」と記載。時刻は各動画内の時刻。両パートとも CLIP STUDIO PAINT 実演で、14_02 途中(09:40 頃)に別ファイル「パース*」へ作画環境が切り替わる。選択レイヤー番号は読取間で不一致が出たため原則観測文から除外した。ev-046 のファイル名判読は要確認(marked-uncertain、manifest recheck 参照)。

| evidence_id | 動画 | 時刻 | frame | 確信度 | 画面の観測(事実のみ) |
|---|---|---|---|---|---|
| ev-001 | 14_01.mp4 | 0:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-00m20s.png]] | high | CLIP STUDIO PAINT EX のキャンバスに黒文字のタイトルスライド「このchapterで学べること」と箇条書き2項目(パースに合わせて人を描くためのコツ/パースに合わせて複数の人を描く方法)。A4 7016x4961px 600dpi。 |
| ev-002 | 14_01.mp4 | 0:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-00m40s.png]] | high | グレーの長方形グリッド(縦3列x約7〜8段の等身分割ボックス)が描かれた。図形・直線サブツール選択、ブラシサイズ6.0。 |
| ev-003 | 14_01.mp4 | 1:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-01m00s.png]] | high | 中央列に赤い水平線と、その線上の1点(消失点)に集束する青い放射状直線数本が追加された。 |
| ev-004 | 14_01.mp4 | 1:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-01m20s.png]] | high | 消失点へ集束する青い放射線群が上下方向にも増え、扇状のパース線群として完成。 |
| ev-005 | 14_01.mp4 | 1:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-01m40s.png]] | medium | 表示80%・回転状態。中央列上部に黒い楕円(輪郭)とそれを貫く青いX状十字線を追加。粗い鉛筆(サイズ15.0)使用。 |
| ev-006 | 14_01.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-02m00s.png]] | high | 楕円+青X線が2個並び、右側に濃い赤の縦線と赤いハッチング風の筆跡が追加。 |
| ev-007 | 14_01.mp4 | 3:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-03m20s.png]] | high | 赤い水平線より上に平たい楕円+青X线のセットが4段積まれ、下側にも薄い楕円の描写開始。36.5%表示。 |
| ev-008 | 14_01.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-03m40s.png]] | high | 赤線より下にも楕円セットが追加され(全体6段程度)、最下部付近に対角線X入り四角形が出現。 |
| ev-009 | 14_01.mp4 | 4:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-04m40s.png]] | high | 平たい楕円+青X线が8段積み上がった全景。上段ほど大きく下段ほど潰えた楕円。粗い鉛筆選択。 |
| ev-010 | 14_01.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-05m20s.png]] | high | 水色の横長楕円ガイド上に濃い鉛筆線で頭部の小箱・胴体の縦長箱・腰の箱が重ねて描かれた。粗い鉛筆(サイズ30.0)。 |
| ev-011 | 14_01.mp4 | 5:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-05m40s.png]] | high | 腰の箱の下に左右2本の脚の線が下方の楕円まで伸び、腰の位置に2本の曲線(鼠径部)が追加。 |
| ev-012 | 14_01.mp4 | 6:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-06m00s.png]] | high | 脚の輪郭が内側曲線を含めて立体化し、膝の横線と最下部に足先のくさび形(台形)が描き足された。 |
| ev-013 | 14_01.mp4 | 6:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-06m20s.png]] | high | 胴体上に胸の丸みを持つ輪郭と首の線が追加され、ふくらはぎの膨らみを持つ曲線に修正。頭部はまだ長方形。 |
| ev-014 | 14_01.mp4 | 6:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-06m40s.png]] | high | 両腕(肩の丸・上腕前腕の筒・肘位置の線)が胴体脇に追加され、胸中央に三角形の線が描き足された。 |
| ev-015 | 14_01.mp4 | 7:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-07m00s.png]] | high | 消しゴムへ切替。頭部が丸い頭蓋の輪郭+顔の十字ガイド線に描き直され、水色楕円の一部が消されている。 |
| ev-016 | 14_01.mp4 | 7:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-07m20s.png]] | high | 全身の内側が薄いグレーで塗りつぶされた(シルエット確認)。レイヤーパネルに新規レイヤー追加。 |
| ev-017 | 14_01.mp4 | 7:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-07m40s.png]] | high | 鉛筆へ戻り水色楕円ガイドが再表示、両足の下に地面を示す補助線が描き足された。グレー塗りは維持。 |
| ev-018 | 14_01.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-08m00s.png]] | high | キャンバス別領域で新しい下書き開始(中央に小さな十字マーク)。レイヤーパネルは Folder 7 内の新規レイヤー群に更新。 |
| ev-019 | 14_01.mp4 | 9:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-09m00s.png]] | high | 頭部の立方体・胸郭の大きな箱・腰の箱が濃い鉛筆線で描き足され、傾いた姿勢の体が箱の組み立てで固められている。 |
| ev-020 | 14_01.mp4 | 9:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-09m20s.png]] | high | 太もも・ふくらはぎの筒、膝の線、足の箱が追加され、傾いた立ち姿の全身構成になる。 |
| ev-021 | 14_01.mp4 | 9:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-09m40s.png]] | high | 両腕(肩の丸・上腕前腕)・首・肩回り輪郭・胸筋の線が描き足され、箱+筒による全身の組み立てがほぼ揃う。 |
| ev-022 | 14_01.mp4 | 10:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-10m20s.png]] | high | 正面立ちの筋肉質人体1体がグレー塗り+黒線画で完成。Multiply レイヤー選択中。 |
| ev-023 | 14_01.mp4 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-10m40s.png]] | high | ズームアウトして人体が左側へ。キャンバス中央寄りに新しい小型下書き(楕円の頭+中心線+細い体の線)開始。 |
| ev-024 | 14_01.mp4 | 11:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-11m20s.png]] | high | 小型図(2体目・背の低い人物)が横向き全身に成長。箱型頭・胴体・前方へ突き出した腕・脚・足、頭部に灰色塗り。 |
| ev-025 | 14_01.mp4 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-12m20s.png]] | high | 小型図の上方に3体目の下書き出現。箱型の頭・円筒(肩/腕)・円(関節)の線画。 |
| ev-026 | 14_01.mp4 | 12:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-12m40s.png]] | high | 3体目は小型図の後ろに立つ背の高い人物で、箱型頭・胴体・小型図の手に伸ばした腕の線画。 |
| ev-027 | 14_01.mp4 | 14:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-01-14m00s.png]] | high | 右上領域に4体目の下書き出現。箱型の頭・胴体の箱・腰の円、背面と思われる構図。ナビゲーターに4体確認。 |
| ev-028 | 14_02.mp4 | 0:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-00m40s.png]] | high | p1末尾で塗り途中だった右端の後ろ姿人物の灰色塗りが完了し全身灰色。新規 Folder 11 内レイヤー選択。 |
| ev-029 | 14_02.mp4 | 1:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-01m00s.png]] | medium | 作画領域が切りわり、白地に薄いパース補助線(放射状ガイド線)のみの状態。新しい俯瞰構図の開始。 |
| ev-030 | 14_02.mp4 | 2:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-02m00s.png]] | high | 左端に小型人物の線画(箱型頭・胴体・脚)+水色の下書き。パースガイド線は残存。 |
| ev-031 | 14_02.mp4 | 3:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-03m40s.png]] | high | キャンバス上部に赤ピンクで塗られた楕円3つ+半円の重なり(頭頂位置の目安)と手書き数字「1」「2」「1/2」。 |
| ev-032 | 14_02.mp4 | 4:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-04m00s.png]] | high | 赤い楕円と数字は消去済み。中央下に箱型オブジェクト(上部に四角、下部に楕円を含む立体)の描き始め。 |
| ev-033 | 14_02.mp4 | 4:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-04m20s.png]] | high | 左右に円筒断面2つ、背もたれ状の箱、座面の構造線が追加され、後ろ姿で座る人物の線画になる。 |
| ev-034 | 14_02.mp4 | 5:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-05m20s.png]] | medium | 中央上部に小さな箱(頭)と薄い水色の下書き線が追加(奥の小型人物の描き始め)。 |
| ev-035 | 14_02.mp4 | 5:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-05m40s.png]] | high | 中央上部の小型人物の黒線画が完成に近い形で描かれる(塗りなし)。画面内の人物は計3体。 |
| ev-036 | 14_02.mp4 | 6:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-06m00s.png]] | high | 画面内に人物4体分。左に大型灰塗り人物、中央上に小型全身線画、右に楕円ガイド+青補助線のみの未完成人物、下中央に背面箱頭の灰塗り人物。背景に薄いパース放射線。 |
| ev-037 | 14_02.mp4 | 8:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-08m00s.png]] | high | 大型人物と手をつなぐ小型人物(子供)の全身線画が描き上がっている。 |
| ev-038 | 14_02.mp4 | 8:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-08m20s.png]] | high | 中央上の人物の左側に大きな直方体のワイヤーフレーム(パースに沿った箱)が描かれた。 |
| ev-039 | 14_02.mp4 | 8:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-08m40s.png]] | high | 直方体に車輪(楕円2つ)と窓枠の線が入り、バン型車両と判別できる形に。消しゴム系ツールへ切替。 |
| ev-040 | 14_02.mp4 | 9:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-09m00s.png]] | high | バンの車体全体がグレーで塗られ、窓部分が白く残る塗り分け完了。Multiply レイヤー選択。 |
| ev-041 | 14_02.mp4 | 9:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-09m20s.png]] | high | 各人物とバンの足元に地面に広がる楕円状の落ち影(グレー)が描き足された。 |
| ev-042 | 14_02.mp4 | 9:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-09m40s.png]] | high | 別ファイル(「パース*」と判読)へ切替。白地+透視定規のみ(放射する黒い曲線群と下部の赤い水平線)。人物・車なし。 |
| ev-043 | 14_02.mp4 | 10:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-10m20s.png]] | high | 黒い鉛筆線で人物の上半身(頭の円・肩・胸・腕の一部)の描写開始。体に交差する青い補助線。 |
| ev-044 | 14_02.mp4 | 10:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-10m40s.png]] | high | 脚・ブーツ状の足まで描き進み、立ち姿の全身線画がほぼ出揃う。頭部は角ばった形状。 |
| ev-045 | 14_02.mp4 | 11:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-11m20s.png]] | high | 塗りつぶしで片腕を斜め上に長く伸ばした人物の全身に薄いグレーの塗りが乗った。Multiply レイヤー選択。 |
| ev-046 | 14_02.mp4 | 12:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-12m00s.png]] | high | 左に大きく灰色塗りの人物(腕を上げた立ち姿)、中央やや右に小さい人物のアタリ線画。青いパース補助線と下部の赤い水平線。※タイトルバーのファイル名は読取間で判読が分れ要確認(uncertain)。 |
| ev-047 | 14_02.mp4 | 12:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-12m20s.png]] | high | 小さな人物が完成形に近づき、両腕を上に上げ、脚と足元の地面線まで描き足されている。 |
| ev-048 | 14_02.mp4 | 13:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-13m00s.png]] | high | キャンバス右側に垂直線とパースに沿った斜め線からなる縦長構造の作画開始。Folder 23 内レイヤー選択。 |
| ev-049 | 14_02.mp4 | 14:00 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-14m00s.png]] | high | 右側の縦長構造が頭部の箱+円筒関節の腕・脚を持つ第3の大型人物(背面立ち姿)として整理され全身線画が揃いつつある。 |
| ev-050 | 14_02.mp4 | 15:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-15m20s.png]] | high | 中央左に背の低いずんぐりした人物(箱型の胴体・短く下げた腕)が薄いグレーの線で判別可能。これで画面内人物4体分。 |
| ev-051 | 14_02.mp4 | 16:20 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-16m20s.png]] | high | キャンバス左右の背景に大きな斜めの直線が描き足され、建物・壁面の骨格線が現れる。 |
| ev-052 | 14_02.mp4 | 16:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-16m40s.png]] | high | 左右の壁面が台形の大きな面として整えられ、両側に壁のある通路状の空間にまとまる。消しゴム(サイズ70.0)、拡大率36.6%。 |
| ev-053 | 14_02.mp4 | 17:40 | ![[wiki/assets/frames/coloso-hide-ch14-figure-perspective/hide-ch14-02-17m40s.png]] | high | 黒背景の中央に白い「Coloso.」ロゴ(ピリオドは黄色)の終端カード。下部に赤帯。ソフトUI非表示。 |

## 関連リンク

- [[coloso-hide-ch12-three-mass-blocking]]
- [[coloso-hide-ch13-limb-blocking]]
- [[coloso-hide-human-drawing-course]]
