---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-06
parent: ../_index.md
---

# 止まり点の解消記録：Blender再現の停止分の棚卸し（2026-09-06）

武田さんの指示「blenderの再現で止まっている部分を理解して、その部分を解決してほしい。
承認は特に必要ない。許可する。」への対応記録。読み取り限定の再裏取り＋本記録の保存のみ。
正規 `.blend`・台帳・共有設定は無変更（実行後に `helen-h0157-repro.blend` のSHAを再確認）。

## 止まっている部分（4点に集約）

1. G10 FAIL：silkstock ramp割当て。renderer→material対応が無く推測禁止。
2. S6 FAIL：顔の白飛び `blown_ratio 0.39334`（原作 0.0）。受入条件 `<= 0.01` に遠い。
3. S8 FAIL：D2アルファ髪が検出できない（欠けの正しい検知のまま）。
4. 照明・ポストグレードが原作由来でない：正規blendの灯は自前のAREA 3灯
   （`LIGHT_主/補/裏`、本記録のためheadless読取で確認）、
   コンポジタは旧来の補正網（Math約150・k_R/k_G/k_B）でf130由来ではない。

## 解決の結果

- 解決ずみ（本日09-06の他作業で確定、本記録は追認のみ）：T1指の差は検証手続きの産物。
  同frame・同順（yxz）の真の差は左右とも 0.00 度
  （根拠 `logs/t1-finger-diff-reverify.json`）。直す対象ではない。
- 解消不能と確定（局所に無い。推測での埋め合わせは禁止のため手を付けない）：
  - G10の鎖：prefab rootは705,289件探索で0件、Helen衣装Materialは20,540件中0件、
    GFFコンテナは目録のみで実データを保持せず（Helen材質トークンはBGM音声のみ）、
    171不足はCDN側（CDN直接取得は403区別不能まで確定ずみ）。
    本記録のため `ledger/h0157-gff-container-scan-v1.json` の
    `target_hits_in_page_texts` が全コンテナ `{}` であることを再確認。
  - S8の実体：Helen材質7枚は全不透明、13,548束に別バリアントなし
    （根拠 `logs/t2-hairline-outcome.json`）。
  - T4転写（contrast/saturation/LUT結線）：`_Lut_Params.www` の実行値が24成分の束記録の
    どこにも存在せず、test.v103の槽対応（UserLut/InternalLut）も未確定。
    全14,072件のMaterial走査で該当0件（根拠 `logs/t4-whiteout-outcome.json`）。
    f173門が未検証の実装を機械的に止める（再現試験5/5）。
  - 照明真値：tetra真値補間は陽性対照8/8 exactで正しいが、最良t=1.0でも
    肌÷髪比 2.66 に対し原作 3.32。キャラ位置自体が未確定（scene root欠損）。
    f153候補は顔の白飛びを 0.39→0.47 へ悪化させたため不採用
    （根拠 `logs/f153-specular-candidate.json`）。
- 仕掛かり：combined-T1T6候補は無音化で土台と0.000一致を実証した段階。
  白飛び隠しのclamp入り版は退役ずみ（根拠 `logs/f179-combined-t1t6.json`）。

## 証拠の境界

- 本記録は停止分の棚卸しであり、修正・原作一致・完成の証拠ではない。
- 正規blend SHA `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`
  は本作業の前後で不変（指紋で確認）。
- 09-04提案の「ローカルに無い時点で破綻」前提への切替え自体は未決のまま。
  本記録はその判断材料であり、方針決定ではない。
  G10・S8・T4-wwwについては局所不在が複数系統で確定したため、
  当該細目の範囲では前提の発動条件を満たす。全体破綻の宣言はしない。
