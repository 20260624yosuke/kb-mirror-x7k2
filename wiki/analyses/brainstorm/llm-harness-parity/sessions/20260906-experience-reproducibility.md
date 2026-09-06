---
type: analysis
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-06
---

# 2026-09-06 良かった理由の分解と、分岐の実測

親: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`

ヘレンの案件の会話からフォークして始まった回。武田さんの問いは
「このLLM体験の再現性を、保管庫でどう安定させるか」。

## この回で確かめたこと（すべて実ファイル）

- `harness_parity_check.py check` … **FAIL・10件**（検査1・2・3・5・guard-card・handoff到達性）
- `/brainstorm` の本文 … Claude 238 行／Codex 77 行／opencode 66＋11 行
- Codex 版の末尾に「巨大な監査台帳や、通常作業を横断する許可レジストリは作らない」
- `llm-wiki` スキルが2実体（`~/.agents/` 8,953B ／ `~/.claude/` 9,642B、見出しは同一）
- `.claude/skills/html` は `.agents/skills/html` への symlink（inode 852375 で同一）
- 保管庫に置いた検査（`prose_guard.py` / `deliverable_path_guard.py` / `context_harness.py`）は
  Claude と Codex の両方から同じ1ファイルが呼ばれている＝分岐していない
- 全ブレストメモの `## 機械化した指摘` … 121 件記録・29 件（24%）未実装

## この回の成果物

- 説明ページ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260906-kb-experience-reproducibility.html`
- 親メモへ「2026-09-06 実測」「2026-09-06 手立ての候補」の2節を追加

## 次の会話が最初に読むもの

1. 親メモの `## 2026-09-06 実測` と `## 2026-09-06 手立ての候補`
2. 上の説明ページ
3. `python3 .opencode/scripts/harness_parity_check.py check` を走らせて現在値を取る

## まだ決まっていない

- 読み取りが武田さんの意図と合っているか
- 記録先を既存の親メモのままにするか、新しい親メモに分けるか
- 手1（判定の本体を保管庫へ寄せる）に進むかどうか。2026-08-29 の「独立方針」を
  上書きすることになるので、武田さんの判断が要る
