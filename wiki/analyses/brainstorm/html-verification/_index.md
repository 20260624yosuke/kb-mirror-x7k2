---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-09-03
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-verification/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/commands/html.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/commands/brainstorm.md
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.agents/skills/html/SKILL.md
---

# /html 起動路の検証

## 武田さんの考え

### 2026-09-03 /html を検書する

> テーマ: /html、検書する（起動路の全文を貼り付け）

- 貼られた起動路は opencode 用の `/brainstorm` 起動路（承認カードの出し方・フック代替・やってはいけないことが更新済みの版）。
- 「検書」の対象が「起動路の文書の校閲」か「`/html` の動作検証」かは、まだ確定していない。

## 決まったこと

- なし（方向確認中）。

## まだ決まってないこと

- 「検書」の対象と合格条件。文書の校閲だけか、実際に HTML を作っての動作検証まで行うか。
- 既存の `html-skill-discovery`（scope が別フォルダ）へ追記するか、この新規メモで進めるか。
- 検証で HTML 成果物まで作る場合、その置き場所と題材。
- 修正した関所の実効確認（再起動が必要か、この会話で効くかは次カードの結果で確定）。

## 捨てた案と理由

- なし

## 直した記録

- 2026-09-03 関所3点を修正（いずれも `.opencode/plugins/skill-gate.js` 内、構文検査PASS・親検査PASS）。
  ①止めたカードで未報告目印を進めていた欠陥（再送時に本文量ゼロになる）を通過時前進へ変更。
  ②同ターン可視件数の要求を未報告本文の実質文字数へ変更（道具回で本文が畳まれる環境では同ターン要求が永遠に通らない）。
  ③html読込済みで書込みゼロの初回カードを許す（方向確認カードが永久に止まる行き止まりを解消。2枚目以降は要求継続）。
- 2026-09-03 起動路と条件付き指示に1行追加（本文だけの応答→カードだけの応答の順序。道具と同じ応答の文章は畳まれて届かない）。

## 再開の入口（実パス）

- この親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-verification/_index.md
- 初回記録: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-verification/sessions/20260903-html-verify.md
- html 起動路: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/commands/html.md
- 既存の html 系メモ: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-skill-discovery/_index.md

## 実装への申し送り

- なし（方向確認中のため空にする）。

### 終わったら次に取る承認

- 「検書」の対象と範囲の方針承認（文書校閲だけか動作検証までか、既存追記か新規継続か）。

## 機械化した指摘

- なし

## 関連リンク

- なし

## セッションメモ（子）

- 2026-09-03 初回（起動・方向確認）: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-verification/sessions/20260903-html-verify.md
