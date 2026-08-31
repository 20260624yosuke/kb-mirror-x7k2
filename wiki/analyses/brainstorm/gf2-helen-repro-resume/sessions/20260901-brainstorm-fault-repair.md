---
type: analysis
title: brainstorm親未選択の故障修理と未再信頼フック
status: active
confidence: high
evidence_level: user-stated+source-backed
last_reviewed: 2026-09-01
---

# brainstorm限定修理の記録

ユーザーは「この会話で、brainstormのこの故障に限って実装修理を許可しますか？」「許可します。」と回答した。今回の修理に適用し、Helen実装・通常カード運用再開・運用受入には拡張していない。

## 直接確認した原因

1. 実保存状態には親選択の記録がなく、旧check_selectionはそれを無条件に読みKeyErrorを起こす。
2. インストール済みCodexのapp-serverへinitializeとhooks/listだけを送信した。事前フックはtrustStatus=modified、事後・Stop・入力・再入フックはtrustedだった。
3. 事前フックの現在定義hashは `sha256:a4839aeb6955188d155ecbf86bd07d01741dbc2674be30222649c2a3b4075c1d`、保存された信頼hashは `sha256:357ae627728897decbf6a313b4c0cacecf991a1eb94ab6f434e359764335ffd5`。登録はあるが現在の定義は再信頼されていない。
4. 公式の[フック仕様](https://learn.chatgpt.com/docs/hooks)は、変更された定義を再信頼まで実行しないと説明している。実カードの前記録なし・後記録ありという観測と一致する。
5. matcherの `*` は[公式コードの発見試験](https://github.com/openai/codex/blob/main/codex-rs/hooks/src/engine/discovery.rs)で受理され、インストール済みのhooks/listも読み込みエラーなし。matcher変更は不要と判断した。

信頼記録の手書き、信頼を迂回する起動、hooks.jsonの変更、CLIのインストール変更は行っていない。補助のprose_guardがuntrustedという別件も観測したが今回の修理対象には広げていない。

## 修理した動作

- 親選択記録なしはBS_PARENT_SELECTION_MISSING、壊れた記録はBS_PARENT_SELECTION_INVALIDとして拒否する。欠損を承認・中断・親選択へ置き換えない。
- カードの実事後イベントだけがあり事前記録がない場合はBS_CARD_PREFLIGHT_MISSINGとして停止する。古い別カードIDは従来どおり無視する。
- 親未確定の技術的停止では、入力時点で発見済みの親候補にある診断記録を検査できるようにした。直接リンク・現在マーカー・停止理由・実在する証拠とSHA・再開全文・実行可能作業の残存・既存基準の保留項目・引継ぎ到達性を検査する。
- この診断経路は親を選ばず、親基準を書かず、memo_write_verifiedも承認状態も変えない。イベント名もunbound_technical_report_verifiedで、正常カードのstop_passやcheckpoint_verifiedとは分離する。
- 「許可します」を「再開」に追加していない。通常カードは明示的な再開要求後の事前検査まで止める。

変更本体: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
追加試験: /Users/takedayousuke/.codex/skills/brainstorm/tests/test_parent_selection_fault.py
変更前保存: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py.bak-20260901-keyerror

## 試験結果と限界

- 追加12試験は合格。旧コードへ同じ試験を当てると10試験が失敗または例外になり、欠落や再入の故障を検出できることも確認した。
- 全体は変更前59件中56件合格、変更後71件中68件合格。同じ既存3件の失敗が残る。今回の変更で増えた失敗はないが、全体合格ではない。
- 残る3件は、done昇格時のマーカー移動、通常タスクの公開検査呼出し期待、入れ子のbash書込検出。それぞれ今回の親未選択・事前記録欠落以外の箇所で、故障限定の許可を広げて修理していない。
- 品質ゲートはplan=PASS、complete=FAIL。実カードと実Stopを通した復旧の実証・運用受入は未完。
- Helenの保護対象11ファイルは、現状整理時のSHAと全件一致。Blend・f166・品質台帳・既存承認は変更していない。
- 信頼状態の照会は実環境の読取結果。カード試験は隔離環境の合成入力であり、実イベントの代わりには数えない。

完全な試験結果・コードhash・信頼状態・保護対象: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-brainstorm-fault-repair-evidence.json

## 次の具体操作

Codexの標準のフック確認画面（CLIでは `/hooks`）で、/Users/takedayousuke/.codex/hooks.json の `pre_tool_use:0:0`、コマンド末尾 `codex_adapter.py" pre-tool` の現在定義を確認し再信頼する。この操作は修理の再許可ではなく、Codex自身の信頼確認であり、エージェントがconfig.tomlを手書きして代用しない。

その後、この会話で「再開」と伝える。私が信頼状態を再照会してから実二問カードの事前検査を行い、親選択・回答・追記・終了まで実ログで検証する。既存の新親にはまだ機械基準がなく、必要な来歴検査を飛ばして初期化しない。信頼確認後も基準の復元が必要ならその実証が揃うまで技術的停止を維持する。

## 関連する正本

- 親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md
- HTML: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260831-helen-repro-project-current-state.html

## 使わなかったもの・落とした情報

なし。以前の診断記録は当時の未許可状態として残す。承認・未回答・原作要件・保留工程を削除せず、親の機械確定を捏造しない。
