---
type: build
title: ヘレン SSR0101 ショートパンツ参考衣装化
created: 2026-07-27
sources:
  - gf2-helen-ssr0101-short-outfit-plan-2026-07-27
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-27
---

# ヘレン SSR0101 ショートパンツ参考衣装化

## 目的

`ヘレン_SSR0101_礼服_930.blend` を元に、参照画像
`gfl2___helen_s002_by_deliquecent_skull_dkpk8wj-pre.png` に寄せた
**ショートパンツ丈の作画資料用衣装**を、元 Blend を壊さず別成果物として作る。

ここでの目的は公式衣装の完全再現ではない。公式 MMD にその衣装一式が無いため、
既存ヘレンの上半身と別モデルの下衣構造を組み合わせ、背面や側面の不足は最小限の補助で埋める
「参考用の創作資料」を作ることだった。

## 現在の状態

- 最終成果物 `01_blend/ドールズフロントライン2/ヘレン_SSR0101_ショートパンツ参考.blend` を作成済み。
- 再生成スクリプト `02_scripts/07-build-helen-short-outfit.py` と検査スクリプト `02_scripts/08-validate-helen-short-outfit.py` を追加済み。
- 中立5視点、代表3モーション `H0157` / `H0176` / `H0705` の自動検査とレンダー確認を完了。
- 2026-07-27 に武田さんが成果物を確認し、「妥協できます」と判断した。よって**作画資料としては運用開始可能**と記録する。

## 経緯

1. 出発点は、`ヘレン_SSR0101_礼服_930.blend` の衣装を、添付参照画像のような短めのショートパンツ衣装へ変えられるかという相談だった。
2. 初回の判断では、「公式 MMD に目的衣装が丸ごと入っているわけではないので、単純な差し替えではなく創作的な移植作業になる」が、Blender 上で既存衣装コードを流用しつつドナー衣装を合わせる方向なら実現可能性は高いと整理した。
3. その後、計画書 `/Users/takedayousuke/llm-uploads/20260727-001858--ヘレン-SSR0101-ショートパンツ衣装化.md` をレビューし、Welrod のショートパンツ構造を第一候補にする実装計画を確定した。正本の要約は [[gf2-helen-ssr0101-short-outfit-plan-2026-07-27]]。
4. 実装では、ヘレンの既存スカートを短丈の外層として残しつつ、Welrod の `Cth1-Pants` を体格に合わせて変形・移植した。
5. 途中試行で、Welrod 側の側面開口がそのまま出ると参考資料として露出が強すぎることが分かったため、trial-03 で充填面(`Fill`)と被覆用プロキシ(`CoverageProxy`)を追加した。
6. 代表3モーションと中立5視点の検証まで通したうえで成果物を提示し、武田さんが「妥協できます」と判断したため、この時点を採用記録とする。

## 実装方針

- ベースはヘレンの `P1-Cloth-Skirt`。腰接続部と上側のシルエットは残し、長い裾を落として短丈外層にした。
- ドナーは `Welrod_デフォルト.blend` の `Cth1-Pants`。閉じたショートパンツ形状を使い、ヘレン体格へ合わせて変形した。
- ウェイトは Welrod 由来をそのまま使わず、ヘレンの `BodySkin` と `P1-Cloth-Underwear` から転送した。
- 参照画像に無い背面柄や細部装飾は創作しない。足りない被覆だけを最小限の補助メッシュで足す。
- 元 Blend と入力ファイル群は不変のまま残し、別 Blend と検証レポートだけを追加する。

## 実装したもの

- 出力 Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/01_blend/ドールズフロントライン2/ヘレン_SSR0101_ショートパンツ参考.blend`
- 再生成スクリプト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/02_scripts/07-build-helen-short-outfit.py`
- 検査スクリプト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/02_scripts/08-validate-helen-short-outfit.py`
- 最終検証レポート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/helen-short-outfit/final-validation.json`
- 中立5視点シート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/helen-short-outfit/trials/trial-03/ヘレン_SSR0101_ショートパンツ参考__neutral-5view.png`
- モーション確認シート:
  `reports/helen-short-outfit/final-motions/`
- Finder 用アイコン:
  `03_output/icons/ドールズフロントライン2/ヘレン_SSR0101_ショートパンツ参考.png`
- ライブラリ用3面シート:
  `03_output/sheets/ドールズフロントライン2/ヘレン_SSR0101_ショートパンツ参考.png`

## 生成した衣装オブジェクト

- `Helen_ShortDress_Outer`: 1,619 頂点 / 2,969 面
- `Helen_ShortDress_Fill`: 841 頂点 / 1,178 面
- `Helen_Shorts_Underlayer`: 863 頂点 / 1,225 面
- `Helen_Shorts_CoverageProxy`: 1,659 頂点 / 2,216 面

`Outer` はヘレン既存衣装の短丈化、`Underlayer` は Welrod 由来のパンツ本体、
`Fill` と `CoverageProxy` は側面や股まわりの不足被覆を埋めるための補助である。

## 自動検証で確認したこと

- 入力ハッシュは不変:
  source `d973555a3f94cf12e9461134d4aff0dc8a91b429711cdc43f4f2ae1ab973c817`
  donor `33a109c09e890c0f46af96f523135387b885d4958d2b8842a7ae5ea13ac72df8`
  reference `cb6bb3733fcb3501e4e2109e3fcbd6f71f1d4be74b7a53df1d6535e85987cb6d`
- 4オブジェクトとも未ウェイト頂点0、未知ボーングループ0、最大4ウェイト、非有限座標0。
- ボーン数は432。欠損画像0。保存 Action 0。
- `.blend1` は生成されていない。
- 最終 Blend ハッシュは `2a4e9864ddf8a1f9ee598499446eb5953b0779f5b531d96cd40dc7064e64fd46`。
- trial-01 / trial-02 / trial-03 を保存し、trial-03 の前段階も `pre-fallback` として退避してある。

## モーション検証

代表動作として次の3本を EEVEE で確認した。

- `H0157_CURRENT_BEST_FULL_ACTION`
- `BED-ACT_H0176_HelenSSR0101_Cloth_Before__ssr0101__SRC`
- `BED-ACT_H0705_Lobby_HelenDorm_Cookunhappy_Start__ssr0101__SRC`

各モーションは3フレーム x 3視点で確認し、巨大な穴、側面の大きな開口、破綻した引き伸ばしが
無いことを基準に通した。ここでの合格は**イラスト資料としての妥協可能ライン**であり、
ゲーム実装相当の完全品質を意味しない。

## 妥協した点と限界

- 公式衣装の完全再現ではなく、既存ヘレン衣装と Welrod パンツを組み合わせた創作補完である。
- 側面や背面の一部は `Fill` / `CoverageProxy` で埋めており、衣装設計として厳密に正しい構造ではない。
- 陰影やレイヤー感は資料用途としては読めるが、量感表現は本制作向けの最終モデル水準ではない。
- 17モーション全件保証、PMX への再輸出、再配布用の整理は対象外のまま。

## 関連リンク

- [[gf2-helen-ssr0101-short-outfit-plan-2026-07-27]]
- [[mmd-library-blender-import]]
- [[gf2-helen-rest-room-motion-v22]]
