---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-30
---

# 工程O0 の結果 — 既存の道具をそのまま1回通した（2026-08-30）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

## 結論

**通った。** Helen の中間データを一切改変せずに `ce_build_blend.py` へ通し、Blend が1つできた。
「Blend を作る道具が流用できる」は推測のままだったが、これで**事実になった**。

## 実行したコマンド（そのまま再現できる形）

```
"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender" \
  -b --python "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/scripts/ce_build_blend.py" -- \
  --intermediate "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/intermediate/Helen.HelenSSR01" \
  --out "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/o0-verify/Helen-HelenSSR01-o0.blend" \
  --build-log "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/o0-verify/build-log-o0.json"
```

- Blender は **4.5.11 LTS**（2026-06-23 ビルド）。`/Applications` にも PATH にも無く、
  実体は `02_ソフトウェア/Blender.app`。上の `-b --python <スクリプト> --` の書き方で動く。
- **スクリプト冒頭の docstring の引数は古い**（`--char/--variant` と書いてあるが、実際の
  argparse は `--intermediate/--out/--build-log`。ce_build_blend.py:504-507）。実際の引数が正しい。
- 所要時間は約1分。

## できたもの

- `blends/o0-verify/Helen-HelenSSR01-o0.blend` — 66,448,676 バイト
- `blends/o0-verify/build-log-o0.json` / `build-log-o0.canonical.json` / `o0-stdout.txt`
- 既存の `blends/*.blend` は**上書きしていない**（新しいフォルダ `o0-verify/` へ出力）。
- 原本 `intermediate/` は読んだだけで書き換えていない。

## ログの中身（エラーの有無）

**止まるようなエラーは無い。**

- `matrix_set_errors: []` / `bones_missing_rest: []` / `gaps: []`
- `self_check.all_face_counts_match_source: true`（面数が元データと一致）
- `post_chain.ok: true`（ノード172）
- アーマチュア 375 ボーン、rest 行列 375 本すべて書き込み済み
- メッシュオブジェクト 75

**記録しておく2点（エラーではないが事実として残す）**

1. `unresolved_parent_count: 160` — 親子関係を prefab のパス由来でしか埋めない規約のため、
   160 本は親が未解決のまま。スクリプトの設計どおりの動作（推定で埋めない）。
2. 材質が付かなかったオブジェクトが 4 件（`slot_source: none` → `no_material_0`）。
   `MP443`（拳銃）×2・`flag_lod0_Effect`・`cloth3_trans_lod0`。
   内訳は `naming_match` 70 / `shared_body_atlas` 1 / `none` 4。
   **水着の対象部位ではない**（銃・旗エフェクト・非表示の透過布）。

## 保存後に開き直して確かめた（作りっぱなしにしていない）

`-b <blend> --python` で読み直した実測値:

- オブジェクト 80（メッシュ 75 + アーマチュア 1 + その他4）
- 頂点 421,408 / 面 224,300 / ボーン 375
- 既定で表示されるメッシュ 58

## 完成条件の判定

1. Blend が1つ新しく保存され、ログにエラーが無いこと → **満たした**
2. 実行コマンドと結果が記録として残っていること → **このページ**
3. 通らなかった場合も可（可否を知る工程） → 通ったので該当なし

## 未確認のまま残ること

- **見た目は確認していない。** 中身の数（オブジェクト・頂点・ボーン）が揃っていることだけを
  確かめた。絵として正しいかは武田さんが Blender で開かないと分からない。
- この Blend は **Helen の原作のまま**で、水着は入っていない（O0 は水着を混ぜない工程）。
