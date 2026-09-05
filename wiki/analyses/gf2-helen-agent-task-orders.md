---
type: analysis
status: active
confidence: medium
evidence_level: inferred
last_reviewed: 2026-09-05
sources: []
---

# 別実行者への指示書（H0157・6件）

別実行者へそのまま貼って渡すための指示書。HTML版
(`wiki/_attachments/project-hub-index/20260905-agent-task-orders.html`)
と同じ内容のテキスト正本。未回収の符号は手元の材料にある前提で回収し、
形にすること。

## 共通の約束（全件の先頭に貼る）

- 作業前に正本3点を読む。古い添付や会話の記憶を正本にしない。
  - `.../gf2-helen-starlit-waltz/06_repro-v51/run-state.json`
  - `.../gf2-helen-starlit-waltz/quality-gate.json`
  - `.../wiki/builds/gf2-helen-repro-v51-handoff.md`
  - 門の契約 `.../06_repro-v51/audit/S6.json` `S8.json` `G10.json`
  - 現状文 `.../06_repro-v51/logs/f171-current-status.json`
- 本体の保存形式 (`blends/helen-h0157-repro.blend`、sha16 `04ef8b79b3fa5b64`)
  は承認なしに書換えない。作業前後で同一性を測る。
- 候補は別名（`blends/_candidate-<件名>/`）で作る。描画の数値判定は門の手順でのみ行う。
- 終わりに門を通す。通らない説明は出さない。
  - `python3 scripts/f171_three_task_status_gate.py check logs/f171-current-status.json`
  - `python3 scripts/f172_canonical_freshness_gate.py check`
  - `python3 scripts/f171_three_task_status_gate.py --replay-test`
  - `python3 scripts/f172_canonical_freshness_gate.py --replay-test`
- 報告は探す・紐づける・作るの順に、状態・根拠・数値を添える。
  見つからない場合は範囲・件数・陽性対照つきの弱い形で書く。

## T1 指の変換誤り

目的は左手キーの反転式の修正。右手は完全一致しており、左手だけが膨らんでいる。
外部の符号は要らない。

- 対象
  - `06_repro-v51/ledger/finger-axis-decision.json`
  - `05_helen-motion-library/library-v2-fidelity/rest-room-v2.2/inputs/h0157-selected-motion.json.gz`
  - `06_repro-v51/scripts/f151_finger_axis_H.py`
  - `06_repro-v51/blends/helen-h0157-repro.blend`（読むだけ）
- 判明済みの差（再検証してから使う）
  - 左人差指3・13コマ：原作88.4度→適用171.7度
  - 左人差指3・251コマ：原作91.0度→適用147.0度
  - 左人差指2・250コマ：原作92.9度→適用139.5度
  - 右手は完全一致（差0.1度）
- 手順
  1. 原作の四元数と適用後の四元数を正規化角度で比べ、差表を再計算する。
  2. 反転式を直し、差が1度以内になることを数値で確認する。
  3. 候補の保存形式を作り、本体が無変更であることを同一性で示す。
  4. 手の門を再測定し、結果を記録する。
- 完了条件は差1度以内・候補の同一性記録・本体の無変更。目視のみの断定は禁止。

## T2 額の生え際

目的は透過付き髪実体の有無の確定。探す場所は内蔵の束4511件。

- 対象
  - `06_repro-v51/scripts/f139_hair_facesdf_enumerate.py`
  - `06_repro-v51/logs/f139-hair-facesdf-scan.json`
  - `06_repro-v51/logs/f139-texture-alpha-census.json`
- 手順
  1. f139の手順を再実行し、陽性対照が通ることを確認する。
  2. 髪の反射絵と凹凸絵の透過溝を走査する。
  3. 別段階・別変種の髪を列挙する。
  4. 見つかれば候補を作り髪の門を再測定し、無ければ弱い形で記録する。
- 完了条件は陽性対照つきの有無の確定、または候補と門の再測定。別個体の値の流用は禁止。

## T3 顔の筋

目的は顔影の元画像の実体の有無の確定。別個体の命名規約を足がかりにする。

- 対象
  - `06_repro-v51/logs/f139b-facesdf-census.json`
  - `06_repro-v51/logs/f158-binfile-table-sweep.json`
  - `06_repro-v51/scripts/f156_loadroombyid_route.py`
- 手順
  1. 命名規約で顔の材質設定を探す。
  2. 元画像の実体の有無を確定する。
  3. あれば顔影の適用候補を作り、無ければ弱い形で記録する。
- 完了条件は実体の有無の確定、または候補。設定自体の未回収は遮断として残す。

## T4 顔の白飛びの値

目的は量産設定の数値の取出。談話室系の束に量産設定の実体がある。

- 対象
  - 内蔵束 `29684a9f82183f96b0cdf1a05b4c517e.bundle`（談話室系・量産設定あり）
  - 内蔵束 `98ab47f2b19f05a56198573b3be49772.bundle`（表の記録あり）
  - `06_repro-v51/scripts/f112_dorm_light_source_parse.py`
  - `06_repro-v51/scripts/f138_compositor_tonemap.py`
  - `06_repro-v51/scripts/f162_aces_rrtodt_transfer.py`
  - 適用順の記録 `ledger/shader-source/post/UberPost/0024_12d8db72203d.msl` の124〜179行
  - 書側の記録 `ledger/shader-source/post/LutBuilderHdr/0007_f31ae486889b.msl`
- 手順
  1. 談話室系の束から量産設定の浮動小数値群を取り出す。
  2. 値を出所つき（束名と通し番号）で台帳に記録する。
  3. 値を鎖へ流し込んだ候補を作る。
  4. 顔の門を再測定し、結果を記録する。
- 完了条件は出所つきの値、候補、本体の無変更。中立値の発明は禁止し、値は束からのみ取る。

## T5 階調の割当て

目的は描画体から材質への鎖の Helen 側の発見。別個体は形式の参照に限り、値の流用は禁止。

- 対象
  - `06_repro-v51/scripts/f35_material_census.py`
  - `06_repro-v51/scripts/f36_gitter_usage_census.py`
  - `06_repro-v51/ledger/material-census.json`
  - `06_repro-v51/ledger/ramp-settings.json`
- 手順
  1. Helen 側の鎖だけを探す。
  2. 見つかれば割当て候補を作り、門相当の検証をする。
  3. 無ければ弱い形で記録する。
- 完了条件は鎖の発見と候補、または弱い形の記録。

## T6 反射のつや

目的は反射係数群の回収と候補への流し込み。構造は確保済みで、階調の後に較準する順序を守る。

- 対象
  - `06_repro-v51/ledger/shader-source/fragment/GFCharForward/0357.msl`（行614〜700）
  - `06_repro-v51/scripts/f153_specular_candidate.py`
  - `06_repro-v51/blends/_candidate-specular/`
- 手順
  1. 手元の材質群から係数集合を数える。
  2. 構造へ流し込んだ候補を作る。
  3. 較準は階調の候補の後にする。
- 完了条件は出所つきの係数と候補。単独の先行較準は禁止。

## 関連リンク

- [[gf2-helen-repro-v51-handoff]]
- [[gf2-helen-repro-v51-current]]
- `wiki/_attachments/project-hub-index/20260905-agent-task-orders.html`（HTML版・同内容）
