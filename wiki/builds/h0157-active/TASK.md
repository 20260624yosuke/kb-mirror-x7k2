---
task_id: H0157-Q0-RUNSTATE-SNAPSHOT-PROPOSAL
capsule_id: H0157-ACTIVE-20260910-R1
gap_id: H0157-GAP-Q0-RUNSTATE-SNAPSHOT
status: ready-for-fixed-input-reconciliation
implementation_authorized: false
evidence_set_sha256: 75d41e19f2d128f8c7cf100017ca98ce326e88056c863d2006a283eb58971f79
---

# H0157 次の不足接続 — stale run-state snapshot の検証候補

## 1件だけの作業

`quality-gate.json` が保存する `project-run-state` memberを、現物 `run-state.json` のfull SHA・size・mtimeへ合わせた**隔離検証候補**として構成し、正本を変更せずに差分と機械照合結果を提出する。

```yaml
gap_id: H0157-GAP-Q0-RUNSTATE-SNAPSHOT
goal_effect: この不一致を解消できる候補が検証されると、現行計画の開始関所を古いrun-state snapshotではなく現物入力で再評価する次段へ進める。Helenの見た目やBlend自体はまだ変わらない。
known_inputs:
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
    sha256: 479f8a1daea14ff3e83597298140555d89488fd223ae2830c2a7b0d8a0f49141
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
    sha256: 4752ff9aceac9254976ee4fc64cf4c0460903eb6be92580bf5d84909dbaf1074
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/helen-h0157-new-agent-entry-20260909.md
    sha256: ad30a746f79409f6ab3ce84c81eaeffaa85fd322d0b4eada97e80c4c1e76ee04
missing_evidence: 現物run-state値を使った隔離候補がJSONとして成立し、変更点がproject-run-state snapshot memberだけで、正本quality-gateを一切変更していないことのreadback証拠。
allowed_actions:
  - 上記3入力のread-only再hashとJSON/本文readback
  - mktempで作った一時コピー上だけでproject-run-state memberのsha256,size,mtimeを現物値へ置換
  - 差分と照合結果を /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/h0157-active/receipts/q0-runstate-snapshot-proposal.json に新規保存
forbidden_actions:
  - 正本quality-gate.jsonの変更
  - 06_repro-v51配下への書き込み
  - 抽出の実行または出力
  - Blendの変更または保存
  - Git初期化・commit・metadata変更
  - 旧計画・旧引き継ぎの修正
  - H0157以外への拡大
expected_outputs:
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/h0157-active/receipts/q0-runstate-snapshot-proposal.json
    content: 入力3件の再hash、置換前後値、変更JSON pointer一覧、隔離候補SHA、正本quality-gateの変更前後SHA一致、判定
mechanical_checks:
  - 入力3件のfull SHAがknown_inputsと完全一致
  - 一時候補と正本をJSON parseできる
  - JSON差分pointerが /execution_audit/current_state_inputs/members のinput_id=project-run-stateに属するsha256,size,mtimeだけ
  - 候補のproject-run-state sha256/size/mtimeが現物run-state readbackと一致
  - 作業前後の正本quality-gate full SHAが479f8a1daea14ff3e83597298140555d89488fd223ae2830c2a7b0d8a0f49141のまま
  - 06_repro-v51とBlendへ書き込んでいないことを対象ファイルSHAで再確認
frontier_decision_required: yes — 隔離候補を正本quality-gateへ昇格する判断と、その後の実装開始は構造・実行方針を変えるため、独立reviewとユーザーの明示承認が必要。
cheap_model_delegable: yes — 上記固定入力の再hash、限定JSON置換、差分照合、proposal receipt作成まで。正本昇格・抽出・Blend変更は委譲範囲外。
stop_condition: 入力SHAが1件でも変化、対象memberが一意でない、指定3フィールド以外の差分が必要、正本への書き込みが必要、意味推測・前面GUI・H0157外の探索が必要になった時点で何も昇格せずtechnical-stopをreceiptへ記録する。
```

## 完了と呼ばないもの

この1件のproposalがPASSしても、quality-gate正本の更新、計画PASS、抽出開始、Helenの見た目改善、原作一致、Blend完成にはならない。
