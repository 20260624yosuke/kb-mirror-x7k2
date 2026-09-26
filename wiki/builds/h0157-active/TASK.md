---
type: build
title: H0157 active — motion exact subscope value freeze（2026-09-26）
status: active
confidence: high
evidence_level: source-backed
created: 2026-09-11
last_reviewed: 2026-09-26
capsule_id: H0157-ACTIVE-20260911-R3
gap_id: H0157-GAP-MOTION-EXACT-SUBSCOPE-VALUE-FREEZE
implementation_authorized: false
---

# H0157 active — motion exact subscope value freeze（2026-09-26）

2026-09-26のgeometry closureでmesh_body_geometryはFINAL_VALUES_CONFIRMEDとして
current taskから外した（Blend変更なし、詳細はCURRENT.jsonのgeometry_closure_20260926と
EV-H0157-045）。whole projectはINCOMPLETEのまま。

本taskはstate更新であり、motion freeze自体は実行しない
（implementation_authorized: false）。

## 閉じたgeometry（history、現行taskにしない）

```yaml
closed: mesh_body_geometry = FINAL_VALUES_CONFIRMED
blend_change_required: false
normal_repair_required: false
unresolved_remainder: []
final_evidence: EV-H0157-045
history_kept:
  - 2026-09-23/24のREADY_FOR_VALUE_FREEZE記録（CURRENT.json sync_20260924・FACT-H0157-020/021/027）
  - 旧geometry freeze第一候補の記述（旧版TASK）
forbidden_now:
  - geometry再調査
  - normals再調査・再適用
  - candidate Blend promotion
```

## PARKED（current next taskにしない）

```yaml
parked: H0157-PARKED-FINGER20-POST-CLIP-RUNTIME-SOURCE
state: PARKED
statement: >-
  Bはoriginal AnimationClip由来として支持される。
  post-clip game runtimeがFinger20を改変するかは、一次graph/source不在のためunprovenのまま。
  Finger20 runtime source探索はcurrent next taskにしない。
resume_condition: >-
  scene/prefab/controller/mask/runtime code等の新一次証拠、
  またはattached runtime traceが利用可能になった場合のみ再開を検討する。
  自動再開しない。
auto_resume: false
forbidden_now:
  - Finger20 runtime source探索
  - Finger20再開
  - Candidate B本番反映
  - Candidate Bをproven final live-runtime truthと書くこと
```

## 次の本体task

```yaml
gap_id: H0157-GAP-MOTION-EXACT-SUBSCOPE-VALUE-FREEZE
goal_effect: >-
  既にsource-backedで完全一致しているmotion部分について、
  final Blendが今後正式に参照する値を恒久固定する（formal value freeze）。
  20 Finger20の未解決を理由に確定済み334個を再調査しない。
scope_A_310_TRS_bitwise_exact:
  description: 310 bone channels（Translation / Rotation / Scale がbitwise exact）
  source_evidence: EV-H0157-032
  source_path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/audit/runs/20260923T180000+0900/stage/current-app-reextract/helen-evidence-corrections-v6/motion-relation-audit-v3.json
  source_sha256: acfdb5dd9fb2087a7b6aec66a40dd87a06ad47e2c252cdfbb78584446970caa4
scope_B_24_face_BIT_EXACT:
  description: 24 face curves（BIT_EXACT）
  source_evidence: EV-H0157-031（freezeable subscope）+ EV-H0157-030（provenance face-motion-readback-v2）
  note: path/SHAはEVIDENCE現物から取得すること。推測pathを使わない。
excluded:
  - 20 Finger20 rotations
  - Finger20 post-clip runtime semantics
  - 新しいanimation extraction
  - unresolved motion部分
  - Blenderへの本番書込み
success_effect: 334個の既に確定済みmotion値がformal frozen valuesになる。
executed_by_this_task: false
forbidden_actions:
  - 本taskでのmotion freeze実行
  - Blender起動
  - parent Blend変更
  - quality-gate変更
  - run-state変更
  - Finger20再開
  - 旧P0再開
  - 新しいtolerance
stop_condition: 入力変化、stage外書込み、Finger20 PARKEDの破損のいずれか。
```

## 保護状態（2026-09-26 closure前後で不変を要求）

```yaml
parent_blend:
  path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
  sha256: 04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5
quality_gate:
  path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
  sha256: e66c16d684b2ea23e49dfb850a1ec57e0e9b427967451a6f6b336bf0e3fbd703
```

## 現行state要点（詳細はCURRENT.json）

- mesh_body_geometry: FINAL_VALUES_CONFIRMED（Blend変更なし、EV-H0157-045）。
- H0157 motion: 330 mapped、unmatched 0、T330/S330/R310/TRS310、残差exactly 20 Finger20。
- Finger20: A=HUMAN_COMPENSATION（保持・削除なし）、B=game-data-derived（AnimationClip scope）、C=NONE。post-clip final runtimeはunproven、PARKED。
- Visual route: NOT_READY、promotionなし。
- 旧TASK本文（geometry freeze第一候補の詳細）は本更新で置換した。旧内容が必要なら版履歴を参照。
