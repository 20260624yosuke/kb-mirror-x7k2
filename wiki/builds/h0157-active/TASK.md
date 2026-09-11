---
type: build
title: H0157 active — 次の欠落1件
status: active
confidence: high
evidence_level: source-backed
created: 2026-09-11
last_reviewed: 2026-09-11
capsule_id: H0157-ACTIVE-20260911-R3
gap_id: H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT
implementation_authorized: false
---

# H0157 active — 次の欠落1件

Q0・Q0-CR・差分再審査フローはいずれも本番反映済み。監査基盤側の積み残しは無い。
次はP0開始前の基準線を取る工程だが、**まだ許可されていない**。

```yaml
gap_id: H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT
goal_effect: >-
  P0以降の調査でアプリ、親Blend、quality-gate、run-state、production scripts、Wiki、rawが
  変わっていないと後から機械比較できるよう、開始前の全ファイル集合をstageへ固定する。
  Helenの見た目は変わらない。取り違えと範囲外書込みを検出する基準線だけを作る。
known_inputs:
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
    sha256: e66c16d684b2ea23e49dfb850a1ec57e0e9b427967451a6f6b336bf0e3fbd703
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
    sha256: 4752ff9aceac9254976ee4fc64cf4c0460903eb6be92580bf5d84909dbaf1074
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
    sha256: 04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/audit/writer-review-receipt.json
    sha256: ead250c2a501c61d4e0ffc6d31934b503ace2d2b0957006f62f80d8ed73890a0
missing_evidence: >-
  protected-before を取ってよいという明示許可。現在 protected_before_precheck は
  may_take_protected_before=false を返す（P0 authorization が未取得のため）。
  app_root、parent_blend、quality_gate、run_state、production_scripts、wiki、raw の
  全regular file path+size+sha256集合とcanonical set SHA、独立readback receipt も未取得。
allowed_actions:
  - 許可が出た後に、protected setをread-onlyで再帰列挙・SHA測定する
  - 新しいrunのstage配下へだけ書く
  - 実装者とは別の読み手が全集合とset SHAを再測定する
forbidden_actions:
  - 許可なくprotected-beforeを取る
  - P0開始、Helen抽出、Blend制作
  - app、親Blend、quality-gate、run-state、production scripts、Wiki正本、raw、Git metadataの変更
mechanical_checks:
  - 全7 protected setsにfile count、各path+size+sha256、set SHAがある
  - 同じ入力から再生成したset SHAが一致する
  - 独立verifierの再測定が一致する
  - protected対象の反映前後SHA差分が0件である
stop_condition: 入力変化、読み取り不能、対象集合欠落、再測定不一致、stage外書込みのいずれか。
```

## 通常運転（script を直したくなったとき）

```
python3 <KB>/tools/h0157_rebaseline.py --registry <KB>/tools/project_quality_gate_required_audits.json verify
python3 <KB>/tools/h0157_rebaseline.py --registry <registry> diff --output <stage>/diff.json
# 独立レビューが diff を読んで判定受領証を作る（実装担当とは別プロセス）
python3 <KB>/tools/h0157_rebaseline.py --registry <registry> propose --diff <stage>/diff.json --review <stage>/review.json --out-dir <stage>/out
# bundle を作り、明示承認を得てから
python3 <KB>/tools/h0157_promote_bundle.py --bundle <bundle> --approval-receipt <receipt> --backup-dir <stage>/backup
```

## この後

`H0157-GAP-P0-AUTHORIZATION`。計画v2・契約v2の固定SHA、独立review、ユーザー承認の4点が
揃うまで registry の `p0_authorization.authorized` は false のまま。
