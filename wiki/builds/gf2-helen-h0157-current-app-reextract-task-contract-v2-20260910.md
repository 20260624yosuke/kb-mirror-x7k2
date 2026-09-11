---
type: build
title: Helen H0157 — 現行アプリ全域再抽出 Q0-P0-R0-R3 作業契約 v2
status: proposed
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-09-10
last_reviewed: 2026-09-11
revision: 2
parent_plan: gf2-helen-h0157-current-app-reextract-workflow-plan-v2-20260910
implementation_started: false
implementation_gate: blocked
blocking_evidence: EA_P0_NOT_AUTHORIZED
blocking_evidence_superseded: EA_KB_SNAPSHOT_STALE (2026-09-10; Q0昇格で解消)
current_state_reviewed: 2026-09-11
supersedes:
  - gf2-helen-h0157-current-app-reextract-task-contract-20260909
---

# Helen H0157 — 現行アプリ全域再抽出 Q0-P0-R0-R3 作業契約 v2

```yaml
contract_version: 2
task_id: H0157-Q0-P0-R0-R3-CURRENT-APP-REEXTRACT
parent_goal: H0157 faithful Blender deliverable
execution_status: q0-complete-p0-not-authorized
execution_status_superseded: proposed-blocked-not-authorized (2026-09-10; Q0未実行かつquality-gate FAILだった頃の記述)
actor_class: cheap-model-implementer
semantic_decisions_allowed: false
input_mode: complete-current-app-root
analysis_mode: inventory-all-then-index-h0157
time_machine_required: false
specific_missing_bundle_required: false
blend_write_allowed: false
production_script_write_allowed: false
```

## 1. Q0開始関所

### 1.1 正本

```yaml
quality_gate:
  canonical_path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
  observed_full_sha256_20260910: 479f8a1daea14ff3e83597298140555d89488fd223ae2830c2a7b0d8a0f49141
  observed_full_sha256_20260911: e66c16d684b2ea23e49dfb850a1ec57e0e9b427967451a6f6b336bf0e3fbd703
  project_id: gf2-helen-starlit-waltz
  canonical_root: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz
  check_command: python3 <KB-root>/tools/project_quality_gate.py check <canonical_path> --phase plan
  observed_result_20260910: FAIL
  observed_reason_20260910: EA_KB_SNAPSHOT_STALE project-run-state sha256 mismatch
  observed_result_20260911: PASS (exit 0, '品質ゲート: PASS (plan)')
  observed_run_state_sha256_20260911: 4752ff9aceac9254976ee4fc64cf4c0460903eb6be92580bf5d84909dbaf1074
  required_before_P0: PASS with exit 0 and saved stdout/stderr receipt
  required_before_P0_status: satisfied as of 2026-09-11; the remaining P0 blocker is EA_P0_NOT_AUTHORIZED, not this gate
```

`06_repro-v51/quality-gate.json`を作らない。templateから既存正本を作り直さない。

### 1.2 Q0で許される更新

1. 既存12-member `current_state_inputs`を現物から再測定した`u0-snapshot.json`候補をstageへ作る。
2. quality-gate自身は`/execution_audit/current_state_inputs`を除外したcanonical projection SHAを使う。
3. `a10_quality_gate.py`相当のvalidation mergeでstage候補を作る。既存の非audit key、family、ground truth、stop条件を保持する。
4. production registryと同じguard/schema/writer/hook SHAを結んだstage registryでplan checkを行う。
5. mutation、rollback、idempotence、writer-scanを実行する。
6. 独立review SHAとuser approval SHAをpromotion receiptへ結ぶ。
7. transactional promotion後、production canonical pathへ同じplan checkを実行する。

Q0以外のactorによるquality-gate変更は禁止する。Q0は抽出実装ではなく、その開始条件を回復するbootstrapである。

**2026-09-11 現在の状態**: この Q0 bootstrap は本番反映済みで、合格基準の補修（Q0-CR）と、差分だけを再審査して baseline を更新する通常運転フローも反映済み。正本 quality-gate の `--phase plan` 検査は PASS。以後の script 変更は `tools/h0157_rebaseline.py` の diff / propose と `tools/h0157_promote_bundle.py` を通す。**残っている P0 の関所は `EA_P0_NOT_AUTHORIZED` だけで、これは本文書と計画v2の固定SHA・独立review・ユーザー承認が揃うまで開かない。**

## 2. 固定入力

```yaml
inputs:
  - input_id: app-root
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app
    observed_regular_files_20260910: 5326
    observed_directories_20260910: 158
    observed_symlinks_20260910: 0
    role: complete-current-app-denominator
  - input_id: app-assets-subset
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Data/Raw/AssetBundles_IOS
    observed_regular_files_20260910: 4513
    role: subset-only
  - input_id: app-main
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/SnqxExilium
    sha256: 025aaafc9c49b6ba37b37678df01d67a7a408d41bb48bbfba0008f34412881b8
  - input_id: unity-framework
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Frameworks/UnityFramework.framework/UnityFramework
    sha256: 8be85a1c692b741be3619eba40b006c3133c2ff8a137030c34090650e75b5c4d
  - input_id: global-metadata
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Data/Managed/Metadata/global-metadata.dat
    sha256: 10609117460b9375c6a9d768f3a489120ac5f4312597c12a99b2df597512a197
  - input_id: parent-blend
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
    sha256: 04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5
```

R0でapp root全体を再帰列挙する。5,326件は計画時観測値であり、実行時に違えば`INPUT_DRIFT`で停止する。

### 2.1 分母の一意化 — `app_root`（2026-09-11 追記）

```yaml
app_root:
  canonical_absolute_path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app
  token_aliases: [app-root, app_root, "<app-root>"]
  rule: >-
    この契約に現れる app-root / app_root / <app-root> は、上の1つの絶対パスだけを指す。
    外側の ドルフロ2_本体.app や .../Wrapper は分母にしない。
    AssetBundles_IOS / Frameworks / Data/Managed などは、すべてこの根の部分集合であり、
    それ自体を app_root と呼ばない。
  denominator_scope: every regular file under the canonical_absolute_path, recursive
  drift_stop: app_root が上のパス以外へ解決されたら INPUT_DRIFT で停止する
```

この節は用語の一意化であり、分母規則そのもの（file / container / object / byte-range の4分母）、
工程、関所、停止条件は変えていない。

## 3. 読み書き境界

```yaml
read_allowlist:
  - <app-root>/**
  - <project-root>/quality-gate.json
  - <run-root>/run-state.json
  - <run-root>/blends/helen-h0157-repro.blend
  - <run-root>/ledger/shader-source/**
  - <run-root>/logs/f166-code-inventory.json
  - <run-root>/logs/f166-msl-per-bundle.json
  - <run-root>/scripts/common.py
  - <run-root>/scripts/a10_quality_gate.py
  - <run-root>/scripts/audit_guard.py
  - <run-root>/scripts/writer_scan.py
  - <run-root>/scripts/f166_code_inventory.py
  - <KB-root>/tools/project_quality_gate.py
  - <KB-root>/tools/project_quality_gate_required_audits.json
write_allowlist_Q0:
  - <run-root>/audit/runs/<run-id>/stage/**
  - <project-root>/quality-gate.json only by approved transactional promotion
write_allowlist_P0_R3:
  - <run-root>/audit/runs/<run-id>/stage/current-app-reextract/**
forbidden_writes_P0_R3:
  - <app-root>/**
  - <run-root>/blends/**
  - <project-root>/quality-gate.json
  - <run-root>/run-state.json
  - <run-root>/scripts/**
  - <KB-root>/wiki/**
  - <KB-root>/raw/**
```

実装スクリプトはstageの`bin/`へ置く。production `scripts/`への昇格は本契約外。

## 4. 保護対象snapshot

Q0後、P0開始直前に次を`protected-before.json`へ記録し、R3終了時に別actorが再測定する。

```yaml
protected_sets:
  app_root: every regular file path + size + sha256
  parent_blend: full-file sha256
  quality_gate: full-file sha256 and canonical-projection sha256
  run_state: full-file sha256
  production_scripts: every .py path + size + sha256
  wiki: every regular file path + size + sha256
  raw: every regular file path + size + sha256
allowed_difference:
  app_root: none
  parent_blend: none
  quality_gate: none after Q0
  run_state: none
  production_scripts: none
  wiki: none
  raw: none
```

差分1件でも`WRITE_SCOPE_VIOLATION`。実装者の自己申告ではなく独立verifierの再測定を使う。

## 5. canonical化

### 5.1 file record

```yaml
encoding: UTF-8
unicode_normalization: NFC
path_separator: /
sort: relative_path UTF-8 byte order ascending
json_serialization: compact JSON; ensure_ascii=false; separators comma-colon
json_key_order: [relative_path, size, sha256, magic_hex, mtime_ns]
line_ending: LF
content_identity_fields: [relative_path, size, sha256, magic_hex]
recorded_but_excluded_from_identity: [mtime_ns]
```

`app_file_set_sha256 = SHA256(concat(identity JSON line + LF for all regular files))`。

### 5.2 run input set

```text
SHA256(
  "H0157-CURRENT-APP-INPUTS-V2\n" ||
  app_file_set_sha256 || "\n" ||
  app-main sha256 || "\n" ||
  unity-framework sha256 || "\n" ||
  global-metadata sha256 || "\n" ||
  parent-blend sha256 || "\n" ||
  parser-registry sha256 || "\n" ||
  quality-gate canonical-projection sha256 || "\n"
)
```

`inputs_set_sha256`はこの式だけを使う。時刻とrun idは含めない。

## 6. P0 parser registry

### 6.1 実行環境候補

```yaml
runtime:
  python: <project-root>/05_helen-motion-library/library-v2-fidelity/runtime/bin/python3
  python_version_observed: 3.14
  UnityPy_version_observed: 1.25.2
  lz4_version_observed: 4.4.5
system_tools_observed:
  - /usr/bin/file
  - /usr/bin/otool
  - /usr/bin/nm
  - /usr/bin/strings
  - /usr/bin/dwarfdump
must_not_assume_installed:
  - dnfile
  - dncil
  - macholib
  - lief
```

実行前にbinary/module実体のSHAまたはdistribution RECORD集合SHAをregistryへ固定する。

### 6.2 parser row

```yaml
required:
  - parser_id
  - detector.byte_signature_or_predicate
  - detector.offset
  - implementation.executable_path
  - implementation.executable_sha256
  - implementation.version
  - input_unit
  - output_units
  - success_condition
  - unsupported_condition
  - coverage_rule
forbidden_detector_basis:
  - llm natural-language judgment
  - entropy alone
  - filename similarity alone
  - file-command prose alone
```

最低検出classはMach-O、UnityFS、serialized Unity object、PE/CLI、IL2CPP metadata、Lua bytecode、
plain text code、known non-code。検出できない形式は`coverage-open`へ置く。

### 6.3 P0停止

- 観測されたmagic/predicateのうちregistry未登録が1種類以上。
- parserのfixtureまたはmutation testがない。
- 子object/rangeの分母式を持たないparserがある。
- productionへ直接書くwriterがある。

## 7. R0 file分母

出力:

```yaml
app-node-inventory.jsonl:
  one_row_per: regular file or symlink
  required: [node_type, relative_path, size, sha256, magic_hex, mtime_ns]
file-status.jsonl:
  one_row_per: regular file
  required: [relative_path, source_sha256, detector_ids, disposition, child_container_count]
  disposition: [accounted, coverage-open, failed]
```

条件:

```text
regular_file_total == 5326
regular_file_total == accounted + coverage_open + failed
asset_subset_total == 4513
outside_asset_subset_total == 813
```

R0では内容SHAを重複排除しない。全pathを分母へ残す。

## 8. R1 container・object・range分母

出力:

```yaml
container-inventory.jsonl:
  required: [container_id, source_path, source_sha256, parser_id, coordinate_space,
             child_total, parsed, coverage_open, failed]
object-inventory.jsonl:
  required: [object_id, container_id, object_type, source_offset, source_length,
             disposition, parser_id]
range-inventory.jsonl:
  required: [range_id, parent_id, coordinate_space, offset, length, disposition,
             output_sha256, parser_id]
```

各container/coordinate spaceごとに次を検査する。

```text
child_total == extracted + non_code_by_registry + coverage_open + failed
bytes_total == extracted_bytes + non_code_bytes + padding_bytes + coverage_open_bytes
overlap_bytes == 0
unexplained_gap_bytes == 0
```

圧縮前file offset、展開block offset、serialized object offset、Mach-O section offsetを別spaceとして記録する。
同じ数式へ混ぜない。

## 9. R2抽出と検証

### 9.1 抽出物

```yaml
extraction-index.jsonl:
  one_row_per: unique extracted byte sequence
  required: [extract_id, class_id, output_relpath, output_sha256, parser_id,
             parser_sha256, origins]
origin:
  required: [source_path, source_sha256, coordinate_space, offset, length, object_id]
```

内容SHAで重複排除しても全`origins`を残す。

### 9.2 unit fixture

```yaml
fixture_id: FX-TERM-BASE
payload_hex: 48303135370a5f46696c6d5768697465436c69700a5f4c75745f506172616d730a
sha256: c3f20fc38ff4f188c0f767c46fc979c28cc4e720b9e3237d0c3f66837d0f52e1
expected_exact_counts:
  H0157: 1
  _FilmWhiteClip: 1
  _Lut_Params: 1
mutations:
  - id: MUT-H0157
    replace_at_base_offset: 4
    replace_hex: 37-to-36
    sha256: 49bd846adcc8e88bae24a47415c17377dc1e93c9a8d2fe765bcd19960eb65473
    expected: H0157=0; other two remain 1
  - id: MUT-FILM
    replace_at_base_offset: 19
    replace_hex: 70-to-71
    sha256: b046b882e9cf711671faa932bb3aafdd7a41dc481eea388d41eeae4c9d3a8bb8
    expected: _FilmWhiteClip=0; other two remain 1
  - id: MUT-LUT
    replace_at_base_offset: 31
    replace_hex: 73-to-74
    sha256: 3d2ddd99c99926eb5206ad0c55eb6943dc2817b67810cedb6e758085cd057f52
    expected: _Lut_Params=0; other two remain 1
```

mutation payloadはbaseの該当token末尾1byteだけを変更する。fixture manifestへ変異後SHAを正規入力として登録する。

### 9.3 production陽性対照

```yaml
id: PC-APP-POST-BUNDLE
source_relative_path: Data/Raw/AssetBundles_IOS/01fc2bee1d07da85dcfee3f3caf0f766.bundle
source_size: 211426
expected_exact_counts:
  _FilmWhiteClip: 4
  _Lut_Params: 322
failure_rule: any count other than exact expected is FAIL
```

### 9.4 陰性対照

```yaml
id: NC-TERM-MUTATED
payload_hex: 4830313537580a5f46696c6d5768697465436c69710a5f4c75745f506172616d740a
sha256: ac28c9aecc2b33216d354d0e16f81b94c50aa8e42eb2796d5134cf8695474c5a
expected_exact_counts:
  H0157: 0
  _FilmWhiteClip: 0
  _Lut_Params: 0
reason: all three tokens differ; exact matcher must not use prefix or fuzzy matching
```

### 9.5 verifier改竄試験

- production入力は変異させない。
- `input-manifest.json`のcritical SHAを1桁変え、抽出開始前に`INPUT_DRIFT`になることを検査する。
- stageコピーの`object-inventory.jsonl`を1行削除し、object分母不一致を検査する。
- stageコピーの抽出物を1byte変更し、output SHA不一致を検査する。
- 元に戻した後、全検査PASSを確認する。

### 9.6 再現性

同じinput set、parser registry、script SHAで別stageへ2回実行する。
時刻、run id、絶対stage prefixを除いたcanonical出力集合SHAが一致しなければFAIL。

## 10. R3 H0157非意味索引

```yaml
exact_terms:
  - H0157
  - HelenSSR0101
  - c_HelenDorm_Bedroom_0101
  - LoadRoomById
  - GFCharForward
  - GFCharFaceShadow
  - GFCharHairTransE
  - GFHairShadow
  - _RampMap
  - RampSetting
  - _Lut_Params
  - _FilmWhiteClip
```

### 10.1 exact hit

```yaml
term-hits.jsonl:
  required: [hit_id, exact_term, encoding, source_path, source_sha256,
             coordinate_space, offset, length, extract_id, parser_id, parser_sha256]
  forbidden: [family_id, interpretation, relevance, chosen]
```

### 10.2 構造edge

```yaml
reference-edges.jsonl:
  required: [edge_id, edge_type, from_source, from_offset, to_source, to_offset,
             parser_id, parser_sha256, evidence_bytes_sha256]
  edge_type: [serialized-pointer, metadata-token, method-call, object-link]
  forbidden: [co-occurrence, name-similarity, llm-inference]
```

edge verifierが同じ入力とparserで両端を再構成できない行は削除せず`rejected-edges.jsonl`へ移し、
R4入力にはしない。family分類はR4の担当である。

## 11. R3終端状態

```yaml
complete_with_candidates:
  requires: coverage_open=0 and failed=0 and partial=0 and term_hit_count>0
closed_zero:
  requires: coverage_open=0 and failed=0 and partial=0 and term_hit_count=0
  next: parent plan B3; do not enter R4
coverage_blocked:
  requires_any: coverage_open>0 or failed>0 or partial>0
  next: remain in P0-R2; do not enter R4
```

`closed_zero`は「exact termが0件」であり、「H0157の必要コードが現行アプリに存在しない」という意味ではない。

## 12. run receipt

```yaml
run-receipt.json:
  required:
    - contract_sha256
    - quality_gate_full_sha256
    - quality_gate_plan_check_exit
    - parser_registry_sha256
    - extractor_sha256
    - indexer_sha256
    - verifier_sha256
    - inputs_set_sha256
    - outputs_set_sha256
    - file_counts
    - container_counts
    - object_counts
    - range_counts
    - control_results
    - protected_set_results
    - terminal_state
    - stop_reason
```

実装者receiptと独立verifier receiptを別ファイルにする。前者だけでは次工程へ進めない。

## 13. 完了条件と停止条件

```yaml
completion_evidence:
  - production quality-gate plan check PASS
  - all 5326 current observation files remeasured and set hash fixed
  - parser registry reviewed and SHA fixed
  - file/container/object/range denominators reconciled
  - coverage_open=0
  - partial=0
  - failed=0
  - exact production controls PASS
  - negative and mutation controls PASS
  - verifier tamper tests fail at expected gates
  - restored tests PASS
  - rerun canonical output hashes match
  - R3 rows contain no semantic fields
  - protected sets unchanged
stop_conditions:
  - QUALITY_GATE_PLAN_FAIL
  - INPUT_DRIFT
  - PARSER_REGISTRY_UNREVIEWED
  - DENOMINATOR_MISMATCH
  - COVERAGE_OPEN
  - PARTIAL_PARSE
  - POSITIVE_CONTROL_FAIL
  - NEGATIVE_CONTROL_FAIL
  - NONDETERMINISTIC_OUTPUT
  - UNRECORDED_FAILURE
  - WRITE_SCOPE_VIOLATION
  - SEMANTIC_DECISION_REQUIRED
```

本契約の完了は、現行アプリ全域を固定parserで棚卸しし、H0157 exact hitと構造edgeを意味づけなしで作れたことだけを意味する。
原作一致、Blender候補完成、見た目受入れを意味しない。

## 14. 使わなかったもの・落とした情報

- 捨てたもの: AssetBundlesだけの分母、意味付きfamily分類、曖昧なunknown-code、production scriptへの直接書込。
- 手元でどう変わるか: app外側813件も棚卸しされ、未対応形式があれば抽出完了ではなく停止する。Blendの見た目は変わらない。
- 戻せるか: 分母縮小はしない。未対応形式はparser追加で閉じ、stage scriptはR3後に別契約で昇格できる。

## 矛盾・未確定

- quality-gate planは2026-09-11時点でPASS。Q0・Q0-CR・差分再審査フローは本番反映済み。
- P0許可の状態: registry の `p0_authorization.authorized` は false。
  同日 `protected_before_precheck` は `may_take_protected_before: false` を返した。
  観測したのはこの2つの値だけで、他の経路は点検範囲外。
- P0 parser registry、stage抽出器、索引器、verifierは未実装。
- 全5,326件の形式内訳、object/range分母、抽出総数は未測定。
- IL2CPP call edge parserの実動は未確認。未成立ならcoverage-openで停止する。
- 本v2契約の独立レビューと実行許可は、2026-09-11 時点で未実施。
