---
type: build
title: H0157 active — 次の欠落1件
status: active
confidence: high
evidence_level: source-backed
created: 2026-09-11
last_reviewed: 2026-09-12
capsule_id: H0157-ACTIVE-20260911-R3
gap_id: H0157-GAP-P0-AUTHORIZATION
implementation_authorized: false
---

# H0157 active — 次の欠落1件

Q0・Q0-CR・差分再審査フローの3件はいずれも本番反映済みで、それぞれ独立読み返しが PASS している。
点検したのはこの3件の範囲であり、それ以外に課題が残っているかは点検していない。
次はP0を開始してよいという許可を取る工程だが、**まだ許可されていない**。

開始前の基準線（protected-before）の関所は、いまは閉じている。2026-09-11 に
`audit_guard.protected_before_precheck` を現物へ実行したところ、
`may_take_protected_before: false` と `EA_P0_NOT_AUTHORIZED` が返った。
点検したのはこの1関数の戻り値だけで、他の経路は点検範囲外。
この関所が開くのは P0 authorization が成立した後。実行可能順は
`H0157-GAP-P0-AUTHORIZATION` → `H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT` → P0。

```yaml
gap_id: H0157-GAP-P0-AUTHORIZATION
goal_effect: >-
  P0（現行アプリ全域の再抽出）を開始してよいという許可を、Q0の技術PASSとは別の束縛として取る。
  Helenの見た目は変わらない。registryの p0_authorization が閉じているかぎり、
  protected-before もP0も機械的に開かない。
known_inputs:
  # sha256 は本文作成時に現物から再計算した値であり、過去の判断の引用ではない。
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/project_quality_gate_required_audits.json
    sha256: 5bf4f4e5a1e984566329178230fc31612d72393ec73328030f163aafc8d5cfa3
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-h0157-current-app-reextract-workflow-plan-v2-20260910.md
    sha256: 2d0f585dd6eb1fedb9bbad403fae64afd5b4da8c96018000970ff164d9f3eeae
  - path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-h0157-current-app-reextract-task-contract-v2-20260910.md
    sha256: 3e27010f156b6b5d16d95cbd8474e62bc6ca562fb4ee66f3c63c6a6dea96f6a4
missing_evidence: >-
  registry の p0_authorization は authorized=false で、independent_review_sha256 /
  user_approval_sha256 / approved_at がいずれも空文字列。audit_guard.validate_p0_authorization は
  この3つが非空であること、authorized=true であること、workflow_plan_v2 と task_contract_v2 の
  現物SHAが記録値と一致することの全部を要求する。PLAN-STATE-01 は束縛済み2文書の最小修正で
  解消済みだが、ae85c78d… の7-target remediation は successor-before-promotion として保持し
  promotion しない。その独立semantic review は P0 authorization 用 review ではなく、後継の
  final approval evidence として流用しない。AUTH-BUNDLE-BINDING-01 修正の patched promoter を
  含む 8-target successor remediation の promotion・独立semantic review・ユーザー承認はまだ存在しない。
  NEW-FILE-POSTHASH-ROLLBACK-01 修正を含む r2 promoter で再検証する。
  TARGET-MAP-AUTH-SCOPE-BINDING-01 修正を含む r3 promoter で再検証する。
  BACKUP-DIR-WRITE-SCOPE-BINDING-01 修正を含む r4 promoter で再検証する。
  r4 は UNAUTHORIZED-PRODUCTION-WRITE-01 の incident-bearing history であり、promoter bytes を byte-identical に再利用して r5 を clean run として fresh 構築・再検証する。
  新しいlive canonical bytesから作るsuccessor P0 authorization packageと、そのpackageを対象に
  した独立semantic review、ユーザー承認もまだ存在しない。
allowed_actions:
  - 本successor remediation反映後のlive canonical plan / contract / registry / promoter bytesから、successor P0 authorization packageを新しいrunのstageへ作る
  - 実装者とは別の読み手が、successor packageと束縛対象を実バイトから独立semantic reviewする
forbidden_actions:
  - package v5、そのreview、approval evidenceを承認・再利用する
  - 脆弱な旧 promoter（feb59c74…）での production promotion
  - 明示承認なしに registry の p0_authorization を authorized=true にする
  - Q0 / Q0-CR / rebaseline の技術PASSをP0許可の根拠に流用する
  - 工程設計そのものを独断で変更する（必要になったら理由と影響を報告して停止する）
  - protected-beforeの取得、P0開始、Helen抽出、Blend制作
mechanical_checks:
  - 2文書の現物SHAが p0_authorization の記録値と一致する
  - independent_review_sha256 / user_approval_sha256 / approved_at が非空である
  - validate_p0_authorization が PASS を返す
  - protected_before_precheck が may_take_protected_before=true を返す
stop_condition: 2文書の記述と現状の食い違いが残る、app_rootが一意でない、独立reviewのmajor finding、
  工程設計の変更が必要になった、のいずれか。
```

## この後（許可が出た後の工程）

`H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT`。P0 authorization が取れて初めて着手できる。

```yaml
gap_id: H0157-GAP-P0-PROTECTED-BEFORE-SNAPSHOT
goal_effect: >-
  P0以降の調査でアプリ、親Blend、quality-gate、run-state、production scripts、Wiki、rawが
  変わっていないと後から機械比較できるよう、開始前の全ファイル集合をstageへ固定する。
  Helenの見た目は変わらない。取り違えと範囲外書込みを検出する基準線だけを作る。
known_inputs:
  # 同名の取り違え注意: これは production 正本。muse が 2026-09-01 の stage へ書いた同名複製
  # (audit/runs/20260901T230943+0900/stage/project/quality-gate.json) とは別物で、そちらは使わない。
  # 下の sha256 は本文作成時に現物から再計算した値であり、muse の判断の引用ではない。
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
