---
type: concept
name: 透過光(SSS)
aliases: [transmitted light, subsurface scattering, SSS, サブサーフェススキャタリング, 表面下散乱]
tags: [saturation, color, light, sss, ye-jji]
sources: [coloso-ye-jji-ch07-color-basic, coloso-ye-jji-ch15-shadow-area, coloso-ye-jji-ch17-shadow-detail, coloso-ye-jji-ch18-hair-eye]
---

# 透過光(SSS)

## 定義

光が **半透明な物質** を通過するとき、物体内部で散乱・反射し、別の地点から出ていく現象 = サブサーフェススキャタリング (Subsurface Scattering, SSS)。出ていく光は **物体の固有色で高彩度** を帯びる。耳・髪のエッジ・指先・布のエッジ・葉などで観察される。[[saido-no-3-points|高彩度 3 ポイント]] の第 1 番目。

## 高彩度 3 ポイントとの位置づけ

3 ポイントの中で **物質依存・空間局所** な現象。逆光や薄い面に限定されるが、適用箇所の彩度ピークは最も鋭くなる。

- **★ PDF p3 の適用範囲限定**: 「SSS は、ある程度密度の低い物質で見られる現象で、密度の高い金属や石ではほとんど見られない」 — 本文には無い情報。**SSS = 薄い半透明物質固有の現象**(肌・葉・布・髪)

## 視覚的特徴 / 描写ポイント

- 出口の光は **物体の固有色 × 高彩度 × 高明度**(逆光時の耳の赤、髪先のオレンジ)
- 厚みのある部分は SSS が起きず、薄い部分でのみピークが現れる
- ch07 PDF 例: スーツのリネン布 / ハスの葉(光が透ける)
- ch17 例: **耳の透過光** — 厚みのある部分はピンクの高彩度で明るく、厚みが大きい部分は暗いまま
- ch18 例: **髪先の透過光** — 空色 + ピンクの SSS

## ye_jji 流の扱い方

- ch07 実習(SD イラスト)で **透過光 → 明暗境界 → 同系色光** の順で塗る
- 「薄い部分だけより鮮やかに透過光を描写していく」(本文 8:47)
- 同じ髪でも **より薄い部分はより高明度** で(8:08)
- シワで重なって厚くなった部分は透過光が弱くなる(9:00)
- 着彩工程としては ch15 「透過光まで」が最終段階(暗部塗りの最後で SSS を載せる)

## 関連

- [[saido-no-3-points]] — 親概念
- [[han-tou-mei-byou-sha]] — SSS は半透明描写の典型現象
- [[mei-an-kyoukai-saido]] — 兄弟ポイント 2
- [[fresnel-reflection]] — 薄い部分・縁での反射・透過の混在
- [[shadow-edge-high-saturation]] — Nekojira 流の SSS 集約テクニック

## 出典

- [[coloso-ye-jji-ch07-color-basic]] — 高彩度 3 ポイントの理論定義(メイン)
- [[coloso-ye-jji-ch15-shadow-area]] — 着彩工程の最終段で「透過光まで」
- [[coloso-ye-jji-ch17-shadow-detail]] — 耳の透過光実装
- [[coloso-ye-jji-ch18-hair-eye]] — 髪先の透過光実装
- `raw/_coloso/01_coloso_ye_jji/c07_要約ノート.pdf` p3 — SSS の定義 + 適用範囲限定 + スーツ / ハスの例画像
