# ドルフロ2 スキン×ジャンル対応表 — 公式画像紐付けレポート

- 実施日: 2026-08-24
- 入力: `/var/folders/.../gf2-skin-genre-map/skin-genre-table.json`(146行)
- 出力: `skin-genre-table.json`(image_url 追加版) / `skin-genre-viewer.html` / 本レポート

## 成果サマリ

- **画像取得成功率: 122 / 146 行(83.6%)**
- 全URLとも IOP Wiki MediaWiki API(`api.php`)の `imageinfo` で解決後、HTTP GET(Range要求)で **status 200/206 を実測確認**して採用。推測URLは一切不使用。
- 確度別件数(元表から変更なし): source-backed 70 / inferred(iopwiki) 57 / 未確認 19

## 紐付け方法

1. キャラ個別ページ(Klukai/Makiatto/Lainie/Krolik/Belka/Dushevnaya/Yoohee/Mosin-Nagant/Welrod MkII/OTs-14 (GFL2)/Mechty 等、表記ゆれはリダイレクト・候補補正で解決)のgalleryを解析。
2. デフォルト衣装行: `<キャラ>.png` / `<キャラ>_Whole.png`(GFL2_接頭辞・Bathilde綴り差なども吸収)を採用。caption「Full artwork ...」も補助根拠。
3. スキン行: gallery caption(例: 「Outfit - Speed Star.」)とゲーム内ENスキン名をトークン照合(通常は曖昧一致許可、**未確認行は完全一致のみ**)。ユニークに決まった場合のみ採用。

## 未取得 24 行の内訳

| 区分 | 内容 |
|---|---|
| wiki側EN名称不一致(約10行) | Suomi Sparkling Ocean/Fluffy Korvatunturi(wiki名 Midsummer Pixie/Korvantunturi Pixie)、Mosin-Nagant Siberian Slide、Jiangyu Raindrop-Cleaving Blades、Lind The Sun Never Rises、Sakura Tale of the Butterflies、Vector Vivi Sometimes…、Florence Alluring Larkspur、Daiyan Narcissus、Lenna等。wiki独自訳のため機械照合不可 |
| キャプション無し・ページ不在(約8行) | Phaetusa Caged in the Evernight Garden(costume1 caption空)、Loreley Twilight Whisper、Sextans Nocte Bewitchment、Dushevnaya Tomorrow's Savior(ページにコスチューム画廊なし)、Centaureissi 50 Days With Centaureissi(イベント皮膚がキャラページに無い)等 |
| 未確認行でcaption一致無し | Welwyn Mist, Enjoy the Fragrance, Marvellous Yam Cake, Molotov Bunny, Indigo Oath, Wisteria Tidings 等 |
| キャラ帰属不明(3行) | Earnest Defense(id 1104000), Cobalt Dream(1106700), Destined Love(1107401)。元表でchar_internal=null。※Starlit Waltz(1106701)はevidence_noteのdoll:Helen経由で取得済み |

## 注意点

- **未確認行で画像を付けた行あり**: Groza Dawn of Battle/Groza's Training Outfit、Qiuhua Dragon Chef、Vepley Sparkling Wish/Summer Echo、OTs14 Dark Sun's Shadow 等。これらは元表では「IOP Wikiに名称一致エントリ無し(GFL2_Costumes頁ベース)」だが、キャラ個別ページではcaption一致を確認できたため画像のみ付与。該当行の `evidence_note` 末尾に追記あり。confidence値自体は触っていない(見直しはユーザー判断)。
- wikiのcaptionはGlobal EN実装名と異なる独自英名を使うケースが多く(Suomi等)、これらは一致保証がないため意図的に空欄のまま残した。
- トークン照合は「全トークンがcaption内に存在」が条件のため、短い汎用名での誤紐付けリスクは低いが、`image_source` でFile名と出典ページを必ず確認できるようにした。
- IOP Wiki はCloudflare保護があり、curl既定UAだと403。ブラウザUAでアクセスした。

---

# 公式X(@EXILIUMJP)スキン紹介投稿の紐付けレポート(2026-08-24 追記)

## 成果サマリ

- **紐付け成功率: 37 / 146 行(25.3%)**
  - 対象を「スキン行」(デフォルト衣装・訓練服・テスター行を除く61行)に限ると **37 / 61 行(60.7%)**。
  - 全37件とも `official_x_match_confidence: exact`(ツイート本文が「【スキン紹介】 <キャラ名JP> 私服・<スキン名JP>」の形式でキャラ名+スキン名が完全一致)。
  - 33件は画像ツイート(pbs.twimg.com `media/...jpg?name=orig`)、4件(蒼き軌跡/ホットエクササイズ/未来への道標/星夜のワルツ)は動画ポストで `amplify_video_thumb` サムネイルを採用。
- **全公式X画像URLをHTTP GETでstatus 200を実測確認**してから採用。ツイートID・画像URLの推測は一切不使用。

## 使用できた経路

1. **websearch**(Googleインデックス経由のx.comページ): ツイートURL+本文断片を発見。最も汎用。
2. **攻略wiki(dollsfrontline2.wikiru.jp)のキャラ個別ページ**: スキンタブ内に「公式ツイッターのスキン紹介」として埋め込み済みの `twitter.com/exiliumjp/status/<id>` を抽出。一括取得に最有力だった(約20件)。
3. **api.fxtwitter.com/status/<id>**: 収集した全IDの実在確認・本文・投稿日・media URL解決に使用。71IDを検証し、本文照合で37件を確定(人形PV・人形キーワード・クリスマスギフト等の非スキン投稿を除外)。
4. kanotypeフォーラム(公式ポストミラー): 4件のツイートIDを提供。

## 失敗・不採用となった経路

| 経路 | 結果 |
|---|---|
| x.com 直接 webfetch | 未実施(JS描画+ログイン壁で失敗する既知問題のため最初から回避) |
| syndication.twitter.com タイムライン | HTTP 429 レート制限で継続不可 |
| Yahooリアルタイム検索(curl) | 過去ポスト対象外+JSONに作者情報が含まれず不採用 |
| DuckDuckGo/Bing/Mojeek/Searx系の直接スクレイピング | 全部チャレンジ画面/CAPTCHA/429 |
| collabase.net(公式Xアーカイブ) | ページ廃止(HTTP 410) |

## 未紐付け 24 スキン行の理由

| 区分 | 行 |
|---|---|
| 常設・報酬系スキンで【スキン紹介】投稿が存在しないとみられる | 星の軌跡/静謐の暗渠(Nemesis)、シャイニングハート(Vepley)、青い春のススメ(Tololo)、放課後の思い出(Mityl)、芳醇なひととき(Springfield)、幻瞳と幽爪(Peritya)、タクティカルバニー(Vector)等 |
| 検索したが出てこない(投稿自体は存在する可能性あり) | 真夜中の囁き(Andoris)、ダイヤの花びら(Leva)、ファントムドリーム(Belka)、食は仁術/秋宴の料理人(Qiuhua)、日焼け止め大作戦(Lewis)、サマータイム(Nagant)、義薄雲天(Qiongjiu)、ひまわりのように/銀月めぐる夜(Nikketa)、アンティア/花信風(Alva)、スイートミラクル(YooHee)、法の執行者(Robella)、レッドゾーン(Soppo)、脱兎の如く(Krolik) |

※デフォルト衣装85行は公式のスキン紹介投稿が存在しないため対象外(空欄のまま)。将来まとめ直す場合は公式YouTubeの【スキン紹介】動画やEN公式(@GFL2EXILIUM_EN)への紐付け拡張が候補。

## HTMLビューア改修内容

- カード・lightbox・比較モードすべてで **公式X画像(主)×Wiki画像(副)を並列表示**(両方ある場合)。片方のみなら従来どおり単独表示。
- lightbox 内に **公式ツイートへのリンクボタン**(外部リンク)+ポスト本文・投稿日・紐付け確度を表示。
- フィルタに「**公式X情報あり/なし**」チェックボックスを追加(件数付き)。
- 埋め込みJSONを更新(`skin-genre-table.json` と同一内容)。新カラム: `official_x_url` / `official_x_image` / `official_x_date` / `official_x_text` / `official_x_match_confidence`、および `official_x_attribution` メタデータ。
