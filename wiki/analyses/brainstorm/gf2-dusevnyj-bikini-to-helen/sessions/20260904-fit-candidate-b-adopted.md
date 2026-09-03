---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-04
---

# 2026-09-04 セッション記録 ─ 適合を候補B へ差し替えて成果物を作り直した

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
実行記録（コマンドと生の出力）:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-20260904-fit-candidate-b.txt`

## 武田さんの判断（本人の選択）

引き継ぎ書 §1 で 2026-09-03 から未回答だった「候補B と候補A のどちらを成果物に入れるか」に、
**「候補B」**と回答（AskUserQuestion の承認カード・3択、それぞれ「選ぶと失うもの」つき）。

## やったこと

1. 3案を実測で再現（引き継ぎ書の記載値と一致・合格線は動かしていない）
2. 退避 → 候補Bで適合 → 原本の位相へ戻す → 材料フォルダ → Blend → probe
3. `build-exception.json` の `accepted_fails` を実測に合わせて 4件 → 2件へ書き替え（W6）
4. 検査を全部＋変異試験を全部再実行

## 結果

判定の不合格 **4件 → 2件**（G4b深い / G9a厚み）。検査の合否の顔ぶれは差し替え前と同一。

**手元でどう変わるか（3点セット）**
- 良くなった: 密着 69.2→92.0 ／ 胸の中央の距離 6.33→5.33（合格へ）／ 二面角 1.156→1.037（合格へ）
  ／ めり込み 19.7→8.5 ／ 肩ひもの厚み 2.18→0.91
- **失ったもの①**: カップの厚みが 1.824→0.664。今度は**平たすぎる**側へ外れた。
  胸が押さえつけられて見える可能性。**目で見た確認はしていない（未確認）。**
- **失ったもの②（差し替えで新たに悪化）**: W7 首のひもの先端が **16.6mm 下がった**
  （ヘレンの Neck_M より +7.3mm → −9.3mm、ドナー比の差 21.5→38.0mm・上限 20.0）。
  上衣をまるごと1つの剛体として置く副作用。首の後ろの結び目が原作より下がって見える可能性。
  **目で見た確認はしていない（未確認）。**
- **戻せるか**: 戻せる。`blends/swimsuit/_bak-20260904/` と
  `output/gf2-helen-swimsuit/visible-set-swimsuit.json.bak-20260904` から戻し、
  適合を既定（旗なし）で走らせ直す。

## 訂正1件（引き継ぎ書）

引き継ぎ書 §2 の「Blend … sha256 9c8da510…」は **Blend ファイルのバイト列の sha256 ではなく**、
`ce_build_blend.py` が出す `canonical_manifest_sha256`（場面の指紋）だった。
同じ名前で書いてあると「成果物がすり替わった」と誤読するため訂正した。成果物は改変されていなかった。

## 新しく最優先になった残作業

- カップの厚み 0.664 を 0.95〜1.05 へ戻す（崩しているのは解く処理。回数を合格線に合わせて
  選ぶのは禁止）
- W7 首のひもの先端 −9.3mm（まるごと剛体に首まわりを合わせる項が無い）
