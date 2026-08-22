---
type: build
title: LLM状態遷移ゲート
created: 2026-08-06
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-13
related:
  - plan-gate-skill
  - llm-project-quality-gate
  - llm-cheap-model-execution-workflow
  - mityl-52-motion-failure-root-cause-2026-08-04
---

# LLM状態遷移ゲート

## 現在の統合見解

LLMは局所的な実装を進められても、自分が採用した前提の誤りや、外部にある正解との差を
自力で監査する場面では過信しやすい。再発防止を「注意深く振る舞う」という心構えに任せず、
**状態は、その状態に適した肯定的な証拠があるときだけ変える**工程として固定する。

無回答から中断を推定することと、内部数値の合格から原作一致を推定することは同型の失敗である。
どちらも「必要な証拠が無い」を、LLMに都合のよい次状態へ読み替えている。

対話・作業時の実動作規約は `AGENTS.md` / `CLAUDE.md`、個別スキルの分岐は各 `SKILL.md` が正本。
本ページは設計理由・共通状態表・事故記録の正本とする。

## 状態遷移の共通規則

状態を変える直前に、必ず次の3点を照合する。

1. 現在の状態。
2. 実際に観測した証拠。
3. その証拠で許される次の状態。

2が空なら状態を変えず、推測で埋めない。
`待機` / `承認待ち` / `実行` / `検証` / `完了` / `中断` / `技術的停止` は別状態であり、
必要な証拠を得ずに途中を飛び越えない。

| 判定対象 | 状態を変えてよい証拠 | 証拠にならないもの |
|---|---|---|
| 承認・中断・方針選択 | ユーザーの明示的な発言または選択 | 無回答、沈黙、離席、空回答、カード閉鎖 |
| 外部正解との一致 | 正解資料・入力・出力の直接比較 | 制作者の成功報告、内部数値だけの合格 |
| 実機反映・運用開始 | 末端の実機ワークフロー成功 | ビルド成功、配布物の存在、設定値だけの一致 |
| 技術的停止 | ツールやUIの明示的なエラー | ユーザーが返事をしなかったこと |

この規則は明示的な承認待ち・入力待ち・監視・外部状態の終端判定に適用する。通常の一問一答や、
承認関所のない通常作業の終了ごとに、追加の承認を要求する規則ではない。

## LLMが自分を信用しすぎない工程

- 制作したLLMの成功報告を、そのまま検証証拠にしない。
- 「エラーが出なかった」「内部数値が整った」「欠陥を発見できなかった」を外部一致へ言い換えない。
- 正解がコード外にある場合は、正解資料・入力・出力を直接読む。[[llm-project-quality-gate]] は
  この原則を高リスク成果物へ適用する具体的な機械ゲートである。
- 欠損入力を推定で埋める場合は、忠実版と分けて `approximation`(近似版)とする。
- 安価なモデルや実行担当へ機械作業を渡す場合も、終端判定を同じ担当の自己報告だけにしない。
  [[llm-cheap-model-execution-workflow]] を参照。

## 2026-08-06 Codex実運用事故

### 確認できた事実

- Codex Plan Mode で `request_user_input` を呼び、`autoResolutionMs` は設定していなかった。
- ツール結果は `{"answers":{}}` だった。
- LLMは非回答を待機として扱わず、最終回答を送ってターンを終了した。
- 武田さんはその間、席を外していた。
- タスクをアーカイブする操作は行われていない。
- 直前には、武田さんの「髪の整合性をどう保証するか」という質問を、LLMが
  「128コマ目の静止版を選んだ」と誤って確定していた。

### 未確認

- `{"answers":{}}` になったCodex UI内部の原因。タイムアウト、カードを閉じた操作、通信状態の
  どれだったかは確認できていない。

### 原因と修正

既存規則には「明示的な承認または中断だけが終了条件」とあったが、空回答時の具体的な分岐が
Codex版スキルに無かった。LLMは既存の終了条件にも違反し、証拠不在を終了理由へ変換した。

修正後は、空回答・離席・質問・懸念では状態を変えず、同じ承認待ちへ戻す。ツール自体の明示的な
エラーだけを技術的停止とし、ユーザーの承認・中断とは記録しない。plan-gate固有の分岐は
[[plan-gate-skill]] を参照。

## 実装先

- 共通の実動作規約: `AGENTS.md` / `CLAUDE.md` の「状態遷移ゲート」。
- plan-gateの状態・証拠・承認正本: `tools/plan_gate/contract.yaml`。
- Codexの承認カードとフック: 個人プラグイン `~/plugins/plan-gate/`。
- Claude Codeの承認カードとフック: `.claude/skills/plan-gate/`。
- 外部正解・視覚品質・欠損入力・量産: [[llm-project-quality-gate]]。

## 2026-08-12 plan-gate Response Gate化

plan-gate固有状態を `DISCOVER / EXECUTE / VERIFY / AUDIT / RETURN_COMPLETE /
RETURN_BLOCKED / USER_STOPPED` へ変更した。これは計画承認だけでなく、明示されたタスクを
Executorが自力で進められる限り続けるための状態機械である。

独立したAudit / Response Gate Agentが `CONTINUE / RETURN_COMPLETE / RETURN_BLOCKED` を判定する。
`CONTINUE` はユーザーへ返さずExecutorへ戻す。`RETURN_COMPLETE` は要求・作業・検証・事実性が揃い、
自力作業が残らない場合だけ許可する。`RETURN_BLOCKED` は本人しか行えない1操作が確定した場合だけ許可する。

APIエラー、利用上限、Hook失敗は中断情報として永続化するが、ユーザーへの返答判定へ変換しない。
旧 `RECOVERY_REQUIRED` は、状態欠落だけを理由に早期回答した実事故を再現したため撤廃した。

Codexでは独立監査の実在を子セッションJSONLの親子関係、revision、監査SHA、最終JSONで証明する。
ExecutorがHook入力を手動再現しただけでは監査を受理しない。詳細は [[plan-gate-skill]] を正本とする。

## 検証状態

- **実装済み**: 永続Controller、Executor台帳、独立Response Gate、Stop／質問／変更ツールガードを実装。
- **自動確認済み**: 必須A〜Eを含む32件、生成同期、全返答verdictの重大finding拒否、Codex／Claude子監査ログ証明。
- **Codex CLI実機確認済み**: 新規threadで実サブエージェント監査、子ログ証明、RETURN_COMPLETE、最終Stopまで完走。
- **Desktop実機確認済み**: Codex／Claudeとも未確認。CLIの結果をDesktopへ流用しない。

## 限界

会話UI内部の空回答発生自体は防げない。Codexのホスト型・特殊ツールにはフック対象外経路があり、
Claude Stopには8回上限、StopFailureには終了阻止不可という限界がある。フックを完全保証とせず、
永続状態、証拠台帳、事前監査、回帰試験と組み合わせる。

## 2026-08-13 plan-gateを計画承認専用へ戻す

schema 4 の Executor / Audit / Controller 型 Response Gate は、明示された任意タスクを最後まで進める
ための仕組みだった。これは「計画を出し、承認を取って、実装せず止まる」という plan-gate 本来の用途に
対して重すぎ、承認後に実装へ進まないという要件と衝突しやすい。

そのため `tools/plan_gate/contract.yaml` を schema 5 へ変更し、状態を `DISCOVERING / DRAFTING /
PRE_APPROVAL_AUDIT / APPROVAL_PENDING / REVISION_REQUESTED / APPROVED / USER_STOPPED /
TECHNICAL_STOP` へ戻した。Codex は `request_user_input`、Claude Code は `AskUserQuestion` を使う。
承認カードは `card_id` と `plan_sha256` に束縛し、無回答・空回答・タイムアウト相当・カード閉鎖・
古いカードの回答を承認や中断へ読み替えない。

高リスク案件では、計画をユーザーへ見せる前に `gpt-5.6-terra` / reasoning effort `medium` の
事前監査を完了させる。監査は承認待ち中には走らせない。指定モデル・effort を使えない場合は
`TECHNICAL_STOP` とし、代替モデルで近似しない。

`APPROVED` と `USER_STOPPED` は当該 plan-gate 呼出しだけの終端状態である。承認報告前の実装は
拒否するが、承認報告後は状態を archive し、次の別ユーザー指示を古い plan-gate 状態で妨げない。

## 使わなかったもの・落とした情報

- **作らなかったもの**: 会話状態を監視する新規スクリプト 0本。
- **落としたもの**: plan-gate schema 4 の汎用 Executor / Response Gate / 子ログ attestation。
- **手元でどう変わるか**: 外部プログラムがタスク画面を強制的に開き続ける仕組みは増えない。
  また、`plan-gate` は「任意タスクを完了まで自走させる道具」ではなくなる。代わりに、計画承認カードを
  保存し、非回答では同じカードへ戻し、承認後は実装せず止まる。修正後の空回答経路は実機未確認。
- **戻せるか**: 将来Codexが会話状態APIを提供した場合、規約を置き換えず、機械ゲートを追加できる。
  schema 4 の Response Gate 版は、必要なら別スキルとして復活できる。

## 変遷

- 2026-08-06: Codexの無回答終了事故と、武田さんの「LLMが自分を信用しすぎない工程を最初から
  強制する」という方針から初版を実装。全作業共通規約、両plan-gateスキル、既存品質ゲートへ接続。
- 2026-08-12: plan-gateをExecutor / Audit / Controller型Response Gateへ変更。早期の
  `RECOVERY_REQUIRED`返答を撤廃し、Codexでは実サブエージェントの子ログ証明を必須化。
- 2026-08-13: plan-gateを計画承認専用のschema 5へ戻した。高リスク計画の事前監査は残し、
  承認カード保存・非回答維持・承認後停止を中心に再設計。

## 関連リンク

- [[plan-gate-skill]] — 承認カード固有の状態遷移。
- [[llm-project-quality-gate]] — 外部正解・欠損入力・視覚品質・量産の機械ゲート。
- [[llm-cheap-model-execution-workflow]] — 実行担当と終端判定の分離。
- [[mityl-52-motion-failure-root-cause-2026-08-04]] — 内部指標を原作一致へ読み替えた失敗。
