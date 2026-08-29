---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-29
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
  - /Users/takedayousuke/.claude
  - /Users/takedayousuke/.agents
entry_paths:
  - /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-brainstorm-skill-design.md
  - /Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py
sources: []
---

# brainstorm スキルを他 LLM へ移植する指示書（2026-08-29）

設計の正本は `wiki/builds/brainstorm-skill.md`（[[brainstorm-skill]]）。
Claude 側の設計経緯は `wiki/analyses/brainstorm-brainstorm-skill-design.md`
（[[brainstorm-brainstorm-skill-design]]、状態 done）。このメモは **移植（他 LLM で同じ使用感を出す）**
だけを扱う。

## 武田さんの考え

- `/brainstorm` を作った。**この KB フォルダを開いた LLM 共通で使用感を同じにしたい**という意図
  だったが、まだ Claude 以外で使えるか確認できていない。
- スキルの実装作業は実際のその LLM にさせるので、**どういう指示を送ればいいかを考えてほしい**。
- （2026-08-28 の既存方針）指示書だけ提出し、その環境でどう実現するかはその LLM に考えさせる方が
  解像度が高い。

## 実測で分かったこと（2026-08-29・すべてファイルを読んで確認）

### 1. 指示書は既にあるが、1日ぶん古い

`wiki/builds/brainstorm-skill.md` に「## Codex / Kimi へ渡す指示書」節が既にある（2026-08-28 版）。
ただし **翌 8-29 に足した引き継ぎ到達性の監査（H1〜H7 / `guard-stop-handoff` / `entry_paths`・
`background_paths` の frontmatter / `## 再開の入口（実パス）` 節）が入っていない。**
今そのまま渡すと、他 LLM 側は 8-28 時点の使用感になり、Claude と揃わない。

### 2. 環境ごとの土台（実ファイルで確認）

| 環境 | 設定ファイル | フックの実績 | 未確認 |
|---|---|---|---|
| Codex | `/Users/takedayousuke/.codex/hooks.json` | Claude と**同じイベント名スキーマ**。SessionStart / UserPromptSubmit / Stop / PreCompact / PostCompact / SubagentStart / SubagentStop を context-harness が登録済み | **PreToolUse の登録実例が無い**。対応可否そのものが未確認 |
| Kimi Code | `/Users/takedayousuke/.kimi-code/config.toml` | `[[hooks]] event = "Stop"` が実在（旧 hold の `hold_guard_kimi.py` が**今も登録されたまま**。hold は休止済みなので残骸） | PreToolUse 相当・UserPromptSubmit 相当の有無 |
| opencode | `/Users/takedayousuke/.config/opencode/opencode.jsonc` | フック設定は無い（中身は `permission: "allow"` のみ）。**プラグイン方式**で `@opencode-ai/plugin` 1.18.21 が導入済み | プラグインで書き込み拒否・毎ターン注入ができるか |

→ **最大の未確認点は「成果物封鎖（PreToolUse で書き込みを拒否）」が Codex / Kimi / opencode で
成立するか。** Stop（会話を閉じさせない）は Kimi で実績があり、Codex も同名イベントを持つ。

### 3. 置き場所が Claude 専用になっている

- 監査スクリプトの実体は `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`。
  旧 hold は共通置き場 `/Users/takedayousuke/.agents/skills/hold/` を使っていた（`llm-wiki` も同じ）。
- `brainstorm_guard.py` の `audit-handoff` は `~/.claude/settings.json` の存在をハードコードで
  確認している（フック登録の検査）。他環境ではこの検査が必ず落ちる。

### 4. 移植できる部分／できない部分

- **移植できる（Python 3 だけで動く）**: メモの探索、`brainstorm_status` / `scope` の読み取り、
  H1〜H7 の到達性監査、`check` サブコマンド。
- **移植できない**: フック用サブコマンド（`inject-*` / `guard-write` / `guard-stop*`）の**入出力**。
  Claude のフック JSON（`transcript_path`、`hookSpecificOutput.permissionDecision`）前提のため、
  環境ごとに薄い変換層（アダプタ）が要る。判定ロジック自体は共有できる。

### 5. Claude 側で今日出た誤検知（実測）

このメモを Bash のヒアドキュメントで作ろうとしたところ、封鎖フックが
`止めた対象: /Volumes/SSD_M.2_Realtek` として拒否した。**KB ルートのパスに半角スペースが
あるため、コマンド文字列からのパス抽出が途中で切れている。** Write ツールでは通った。
移植時に同じ誤検知を持ち込まないよう、指示書に明記する必要がある。

## 決まったこと

（まだ無し）

## まだ決まってないこと

- 共通本体の置き場所: 現状の `~/.claude/skills/brainstorm/` のままにするか、
  `~/.agents/skills/brainstorm/` へ移して Claude 側は参照だけにするか。
- 指示書の粒度: 条件だけ渡して実現方法は各 LLM に任せる（8-28 方針）か、
  共有本体のコマンド仕様（アダプタの入出力）までこちらで決めて渡すか。
- 指示書を1本の共通文書にするか、環境ごとに分けるか。
- Kimi の `config.toml` に残っている休止済み hold の Stop フックをどう扱うか。
- Claude 側の封鎖フックのパス抽出の誤検知（上の 5）を直すか、指示書で回避だけ書くか。

## 捨てた案と理由

（まだ無し）

## 直した記録

（まだ無し）

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm-brainstorm-skill-portability.md`
- 仕様の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
- 規則本体: `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`
- 監査スクリプト: `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`

## 実装への申し送り

（承認後に記入）

## 関連リンク

- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-brainstorm-skill-design]] — `wiki/analyses/brainstorm-brainstorm-skill-design.md`
