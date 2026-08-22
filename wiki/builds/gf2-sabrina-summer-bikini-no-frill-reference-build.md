---
type: build
title: Sabrina 夏水着 フリルなし参考 Blend
created: 2026-07-27
sources:
  - mmd-library-blender-import
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-27
---

# Sabrina 夏水着 フリルなし参考 Blend

## 目的

`Sabrina_夏_水着.blend` を元に、胸フリルだけを外した**作画資料用の派生 Blend**を作る。

目的は高品質な衣装改造ではない。ビキニの輪郭、ラッピングライン、首から胸へ下りる紐、
胸下端のつながりを、公式モデル由来の1次資料として観察できる状態にすることだった。

## 現在の状態

- 最終成果物 `01_blend/ドールズフロントライン2/Sabrina_夏_水着_フリルなし参考.blend` を作成済み。
- 再生成スクリプト `02_scripts/10-build-sabrina-no-frill-bikini.py` を追加済み。
- `reports/sabrina-no-frill-bikini/build-report.json` に、削除前後の面数、ボーン数、シェイプキー数、入力ハッシュ、確認画像パスを固定した。
- 2026-07-27 に武田さんが成果物を確認し、「問題ない」と判断した。よって**作画資料として運用開始可能**と記録する。

## 経緯

1. 出発点は、`Sabrina_夏_水着.blend` の胸フリルを除いた状態を、ビキニの形状観察用資料として使えるかという相談だった。
2. 事前確認では、対象メッシュは `GirlsFrontline SabrinaSummer Ver1.1_mesh` の単一メッシュ、ボーン266、シェイプキー67であり、胸フリル候補は `ClothB` 材質にまとまっていることを確認した。
3. 相談段階では、テクスチャ品質や最終仕上げではなく、**フリルを除いたビキニのシルエットが読めるか**が完成条件だと整理した。除去範囲は胸フリルのみ、成果物は派生 Blend、検証は中立360度確認で合意した。
4. その後、計画書 `/Users/takedayousuke/llm-uploads/20260727-094043--Sabrina-夏-水着-フリルなし参考-Blend-作成計画.md` をレビューし、「`ClothA / ClothB` を広く触る案は危険で、第一実装は `ClothB` 面削除に固定すべき」と差し替えた。
5. 正本計画 `/Users/takedayousuke/llm-uploads/20260727-094849--Sabrina-夏-水着-フリルなし参考-Blend-実装計画.md` に従い、`ClothB` 面だけを消す専用スクリプトを実装した。
6. 初回実装では `bpy.ops.mesh.delete(type="FACE")` が孤立頂点も減らし、頂点数保持条件に反したため停止した。Blender 4.5.11 LTS では `ONLY_FACE` へ変更する必要があると確認し、削除方式を修正して再実行した。
7. 修正版では、元 Blend のハッシュ不変、`ClothB` 995面→0面、`ClothA` 9425面不変、頂点数30821不変、ボーン266、シェイプキー67、欠損画像0、`.blend1` 0 を通したうえで派生 Blend と確認画像を保存した。
8. 胸部アップ4方向と全身5方向の画像を確認し、胸フリルが消え、赤いカップ本体・首紐・胸中央紐・ボトム・腰紐が残っていることを確認した。
9. 2026-07-27 に武田さんが成果物を確認し、「問題ない」と判断したため、この時点を採用記録とする。

## 実装方針

- 対象は `GirlsFrontline SabrinaSummer Ver1.1_mesh` と `GirlsFrontline SabrinaSummer Ver1.1_arm`。
- `ClothB` が胸部付近の995面・8連結成分であることを削除前ガードとした。
- `ClothA` は赤いカップ本体、首紐、胸中央紐、ボトムを含むため変更しない。
- シェイプキー整合を崩さないことを優先し、頂点追加、頂点削除、補助面追加、UV再作成、アーマチュア変更は行わない。
- Blender 4.5.11 LTS では `FACE` 削除が孤立頂点まで減らすため、実装は `ONLY_FACE` に固定した。
- 元 Blend は上書きせず、`.blend1` も発生させない。

## 実装したもの

- 出力 Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/01_blend/ドールズフロントライン2/Sabrina_夏_水着_フリルなし参考.blend`
- 再生成スクリプト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/02_scripts/10-build-sabrina-no-frill-bikini.py`
- 実装レポート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini/build-report.json`
- 全身5方向シート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini/renders/Sabrina_夏_水着_フリルなし参考__full-5view.png`
- 胸部アップ4方向シート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini/renders/Sabrina_夏_水着_フリルなし参考__bust-4view.png`

## 自動検証で確認したこと

- 入力 Blend の SHA-256 は実行前後で一致:
  `fef85a6b3efd0c9e1b52824a376282da7094bfa0f53a9b4ce5299c7700084511`
- 削除前:
  30,821頂点 / 43,375面 / ボーン266 / シェイプキー67 / `ClothB` 995面 / `ClothA` 9,425面
- 削除後:
  30,821頂点 / 42,380面 / ボーン266 / シェイプキー67 / `ClothB` 0面 / `ClothA` 9,425面
- 派生 Blend を再読込しても同じ値を維持。
- 欠損画像0。`.blend1` は生成されていない。
- 独立再読込でも `ClothB` 0面、`ClothA` 9,425面、頂点30,821、面42,380、ボーン266、シェイプキー67を再確認した。

## 妥協した点と限界

- 実装はフリル除去だけであり、胸下端や布厚みを理想形へ彫り直したわけではない。
- 高品質な衣装改造、ゲーム実装相当の自然さ、物理動作の検証は対象外。
- `ClothB` を完全に落とす方式なので、もし元モデル側でフリルと一体化した陰影表現が必要な用途には向かない。
- 今回の合格は**中立姿勢の作画資料として問題ない**という意味であり、完成モデル品質の保証ではない。

## 関連リンク

- [[mmd-library-blender-import]]
- [[gf2-helen-ssr0101-short-outfit-reference-build]]
- [[gf2-sabrina-summer-bikini-center-refine-attempt]]
