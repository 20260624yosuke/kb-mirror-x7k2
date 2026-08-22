---
type: build
title: ヘレン一次情報モーションライブラリ v2.1 投影修正パイロット
created: 2026-07-22
sources:
  - gf2-helen-starlit-waltz-3d-reference-build
  - motion-browser-v21-launch-failure-2026-07-22
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-23
---

# ヘレン一次情報モーションライブラリ v2.1 投影修正パイロット

## 目的

ヘレン「星夜のワルツ」の公式 MMD モデルへ、ゲームローカルキャッシュから抽出したヘレン本人または本人候補の身体モーションを投影し、作画資料として閲覧できるライブラリを作る。

当初の狙いは「ヘレンのバストサイズ・衣装・体型に近い一次情報を、静止ポーズだけでなく実装済みモーションから参照すること」だった。動画制作やゲーム実行時の完全再現ではなく、イラスト資料として、角度・姿勢・胸・髪・衣装の動きを確認できる状態を目標にしている。

## 経緯

1. 2026-07-15 時点では、公式 MMD モデルを Blender で開き、静止資料・物理付き資料・PNG レンダーとして使える状態まで進めた。これは [[gf2-helen-starlit-waltz-3d-reference-build]] に記録済み。
2. その後、武田さんは「ヘレン本人に実装されていそうなモーションを抽出し、手元のヘレン MMD へ適用できないか」を確認した。録画ではなく、ローカルキャッシュ内の一次情報を使う方向へ移った。
3. 最初のパイロットでは、ロビー系の数本を Action として収録したが、見た目がほぼ同じに見える、T 字に近い、胸がねじれる、前髪や表示が不自然、Action の再生・切替が分かりにくいという問題が出た。これは会話上のユーザー指摘であり、当時の成果物は閲覧資料として不十分だった。
4. v1 では 9 カテゴリーの Blend と多数 Action を生成したが、武田さんの実見では「642件あると言われても見た目は9種類程度に見える」「下半身が動かない」「胸が動かない」と感じられた。原因候補は、同じ Avatar の一律適用、Transform パス解決不足、MMD 側に投影する骨数の少なさ、胸曲線・二次物理の扱い、閲覧 UI の分かりにくさだった。
5. v2 計画では、全候補 906 レコード、身体 761 レコード、確定 668 件、未確定 93 件、補助 145 件という母数を分け、見た目単位の代表・全差分・動画・Motion Browser を作る方針になった。
6. しかし v2 のブラウザー上で確認できる MMD は、H0171 などで胴体反転、服貫通、捻れが目立ち、武田さんから「元のドルフロのデータがそういうデータなのか、MMD変換ができていないのか」と確認が入った。
7. 結論は、ゲームから取れる一次曲線が曖昧なのではなく、取れている一次情報を MMD 骨格へ投影する変換器側が主なボトルネック、という判断になった。
8. そのため v2.1 では、抽出量を増やす前に、13 レコード / 19 閲覧 Action だけを対象に、Root、体幹、脚、胸、髪、衣装の投影を作り直すパイロットへ絞った。

## 実装方針

正本計画は `/Users/takedayousuke/llm-uploads/20260720-201624--ヘレンMMD投影修正-v2-1-変換器再設計-実装確定計画.md`。

主な方針は次の通り。

- 出力は `library-v2-fidelity/retarget-fix-pilot/` のみ。v1、既存 v2、ゲームキャッシュ、既存 Blend は変更しない。
- `Root_M` の回転を腰へ押し込まず、MMD の `センター` へ全身回転として渡す。
- 生成器と同じ計算式で検証しない。一次情報から固定した目標関節データを保存し、別工程で評価済み MMD 姿勢と照合する。
- 服・髪は Action ごとに独立 Blender プロセスで物理計算し、最終 Blend には live 物理ではなく焼き込み Action として保存する。
- 胸は MMD 物理代用ではなく、元 Avatar の左右胸の親座標差分を MMD 胸骨へ移す。ただしねじれや中央交差は不合格にする。
- 表情、家具、武器、小物、カメラ、Dorm 用変換プロファイル、残り全 761 件は今回対象外。
- ユーザー実機確認までは、全件処理へ進まない。

## 現在の成果物

出力ディレクトリ:

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/05_helen-motion-library/library-v2-fidelity/retarget-fix-pilot/`

主な成果物:

- `pilot/helen-motion-v2.1-representative-6.blend`
- `pilot/helen-motion-v2.1-pilot-19-actions.blend`
- `retarget-v2.1.sqlite`
- `targets/*.json.gz`
- `profiles/outfit-ssr0101.json`
- `profiles/base-ssr01.json`
- `previews/videos/` の 19 本動画
- `previews/poses/` の 114 枚画像
- `browser/` と `Motion Browser.app`
- `IMPLEMENTATION-STATUS.md`
- `USER-REVIEW.md`
- `reports/final-audit.json`

## 自動検証済み

`reports/final-audit.json` と `IMPLEMENTATION-STATUS.md` に基づく現在状態は、
`pilot-built-shape-pass-penetration-audit-unresolved-awaiting-user-review`。

通過している検証:

- 入力ハッシュ不変。
- 身体・胸の別プロセス 2 回生成。
- 衣装物理の別プロセス 2 回生成。
- 全 frame 形状検査。
- 胸左右交差 0 frame。
- 一次情報から固定投影目標への独立検査。
- 12,847 Binding の分類漏れ 0。
- 最終 Blend に 19 Action。
- 19 本動画、114 枚ポーズ画像。
- Motion Browser の API / カタログ / 動画 Range / 登録済み Action 起動要求の自動試験。
- 2026-07-22 に Motion Browser.app の LaunchServices 経由起動を修正し、API / トップページ / 動画 Range / 登録済み Action 起動要求の検証を通過。
- H0201 と H0542 は、元関節軌跡と MMD 結果の両方で別物として説明可能。

数値:

- Binding 数: 12,847。
- final Blend: `pilot/helen-motion-v2.1-pilot-19-actions.blend`。
- final Blend SHA-256: `1446c4e1e9f84546077870830a2fd92dd74e17c733759055a2f22cf2cd616ff6`。
- cold background open: 3.512 秒。
- 出力容量: 16.507 GiB。
- 作業時 SSD 空き: 510.31 GiB。

## 未完了・未検証

- 武田さんの Blender 実機確認は未完了。したがって、運用開始可能・全件展開可能とは扱わない。
- 貫通監査は未解決。MMD の衝突剛体が表示メッシュより大きいため、剛体ベースの重なり違反が、画面上の服・身体の貫通と一致しないケースがある。これは合格に書き換えず、`reports/penetration-summary.json` に失敗として残している。
- `Motion Browser.app` は 2026-07-22 に起動入口を修正済み。詳細は [[motion-browser-v21-launch-failure-2026-07-22]]。ただし前面ブラウザ表示そのものは自動操作せず、LaunchServices 経由のサーバー応答で確認した。
- 表情モーフは v2.1 対象外。曲線は `deferred-facial` として保持しているが、MMD には適用していない。
- 家具・武器・小物は出さないため、休憩室や射撃系では手や身体が空中の対象物に合うように見える可能性がある。
- 残り全 761 身体レコードへの展開は未実施。

## ユーザー確認の意味

次の確認は、原因調査ではなく、実装後の改善確認である。確認前に「完成」「全件処理可能」とは断定しない。

最低限確認する 6 本:

- H0052: 待機・感情、胸とマント。
- H0157: 休憩室、座り・下半身。
- H0171-S06: 旧版で最も崩れた日常イベント。
- H0215: 転倒、全身の大きな回転。
- H0223: 射撃、腕・体幹・下半身の連動。
- H0344: 走行、脚と Root の移動。

見るべき点:

- 6 本が同じポーズに見えないか。
- 下半身が元動作に合わせて動くか。
- 胸が動き、左右入れ替わりや中央ねじれがないか。
- 髪、マント、ドレスが伸びたり破裂したりしないか。
- Motion Browser の frame と Blender の frame が一致するか。
- Action 切替や再起動後に姿勢が前 Action から汚染されないか。

## 現在の判断

このパイロットは、抽出不足の問題ではなく MMD 投影変換器の問題を切り分けるための成果物である。

> [!warning] H0157の胸変形については再検討中
> 「抽出不足ではなく投影変換器が主な問題」という判断はパイロット全体には残すが、H0157の胸変形へはそのまま適用しない。2026-07-23の[[h0157-chest-mechanism-audit-history|再監査]]で、対象未特定477本、身体Mesh・頂点ウェイト、実行時計算式、処理順が未解決と確認されたため、H0157については `contested`（旧判断を再検討中）として扱う。

現時点で言えること:

- ゲーム側の一次曲線・Binding・Avatar 参照は相当量取得できている。
- v1 / v2 の「同じポーズに見える」「下半身が動かない」「胸が動かない」「H0171 が破綻する」は、主に MMD への投影、Root の扱い、体幹軸、二次物理、胸曲線の扱いに起因する。
- v2.1 は 19 Action のパイロットとしては自動検証の大半を通過している。
- ただし、貫通監査とユーザー実機確認が未完了なので、実用資料として合格とはまだ記録しない。

## H0157胸変形判断の変遷(2026-07-23)

- 旧判断: H0157を含む問題は主にMMD投影側にあり、取得済み一次曲線を正しく投影することが中心課題と考えた。
- 現行判断: H0157は1,011曲線値を取得済みだが、477本の対象階層、身体Mesh・頂点ウェイト、実行時の計算式・処理順が未解決。胸変形については抽出・認識が十分だったと断定しない。
- 旧判断は削除せず、H0157に限って再検討中であることを明示する。詳細は[[h0157-chest-mechanism-audit-history]]。

## Action Editor 切替バグの調査・修正(2026-07-22)

武田さんが `representative-6.blend` を Blender で開き、アクションエディターのドロップダウンで
Action を切り替えても同じモーションに見える不具合を報告した。

### 原因1: `use_nla = True` による自動退避

- `animation_data_create()` の既定値 `use_nla = True` のまま UI からドロップダウンで Action を
  切り替えると、Blender が直前の Action を NLA(ノンリニアアニメーション)トラックへ自動退避し、
  退避された Action が以後の選択より優先される。プログラム的な `ad.action = action` 代入では
  この自動退避は起きないため、ビルド時の自動検証では発覚しなかった。
- 診断で NLA トラックは 0 本、6 Action は互いに異なる `sample_hash`・キーフレーム数を持つことを
  確認済み → データ自体は破損していなかった。原因は UI 操作時の挙動のみ。
- 修正: 対象 5 Blend の armature `animation_data.use_nla` を `False` に変更、NLA トラックを掃除。
  ビルドスクリプト `blender_combine.py`・`helen-build-motion-v2-pilot.py` にも同じ修正を追加し、
  以後の再ビルドでも同じ不具合が再発しないようにした。
- 対象ファイル:
  - `pilot/helen-motion-v2.1-representative-6.blend`(優先、バックアップ `.bak-nla-fix` あり)
  - `pilot/helen-motion-v2.1-pilot-19-actions.blend`(優先、バックアップ `.bak-nla-fix` あり)
  - `05_helen-motion-library/pilot/helen-motion-library-pilot.blend`(v1)
  - `05_helen-motion-library/pilot/helen-motion-library-pilot-stable.blend`(v1)
  - `05_helen-motion-library/library-v2-fidelity/pilot/helen-motion-library-v2-pilot.blend`(v2)

### 原因2: メッシュが選択された状態でアクションを切り替えていた

`use_nla` 修正後も武田さんの画面では変化が見えなかった。実機画面を確認した結果、
アウトライナーで選択・アクティブになっていたのは **メッシュ**(`GirlsFrontline HelenSSR0101`、
見た目の3Dモデル本体)で、モーションを持つ **アーマチュア**(`_arm`、骨格)ではなかった。
ビルドスクリプトがアーマチュアを `hide_viewport = True` にしているため、3D画面クリックでは
選べない状態だった。

- 武田さんが自力で気づいた回避策: アウトライナーの階層を展開し、アーマチュアのアイコン(大の字の
  人型マーク)をクリックしてアクティブにすると、Action 切替がポーズへ反映された。
- LLM 側の恒久修正: `representative-6.blend` について、保存時にアーマチュアを
  `view_layer.objects.active` にし、選択状態にして保存し直した。次回開いたときから
  最初からアーマチュアが選択された状態になる。
- **未実施**: この「アクティブオブジェクトをアーマチュアにする」修正は `representative-6.blend`
  にしか適用していない。`pilot-19-actions.blend`・v1 2 本・v2 1 本の残り 4 ファイルと、
  ビルドスクリプト側(`blender_combine.py`・`helen-build-motion-v2-pilot.py`)には未反映。
  同じビルドスクリプトで生成されたファイルなので同じ問題を抱えている可能性が高い。

### 実機確認結果(2026-07-22、武田さん)

`representative-6.blend` で、アーマチュアをアクティブにした状態から Action Editor でモーションを
切り替えると、ポーズが反映されることを武田さんが実機で確認した。本人の言葉: 「完璧ではないが、
とりあえず実装されてはいる」。

- 確認できたこと: Action 切替そのものが機能する(実機確認済み)。
- 未確認・未特定: 「完璧ではない」の具体的な内容(見た目の細部か、操作性か、他の未確認項目かは
  本人から明言されていない)。次回、具体的にどこが気になったかを確認する余地がある。
- この確認は 6 Action 版(`representative-6.blend`)についてのみ。19 Action 版・v1・v2 の残り
  4 ファイルは未確認。

## 関連リンク

- [[gf2-helen-starlit-waltz-3d-reference-build]]
- [[motion-browser-v21-launch-failure-2026-07-22]]
- [[h0157-chest-mechanism-audit-history]]
