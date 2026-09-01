---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-01
sources: []
---

# helen 原作再現の整理タスク — 別エージェントへの入口

2026-08-31 の武田さんの裁定「**helen プロジェクトの整理は、後で別エージェントへ投げる
（整合性を崩すのは禁止と明示して）**」に基づく、その別エージェント用の入口。

## 最初に読む1枚（これだけでよい）

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md
```

**引き継ぎ資料は別に作りません。** 上の1枚が現在位置の正本です。
何も知らないエージェントに1枚だけ渡す試験を2回実施し、
**「この1枚だけで武田さんへ選択肢を提示できる」**ところまで確認済み（2026-09-01）。

## 併せて渡すもの

| 何 | 実パス |
| --- | --- |
| 武田さんが見る一覧（4案件の盤面） | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260901-projects-current-state.html` |
| 機械可読の記録（正本・226KB。全文は読まない） | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json` |
| 現在位置ページを書き直す道具 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/current_state.py` |
| 仕組みの正本メモ | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md` |

## このタスクで守ること（武田さんの明示）

1. **整合性を崩すのは禁止。** KB フォルダの整合性を壊す変更をしない。
2. **`run-state.json` の既存の欄を消さない・書き換えない。** 足すだけ。
   `common` 欄は現在位置ページの仕組みが使うので触らない。
3. **成果物（blend）に触らない。** この整理は記録と仕組みの話。
4. **節2・6・7 は機械が書く区画。** 手で書き換えない。書き直すときは
   `python3 tools/current_state.py write <run-state.json>` を使う。
5. **節4（次の選択肢）は、正本の候補を増やしても減らしても言い換えてもいけない。**
   2026-09-01 に、候補を1つ落として存在しない候補を1つ作り出す事故を起こしている。

## 整理の対象として残っているもの（2026-09-01 時点）

| # | 何が壊れているか | 実態 |
| --- | --- | --- |
| 1 | 引き継ぎ資料が7枚あり、**4枚が互いに「自分が正本」と主張**している | `run.md` 14行目 / `handoff.md` 30行目 / `plan-repair` 54行目 / `conversation` 26行目 |
| 2 | `run-state.json` が**追記しかできず、片付いた項目が「未着手」のまま残る** | 2026-08-30 にこれを拾って誤報告した実例あり |
| 3 | helen 関連の wiki ページ54枚のうち**16枚がどこからもリンクされていない** | 2026-08-30 実測 |
| 4 | 派生3案件の所在がばらばら（水着化だけ KB フォルダの中） | 現在位置ページの節6 に実パスあり |

**注意**: 上の #1 の書き換えは、別セッションの承認済み計画が「既存の Helen 原作再現計画を書き換えるな」と
禁じている範囲に触れる可能性がある。着手前に
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md` の禁止事項を読むこと。

## 関連リンク（実パス併記・`[[slug]]` は新しいセッションでは解決できないため）

| ページ | 実パス |
| --- | --- |
| [[gf2-helen-repro-v51-current]] 現在位置の正本 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md` |
| [[gf2-helen-repro-v51-handoff]] 引き継ぎの全文 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md` |
| [[gf2-helen-repro-v51-run]] 実行記録の全文 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md` |
| 別セッションの承認済み計画（禁止事項を先に読む） | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md` |

**作業ディレクトリ**: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
（このページ内の相対パスは、すべてここを起点にしている）
