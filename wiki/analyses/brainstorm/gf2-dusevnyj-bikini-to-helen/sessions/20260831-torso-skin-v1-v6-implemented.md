---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-31
---

# 胴体を素肌の上半身に確定し、検査 V1〜V6 を作って作り直した（2026-08-31）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

承認: 親メモ「実装への申し送り」の【最優先・2026-08-31 実行の承認】。
実行記録: `output/gf2-helen-swimsuit/run-20260831-torso-skin-v1-v6.txt`。
説明ページ: `wiki/_attachments/helen-swimsuit-status/20260831-torso-skin-rebuilt.html`。

## 結果

| 完成条件 | 状態 |
|---|---|
| 1. 検査6本を作る（成果物の数値を読む） | 済 `tools/swimsuit_visible_checks.py` |
| 2. 6本すべてに変異試験 | 済（6種すべて検出 ＋ V4 は別データで検出力を確認） |
| 3. 6本すべてに「測っていないもの」を1行 | 済（ファイル冒頭と各検査の出力の両方） |
| 4. 脚の灰色を直し、V1 が実測で PASS | 済（ただし**近似**。下の「2」を参照） |
| 5. 素肌の上半身へ合わせ直して Blend を作り直し提出 | 済（合格していない状態のまま提出） |
| 6. 実行記録を残し `plan_audit.py` を通す | 済（**14 / 14 PASS**） |

検査の結果は **V1・V2・V3・V5・V6 が PASS、V4 が FAIL**。

## 申し送りの前提と違っていた点（実測）

### 1. 素肌の上半身は1枚の胴体ではなかった

`c_HelenSSR01_slg_body_lod0`（1458頂点・2257面）を座標で溶接して連結成分に分けると
**13個のかたまり**。大きいのは 帯（719cm²・Y1.234..1.423）と 胸（210cm²・Y1.302..1.407）で、
腰の布の上端 Y1.192 とこの帯の下端 Y1.234 のあいだ **42mm には面が1枚も無い**。

正面（真正面の平行投影・2mm 格子）から測ると:

| 高さ帯 | 輪郭内 | 肌が無い | 水着が覆う | 見える穴 |
|---|---|---|---|---|
| 胸 Y1.234..1.423 | 410.2 cm² | 296.7 cm² | 22.3 cm² | **274.4 cm²** |
| 肩 Y1.423..1.538 | 33.5 cm² | 19.0 cm² | 0.6 cm² | **18.4 cm²** |
| 腰 Y0.95..1.17（対照） | 5.7 cm² | 0.0 cm² | — | 0.0 cm² |

対照の腰が 0.0 cm² なので、この物差しは閉じた面を穴と誤判定していない。
実測は `tools/swimsuit_front_coverage.py`、文字の地図は
`output/gf2-helen-swimsuit/front-coverage-map.txt`。

ドレスが覆っていた面は原作に存在しない。新しく面を作るのは 2026-08-30 に却下済みなので、
**穴は穴のまま提出した**（承認の「実装側で判断してよいこと」#3 のとおり）。

### 2. 「脚の灰色」は産出側の不具合ではなかった

原作の `c_HelenSSR0101_slg_P1_body_d` は全面 197,197,197 の無彩色で、これは**礼服の白い脚衣**。
ヘレンには素肌の脚メッシュが1本も無い（P1 白 197 / P2 濃 46 / P3 濃 56。
素肌は `P3_body_trans` の 369頂点の帯だけ）。

共有肌アトラス（原作データ内の全キャラ共通テクスチャ）へ差し替えて肌色にした。
**忠実再現ではなく近似**で、共有アトラスのどの領域が脚に対応するかは検証できていない
（脚の UV の並びは P3 の脚衣と 92% 一致し、共有アトラスの並びとは一致しない）。
台帳 `review-findings.json` の **F010** に `human-kept` で記録した。

### 3. 素肌の上半身は、そのまま組むと胴体がほぼ真っ黒になる

`c_HelenSSR01_slg_body` には自前の albedo が無く、産出側の名前解決が失敗して
**顔アトラス流用**へ落ちる。顔アトラスの当該領域は空で、中央値 20,13,11・輝度32未満が51%。
共有肌アトラスを exact 名で引けるように材料フォルダ側へ置いて解決した
（`tools/swimsuit_material_folder.py` の `SKIN_ROUTES`）。
**産出側の正規表現は触っていない**（兄弟の原作再現ビルドと共用のため）。

## 作ったもの

| ファイル | 役割 |
|---|---|
| `tools/blend_probe.py` | 成果物の Blend を開いて、材質が実際に読んでいる画像・座標・UV を取り出す |
| `tools/swimsuit_visible_checks.py` | 検査 V1〜V6 ＋ 変異試験 ＋ 合格線の較正 |
| `tools/swimsuit_front_coverage.py` | 正面から見た穴の実測（対照つき） |
| `output/gf2-helen-swimsuit/v4-contact-baseline.json` | V4 の合格線（ドゥルシーヌヴイ原着装の実測から） |
| `output/gf2-helen-swimsuit/resolution-ledger.json` | V6 が書き出す解決の台帳（在庫差つき） |
| `output/gf2-helen-swimsuit/front-coverage.json` / `front-coverage-map.txt` | 穴の実測と文字地図 |

変更したもの: `tools/helen_swimsuit_fit_p.py`（`skin` の追加と縦の合わせ方）、
`tools/swimsuit_restore_original_form.py`（`--body skin`）、
`tools/swimsuit_material_folder.py`（ドレスを外す・素肌を残す・肌の経路）、
`tools/plan_audit.py`（A11 の対象を成果物検査2本立てへ）、
`output/gf2-helen-swimsuit/visible-set-swimsuit.json`（退避 `.bak-20260831`）、
`output/gf2-helen-swimsuit/review-findings.json`（F009・F010 を追加）。

## 検査 V1〜V6 の設計で気をつけたこと

- **入力は build-log ではなく成果物の Blend**。build-log は産出側が自分について書いた記録なので、
  産出側が誤っていれば同じ誤りのまま合格する（2026-08-30 の抜けの正体）。
- **V3 は役割を台帳からではなく色の実測から取る**。台帳が「これは肌だ」と言い張っても、
  色が肌でなければ肌として数えない（変異試験3で確認）。
- **V4 の合格線はドゥルシーヌヴイ原着装の分布**（±5mm 以内 0.313 / 最深 −37.28mm）。
  端数はその原着装が定義上ちょうど合格する側へ丸めた。丸めないと合格線の出どころ自身が
  浮動小数の誤差で落ち、検出力を測れなくなる。
- **V1 の限界を明記した**: V1 が測るのは出力の色。産出側が「肌色になる方を選ぶ」作りに変われば
  V1 は構造的に通ってしまう。いまの産出側は色を見ずに名前で割り当てているので、その限りで
  検出力がある。

## 次に武田さんの判断が要ること

1. **胸の穴 274.4cm² をどうするか。** ①このまま ②ドレスの胴体を戻す（水着姿にならない）
   ③水着の幅を広げる（意匠が変わる）。新規に面を作る道は却下済み。
2. **脚を肌色（近似）のまま進めるか、原作どおり白い脚衣へ戻すか。**
3. **肩ひもが肩を越えて首の高さまで伸びる件**（147頂点が胴体上端 Y1.538 より上）を
   次の適合の課題に入れるか。
