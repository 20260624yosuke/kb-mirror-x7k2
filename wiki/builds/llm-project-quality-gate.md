---
type: build
title: LLM高リスク成果物の品質ゲート
created: 2026-08-04
sources:
  - mityl-52-motion-failure-root-cause-2026-08-04
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-08-06
---

# LLM高リスク成果物の品質ゲート

## 目的

LLMが、自分で作った成果物を自分に都合のよい内部指標だけで合格させ、量産後に欠陥発見を
ユーザーへ委ねることを防ぐ。[[mityl-52-motion-failure-root-cause-2026-08-04]] の再発防止実装。

## 共通の状態遷移原則

本ゲートは [[llm-state-transition-gate]] の「状態は、その状態に適した肯定的な証拠があるときだけ
変える」を、高リスク成果物へ適用したもの。内部検査の合格や制作したLLMの成功報告だけでは、
`source-compared`(原作比較済み)、`user-accepted`(用途上の受入済み)、`batch-safe`(同種への量産可能)
へ遷移させない。各状態に必要な外部証拠が欠ける場合は、前の状態を維持する。

## 何が変わるか

- ユーザーが案件の特殊性やハルシネーション箇所を事前予知しなくてよい。
- LLMが外部正解・異形式変換・欠損入力・視覚品質・量産を自動で高リスク判定する。
- 立ち動作1本の承認を、歩行・座り・寝姿など別の対象群へ拡張できない。
- 原作比較、承認範囲、欠損入力、独立検証の記録が無ければ、判定器が量産前に失敗する。
- ユーザーは全件監査や原因診断をせず、LLMが示した差を許容するかだけ判断する。

## 構成

- `AGENTS.md` / `CLAUDE.md` の `1A. 高リスク成果物の品質ゲート`
- `tools/quality-gate.template.json` — 各プロジェクトへ置く記録の雛形
- `tools/project_quality_gate.py` — `plan` / `batch` / `complete` の3段階判定器
- `tools/tests/test_project_quality_gate.py` — 承認範囲・欠損入力・全件監査の回帰試験

## 自動適用条件

次のいずれか: コード外に正解がある再現、異なる実行系をまたぐ変換、難読化・分割・欠損入力、
見た目・音・操作感が完成条件、5件以上または異質な対象への量産。

明示コマンドをユーザーに要求しない。LLMが条件を読んで自動適用する。

## 3段階の機械判定

```bash
python3 tools/project_quality_gate.py check /path/to/project/quality-gate.json --phase plan
python3 tools/project_quality_gate.py check /path/to/project/quality-gate.json --phase batch
python3 tools/project_quality_gate.py check /path/to/project/quality-gate.json --phase complete
```

- `plan`: 正解資料、対象群、承認範囲、停止条件、責任分界が記録済みか。
- `batch`: 原作・代表入出力・比較証拠・独立検証報告が実在し、対象群ごとのユーザー許容と
  量産承認があるか。欠損入力があれば忠実版を失敗させる。
- `complete`: 要求数と成果物数が一致し、全件監査報告が実在して合格したか。

終了コードは合格0、失敗1。AGENTS.md / CLAUDE.md により、失敗時は対応段階へ進んではならない。

## 完了状態

概念上は次を区別する。

1. データ取得済み
2. 内部検査済み
3. 原作比較済み
4. 代表例をユーザーが許容
5. 対象群への量産可能

内部検査だけで5へ飛ばない。単一成果物でも `complete` 判定を使い、量産数を1として扱う。

## 欠損入力

Root移動、表情、小物、支持面など意味に必要な入力が欠ける場合、忠実版の `batch` 判定は失敗する。
推定版を作る場合は `mode: approximation`、`approximation_approved: true`、欠けた内容を
`accepted_gaps`へ記録する。これは「忠実に再現した」という意味には戻らない。

## 責任分界

- LLM: リスク判定、一次資料確認、欠損検出、比較資料、欠陥候補、停止、再検証。
- ユーザー: LLMが示した差を用途上許容するかの判断。
- 禁止: ユーザーへ全件監査、違和感箇所の探索、原因診断、完了報告の監査を委ねること。

## 限界

判定器は、記録漏れや承認範囲の無自覚な拡張を機械的に止める。LLMが虚偽の証拠や承認を記入する
悪用まで暗号学的に防ぐものではない。そのため検証役は制作報告を読んで追認せず、正解資料・入力・
出力を直接読む規則を併用する。

## 使わなかったもの・落とした情報

なし。既存プロジェクトや成果物は変更していない。品質ゲートは今後の高リスク実装と、再開時に
対象プロジェクトへ適用する。

## 関連リンク

- [[llm-state-transition-gate]] — 完了・承認・中断を証拠不在から推定しない共通原則
- [[mityl-52-motion-failure-root-cause-2026-08-04]]
- [[gf2-mityl-game-motion-transfer]]
