---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-31
sources: []
---

# html スキルがこのタスクに読み込まれない原因

## 確認結果

2026-08-31 のローカル調査。修正・インストール・設定変更・GUI操作は実施していない。

- 現在の作業ディレクトリは `/Users/takedayousuke/Documents/Codex/2026-08-31/new-chat-2`。このターンの利用可能スキル一覧に `html` は無い。
- 原本は `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.agents/skills/html/SKILL.md` に存在。先頭は `name: html` と description を持つ正常な frontmatter。全文読取成功。SHA-256: `9b7c9137585b89dc8dda5ab27058edc059079d57fbab8cb1681c16ec9dd8019c`。
- 同じフォルダの `design-system/component-samples.html`、`document.css`、`math-copy.js`、`render-pdf.sh` は存在。
- `/Users/takedayousuke/.agents/skills` には `hold` と `llm-wiki` のみ。`/Users/takedayousuke/.codex/skills` には `.system`、`brainstorm`、`grill-build` のみ。共通置き場に html はない。
- KB の `.claude/skills/html` は `../../.agents/skills/html` への有効なリンク。リンク切れではない。
- `/Users/takedayousuke/.codex/config.toml` のスキル無効化は `plan-gate:plan-gate` のみ。html の無効化指定は見つからない。
- 過去の Codex 実ログ `/Users/takedayousuke/.codex/sessions/2026/08/31/rollout-2026-08-31T11-13-49-01a05598-2374-7fc0-a438-d2a4f5bd95ff.jsonl` の session_meta.cwd は KB。developer の利用可能スキル一覧に `- html: ... (file: r8/html/SKILL.md)` が実在する。
- `/Users/takedayousuke/.codex/sessions/2026/08/30/rollout-2026-08-30T23-33-14-01a05316-bcbe-7641-b939-ddc35a9b8d69.jsonl` でも cwd は KB で、`.agents/skills/html/SKILL.md` を実際に読むツール呼出しが残る。
- 公式資料 https://learn.chatgpt.com/docs/build-skills の Where Codex loads local skills は、作業場所に応じた `.agents/skills` と共通の `$HOME/.agents/skills` を区別している。シンボリックリンクもサポートする。

## 判断と限界

このタスクで html が検出されない直接原因は、原本が別プロジェクトのローカル配置にあり、現在のタスクの探索範囲に入らないこと。削除や設定無効化は確認されなかった。

ユーザーが「使えなくなった」と観測した画面やエラーは未提示。元の KB 内でも失敗する場合は、この原因だけでは説明できない。大文字 `/Html` と小文字 `/html` のUIでの違いは未試験。名称は `html` だが、大文字が主因とは断定しない。Codex更新で壊れたという証拠も無い。

既存親 `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md` の773行以降には、Claude側でカード内の /html を起動し損ねた別件の記録がある。この調査ではその件の再現や修理検証は行っておらず、今回の原因として流用しない。

## 修復候補（未承認・未実行）

1. KB 内だけで使う場合は、KB のタスクで html を選ぶ。現在のプロジェクト限定配置を維持する。
2. プロジェクトなしを含め全タスクで使う場合は、共通の `/Users/takedayousuke/.agents/skills` へ登録する計画を作る。元をリンクする最小案は外付けSSD依存が残る。フォルダ一式を共通原本へ移す案は正本・既存リンクの変更を伴うため別の判断が必要。SKILL.mdだけをコピーすると隣接アセットが欠けるため採用しない。
3. 修復完了はファイル配置だけで判定せず、対象タスクで候補に出ること、明示選択で原本を読むこと、必要時はHTMLの出力と表示を確認する。まだ変更していないため、現在の調査確認で改善が起きたとは扱わない。

## 親メモ

/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/html-skill-discovery/_index.md
