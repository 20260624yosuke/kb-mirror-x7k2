---
type: build
title: H0157 — 現行状態復旧と実行カプセル作成契約
status: active
confidence: high
evidence_level: user-stated+source-backed
created: 2026-09-10
last_reviewed: 2026-09-10
contract_version: 1
execution_scope: recovery-only
recovery_execution_authorized: true
implementation_authorized: false
target_model: gpt-5.6-sol
user_view: wiki/_attachments/project-hub-index/20260910-h0157-kb-conflict-recovery-guide.html
---

# H0157 — 現行状態復旧と実行カプセル作成契約

## 0. この契約だけで開始する

この契約の目的は、H0157の既存成果と証拠を現物から再照合し、次の担当が巨大なKB全体を
再解釈せずに作業できる小さな実行カプセルを作ることである。

これはHelenの再抽出、Blend修正、Git導入、汎用ワークフロー開発の契約ではない。
旧計画の修正も行わない。

```yaml
task_id: H0157-ACTIVE-STATE-RECOVERY-20260910
actor: gpt-5.6-sol
input_entry: this file only
mode: evidence-reconciliation
semantic_guessing: forbidden
blender_write: forbidden
extraction_write: forbidden
quality_gate_write: forbidden
git_init: forbidden
raw_write: forbidden
```

## 1. 完成条件

次の5ファイルを新規作成し、相互のIDとSHAが一致した時点で終了する。

```text
wiki/builds/h0157-active/
  CURRENT.json
  EVIDENCE.jsonl
  TASK.md
  decisions/0001-active-baseline.md
  receipts/recovery-readback.json
```

- `CURRENT.json`: 現在採用できる成果物、状態、証拠ID、未解決点を1か所に固定する。
- `EVIDENCE.jsonl`: 実測事実ごとに、証明できることと証明できないことを分ける。
- `TASK.md`: Blender完成までの「次に不足している接続」を1件だけ記す。
- `0001-active-baseline.md`: このカプセルを現行入口とする決定、代償、戻し方を記す。
- `recovery-readback.json`: 別読みによるpath、SHA、JSON、参照IDの照合結果を記す。

完成後も、Helenの見た目、原作一致、抽出完了を達成したとは報告しない。

## 2. 高リスク項目

| 項目 | この契約での固定 |
|---|---|
| 正解の所在 | 現在ディスクにあるBlend、コード、ログ、アプリ入力と、直接のユーザー判断 |
| 欠けうる入力 | 消失cache、未回収runtime情報、未再実行の抽出、原作見た目の比較資料 |
| 性質の違う対象群 | Blend成果物、抽出コード、実行ログ、Wiki判断、ユーザー受入れ |
| 代表例 | H0157だけ。他action・水着・別キャラへ展開しない |
| 比較方法 | path・full SHA・生成コマンド・出力を直接読み、主張ごとに証拠IDへ結ぶ |
| 停止条件 | 根拠不明、SHA変化、意味推測が必要、前面GUIが必要、H0157外へ拡大 |

## 3. 読み取り優先順位

同じ主張が衝突した場合、文章の新しさだけで選ばない。次の順で採用可否を決める。

1. 現在のファイル実体を直接readbackした値とfull SHA。
2. 入力・実行コマンド・exit code・陽性対照を持つ機械ログ。
3. 出典のsession/eventを特定できるユーザー判断。
4. `status`、`evidence_level`、`last_reviewed`を持つ現行Wikiページ。
5. 旧計画、引き継ぎ、要約、Muse由来判断。探索候補にだけ使い、状態証拠にはしない。

上位同士が衝突した場合は片方を選ばず、両方を`contested`として記録する。

## 4. 最初に直接確認する場所

次のパスは証拠候補であり、記載内容をそのまま事実として転記しない。

```text
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/common.py
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f166_code_inventory.py
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f166-code-inventory.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f166-msl-per-bundle.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/helen-h0157-new-agent-entry-20260909.md
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md
```

追加ファイルを読むのは、上記の行が直接参照しており、特定の主張の採否に必要な場合だけとする。
KB全域走査から始めない。

## 5. 証拠schema

`EVIDENCE.jsonl`は1行1主張とし、最低限次を持つ。

```yaml
evidence_id: stable-id
claim: 観測した事実だけ
status: current | stale | contested | unverified
source_path: absolute path
source_sha256: full sha256
observation_method: command or exact reader
observed_at: ISO-8601
proves: この証拠だけで言えること
does_not_prove: 原作一致や完成など、言えないこと
provenance: source-backed | user-stated | llm-inferred
```

`llm-inferred`は`CURRENT.json`の状態遷移根拠に使わない。

## 6. CURRENT.jsonの条件

```yaml
required:
  - schema_version
  - project_id
  - goal
  - current_artifacts
  - established_facts
  - contested_claims
  - stale_claims
  - excluded_context
  - next_gap_id
  - evidence_set_sha256
```

- `current_artifacts`は存在確認とfull SHAを持つものだけ。
- `established_facts`は全行が`EVIDENCE.jsonl`のcurrent証拠IDを参照する。
- `excluded_context`には旧計画、Muse判断、未再実行結果を理由付きで入れる。
- 不明な値を埋めず、`unverified`または`contested`に置く。

## 7. TASK.mdの条件

記載する作業は1件だけ。次の全項目が埋まらなければ、実装ticketにしない。

```yaml
gap_id: one exact missing connection
goal_effect: 解消するとHelenの成果物で何が進むか
known_inputs: paths and sha256
missing_evidence: exact unknown
allowed_actions: fixed list
forbidden_actions: fixed list
expected_outputs: fixed list
mechanical_checks: fixed list
frontier_decision_required: yes or no with reason
cheap_model_delegable: yes or no with reason
stop_condition: exact condition
```

「自由に調べる」「必要なら広げる」「全域を作り直す」を書かない。

## 8. 作業順序

1. 上記10パスの存在、size、mtime、full SHAをread-onlyで記録する。
2. 各ファイルから主張を抽出し、直接証拠、推測、旧版を分ける。
3. 同じ対象の矛盾を一覧化し、根拠が勝たないものは`contested`にする。
4. 既存成果からH0157完成条件を差し引き、未接続部分だけを並べる。
5. ユーザーの見た目判断を要せず、次に機械的に解消できる1件を`next_gap_id`にする。
6. 5ファイルをstage名ではなく上記正規出力先へ作る。
7. 全JSON parse、全参照ID、全source SHAを別プロセスで再読し、receiptを作る。
8. `CURRENT.json`と`TASK.md`の内容が一致したら終了する。

## 9. 書き込み範囲

```yaml
write_allowed:
  - <KB-root>/wiki/builds/h0157-active/**
  - <KB-root>/index.md
  - <KB-root>/log.md
write_forbidden:
  - <KB-root>/raw/**
  - <project-root>/quality-gate.json
  - <project-root>/06_repro-v51/**
  - any .blend
  - any app bundle
  - any existing H0157 plan or handoff
  - any git metadata
```

既存ファイルは`index.md`とappend-onlyの`log.md`以外変更しない。

## 10. 終端状態

```yaml
complete:
  requires:
    - five deliverables exist
    - every current claim has a current evidence id
    - all recorded source hashes match readback
    - no inferred claim drives next_gap_id
    - TASK contains exactly one gap
    - receipt PASS
blocked:
  when:
    - current artifact cannot be identified
    - competing direct evidence cannot be reconciled
    - next gap requires user visual judgment
    - required source is unreadable or missing
```

`blocked`でも推測で埋めない。確認済み部分と技術的停止点を5ファイルに残して終了する。

## 11. 使わないもの・落とすもの

- 捨てるもの: 旧計画を実行入口として読むこと、KB全域を最初から検索すること、Gitを先に導入すること。
- 手元でどう変わるか: HelenのBlendは変化しない。新しい担当が読む入口だけが小さくなる。
- 戻せるか: 既存Wikiとプロジェクトは削除・移動しないため、必要な旧資料はいつでも読み直せる。

## 12. 現在状態

- ユーザーは2026-09-10に、この方針で進めることを明示した。
- 本契約の作成と別タスクへの引き渡しは許可済み。
- 実行カプセル作成は新しいタスクで開始する。
- Helen抽出、Blend変更、Git初期化は未許可であり、本契約の対象外。
