---
type: build
title: Helen H0157 — 現行アプリ全量コード再抽出 R0-R3 作業契約
status: proposed
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-09-09
last_reviewed: 2026-09-09
parent_plan: gf2-helen-h0157-current-app-reextract-workflow-plan-20260909
implementation_started: false
---

# Helen H0157 — 現行アプリ全量コード再抽出 R0-R3 作業契約

```yaml
contract_version: 1
task_id: H0157-R0-R3-CURRENT-APP-REEXTRACT
parent_goal: H0157 faithful Blender deliverable
execution_status: proposed-not-authorized
actor_class: cheap-model-implementer
semantic_decisions_allowed: false
input_mode: current-app-only
analysis_mode: extract-all-then-index-h0157
time_machine_required: false
specific_missing_bundle_required: false
blend_write_allowed: false
```

## 1. 固定入力

```yaml
inputs:
  - input_id: app-main
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/SnqxExilium
    sha256: 025aaafc9c49b6ba37b37678df01d67a7a408d41bb48bbfba0008f34412881b8
    role: app-entry
  - input_id: unity-framework
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Frameworks/UnityFramework.framework/UnityFramework
    sha256: 8be85a1c692b741be3619eba40b006c3133c2ff8a137030c34090650e75b5c4d
    role: native-il2cpp
  - input_id: global-metadata
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Data/Managed/Metadata/global-metadata.dat
    sha256: 10609117460b9375c6a9d768f3a489120ac5f4312597c12a99b2df597512a197
    role: il2cpp-metadata
  - input_id: app-assets
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app/Data/Raw/AssetBundles_IOS
    expected_regular_files_recursive: 4513
    role: bundle-and-code-assets
  - input_id: parent-blend
    path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
    sha256: 04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5
    role: read-only-artifact
```

ディレクトリ入力 `app-assets` は、R0で各regular fileの相対パス・size・mtime・SHA-256を列挙し、集合SHAを計算する。件数または固定ファイルSHAが一致しなければ、実装を始めず入力driftとして停止する。

## 2. 読み書き境界

```yaml
read_allowlist:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app/Wrapper/SnqxExilium.app
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/shader-source
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f166-code-inventory.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f166-msl-per-bundle.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f166_code_inventory.py
write_allowlist_template:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/audit/runs/<run-id>/stage/current-app-reextract/
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f202_current_app_code_extract.py
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f203_h0157_code_index.py
forbidden_writes:
  - SnqxExilium.app/**
  - 06_repro-v51/blends/**
  - quality-gate.json
  - 06_repro-v51/run-state.json
  - wiki/**
  - raw/**
```

## 3. 出力仕様

```yaml
outputs:
  input-manifest.json:
    required: [run_id, measured_at, fixed_inputs, app_asset_file_count, app_asset_set_sha256]
  file-inventory.jsonl:
    one_row_per: current app regular file
    required: [relative_path, size, mtime, sha256, detected_format]
  extraction-index.jsonl:
    one_row_per: extracted unique code object
    required: [extract_id, class_id, output_relpath, output_sha256, origins]
  failures.jsonl:
    one_row_per: unprocessed or failed object
    required: [source_path, source_sha256, source_offset, stage, error_class, error_text]
  controls.json:
    required: [positive_controls, negative_controls, mutation_tests, rerun_determinism]
  h0157-candidates.jsonl:
    one_row_per: exact match or observed reference edge
    required: [candidate_id, family_id, source_path, source_sha256, source_offset, matched_term, incoming_refs, outgoing_refs, positive_control_id]
  run-receipt.json:
    required: [extractor_sha256, indexer_sha256, inputs_set_sha256, outputs_set_sha256, counts, stop_reason]
```

`origins` は、同じコードが複数bundleにある場合も全出現元を保持する。抽出物名は連番ではなく、classと内容SHAから安定生成する。

## 4. R0 手順

1. 固定ファイル3件と親BlendをSHA-256で再測定する。
2. `app-assets` を再帰列挙し、regular file全件をrelative path順に並べる。
3. 各fileのsize、mtime、SHA、magicを記録する。
4. 正規化した全行から `app_asset_set_sha256` を生成する。
5. 入力不一致なら `INPUT_DRIFT` で停止し、抽出器を実行しない。

## 5. R1 手順

1. 各入力をformat分類する。分類不能でも削除せず `unknown` とする。
2. UnityFSを展開し、serialized objectとraw blobを列挙する。
3. MSL、ShaderLab、PE、IL2CPP metadata、script bytecode、unknown-codeを抽出する。
4. 抽出したbytesを内容SHAで重複排除し、全出現元を `origins[]` に残す。
5. 例外を握りつぶさず `failures.jsonl` へ一件ずつ保存する。
6. `processed_source_count + failed_source_count == inventory_source_count` を確認する。

## 6. R2 検査

### 正常対照

```yaml
positive_controls:
  - id: PC-APP-POST-BUNDLE
    source: 01fc2bee1d07da85dcfee3f3caf0f766.bundle
    expected:
      _FilmWhiteClip_min: 1
      _Lut_Params_min: 1
    prior_observation:
      _FilmWhiteClip: 4
      _Lut_Params: 322
  - id: PC-EXISTING-MSL
    source: ledger/shader-source
    expected_existing_files:
      fragment: 144
      post: 362
    claim_limit: comparison corpus only; not current total
```

### 陰性対照

- app内の非コード小ファイルを固定し、コード抽出0件になることを確認する。
- `unknown` を自動的にコード無しへ分類しない。

### 単一変異

- 隔離コピー上の陽性対照文字列を1バイト変え、対照FAILを確認する。
- manifestの入力SHAを1桁変え、開始拒否を確認する。
- `failures.jsonl` の1行を除き、分母不一致を確認する。
- 復元後に全検査PASSへ戻ることを確認する。

### 再現性

- 同じ入力と同じ抽出器を2回実行する。
- 時刻とrun_idを除いたcanonical outputの集合SHAが一致することを確認する。

## 7. R3 H0157索引規則

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
families:
  - mesh-static
  - motion-h0157
  - shading
  - hair-s8
  - unknown
```

- exact match、serialized pointer、method call、object referenceを別種として記録する。
- 同一ファイル内の共起を参照辺へ昇格しない。
- 難読名は、その名前だけでH0157候補へ昇格しない。
- 全候補の `interpretation` は空のまま返す。
- 候補0件でも、陽性対照と処理分母が合格するまで「現行アプリに無い」と書かない。

## 8. 完了条件と停止条件

```yaml
completion_evidence:
  - fixed input hashes matched
  - inventory denominator reconciled
  - all extracted objects have reversible provenance
  - all failures are enumerated
  - positive controls pass
  - negative controls pass
  - mutation tests fail as expected
  - restored tests pass
  - rerun canonical output hashes match
  - h0157 candidate rows contain no semantic conclusion
stop_conditions:
  - INPUT_DRIFT
  - DENOMINATOR_MISMATCH
  - POSITIVE_CONTROL_FAIL
  - NONDETERMINISTIC_OUTPUT
  - UNRECORDED_FAILURE
  - WRITE_SCOPE_VIOLATION
  - SEMANTIC_DECISION_REQUIRED
```

この契約の完了は「現行アプリからのコード再抽出とH0157候補索引が機械的に成立した」ことだけを意味する。H0157原作一致、Blender候補完成、見た目受入れを意味しない。

## 9. 使わなかったもの・落とした情報

- 捨てたもの: Time Machine探索、特定2bundle探索、旧f166出力の継続利用。
- 手元でどう変わるか: 現在読めるアプリ全量を再抽出し、H0157候補一覧を新しく作る。Blendの見た目は変わらない。
- 戻せるか: 全量抽出後に直接必要性が示された取得経路だけ、親計画B4で再選択できる。

## 矛盾・未確定

- `f202_current_app_code_extract.py` と `f203_h0157_code_index.py` は未実装。
- 出力run-idと実行時のapp asset集合SHAは未確定。
- 現行アプリから抽出できるコード総数は未測定。
- 独立レビュー、実行承認、実行結果はまだ無い。
