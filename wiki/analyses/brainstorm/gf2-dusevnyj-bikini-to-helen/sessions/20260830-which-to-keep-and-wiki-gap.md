---
type: analysis
status: active
confidence: high
evidence_level: source-backed+user-stated
last_reviewed: 2026-08-30
---

# 「どれを残すか」の既存記録と、wiki 整合の調査（2026-08-30）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

## この回でやったこと

1. 武田さんの「事前に話して記録があるはず」を受けて既存記録を探し、**見つけた**
   （`06_repro-v51/ledger/visibility-decision.json` と `logs/gate-results.json` の G13）。
2. その結果、**前回作った D1 の判定規則が3つとも誤り**と判明した。
3. 「なぜ wiki と整合が取れていないのか」を実ファイルで調査した（憶測禁止の指示）。
4. 承認: **ドレス部品は外す** / **D1 の規則訂正＋参照の関所 A11** / **今回の件を機械化の候補にする**。

## 承認された内容

親メモの `## 実装への申し送り` の先頭節（水着版の表示セット確定 ＋ D1 の作り直し ＋ A11 ＋ F005）。

## 調査の結論（詳細は親メモ）

- **wiki の不整合ではない。** 派生（Flat）の答えは KB の3か所、版の答えは親メモ 1765〜1800 にあった。
- **一次原因は私の読む範囲。** 親メモ 2605行のうち約400行しか読まず、決着が書かれた
  1722〜1866 行を読まなかった。モデル・エフォートで説明できる根拠は無い。
- **仕組みの穴も3つ実在。** `background_paths` に兄弟プロジェクトが0件（この回に追加）／
  再注入が親メモの一部で切れる（今回注入されたのは別案件のメモ）／
  新しい検査が既存台帳と矛盾しても止まる関所が無い。

## 説明ページ（人が読む用）

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/20260830-which-to-keep-already-decided.html`
