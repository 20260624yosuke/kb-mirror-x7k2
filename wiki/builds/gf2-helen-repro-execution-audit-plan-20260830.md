---
type: build
title: Helen 原作再現 実行保証・機械監査計画 2026-08-30
status: active
confidence: medium
evidence_level: user-stated+source-backed
created: 2026-08-30
last_reviewed: 2026-08-30
plan_status: draft-unapproved
related:
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
  - "[[brainstorm-gf2-dusevnyj-bikini-to-helen]]"
tags: [gf2, helen, audit, quality-gate, execution-plan]
revision: 1
---

# Helen 原作再現 実行保証・機械監査計画 — 2026-08-30

> [!warning] 承認状態
> **草案・未承認・未実装。** これは既存の技術計画を置き換えない。既存計画が「何を調べ、何を
> Blenderへ実装するか」を担当し、本計画は「どの証拠が揃えばLLMが次へ進み、完了と言ってよいか」
> を担当する。承認されても、このbrainstorm中には実装しない。

## 目的と完成条件

目的は、**コードから Helen の原作再現を Blender 成果物として成立させること**である。
監査ハーネス自体を成果として数えず、未回収入力・因果未確認・古い結果があるのに候補Blendを書き出す、
または完成と報告する経路を機械的に止める。

最低限の完成条件は次のとおり。

1. S6・S8・G10が共通スキーマの別々の契約になり、正解、現在値、対象範囲、候補因果、反証、
   変異試験（わざと壊して検査が落ちるか確かめる試験）、非対象、状態を機械判定できる。
2. 小さい監査状態ファイルを正本とし、既存run-stateとの矛盾、古い証拠、入力・writer・Blendの
   SHA-256（ファイル内容を識別する値）不一致をFAILにする。
3. 候補Blend書出し前と既存品質ゲートの `complete` 判定の両方へ同じ監査を接続する。
4. 未解決の現行G10を「正しく `blocked`」に止め、根拠のない候補書出しと完成報告を拒否できる。
5. 監査が通っても、原作一致・実機確認・運用可能を自動では主張しない。

## 高リスク成果物の6点ゲート

| 項目 | 本計画で固定する内容 |
|---|---|
| 正解の所在 | 原作H0157の比較フレーム、回収コード、Unityのprefab・renderer・material参照、原作実機の見た目 |
| 欠けうる入力 | InternalLut、実行時の髪処理、Helen本人のprefab・material・RampSetting参照、`f166`の最終再実行結果 |
| 性質の違う対象群 | S6＝顔の白飛び、S8＝髪の被覆・形、G10＝renderer・材質・RampSetting対応 |
| 代表例 | **G10**。Ramp値の転写ではなく、権威ある割当の参照鎖不足を扱う |
| 原作比較方法 | `f152`・`f128`・`gate-results.json`、参照IDの直接追跡、候補Blendと原作フレームの比較 |
| 停止条件 | SHA・対象範囲・参照鎖の不足、未解決指摘、古い結果、未登録writer、別Blend、監査未接続 |

## 作るファイルと既存系への接続

新設先は `06_repro-v51/audit/` とする。ただし、置くだけのフォルダは禁止する。

```text
06_repro-v51/
├── audit/
│   ├── contract.schema.json
│   ├── S6.json
│   ├── S8.json
│   ├── G10.json
│   ├── state.json
│   └── review-findings.json
└── scripts/
    └── audit_guard.py
```

- 候補writerは書出し直前に
  `audit_guard.require_candidate_write(defect_id, input_sha, writer_sha)` を呼ぶ。
- `scripts/a10_quality_gate.py` は、プロジェクト直下の `quality-gate.json` に任意の
  `execution_audit` を生成する。
- Wiki側の `tools/project_quality_gate.py` は `execution_audit` がある場合、`plan`・`batch`・
  `complete` で `audit_guard` を呼ぶ。`complete` では契約、指摘、証拠SHA、`f128`・`f152`・
  `gate-results.json` が同じBlendを見ていることまで照合する。
- OSやBlenderによる任意のファイル作成そのものは物理的に禁止できない。代わりに、未登録成果物は
  SHA不一致で採用・完了報告できないようにする。

## 実装順

### P0 現在地を凍結する

Blendを変更せず、現行Blend SHA、S6・S8・G10、`f166`、run-state、既存ゲートの版と結果を記録する。
食い違いは推測で直さず、初期指摘として台帳へ入れる。

### P1 契約と監査器を作る

共通スキーマ、3契約、監査状態、指摘台帳、`audit_guard.py` を作る。状態は
`scoped → coverage_verified → candidate_traced → causal_tested → artifact_candidate →
artifact_measured → human_review → accepted` とし、途中停止は `blocked`・`contested`・`rejected`
で区別する。

変異試験は、監査呼出し削除、古い証拠、別Blend、監査manifest欠落、open指摘を最低限含め、
すべてFAILを返すことを確認する。

### P2 二重接続を実装する

候補writerの書出し前と、既存品質ゲートの完了判定へ接続する。専用ランナーへの一本化は行わず、
既存スクリプトの独立実行は残す。ただし独立実行の結果を正式成果へ採用するには、監査記録との一致を要する。

### P3 G10で最初の通し試験を行う

これはG10の見た目修正試験ではなく、**執行力の試験**である。現状はRampの4帯の数値転写が
最大誤差0.00039（許容0.001）でも、Helen本人の
`renderer → submesh → material → RampSetting` の権威ある参照鎖がない。

したがって最初の正しい結果は `scoped` または `blocked` であり、次を実演できれば通し試験は成功とする。

1. 根拠のないG10候補書出しを拒否する。
2. 現行プロジェクトの `complete` 判定をFAILにする。
3. 不足している参照鎖と再開点を短い状態報告へ出す。

実際のG10候補writerができた時点で、その絶対パスとSHAを登録し、拒否経路と許可経路の両方を
実演する。そこまで確認できなければ「候補writerへの完全接続済み」とは報告しない。

### P4 S6・S8を初期化する

S6・S8も同じ深さの契約として初期状態を記録する。この工程ではBlend変更、候補制作、`f166`再実行をしない。

### P5 短い状態報告を生成する

正本から「現在段階、通った関所、停止理由、次の1件」だけを生成する。長い引き継ぎ文を現在状態の
代わりにしない。

## してはいけないこと

- 既存のHelen原作再現計画を書き換える。
- Blend、抽出コード、`f166`結果、原作入力をこの計画の実装中に変更する。
- 未回収の参照対応を他キャラや既定の白黒Rampから推定し、忠実版として扱う。
- S6・S8・G10の合格線を通りやすく下げる。
- ハーネス完成を、Helen原作再現またはG10解決の完成と言い換える。
- 原作再現以外へ一般化するための汎用CI、常駐reviewer agent、専用ランナーを先に作る。

## 機械が決めないこと

- 見た目の差が原作用途として許容できるか。
- 権威ある入力が回収できないとき、忠実版を止めたままにするか、失う見え方を示した近似版へ分けるか。
- G10が通った後にS6・S8のどちらを技術実装へ進めるか。

## 捨てた案と、選択で失うもの

- 既存計画の全面改訂: 技術上の未完了点とLLMの進行欠陥を一冊にまとめる単純さを失う。代わりに、
  既存の技術判断を壊さず監査だけを交換できる。
- 専用ランナー一本化: 一つの入口だけ見れば済む単純さを失う。代わりに、既存スクリプトの独立性を残す。
- S6先行: 見た目が分かりやすい欠陥から早く直し始める速さを失う。代わりに、入力不足を正しく止める
  G10で、監査の執行力を先に証明する。
- 3契約を1 JSONへ統合: 一覧性を失う。代わりに、1件の更新で別欠陥の状態が動く事故を避ける。

## 実装時の変更対象

- 新規: `06_repro-v51/audit/` 内の6ファイル、`06_repro-v51/scripts/audit_guard.py`
- 変更: `06_repro-v51/scripts/a10_quality_gate.py`
- 変更: Wikiの `tools/project_quality_gate.py`
- 将来変更: 最初に実在するG10候補writer。作成前は「writer接続待ち」と明記する
- 読み取りのみ: 現行Blend、`f128`、`f152`、`f166`、`gate-results.json`、run-state、原作入力

## 実装後に取る次の承認

本計画の実装とG10停止試験が完了しても、自動的に原作再現の技術作業へ進まない。次は、既存計画にある
G10の参照鎖回収を実行してよいかを別に承認し、その後S6・S8の展開順を決める。

## 使わなかったもの・落とした情報

なし。この文書は計画草案であり、Blend、コード、入力、検査結果を変更していない。
