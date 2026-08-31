---
type: analysis
title: brainstorm終了検査のKeyError原因調査
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-01
---

# 終了検査 BS_INTERNAL:KeyError の原因

## 直接確認したこと

- 実Stopから `BS_INTERNAL:KeyError` が返った。セッション `d9e9f2b6fbe52b8a1aba297d9ed3b7635b77292f556c53104947a1852cb905c7` のphaseは機械側でも `technical_stopped` になった。
- 保存状態は `dialog_stage=parent_select`、`target_memo=""`。キー `parent_selection` と `card_call_id_hash` は無い。
- 当該sessionのcard-eventsにはactivate、UserPromptSubmit、stale_card_ignored、stop_blockがある。正常なcard_pre・parent_selection_responseは観測できない。
- `codex_adapter.py:643` の `check_selection` は `state["parent_selection"]` を無条件に読む。
- 実保存状態を読み込み、この読取関数だけを実行したところ、同じ `KeyError: 'parent_selection'` を再現した。実Stopの偽イベント投入・状態書換えはしていない。
- Stopの親未選択分岐がcheck_selectionを呼ぶため、技術的停止を説明する返答でも同じ場所で失敗しうる。
- hooks.jsonにPreToolUseの `matcher: "*"`、PostToolUseの `matcher: "request_user_input|apply_patch"` が存在する。ただし、事前記録欠落の根本原因がこのmatcherか、ツール呼出し形か、実行環境側かは未確定。

## 直す必要のある範囲（未実装）

1. カード事前記録の欠落原因を実環境で確定する。正式な実イベントを観測し、呼出しID・回答・session/turn・親の対応を結ぶ。
2. 親選択記録が欠けた入力を、汎用KeyErrorではなく、欠損箇所が特定できる停止理由として扱う。欠損を許容して承認や通常終了へ進めない。
3. 親未選択中の機構故障でも、保存した原因・未回答点・再開点を示して技術的停止を報告できる経路を定義する。新親作成の実会話記録を保持し、基準を勝手に初期化しない。
4. 正常な親選択、記録欠損、古いカード、部分回答、技術停止の再入を試験する。単に終了できたことを、カード連携正常の証拠にしない。

## 今回変更していないもの

スキル本体、hooks.json、実行時state、カード監査基準、ヘレンのBlend・原作再現コードは未変更。
brainstormの実装禁止と、成果物封鎖を独断で解除しない規定のため、当該故障に限った実装修理の許可をユーザーへ尋ねた。まだ回答を受け取っていない。

## 正本への経路

- 親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md
- 終了検査: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
- 実行時状態: /Users/takedayousuke/.codex/skills/brainstorm/scripts/state/d9e9f2b6fbe52b8a1aba297d9ed3b7635b77292f556c53104947a1852cb905c7.json
- 実イベント記録: /Users/takedayousuke/.codex/skills/brainstorm/scripts/card-events.jsonl
- 登録: /Users/takedayousuke/.codex/hooks.json

## 使わなかったもの・落とした情報

なし。ガードの無効化、実回答の捏造、過去承認の取消し、基準のリセットは行っていない。
