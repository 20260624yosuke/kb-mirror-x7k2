---
type: analysis
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-08-31
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
  - /Users/takedayousuke/.claude/skills/brainstorm
  - /Users/takedayousuke/.codex/skills/brainstorm
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md
background_paths:
  - /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md
  - /Users/takedayousuke/.codex/skills/brainstorm/SKILL.md
  - /Users/takedayousuke/.codex/hooks.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/AGENTS.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/CLAUDE.md
---

# Claude と Codex の使用感の差を、KB 側から縛る（2026-08-31）

移植（他 LLM に同じスキルを作らせる）の経緯は
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
（[[brainstorm-brainstorm-skill-portability]]、状態 ready）。このメモは **差を今後禁止するための運用ルール**
だけを扱う。

## 武田さんの考え

### 2026-08-31 使用感の差を禁止したい

> の使用感が、claudeとcodexで違うのなんなん？
> このkbフォルダの運用でそういうの禁止にしたいんだけど。
> codexのハーネスが機能しなくて、トークンを無駄にするし、認知負荷も強くて大変。
> こっちから調整できない？
> /htmlで回答の可視性を上げて。

- 差そのものを「起きうるもの」として運用しない。KB フォルダの運用ルールとして禁止したい。
- 実害は2つ。**トークンの無駄**と**認知負荷**。品質の話ではなく、使っていて疲れるという話。
- 「こっちから調整できない？」= KB 側（この保管庫の規約とファイル）で縛れないか、という問い。

## 実測で分かったこと（2026-08-31・すべてファイルを直接読んで確認）

### 1. 差の正体は「長さ」ではなく「Codex 側にだけ足された機構」

| 項目 | Claude 版 | Codex 版 |
|---|---|---|
| SKILL.md の行数 | 206 | 224 |
| checkpoint（現在地・保留の JSON 記録） | 記述なし | 6 箇所 |
| resume_contract.py（終了時の構造検査 CLI） | 無し | 364 行 |
| `bs:v1` ターンマーカー | 記述なし | あり |
| parent_select（親メモ選択の段階） | 記述なし | あり |
| publication pass（通常タスクの最終返答検査） | 記述なし | あり |
| technical_stopped | 記述なし | あり |

行数はほぼ同じなのに、Codex 版だけが「終了・再開の契約」「エラーコード 13 種」「親別基準ファイルの
排他ロック」を持つ。**同じ名前のスキルで、中身の違う2つの規則が動いている。**

### 2. 差が生まれた原因は、承認済みの方針そのもの

2026-08-29 に「**スキル本体は LLM ごとに独立**。互いのフォルダを参照しない」を武田さんが承認した
（[[brainstorm-brainstorm-skill-portability]] の「決まったこと」）。以後、Codex 側だけが 8-30・8-31 の
修理依頼を受けて育ち、Claude 側はそのままだった。**差はバグではなく、方針の副作用。**
禁止するなら、この 8-29 の決定を上書きすることになる。

### 3. Codex のハーネスは「不発」ではない

`/Users/takedayousuke/.codex/hooks.json` に 9 イベント（SessionStart / UserPromptSubmit / PreToolUse /
PostToolUse / Stop / SessionEnd / PreCompact / PostCompact / SubagentStart / SubagentStop）が登録済みで、
`/Users/takedayousuke/.codex/skills/brainstorm/scripts/guard.log` は 447 行、直近も pass を記録している。
**動いていないのではなく、動いた結果が重い。** 何が重いかは 4 と 5。

### 4. トークンを食っている実測原因：読み取りだけのコマンドまで封鎖が止める

Claude 側 `guard.log` の DENY は累計 72 件。日別で 8-28=13、8-29=9、8-30=21、**8-31=29** と増えている。
今日のこのセッションだけでも、`ls` `find` `wc` という**書き込みを一切しないコマンド**が 4 回止まった。
止まるたびに拒否文（約 200 字）が返り、こちらは同じことを別の書き方でやり直す。
これは [[brainstorm-brainstorm-skill-portability]] の「課題1」（封鎖側のパス抽出）として 8-29 に
引き継ぎ済みだが、**未着手のまま**。

### 5. グローバルの規約ファイルが Codex 側だけ空

- `/Users/takedayousuke/.claude/CLAUDE.md` … 4,663 バイト（ファイルの示し方などの共通ルール）
- `/Users/takedayousuke/.codex/AGENTS.md` … **0 バイト**

KB フォルダ直下は `CLAUDE.md` と `AGENTS.md` が両方あり中身も揃っているので、**プロジェクト規約は
既に一致している**。食い違うのはグローバル側だけ。

## 決まったこと

（まだ無し）

## まだ決まってないこと

- 「差を禁止する」を、どの水準で実現するか（下限に揃える / 上限に揃える / 外部仕様1枚で機械照合 /
  1環境に限定）。
- Codex 側の checkpoint（先の停止地点の可視化）を残すか捨てるか。8-31 に武田さん自身が
  「曖昧な停止を機械的に修理しろ」と要求して作らせたもの。

## 捨てた案と理由

（まだ無し）

## 直した記録

（まだ無し）

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- 移植の経緯: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- 仕様の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
- 説明 HTML: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260831-claude-codex-usage-gap.html`

## 実装への申し送り

（承認前。まだ無し）

## 機械化した指摘

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
| 同じ名前のスキルで環境ごとに中身が食い違う | する | できる（外部仕様の項目が両 SKILL.md に在るかの照合） | 未決定（今回の承認待ち） |
| 読み取り専用コマンドを封鎖が止める | している（今日4件） | できる | 既存の課題1（封鎖側のパス抽出）。未着手 |

## 関連リンク

- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-guard-fix-handoff-20260829]] — `wiki/builds/brainstorm-guard-fix-handoff-20260829.md`

## セッションメモ（子）

- 親: このファイル。子はまだ無し。
