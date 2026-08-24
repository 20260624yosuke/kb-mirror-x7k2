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
