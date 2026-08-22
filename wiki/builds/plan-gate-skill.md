---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-13
sources:
  - tools/plan_gate/contract.yaml
  - tools/plan_gate/plan_gate_guard.py
  - tools/plan_gate/tests/
  - tools/plan_gate/templates/
  - AGENTS.md
  - CLAUDE.md
---

# plan-gate — 計画承認ゲート

## 現在の目的

`plan-gate` は、実装前に計画を作り、ユーザーの明示承認を取ってから止まるためのゲート。
作業を最後まで自走する実行ハーネスではない。承認後も実装には進まず、実装は別の明示指示が必要。

入口は Codex の `$plan-gate` と Claude Code の `/plan-gate`。暗黙起動しない。通常相談、通常レビュー、
llm-wiki ingest/query、`/transcript`、`grill-build` は対象外。

正本は Wiki ルートの `tools/plan_gate/`。`generate.py --write` が Codex 個人プラグイン
`~/plugins/plan-gate/` と Claude スキル `.claude/skills/plan-gate/`、`AGENTS.md` / `CLAUDE.md`
の generated block を同期する。

## 制御構造

- **Plan Author**: 現状を読み、計画を作る。実装はしない。
- **Pre-Approval Audit Agent**: 高リスク案件だけ、計画をユーザーへ出す前に `gpt-5.6-terra` /
  reasoning effort `medium` で監査する。承認カード待機中には走らせない。
- **Approval Controller**: 永続 JSON と Hook で、計画本文、`plan_sha256`、承認カード、非回答、承認、
  中断、技術停止を記録する。

## 状態

schema 5 の状態は次の8つ。

- `DISCOVERING`: 依頼・関連ファイル・環境を読む。
- `DRAFTING`: 計画を作る。計画保存時に旧カード・旧監査は無効化する。
- `PRE_APPROVAL_AUDIT`: 高リスク計画の事前監査。指定モデル・effort 以外では進めない。
- `APPROVAL_PENDING`: `request_user_input` / `AskUserQuestion` の承認待ち。
- `REVISION_REQUESTED`: 「ここを直す」が選ばれたが、具体的な修正文が未受領または反映前。
- `APPROVED`: 明示承認。承認報告後に状態を archive し、次の明示依頼を妨げない。
- `USER_STOPPED`: 明示中断。中断報告後に状態を archive する。
- `TECHNICAL_STOP`: ツール・UI・指定監査モデル・状態保存の明示的エラー。承認・中断とは扱わない。

## 承認カード

Codex では `request_user_input`、Claude Code では `AskUserQuestion` を使う。
カードは状態ファイルに次を保存してから出す。

- `card_id`
- 質問 id
- 質問文
- 選択肢ラベルと順序
- 対象 `plan_sha256`
- 計画本文

空回答、無回答、`null`、空文字、タイムアウト相当、カードを閉じただけ、古い `card_id`、古い
`plan_sha256`、重複回答は承認でも中断でもない。`APPROVAL_PENDING` を維持し、保存済みの同じカードを
再提示する。

Codex UI について「カードが無期限に開き続ける」とは断言しない。空結果やタイムアウト相当が起きても、
保存済みの plan/card から再開できる設計にする。

## 高リスク計画

高リスク判定は `AGENTS.md` / `CLAUDE.md` の 1A を正本にする。コード外に正解がある再現、異なる実行系を
またぐ変換、入力欠損、見た目・音・操作感、5件以上または性質の違う対象への展開などが対象。

高リスクでは、計画提示前に次の3条件が必要。

- `gpt-5.6-terra`
- reasoning effort `medium`
- 同一 `plan_sha256` で major finding なし

指定モデル・effort を使えない場合は `TECHNICAL_STOP`。代替モデルで近似監査して承認カードへ進めない。

## 検証状態（2026-08-13）

- **実装済み**: schema 5、承認カード永続化、`plan_sha256` 結合、非回答維持、技術停止、schema 4 退避、
  高リスク事前監査、未承認中の変更ツール拒否、承認/中断/技術停止の Stop marker、生成同期。
- **自動試験済み**: 24件合格。明示起動、権限、生成同期、Codex/Claudeカード、空回答、古いカード、
  高リスク監査、モデル/effort不一致、major finding、schema 4退避、承認後archiveを含む。
- **生成物同期済み**: `~/plugins/plan-gate/`、`.claude/skills/plan-gate/`、`AGENTS.md`、`CLAUDE.md`。
- **Codexプラグイン導入済み**: 個人マーケットプレイス `personal` から
  `2.0.0+codex.20260813132646` を再インストール。旧 `2.0.0+codex.20260812093119` の hook root は
  互換ブリッジ復元済み。
- **実機確認**: Codex Desktop / Claude Code の新規実タスクでは未確認。自動試験の合格を実機運用合格へ
  言い換えない。

## 使わなかったもの・落とした情報

- **落としたもの**: v2 schema 4 の Executor / Audit / Response Gate / revision ledger / 子ログ
  attestation / `RETURN_COMPLETE` / `RETURN_BLOCKED`。
- **手元でどう変わるか**: `plan-gate` は「作業が終わるまで返さない」道具ではなくなる。
  代わりに、計画承認カードの保存・再提示・承認後停止に集中する。汎用タスクの早すぎる完了を
  plan-gate単体で止める力は弱くなる。
- **戻せるか**: 旧 schema 4 は `tools/plan_gate/backups/` と過去生成状態に残る。必要なら schema 4 の
  Response Gate 版を別スキルとして復活できるが、計画承認専用の `plan-gate` へ混ぜない。

## 変遷

- 2026-07-11: 計画承認用の初版を作成。
- 2026-08-12: 証拠・承認・永続復旧を持つ v2 へ更新。
- 2026-08-12: `RECOVERY_REQUIRED` 早期返答事故を受け、任意タスクを最後まで進める Executor /
  Audit / Controller 型 schema 4 へ変更。
- 2026-08-13: schema 4 は目的に対して重すぎるため、`plan-gate` を計画承認専用の schema 5 へ戻した。
  高リスク案件の事前監査だけを残し、承認後は実装せず停止する設計に変更。

## 関連リンク

- [[llm-state-transition-gate]] — 肯定的証拠だけで状態を進める共通原則。
- [[llm-project-quality-gate]] — コード外の正解を扱う品質ゲート。
