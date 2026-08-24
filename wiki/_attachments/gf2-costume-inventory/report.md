# GF2 キャラ×所持衣装(スキン) 一覧レポート

作成日: 2026-08-24 / 作成者: opencode (調査セッション)
正本データ: [table.json](table.json)

## 0. このレポートの読み方

全主張に確度ラベルを付けている。

| ラベル | 意味 |
|---|---|
| **source-backed** | ローカル実データ(bundle カタログ/台帳/テーブル bytes)で直接確認 |
| **inferred** | データのパターンからの推定 |
| **ai-hypothesis** | AI 仮説。検証未実施 |
| **未確認** | 確認できなかった |

web 由来の情報(公式サイトのキャラ名対応)とデータ由来の情報は、節を分けて記載する。
**重要な前提(source-backed)**: 名前ヒット ≠ 完全在庫。HelenSSR0101 は名前ヒットがあるのに
材質(material)0件だった実測例がある。台帳 status が `complete_shape` でも抽出可否は別途
オブジェクト単位で確認する必要がある。

---

## 1. データ由来の情報(ローカル実データ)

### 1.1 キャラ数とバリアント総数(source-backed)

- GunData.bytes(ゲーム内テーブル)から戦術人形 59 体のデフォルトモデルコード
  (`<キャラ名><SSR/SR>01` 形)を確認。
- model-name-candidates.json(888 候補)のうち、キャラ衣装バリアント相当は 174 コード
  (RX_/CF_/MG 接頭辞の特殊形を除くプレイアブル分)。
- 本レポートの table.json は **56 キャラ・125 バリアント行**(代替スキン 69 コードを含む)。

### 1.2 命名規則(inferred — Helen での陽性対照 PASS 実績あり)

`<キャラ名><レア度SSR/SR><01><スキン番号2桁>`

- 例: `HelenSSR01`=デフォルト, `HelenSSR0101`=スキン1
- 台帳の diff-Helen-HelenSSR01.json は `verdict: PASS`(Blender 再現との突合)であり、
  この命名規則に基づくバリアント分解は Helen について実証済み。

### 1.3 代替スキンを持つキャラ(source-backed: コード自体は実在、ジャンル分けは inferred)

69 コード。例(全文は table.json):

- 多め: Clukay 4種(SSR0101〜0104), Nemesis 3种(SR0103〜0105), Groza 3种(SR0101/0103/0105),
  Macqiato 3种(SSR0101〜0103)
- 2種: Florence, Lenna, Leva, Nikketa, Peritya, Robella, Springfield, Suomi, Tololo,
  Vector, Vepley, Voymastina, Charolic, Colphne
- 1種: Andoris, Basti, Biyoca, Centaureissi, Cheeta, Cheyanne, Daiyan, Dusevnyj, Faye,
  Helen, Jiangyu, Lind, Liushih, Loreley, Lotta, Mosinnagant, Nagant, Qiongjiu, Sabrina,
  Sakura, Sextans, Sharkry, Soppo, Suomi ほか

**衣装ジャンル推定について**: 「01=デフォルト/02以降=代替」という構造は inferred。
「水着」「私服」等の具体ジャンルは**データ上未確認**。唯一の手がかりとして
ClothesDutyData.bytes 内に `Img_SkinType_Special_Swimsuit` / `_Romantic` /
`_Special_MacqiatoSSR02` / `_Kabutack` の文字列がある(source-backed: 文字列の存在のみ。
どのバリアントがどのタイプかは未確認)。

### 1.4 特殊形のコード(source-backed: 文字列実在 / 意味は inferred)

- `BattleVoymastinaSSR01/0101/0102`, `NemesisGnosisSSR01` — 戦闘形態・覚醒形態と思われる
  別モデル(ai-hypothesis)。通常立ち絵用の衣装資料とは別物の可能性。
- `RX_<キャラ>SSR01...` — RX_ 接頭辞付き 57 コード。用途未確認(おそらく別表示系。ai-hypothesis)。
- `<キャラ>Dorm` 族 — 寮(Dorm/Aimo)シーン用の軽量アセット群。大半が texture のみ partial
  status(source-backed)。寮シーンの見た目再現には不足の可能性。
- `CF_Groza`, `CF_Sextans` 等 — CF_ 接頭辞(意味未確認)。
- `MacqiatoSSR02` が ClothesDutyData.bytes に存在するが、bundle ヒット・ModelConfigData 両方に
  **不在**(source-backed)。配信前データ or 未取得アップデートの可能性 → 要確認扱い。

### 1.5 台帳族statusの注意点(source-backed)

- 211 族中、`complete_shape` は一部(例: Helen mesh138/material8, Clukay mesh409/material52)。
- 材質0件でも名前ヒットのある族が複数(BattleVoymastina 等)。
- Alva / Phaetusa / Qiuhua / YooHee の4体は、needle スキャンが**族名のみ**で実施されており
  バリアント単位のヒット数が未計測(table.json では inferred 扱い)。台帳上は族levelで
  complete_shape を確認。

### 1.6 LocalCache 軽走査の結果(source-backed)

- セーブデータ側: `GameConfig.cfg` 内に `Gun_Wedding_Skin_Store_Key/clothesId:False` の1キーを
  確認。所持スキンの実リストはこの軽走査では発見できず(大規模走査は実施せず)。
- Table 配下に衣装関連テーブルを複数確認: `BattleClothesData.bytes`(中身は
  BattleVoymastinaSSR01/0102 のみ), `SkinTypeData.bytes`, `GachaClothesRewardData.bytes`,
  `EventFreeSkin*Data.bytes`, `LoungeClothesData.bytes` 等。**これらを解析すれば
  バリアント↔衣装ジャンル↔入手経路の正式対応が取れる可能性が高い(次の一手)**。

---

## 2. web 由来の情報(2026-08-24 取得)

出典: 公式サイト https://gf2.haoplay.com/jp/roleinfo/allroles.html (日本語版)。
内部コード名→日本語名の対応は以下の通り。**公式サイトは現在稼働中の JP サーバ roster を
映すため、手元ローカルデータ(古い/新しい可能性)と一致するとは限らない。**

| 内部名 | 日本語名 | 確度 |
|---|---|---|
| Suomi | スオミ | web由来 |
| Ullrid | ウルリド | web由来 |
| Charolic | キャロリック | web由来 |
| Vepley | ヴェプリー | web由来 |
| Groza | グローザ | web由来 |
| Nemesis | ネメシス | web由来 |
| Sharkry | シャークリー | web由来 |
| Qiongjiu | 瓊玖 | web由来 |
| Peritya | ペリティア | web由来 |
| Tololo | トロロ | web由来 |
| Lotta | ロッタ | web由来 |
| Papasha | ペーペーシャ | web由来 |
| Macqiato | マキアート | web由来 |
| Clukay | クルカイ | web由来 |
| Nikketa | ニキータ | web由来 |
| Lind | リンド | web由来 |
| Leva | リヴァ | web由来 |
| Robella | ロベラ | web由来 |
| Lewis | ルイス | web由来 |
| Voymastina | ヴォイマスティナ | web由来 |
| Phaetusa | パエトゥーサ | web由来 |
| Cheyanne | シャイアン | web由来 |
| Liushih | 劉蒔 | web由来 |
| Harpsy | ハープシー | web由来 |
| Sakura | サクラ | web由来 |
| Biyoca | ビヨーカ | web由来 |
| Qiuhua | 秋樺 | web由来 |
| Faye | 緋 | web由来(同一性は表記一致からの推定=inferred) |
| Zhaohui | 朝暉 | web由来 |
| Daiyan | 黛煙 | web由来 |
| Loreley | ローレライ | web由来 |
| Basti | バスティ | web由来 |
| Alva | アルヴァ | web由来 |
| Balthilde | バチルダ | web由来 |
| Lenna | レナ | web由来(Lene との混同注意) |
| Florence | フローレンス | web由来 |
| Jiangyu | 絳雨 | web由来 |
| Springfield | スプリングフィールド | web由来 |
| YooHee | 幼熙 | web由来 |
| Vector | ヴェクター | web由来 |
| Mishty | ミシュティ | web由来 |
| Centaureissi | センタウレイシー | web由来 |
| Dusevnyj | ドゥシェーヴヌイ | web由来 |
| Mosinnagant | モシン・ナガン | web由来 |
| Ksenia | クシーニヤ | web由来 |
| Colphne | コルフェン | web由来 |
| Cheeta | チータ | web由来 |
| Nagant | ナガン | web由来 |
| Helen | ヘレン | web由来 |
| Andoris | アンドリス | web由来 |
| Peri | ペリー | web由来 |
| Sabrina | サブリナ | web由来 |
| Sextans | セクスタンス | web由来 |
| Littara | リッタラ | web由来 |

---

## 3. 分からなかったこと(正直な列挙)

1. **スキン番号↔ゲーム内表示名の対応は全部 未確認**(例: HelenSSR0101 = 「Starlit Waltz」
   という対応は、フォルダ名 gf2-helen-starlit-waltz からの示唆があるだけで、データ上では未確認)。
2. 衣装ジャンル(水着/私服/ウェディング等)の個別判定は 全部 未確認。Swimsuit/Romantic 等の
   SkinType 分類の存在のみ確認。
3. Lene / Mityl / Soppo / Welrod / OTs14 / BattleVoymastina / NemesisGnosis の**日本語名は
   公式サイト一覧に見つからず 未確認**(サイトは現行サーバ roster のため、未実装or旧表記の
   可能性)。
4. RX_ 接頭辞・CF_ 接頭辞・MGDrone の意味は 未確認。
5. Alva/Phaetusa/Qiuhua/YooHee のバリアント単位ヒット数は 未計測(needle スキャンが族名のみ)。
6. セーブデータ内の所持スキンリスト(clothesId の実値)は 未発見。今回の軽走査では
   GameConfig.cfg のキー1本のみ。
7. MacqiatoSSR02 は設定文字列にのみ存在し bundle 未確認 → 手元データに含まれない可能性。
8. バリアントコード実在(名前ヒット)と「3D完全在庫(mesh+tex+mat揃い)」は別問題。
   HelenSSR0101 の材質0件実例があるため、**各バリアントの抽出可否は台帳だけでは断定できない**。

## 4. 次に必要な追加調査の提案

1. **Table/*.bytes の本格解析**(最優先): `SkinTypeData.bytes`, `ClothesDutyData.bytes`,
   `GunData.bytes` のバイナリ構造(f79/f91 方式の応用)を解けば、バリアントコード↔衣装名↔
   タイプの正式対応が取れるはず。ハルシネーション無しの正本マップになる。
2. needle スキャンの補完: Alva/Phaetusa/Qiuhua/YooHee を variant 単位で再スキャン
   (既存スクリプトの needle リストに追加するだけの想定)。
3. 各バリアントの material/tex 実カウント: char-inventory は族単位 attribution のため、
   バリアント単位の object-level 集計(char-inventory-objects.json の活用)で
   「抽出可能な衣装」の最終一覧を作る。
4. セーブデータ/アップデート差分: cache catalog(26111)と app catalog(21899)の版ズレ確認。
   MacqiatoSSR02 等の新規スキンが cache 側のみに来ていないか。
5. 別セッション(gf2-char-extract 稼働中)の完了後に、その成果物と本一覧を突合。

## 5. ファイル

- 正本テーブル: [table.json](table.json)(56キャラ×125バリアント行、確度ラベル付き)
- 中間データ: [intermediate.json](intermediate.json)
