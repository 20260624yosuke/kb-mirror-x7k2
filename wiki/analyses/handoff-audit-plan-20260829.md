---
type: analysis
title: brainstorm 引き継ぎ到達性の機械監査（実装計画）
status: active
confidence: medium
evidence_level: source-backed+inferred
created: 2026-08-29
last_reviewed: 2026-08-29
revision: 3
approval: 設計2点のみ承認済み（発火点=渡す瞬間 / 方式=宣言＋走査）。実装は未承認。
review: 2026-08-29 独立レビュー（別エージェント・opus）を2回実施。1回目 重大3/中6/小5、2回目 実装前必須7件。全件反映して revision 3。
tags: [brainstorm, harness, audit, handoff]
---

# brainstorm 引き継ぎ到達性の機械監査（実装計画）

> [!warning] 未承認
> 本書は実装前の計画である。コードは1行も書いていない。

## 0. 作業ディレクトリと関連ファイルの実パス

**作業ディレクトリ（以降の相対パスの起点）**

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
```

本文中の `[[slug]]` は Obsidian の記法で、実体は `wiki/` 配下の `<slug>.md`。

| 役割 | 実パス |
|---|---|
| 改修対象・監査スクリプト | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` |
| 改修対象・スキル定義 | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` |
| 動作ログ（既存） | `/Users/takedayousuke/.claude/skills/brainstorm/guard.log` |
| フック登録の実体 | `/Users/takedayousuke/.claude/settings.json` |
| スキルの正本 wiki ページ | `wiki/builds/brainstorm-skill.md` |
| 事故が起きた実ログ | `/Users/takedayousuke/llm-uploads/20260829-082928-セッションを新しくするので-エージェントがタスクを再開するのに必要なファイルパス.md` |
| 適用する考え方（GPT との検討） | `/Users/takedayousuke/llm-uploads/20260828-223742--AI開発における-レビュー-検証ボトルネック-を現在のプロジェクト計画へ適用す.md` |
| 監査対象になる既存メモ（active） | `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` |
| 監査対象**外**の既存メモ（done。参考） | `wiki/analyses/brainstorm-brainstorm-skill-design.md` |
| 事故の現場になった計画書 | `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md` |

## 1. 直す不備（実測で特定した事実）

2026-08-29 のセッションで、brainstorm で作った計画書を新セッションへ引き継ぐ際、
計画書に関連ファイルの実パスが無く、`[[slug]]` だけが書かれていた。新セッションの LLM は
`[[slug]]` を解決できないため、関連ファイルへ到達できない状態だった。武田さんの指摘で初めて発覚した。

実ファイルを読んで確認した原因は次の3点。**いずれも「書き忘れ」ではなく「検出器の不在」である。**

1. `brainstorm_guard.py` が持つ関所は3つのみ — `guard-write --lockdown`（成果物封鎖）、
   `guard-write --unread`（申し送り未読ブロック）、`guard-stop`（カード無し終了の停止）。
   **メモや計画書の中身を検査するコマンドは存在しない。**
2. `--unread` は `live_memos(cwd, ("ready",))` に限定されている。今回の引き継ぎは
   `brainstorm_status: active` のまま行われたため、**一度も発火していない**（`guard.log` に該当拒否なし）。
3. `SKILL.md` のひな型に、関連ファイルの実パスを書く欄が無い。`## 実装への申し送り` は自由記述で、
   空欄でも `[[slug]]` だけでも通る。実際、事故時点でこの節は空欄だった。

適用する考え方は上記 GPT 資料の §12「人間が一度した指摘を、二度させない形へ変換する」と
§13「検証を LLM の自主性に依存させない」。本件はこれが未実施の箇所にあたる。

## 2. 承認済みの設計方針（2026-08-29・承認カード）

- **発火点は「渡す瞬間」だけ。** ①`brainstorm_status` を `ready` へ上げる編集時、
  ②アシスタントの応答がメモ・計画書のパスや `[[slug]]` を含んだまま終わろうとした時（Stop フック）。
  却下した案: 毎ターン監査（執筆途中で頻繁に中断する）／`ready` 昇格時のみ（今回の事故が active
  のままだったので防げない）。
- **方式は宣言＋走査の両方。** frontmatter に `entry_paths:` を必須化して実在照合し、
  加えて本文の `[[slug]]`・パス文字列も全部照合する。
  却下した案: 走査のみ（何も書かなければ照合対象ゼロで素通り）／宣言のみ（本文の切れリンクが通る）。

