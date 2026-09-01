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

## 整理結果（2026-09-01・kimi-code が実施）

武田さんの承認（2026-09-01 このタスクの実行セッション内）: #1 は「冒頭に降格バナー追記」方式、
plan-repair も対象に含める、#3・#4 は「wiki 側の記録で完了」。

| # | 対象 | やったこと | 状態 |
| --- | --- | --- | --- |
| 1 | 正本主張4枚 | 4枚すべての冒頭に「現在位置の正本は `gf2-helen-repro-v51-current`」のバナーを追記。本文・作成当時の主張行・frontmatter は不変。plan-repair は計画・提案の記録としての有効性を変えない文言に留め、承認済み計画の「既存計画を書き換えるな」には触れていない | **完了** |
| 2 | run-state.json の陳腐化 | 新規トップレベル欄 `status_corrections_2026_09_01` を追加（`agent_executable_remaining` の3件が「実行可能な残りではない」ことの訂正オーバーレイ）。既存の全キーの欠落・変更ゼロを機械検証済み。`common` 欄は不変 | **完了** |
| 3 | 孤立ページ | **再測定で 2026-08-30 の値（54枚中16枚孤立）は再現しなかった。** 2026-09-01 実測: gf2/helen 関連43枚のうち、パス言及まで含めれば孤立0枚。厳密な `[[slug]]` リンク基準では3枚（brainstorm sessions 2枚＋`wiki/analyses/gf2-helen-plan-audit-design-20260829.md`）だが、いずれも親メモ等から実パスで参照されている。9/1 の current ページ整備で大部分は解消済みと判断し、追加のリンク張りは行わない | **完了（追加作業なし）** |
| 4 | 派生3案件の所在 | 実パスは current ページ節6 に列挙済み。`common` は触れない制約のため run-state.json には新規欄を作らず、「節6 が所在の記録場所」とここに記録して完了とする（武田さんの選択。同じ情報を2か所に持つ同期ズレの種を作らない判断） | **完了（記録のみ）** |

**守った制約の確認**: 成果物 blend は未変更（節2 の SHA `04ef8b79…` が再生成後も一致）。
節2・6・7 は `python3 tools/current_state.py write <run-state.json>` で再生成。
節4（次の選択肢）は未編集。`run-state.json` は追加のみ（SHA 先頭16桁: `b176b17bb1d1cb9c`）。

**やらなかったこと・残存リスク**:

- 派生3案件の物理的な統合（フォルダ移動）はしていない。パスが変わると既存の参照・仕組みが壊れるため。
- 厳密 `[[slug]]` 基準の孤立3枚は残るが、実パス参照があるため再開の支障にはならないと判断。
- 旧4枚の本文中の主張行（「衝突したらこちらが新しい」等）は履歴として残っている。バナーを読み飛ばした場合の誤解リスクは残るが、AGENTS.md の「旧主張を無言で削除しない」に従った。
