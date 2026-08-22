---
type: build
title: Sabrina 夏水着 中央部裁断再構成の試行
created: 2026-07-27
sources:
  - gf2-sabrina-summer-bikini-no-frill-reference-build
  - mmd-library-blender-import
status: superseded
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-27
---

# Sabrina 夏水着 中央部裁断再構成の試行

## 目的

`Sabrina_夏_水着_フリルなし参考.blend` を入力に、胸中央へ寄りすぎた布形状を裁断し、
左右カップを分離したうえで中央紐を独立追加し、**ビキニとして読める中央形状**へ寄せる
作画資料を作ることを狙った。

## 現在の状態

- 2026-07-27 にユーザーレビューで不採用となり、**プロジェクトは中止**。
- 自動検証は通ったが、形状の成立条件そのものを取り違えたため、採用記録には進めない。
- このページは「何を作ったか」よりも、「どこで判断を外したか」を残す失敗記録として扱う。

## 経緯

1. 出発点は [[gf2-sabrina-summer-bikini-no-frill-reference-build]]。胸フリルを除いた参考 Blend を入力に固定し、そこから中央部だけを再構成する計画へ進んだ。
2. 正本計画 `/Users/takedayousuke/llm-uploads/20260727-191340--Sabrina-水着中央部の裁断再構成計画.md` では、「左右カップを押し縮めず、中央へ突き出した部分を裁断して独立した中央紐で接続する」と定義した。
3. 実装では `ClothA` 内の左右カップ相当 484 面だけを元メッシュから `ONLY_FACE` で除去し、左右カップを別メッシュとして再生成した。中央紐も独立メッシュで追加した。
4. 生成物は `Sabrina_夏_水着_フリルなし参考_中央修正版.blend` と比較シート、診断画像、`build-report.json` まで出力した。
5. 自動検証では、入力 Blend の SHA 一致、元頂点不変、シェイプキー 67 維持、左右カップの UV/ウェイト保持、中央紐の閉メッシュ化、`BodySkin` との交差 0、独立再読込成功を通した。
6. しかし 2026-07-27 のユーザーレビューで、「こんなものはビキニと呼ばない」「今回の会話では、まずビキニとはどういう形状かを考えなければいけなかった」「成果物がゴミだったのは重い失敗」と評価され、中止が確定した。

## 失敗要因

## 1. 形状要件の定義不足

今回いちばん大きかった失敗は、**ビキニとして最低限どんな形状であるべきか**を、実装前に言語化せずに進めたこと。

- 中央で布がどう終わるべきか
- 左右カップがどこまで独立して見えるべきか
- 中央紐が「布の残骸」ではなく独立した接続として読めるべきか

この基準を先に固定せず、「中央へ突き出した部分を切って紐を足せば近づくはず」という局所手術として扱った。そのため、自動検証を満たしても、肝心の見た目がビキニとして成立しているかを担保できなかった。

## 2. 検証軸のずれ

自動検証は主に以下を見ていた。

- 元データを壊していないか
- UV、ウェイト、シェイプキーを保てているか
- 貫通や無効メッシュがないか

これらは必要条件ではあるが、**ビキニとして見えるか**の十分条件ではない。技術的に壊れていないことと、形状判断が正しいことを混同した。

## 3. 停止点の位置が遅かった

比較シートと診断画像は出したが、最初に問うべきだったのは「この中央処理方針で、そもそもビキニへ近づくのか」だった。つまり、再構成後の出来栄え確認より前に、**形状認識そのものの確認停止点**が必要だった。

## 実装したもの

- 出力 Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/01_blend/ドールズフロントライン2/Sabrina_夏_水着_フリルなし参考_中央修正版.blend`
- 再生成スクリプト:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/02_scripts/11-refine-sabrina-center-bikini.py`
- 実装レポート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini-center-refine/build-report.json`
- 比較シート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini-center-refine/renders/comparison-sheet.png`
- 診断シート:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/mmd-library/reports/sabrina-no-frill-bikini-center-refine/renders/diagnostic/sheet.png`

## 自動検証で確認したこと

- 入力 SHA-256 を `7e1dbe4cfb27ce0da98c86a45e7db51af47da810d8f602358f39b54648dbbec1` に固定し、実行前後で一致させた。
- 元メッシュでは対象カップ 484 面だけを除去し、元頂点と 67 個のシェイプキーを保持した。
- 再生成カップは左右で 232 面 + 233 面、評価時三角形は 236 + 236 と記録した。
- `UVMap`、`UV1`、`ClothA` 材質、スムーズ表示、ウェイト、アーマチュア接続を保持した。
- 中央紐は 40 頂点 / 34 面の閉メッシュで、`BodySkin` との交差は 0 と記録した。
- 保存後の独立再読込と最終監査も通った。

## 今回の記録として残す結論

- **メッシュが壊れていないこと**と、**目的の形状が成立していること**は別物。
- ビキニのように見た目の成立条件が主題の作業では、局所編集に入る前に、まず形状要件を短く固定する必要がある。
- 今回はそこを外したまま進めたため、自動検証付きの成果物でも不採用になった。

## 関連リンク

- [[gf2-sabrina-summer-bikini-no-frill-reference-build]]
- [[mmd-library-blender-import]]
