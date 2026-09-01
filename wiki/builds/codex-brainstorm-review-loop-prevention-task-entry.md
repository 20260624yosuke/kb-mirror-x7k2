---
type: build
status: active
confidence: high
evidence_level: user-stated+source-backed+inferred
last_reviewed: 2026-09-01
implementation_status: unapproved
owner_route: separate-agent
sources:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-review-loop-mechanical-prevention-design.md
---

# Codex brainstorm レビューループ防止 — 別エージェント用タスク入口

**この文書だけを入口に、今回の会話を読まずに調査を始められる。**

## ユーザーの現在判断

- 環境整備はH0157本題の会話で実装せず、別エージェントへ分離する。
- H0157原作再現の方が優先度は高い。この環境修理をHelen計画・Blend・f166・quality-gateへ混ぜない。
- 「担当者がレビュー済みファイルを触らないよう注意する」だけの解決は不承認。機械的拒否が必要。
- このページ作成は承認済み。**環境コードの実装方式と変更範囲は未承認**なので、着手エージェントは読取監査と具体差分を先に提示する。

## 観測した問題

一本化計画revision 4の独立レビューで、3回目にCritical 0 / Major 0 / Minor 0を得た後、その結果をレビュー入力であるcurrentへ書き戻した。currentのSHAが変わり、同じ意味内容の最終再レビューがもう1回必要になった。

これは利用上限やモデル性能ではなく、**レビュー出力からレビュー入力へ戻る辺を許した依存関係**が直接原因。現在のCodex brainstorm guardには、review入力をfreeze（凍結）し、合格後の書戻しや自動再reviewを拒否する契約がない。

今回のHelen計画は最終receiptで終端済み。以下4件はこの環境修理で変更してはならない。

| 役割 | 絶対パス | 2026-09-01 SHA-256 |
|---|---|---|
| 一本化revision 4 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md` | `04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a` |
| current | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md` | `5bb60fb5fab92d7fa8c8d310b4318f6121ef67df8aadca9b932d7b61f56ad87e` |
| 具体計画 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-h0157-mechanical-audit-concrete-integration-plan.md` | `cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f` |
| 最終receipt | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-unified-route-revision4-independent-review.md` | `83ecf2868e835bd3cd6466d19c42be58a3ad85533a0f4a4bff4d58f03a41b7ea` |

## 最初に読む実ファイル

| 役割 | 絶対パス | 2026-09-01 SHA-256 / 注意 |
|---|---|---|
| Codex実接続先 | `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py` | `29caedcdf0cee738f943ba3611297362e569042d1cc6e0807e719e0a029d0cb8` |
| 既存単体試験 | `/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py` | `7c53f05774c4b9c0b980480db3f178a3cfe59dbfc2ed1902e458b92e6853dd7b` |
| Codex hook登録 | `/Users/takedayousuke/.codex/hooks.json` | `e2e70d262b040e6e2210de4b7c002bb78b922db35f734447584c735b1d1b083a`。PreToolUse `matcher=*` がadapterの `pre-tool` を呼ぶ |
| brainstorm正本 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md` | `e2b50d3e6ed3efe89063c0c87437cd0364b253bcfe81e39d4cec180130732fbe`。2026-08-28 reviewでClaude中心の記述を含むため、Codex現物より優先しない |
| 今回の設計案 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-review-loop-mechanical-prevention-design.md` | `60a50e2a6416ca125b911e4f5019a8e6589cd7c007ab8b0960fa739255ba869f`。未承認案 |

## 現物から分かること

- `codex_adapter.py` の `pre_tool` は `request_user_input`、`apply_patch`、`exec_command` を既に検査する。
- `apply_patch` はbrainstorm承認前の書込先を親メモと `sessions/` に限定するが、レビュー済み入力の凍結は持たない。
- `exec_command` は承認前だけ読取専用形式へ限定し、承認後のレビュー入力書戻しを止めない。
- `hooks.json` のPreToolUseは既に全toolへ接続済み。新しい常駐サービスや別hook系統を増やす必要はない可能性が高い。
- 既存試験にはカード、親選択、stale card、書込境界、shell迂回がある。review freezeの試験は無い。

## 検討中の最小案（未承認）

選択済み親の `sessions/` に `<review-id>.review-lock.json` を1件置き、既存PreToolUseで読む。中央の第二正本は作らない。

必須欄候補:

```json
{
  "schema_version": 1,
  "review_id": "...",
  "status": "passed",
  "receipt_path": "/absolute/path/to/receipt.md",
  "receipt_sha256": "...",
  "locked_inputs": [
    {"absolute_path": "/absolute/path", "role": "plan", "sha256": "..."}
  ]
}
```

必要な機械動作候補:

1. `apply_patch` によるlock対象のAdd / Update / Delete / Moveを拒否。
2. 非読取 `exec_command` がlock対象を直接・変数・glob・親ディレクトリ経由で書く場合を拒否。不明なら技術的停止。
3. receiptを入力集合へ入れる循環と、passed済みmanifestの再reviewを拒否。
4. 入力変化を検出しても自動再reviewしない。新revisionは理由・対象・失うものを示した別承認後だけ発行。
5. 親メモ、sessionsの別メモ、説明HTMLなどlock外は変更可能。

## 着手エージェントが最初に提出するもの

コードを変更する前に、次の5点を1枚の具体差分として提出する。

1. 既存adapterのどの関数へ何行相当を足すか。
2. review-lockの正本・生成時点・解除権限。
3. `apply_patch` とshell迂回をどう同じ強さで止めるか。
4. lock機構自体が壊れた場合に、H0157本題を巻き込まず停止・復元する方法。
5. 下記の正常・単一変異試験と、実PreToolUse発火をどう証明するか。

ここでユーザーの実装承認を取る。未承認のまま `codex_adapter.py`、`test_adapter.py`、`hooks.json` を変更しない。

## Helen軸との機械的分離

この環境整備エージェントが書いてよい候補は、`/Users/takedayousuke/.codex/skills/brainstorm/` 配下のreview-lock実装・試験と、本ページ・review-loop設計の記録だけ。Helen project root、Helen Wiki計画・current・承認資料、`tools/helen_route_hook.py` は書込み禁止。

共有 `/Users/takedayousuke/.codex/hooks.json` は本タスクでは読取り専用とする。既存PreToolUse接続を利用し、設定変更が本当に必要と判明したら、直接変更せず候補差分を本タスクの隔離試験出力へ保存して停止する。

開始前と終了時にHelen固定4ファイルのSHAを再測定し、上表から1件でも変われば `technical-stop-helen-axis-drift`。環境修理の成功として受領しない。これは「Helenを触らないよう注意する」という運用ではなく、固定SHA不一致を受入れ失敗にする境界である。

## 最低限の受入れ試験

| ID | 正常 | 1項目だけ壊す | 必須結果 |
|---|---|---|---|
| RL1 | lock外の親メモ・HTMLを更新 | lock対象planをapply_patch | planだけ拒否 |
| RL2 | receiptを入力外に保存 | receiptをlocked_inputsへ追加 | 循環拒否 |
| RL3 | passedで1回終端 | 同じreview IDを再review | 再review拒否 |
| RL4 | 入力SHA不変 | 入力1バイト変更後に自動再review | 承認待ちで停止 |
| RL5 | read-only shellでSHA確認 | `sed -i` / Python / `mv` / `cp` / 変数 / globでlock対象を書換え | 全経路拒否 |
| RL6 | 正しいreview ID・新revision承認 | 自由文・古いカード・確認なし・別review IDで解除 | 解除拒否 |
| RL7 | 単体試験PASS | 実Codex PreToolUseでlock対象変更を1回試す | 実hook拒否ログまたは同等の実イベント証拠 |

単体試験PASSだけを実hook接続済み・運用開始可能と言い換えない。

## 停止条件

- `hooks.json` の新系統追加、Claude側ファイル、Helenのplan/current/Blend/f166/quality-gateへ変更が必要になる。
- lock対象外まで一律に書込不能にする設計になる。
- shell迂回を止めるために全shellを禁止し、通常のH0157実装を妨げる。
- 実イベントを作らず、単体fixtureだけで運用合格とする。
- 既存カード・親選択・Stop契約の試験が回帰する。

該当したら実装を拡大せず、事実・失敗試験・復元状態をこのページへ記録して停止する。

## このタスクでやらないこと

- H0157の一本化計画を再レビューしない。
- Helenコード、f166、Blend、project quality-gateを変更しない。
- Claude Code側へ同時展開しない。
- 「もう触らない」「別エージェントなら大丈夫」という注意だけで完了にしない。
- 環境修理の成功をH0157原作一致・監査導入・成果物完成の証拠にしない。

## 完了報告に必要な証拠

- 変更前後の対象SHAと差分。
- 既存試験、RL1〜RL7の実行結果。
- 実PreToolUseで許可1件・拒否1件・復元後許可1件の対応。
- Helenの固定4ファイルが上表SHAのまま不変であること。
- 未確認事項と、環境整備後にH0157へ戻る入口。

## 関連リンク

- [[brainstorm-skill]]
- [[gf2-helen-repro-v51-current]]
- [[gf2-helen-deliverable-unified-route-plan-20260831]]
- [H0157再開用親メモ](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md>)
- [レビューループ機械防止設計](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-review-loop-mechanical-prevention-design.md>)

## 矛盾・未確定

- `brainstorm-skill.md` は2026-08-28 reviewでClaude中心。Codex現物のlite adapterと一致しない箇所があり、今回一括修正はしない。
- review-lock schema、解除操作、shellの変数・glob解析は未承認・未実装。
- Codex側を受け入れた後にClaude側へ展開するかは未決定。

## 使わなかったもの・落とした情報

- H0157本題の会話で環境guardまで実装する案は捨てた。手元では環境修理が別タスクになり、H0157のコンテキストへ修理ログが増えない。戻す必要はなく、この入口から別エージェントが担当する。
- 自動再reviewは候補から外す。入力変更後に即再検証できなくなるが、同じ意味の反復を止める。新revisionの承認でのみ再開できる設計候補。
- 既存コード・hook・Helen成果物はこのWiki整備では変更していない。
