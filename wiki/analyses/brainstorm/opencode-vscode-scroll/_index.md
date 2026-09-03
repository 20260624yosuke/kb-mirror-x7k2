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
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/opencode-vscode-scroll/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/opencode.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260903-vscode-opencode-setup.html
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260903-opencode-vscode-scroll.html
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260903-opencode-ui-alternatives.html
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.agents/skills/html/design-system/component-samples.html
---

# opencodeをVSCodeで使うときのスクロール改善

## 武田さんの考え

- 2026-09-03: 「opencodeをvscodeで使っている。スクロールが重いというかもっと早くていいだろって感じるんだけど、解決策ある？」原文のまま。
- テーマは /html、この環境整備の話。説明ドキュメントとしてHTML化する想定。
- 2026-09-03: 「どうやって解決できる？何かアプローチはないの？」原文のまま。

## 決まったこと

- なし（初回。原因の切り分けと対策案を提示する段階）

## まだ決まってないこと

- 重いのが opencode TUI側のスクロール量の問題か、VSCode統合ターミナル側の描画の問題か、どちらを主因とするか
- 対策を tui.json の変更で済ませるか、VSCode側設定まで変えるか
- HTML成果物を作るか、設定変更だけ試すか
- 実装をこの会話で行うか（武田さんが決める。私からは指定しない）
- 2026-09-03実測：専用TUI設定は未作成（既定の3行が有効）、VSCode側の端末系指定なし（既定値で動作中）。速くする余地は両方に残っている（未確認ではなく実ファイル不在を確認済み）

## 捨てた案と理由

- なし

## 直した記録

- なし

## セッションメモ（子）

- なし

## 再開の入口（実パス）

```python
parent = "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/opencode-vscode-scroll/_index.md"
opencode_config = "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/opencode.json"
setup_html = "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260903-vscode-opencode-setup.html"
draft_html = "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260903-opencode-vscode-scroll.html"
```

## 実装への申し送り

- 未定（方針承認後に書く）

## 機械化した指摘

- なし

## 関連リンク

- wiki/analyses/brainstorm/opencode-vscode-scroll/_index.md（実パスは上記入口を参照）
