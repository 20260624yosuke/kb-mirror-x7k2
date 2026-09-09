---
type: build
title: Helen H0157 — 現行アプリ全域の検証付き再抽出から忠実Blendまでの実行計画 v2
status: proposed
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-09-10
last_reviewed: 2026-09-10
revision: 2
implementation_started: false
implementation_gate: blocked
blocking_evidence: EA_KB_SNAPSHOT_STALE
supersedes:
  - gf2-helen-h0157-current-app-reextract-workflow-plan-20260909
first_task_contract: gf2-helen-h0157-current-app-reextract-task-contract-v2-20260910
user_view: wiki/_attachments/project-hub-index/20260909-h0157-current-app-reextract-plan.html
---

# Helen H0157 — 現行アプリ全域の検証付き再抽出から忠実Blendまでの実行計画 v2

## 0. 固定済みの目的と今回の状態

| decision_id | 決定 | 証拠状態 |
|---|---|---|
| D1 | 完成条件はH0157の忠実Blend。原作入力で裏づけられない近似を完成へ含めない | user-selected |
| D2 | 現在読めるドルフロ2アプリを全域棚卸しし、抽出後の意味分析はH0157に限定する | user-selected + v2で分母修正 |
| D3 | Time Machineと特定bundle探索を既定経路にしない | user-corrected |
| D4 | 結果を変える曖昧な分岐だけ、推論前に選択肢を出す | user-required |
| D5 | 廉価モデルは意味判断をせず、機械的な実装と列挙だけを担当する | user goalから導出 |

本版は計画修正までである。コード抽出、品質ゲート更新、プロジェクトスクリプト追加、Blend変更は開始していない。

現在の実装開始判定は `blocked`。2026-09-10に正本
`gf2-helen-starlit-waltz/quality-gate.json` を
`python3 tools/project_quality_gate.py check ... --phase plan` で直接検査し、
`EA_KB_SNAPSHOT_STALE: project-run-state: sha256 mismatch` を観測した。

## 1. 初回独立レビュー9件への処置

| review_id | 指摘 | v2の処置 |
|---|---|---|
| RV1 | 4,513件ではアプリ全体でない | app root全5,326 regular filesを分母に変更。AssetBundles 4,513件と外側813件は内訳に降格 |
| RV2 | quality-gateとの接続欠落 | 「不存在」は現物と不一致だったため撤回。親プロジェクトに実在する正本を使い、stale解消とplan PASSをQ0に固定 |
| RV3 | file分母とobject分母が混在 | file、container、object、byte-rangeの4分母を分離 |
| RV4 | `codeらしい`が意味判断 | 廃止。署名と固定parserだけを使うparser registryへ置換 |
| RV5 | R3のfamilyと参照辺が意味判断 | R3からfamily分類を除去。exact hitと構造edgeを別schemaに分離 |
| RV6 | 候補0件・未処理でも完了できる | coverage-open、partial、unsupportedをfail-closed。0件はR4へ進めず分岐カード |
| RV7 | 変異試験が入力SHA検査と衝突 | unit fixture、production対照、verifier改竄試験を分離 |
| RV8 | 集合SHAのcanonical化が未定義 | JSONLのencoding、正規化、key順、mtime除外、set SHA式を固定 |
| RV9 | allowlistと不変性検査が不十分 | 親Blendをread allowlistへ追加。実装物はstage限定、保護対象の前後集合SHAを独立検証 |

RV2について、正しい正本パスは
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json`。
`06_repro-v51/quality-gate.json` を新規作成しない。既存正本のSHAは2026-09-10観測時点で
`479f8a1daea14ff3e83597298140555d89488fd223ae2830c2a7b0d8a0f49141`。

## 2. 高リスク品質ゲート

### 2.1 正解の所在

- 現行 `SnqxExilium.app` 全域と、そこから復元可能なコード・参照・値。
- H0157へ接続する原作mesh、motion、material、shader、lighting、post処理。
- 原本Blendと候補Blendの直接差分。
- 最終的な見た目は、同一候補SHAを武田さんが確認した受入記録。

### 2.2 欠けうる入力

- 5,326 regular files全件の現行集合SHAと形式別内訳。
- Mach-O、UnityFS、PE、IL2CPP metadata、script bytecode等の全観測形式に対する固定parser。
- H0157から原作値まで閉じた参照鎖。
- 原作値からBlender値への単位・軸・フレーム込み変換式。
- 現行アプリ外または実行時生成でしか得られない入力。必要性は全域棚卸し後まで未確定。

### 2.3 性質の違う対象群

| family_id | 対象 | 合格を流用しない相手 |
|---|---|---|
| mesh-static | 全身形状、骨、ウェイト、shape key | motion、shading |
| motion-h0157 | H0157の300フレーム | 静止画、別action |
| shading | material、Ramp、顔影、照明、post | meshだけの一致 |
| hair-s8 | 髪mesh、透過、深度、被覆 | shading単独、mesh単独 |
| artifact | 候補Blend、画像、来歴 | 別SHAの候補 |

### 2.4 代表例

H0157一件だけ。別action、別衣装、水着、14件への量産へ展開しない。

### 2.5 原作比較方法

1. 抽出物を `source path + source SHA + coordinate space + offset + length + parser SHA + output SHA` へ結ぶ。
2. exact文字列hitと構造参照edgeを分離し、edgeは同じparserで再構成できるものだけ採る。
3. 原作値、変換式、Blender対象、候補SHAを一行で結ぶ。
4. mesh、骨、material、node、key、300フレームを候補Blendから独立readbackする。
5. 機械比較後、LLMが差を先に列挙し、武田さんは同じ候補SHAの見た目だけを受け入れる。

### 2.6 停止条件

- 正本quality-gateの`plan`検査がFAIL。
- 入力集合、parser registry、実行スクリプトのSHAが実行中に変化。
- file、container、object、byte-rangeのいずれかで分母不一致。
- `partial`、`unsupported`、`encrypted`、`obfuscated`、`runtime-generated-candidate`が1件以上残る。
- production陽性対照がexact期待値と不一致。
- 再実行したcanonical出力集合SHAが不一致。
- R3に意味分類、自然文解釈、共起由来edgeが混ざる。
- 原作値からBlender値への変換式、単位、軸、フレームが空。
- 未承認の推定または近似がfaithful候補へ入る。

## 3. 直接確認した入力と実装基盤

| input_id | 2026-09-10の観測 | 用途 |
|---|---|---|
| app-root | 5,326 regular files、158 directories、symlink 0 | R0の全分母 |
| app-assets | 4,513 regular files | app-rootの部分集合 |
| app-outside-assets | 813 regular files | 旧計画で分母外だった領域 |
| app-main | SHA `025aaafc9c49b6ba37b37678df01d67a7a408d41bb48bbfba0008f34412881b8` | Mach-O入口 |
| UnityFramework | SHA `8be85a1c692b741be3619eba40b006c3133c2ff8a137030c34090650e75b5c4d` | Mach-O / IL2CPP入力 |
| global-metadata | SHA `10609117460b9375c6a9d768f3a489120ac5f4312597c12a99b2df597512a197` | IL2CPP metadata入力 |
| parent-blend | SHA `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` | read-only親Blend |
| project-quality-gate | 実在、SHA `479f8a1d...f49141`、plan FAIL | Q0でstale解消が必要 |
| project-run-state | SHA `4752ff9a...af1074`。gate記録値`b176b17b...39fc8e`と不一致 | 現在のblocker |
| bundled runtime | Python 3.14、UnityPy 1.25.2、lz4 4.4.5 | UnityFS parser候補 |
| system tools | `/usr/bin/file`、`otool`、`nm`、`strings`、`dwarfdump` | Mach-O観測候補 |
| unavailable modules | dnfile、dncil、macholib、liefはbundled runtimeで未導入 | 存在を仮定しない |

旧`f166_code_inventory.py`はSHA
`5ec2fbfd4efbe4622774845bfa5a9105b48835e56a06f9337c3ed2bcea431c27`。
UnityFS非対応時のraw fallback、Shader以外のobject型内MSL対象外などの限界を明記しているため、
v2の全域抽出器そのものとしては採用しない。陽性対照とparser実装例だけ再利用する。

## 4. 役割境界

### 4.1 廉価モデル実装役

担当:

- Q0の固定式に従うstale snapshot候補作成とstage検査。
- parser registry、全file/container/object/range目録、抽出器、索引器、verifierの実装。
- 署名、件数、SHA、offset、length、exact hit、parser生成edgeの列挙。
- 固定済みcausal mapと変換契約に従う候補Blend生成。

禁止:

- byte列を見て「codeらしい」と決める。
- R3でmesh、motion、shading、hairへ意味分類する。
- 共起や名前の類似を参照edgeにする。
- unsupported、partial、0件を不存在または完了へ変える。
- 新しい取得経路、近似、完成、採用を決める。

### 4.2 フロンティア主担当

- parser registryで未対応形式を閉じる仕様判断。
- R3のhit/edgeと原文を直接読み、H0157のfamilyと因果参照鎖を決める。
- 原作値からBlender値への変換式を固定する。
- 見た目が変わる複数候補を、証拠・未知・失う見た目・戻し方付きで提示する。

### 4.3 独立検証役

- 実装者の説明を根拠にせず、入力、parser、抽出物、Blendを直接読む。
- 分母、range被覆、対照、変異、再現性、write scope、SHAを再実行する。
- Q0 promotion、R3からR4、R6からR7の境界で別receiptを発行する。

## 5. 実行工程

| phase | 主担当 | 出力 | 次へ進む肯定証拠 |
|---|---|---|---|
| Q0 gate-freshness | 廉価実装＋独立検証 | staged gate、fresh U0 snapshot、promotion receipt | 既存gate非audit内容不変、production `plan` PASS |
| P0 parser-freeze | 廉価実装＋フロンティア仕様確認 | `parser-registry.json`、writer分類、fixture | 全観測形式がdeterministic parserかcoverage-openへ排他的分類 |
| R0 app-tree-freeze | 廉価 | 全5,326件の`file-inventory.jsonl` | file数、critical SHA、canonical set SHA一致 |
| R1 coverage-inventory | 廉価 | container/object/range目録 | 4分母が一致し、gap/overlapがない |
| R2 extract-verify | 廉価＋独立再実行 | 抽出物、controls、verification | open coverage 0、exact対照、変異、復元、再実行すべてPASS |
| R3 h0157-index | 廉価 | `term-hits.jsonl`、`reference-edges.jsonl` | 意味分類0、全行に可逆来歴、candidate countを確定 |
| R4 causal-review | フロンティア＋独立検証 | `causal-map.json` | family別に原作値までedgeが閉じる |
| R5 value-freeze | 廉価 | mesh/motion/material/shading値JSON | 値、単位、軸、フレーム、出所SHA固定 |
| R6 blend-candidate | 廉価 | 新規候補Blend、readback、比較画像、receipt | quality-gate `batch` PASS、親不変、契約内変更、回帰PASS |
| R7 acceptance | 独立検証＋武田さん | 同一候補SHAの比較と受入記録 | 全family検証、見た目受入、quality-gate `complete` PASS |

Q0からR3の機械仕様は
[[gf2-helen-h0157-current-app-reextract-task-contract-v2-20260910]]を正本とする。

## 6. 「全域再抽出」の完了定義

`全域`は「アプリ内の意味をすべて理解した」ではない。次の4つを同時に満たす状態である。

```yaml
file_denominator:
  total: 5326 at plan observation; remeasure at R0
  invariant: total == accounted + coverage_open + failed
container_denominator:
  invariant: detected == fully_parsed + coverage_open + failed
object_denominator:
  invariant: enumerated == extracted + non_code_by_registry + coverage_open + failed
range_denominator:
  invariant: bytes_total == extracted + non_code_by_registry + padding + coverage_open
promotion_condition:
  coverage_open: 0
  failed: 0
  partial: 0
  overlaps: 0
  unexplained_gaps: 0
```

圧縮前file、展開block、serialized object、Mach-O sectionなどcoordinate spaceが違う分母を足さない。
各spaceごとに別式を持つ。

## 7. parser registryの条件

各parser行に次を必須とする。

```yaml
parser_id: stable-id
detector:
  byte_signature: exact bytes or exact structural predicate
  offset: integer
implementation:
  executable_path: absolute path
  executable_sha256: sha256
  version: exact version
input_unit: file | block | node | object | section | byte-range
output_units: explicit list
success_condition: machine predicate
unsupported_condition: machine predicate
coverage_rule: how all child units or bytes reconcile
```

初期registry候補はMach-O、UnityFS、serialized Unity object、PE/CLI、IL2CPP metadata、Lua bytecode、
plain text code、既知非code形式。検出規則を作れない形式は`opaque`ではなく`coverage-open`にする。
`file`コマンドの説明文、拡張子、entropyだけでcode/non-codeを決めない。

## 8. R3の非意味schema

exact hitと構造edgeを分ける。

```yaml
term_hit:
  required: [hit_id, exact_term, encoding, source_path, source_sha256,
             coordinate_space, offset, length, extract_id, parser_id, parser_sha256]
  forbidden: [family_id, interpretation, relevance, chosen]
reference_edge:
  required: [edge_id, edge_type, from_source, from_offset, to_source, to_offset,
             parser_id, parser_sha256, evidence_bytes_sha256]
  allowed_edge_types: [serialized-pointer, metadata-token, method-call, object-link]
  forbidden_edge_types: [co-occurrence, name-similarity, llm-inference]
```

family分類、関連性、採否はR4でフロンティア主担当が原文を再読して追加する。

## 9. 分岐規則

### B1 Q0、parser、抽出、検査がFAIL

- ユーザーへ選択を求めない。
- 同じ入力とstage内で、failure classを機械的に切り分ける。
- FAILをコード不存在または忠実再現不能へ変換しない。

### B2 coverage-openが残る

- 既存入力内の未対応形式なら、parser追加を同じ計画内の修正単位として実装・再レビューする。
- 新しい外部取得または前面GUIが必要になるまで、ユーザーへ選択を求めない。
- coverage-openが1件でもある間はR3完了にしない。

### B3 coverageが閉じてexact候補0件

次の3択をカードで出す。

| 選択 | 選ぶと失うもの |
|---|---|
| exact term集合を根拠付きで改訂 | 旧term集合だけで再現可能だったという前提 |
| 実行中ゲームまたはネット取得を設計 | ローカル完結、画面非占有、取得経路の単純さ |
| faithfulをblockedで維持 | 当面の候補Blend生成 |

Time Machineは武田さんが明示選択した場合だけ追加候補にする。

### B4 複数の因果候補でBlenderの見た目が変わる

- 推論前にカードを出す。
- 直接証拠、反証、残る未知、選ぶと失う見た目、戻し方を候補ごとに示す。

### B5 一意な因果参照が閉じる

- 選択肢を出さずR5へ進む。
- 独立検証のedge再構成receiptを必須とする。

### B6 機械検査PASS後の見た目差

- LLMが原作との差を先に列挙する。
- 同じ候補SHAを提示し、武田さんが受入れまたは差戻しを選ぶ。

## 10. write scopeと不変性

- Q0だけが、独立reviewとユーザー承認に結ばれたtransactional promotionで正本quality-gateを更新できる。
- P0〜R3の全実装物・fixture・出力は
  `06_repro-v51/audit/runs/<run-id>/stage/current-app-reextract/` 内だけに置く。
- `06_repro-v51/scripts/f202...`、`f203...`へ直接書かない。production昇格はR3合格後の別契約にする。
- app root、親Blend、quality-gate、run-state、Wiki、raw、production scriptsの前後集合SHAを取る。
- Q0後のquality-gateを含む保護対象に差分があれば`WRITE_SCOPE_VIOLATION`で停止する。
- 実装者とは別のverifierがstage外差分を再計測する。

## 11. ハルシネーションを成果物へ入れない条件

- 廉価モデルの自然文報告を状態遷移証拠にしない。
- parser registry外の分類を出力へ入れない。
- exact hit、構造edge、意味判断を別schema・別担当にする。
- 0件、unsupported、partialを成功にしない。
- 変換式の単位、軸、フレーム、原文位置が空ならBlendを生成しない。
- 検証した候補と提示した候補のSHAが違えば受入れへ進めない。
- quality-gateの`plan`、`batch`、`complete`を対応工程で実際に再実行する。

## 12. 現在状態

| item | state |
|---|---|
| ユーザー決定D1〜D5 | fixed |
| v2計画 | proposed |
| v2 Q0〜R3契約 | drafted |
| 初回独立レビュー | 9 findings、v2へ反映 |
| v2独立レビュー | pending |
| quality-gate plan | FAIL: EA_KB_SNAPSHOT_STALE |
| Q0〜R3実装 | not-started |
| Blend変更 | none |
| 親Blend | SHA `04ef8b79...d7f5`、未変更 |

## 13. 使わなかったもの・落とした情報

### AssetBundles 4,513件だけを全量分母にする案

- 捨てたもの: app内の全量を`AssetBundles_IOS`だけで代表する定義。
- 手元でどう変わるか: Frameworks、catalog、app mainなど外側813件も最初から棚卸しされる。Blendの見た目はまだ変わらない。
- 戻せるか: 戻さない。4,513件は内訳として残る。

### `codeらしいunknown`を廉価モデルが選ぶ案

- 捨てたもの: entropyや見た目でcode候補を選ぶ裁量。
- 手元でどう変わるか: 判定不能形式は抽出完了にならず停止する。Blendへ推測が入る経路が減る。
- 戻せるか: parser規則を追加して機械判定可能にすれば再開できる。

### production scriptsへ直接実装する案

- 捨てたもの: `06_repro-v51/scripts/f202...`と`f203...`へ最初から書く方法。
- 手元でどう変わるか: 実装と抽出結果はstage内に留まり、親Blendとproduction scriptsは変わらない。
- 戻せるか: R3合格後、独立review済みSHAだけを別契約で昇格できる。

### Time Machine・特定2bundleを既定経路にする案

- 捨てたもの: 消失ファイル探索を開始条件にすること。
- 手元でどう変わるか: 現行アプリ全域からH0157参照を取り直す。Blendの見た目はまだ変わらない。
- 戻せるか: B3で必要性が直接示され、武田さんが選択した場合だけ戻せる。

## 14. 根拠

- [[helen-h0157-new-agent-entry-20260909]]
- [[gf2-helen-h0157-current-app-reextract-workflow-plan-20260909]]
- [[gf2-helen-h0157-current-app-reextract-task-contract-20260909]]
- `tools/project_quality_gate.py`
- `tools/project_quality_gate_required_audits.json`
- `tools/quality-gate.template.json`
- `06_repro-v51/scripts/common.py`
- `06_repro-v51/scripts/a10_quality_gate.py`
- `06_repro-v51/scripts/audit_guard.py`
- `06_repro-v51/scripts/f166_code_inventory.py`
- `gf2-helen-starlit-waltz/quality-gate.json`

## 矛盾・未確定

- quality-gateのstale snapshotは未修正で、plan検査はFAILのまま。
- P0 parser registryとQ0更新器は未実装。
- 5,326件の全file SHA集合、形式内訳、object/range分母は未測定。
- IL2CPP method/call edgeを閉じるparserは現時点で実動未確認。
- 現行アプリに必要参照が無い場合の取得経路はB3まで未選択。
- v2の独立レビューと実装許可はまだ無い。

