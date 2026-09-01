---
type: build
title: Helen H0157 U0〜U3 別エージェント用タスク入口
status: active
confidence: high
evidence_level: user-stated+source-backed+inferred
last_reviewed: 2026-09-01
implementation_status: unapproved
owner_route: separate-agent
---

# Helen H0157 U0〜U3 — 別エージェント用タスク入口

**この文書をHelen原作再現エージェントの最初の入力にする。環境整備エージェントには渡さない。**

## 現在の状態

- 一本化revision 4は計画として承認済み、最終独立reviewはCritical 0 / Major 0 / Minor 0。
- U0〜U3の実装は未承認。直前カードでユーザーは実装可否を選ばず、新しいエージェントへ分離する判断をした。
- 最初の作業は実装ではなく、下記承認資料を読み、同じ範囲の承認カードを提示すること。
- H0157を先に成立させる。他アクションへ展開しない。水着化の「静止した創作資料」条件を持ち込まない。

## 最初に読む正本

1. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-unified-rev4-u0-u3-implementation-approval-material.md`
2. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md`
3. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-current.md`
4. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md`

## 固定版

| 役割 | SHA-256 |
|---|---|
| U0〜U3実装承認資料 | `f99dc8de1fea587b8b637e2ed9c4c754cb80e5ddaa6961e5db31a3ebca0e02ea` |
| 一本化revision 4 | `04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a` |
| current | `5bb60fb5fab92d7fa8c8d310b4318f6121ef67df8aadca9b932d7b61f56ad87e` |
| 具体計画 | `cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f` |
| 最終review receipt | `83ecf2868e835bd3cd6466d19c42be58a3ad85533a0f4a4bff4d58f03a41b7ea` |

どれかが違えば旧承認を使わず、`EA_KB_SNAPSHOT_STALE` と差分を提示して停止する。

## 機械的な軸分離

### Helen側で書いてよい候補領域

- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/audit/`
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/helen_route_hook.py`
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/`
- project root `quality-gate.json` の `/execution_audit` 節だけ

上記もU0〜U3の実装承認を得るまでは書かない。

### Helen側で書いてはいけない領域

- `/Users/takedayousuke/.codex/skills/brainstorm/` 全体
- `/Users/takedayousuke/.codex/hooks.json` の直接変更
- `wiki/builds/codex-brainstorm-review-loop-prevention-task-entry.md`
- review-loop設計・lock・RL1〜RL7の実装物
- `helen-h0157-repro.blend`、f154展開、f166全量走査、U4以後
- 水着化、他キャラ、他アクションの成果物・記録

`hooks.json` へ必要なHelen 3枝は承認資料どおりの候補差分をaudit run配下へ作るだけにする。環境整備エージェントの完了後、現物SHAを再取得し、共有設定promoteの別承認を得るまで直接適用しない。

### 開始前・終了時の衝突検査

次の環境3ファイルを読取りSHA照合する。開始後に1件でも変わった場合は、並行する環境整備と衝突したものとして採用・hook接続を停止する。

| 環境ファイル | この入口作成時のSHA-256 |
|---|---|
| `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py` | `29caedcdf0cee738f943ba3611297362e569042d1cc6e0807e719e0a029d0cb8` |
| `/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py` | `7c53f05774c4b9c0b980480db3f178a3cfe59dbfc2ed1902e458b92e6853dd7b` |
| `/Users/takedayousuke/.codex/hooks.json` | `e2e70d262b040e6e2210de4b7c002bb78b922db35f734447584c735b1d1b083a` |

これは注意喚起ではない。SHA不一致時は `technical-stop-environment-axis-drift` と記録し、変更後環境へ自動追従しない。

## 最初の返答

承認資料の要点を、U0〜U3で変わるもの・変わらないもの・停止条件に絞って示す。承認カードで次のどれかを取る。

1. U0〜U3を承認。
2. 資料を修正。
3. 中断。

承認されても、U3の第1search-contractを提示した時点で再び承認待ちに戻る。

## 完了報告で分ける証拠

- 実装済み。
- unit/mutation test済み。
- 実hook到達済み。
- 第1search-contract作成済み。
- 実探索、原作比較、Blend反映、完成は未実施。

環境整備の成功・失敗をH0157の進捗証拠にしない。

## 使わなかったもの・落とした情報

- **捨てたもの**: 同じエージェントがreview-loop環境修理とH0157実装を連続して担当する方式、共有 `hooks.json` への並行書込み。
- **手元でどう変わるか**: Helen側はhook候補まで作れても、環境側が終わるまで共有設定への接続が保留になる。3Dの見た目はこの入口作成では変わらない。
- **戻せるか**: 入口文書なので戻せる。共有設定の直更新へ戻すには、環境側との衝突が無い現物SHAと別承認が必要。

## 関連

- [U0〜U3実装承認資料](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/sessions/20260901-unified-rev4-u0-u3-implementation-approval-material.md>)
- [H0157親メモ](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md>)
