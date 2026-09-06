---
type: analysis
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-06
---

# 2026-09-06 良かった理由の分解と、分岐の実測

親: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`

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

- 説明ページ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260906-kb-experience-reproducibility.html`
- 新しい親メモ `kb-experience-reproducibility/_index.md` を作成（武田さんの指示で分離）
- フックの能力の実測（追記1への回答）と、手立て4つの詳しい説明（追記3への回答）を親へ追加
- 合格ラインの範囲について武田さんの訂正（追記2）を受け、節Gを訂正済みとして書き換え

## 次の会話が最初に読むもの

1. 親メモの `## 2026-09-06 実測` と `## 2026-09-06 手立ての候補`
2. 上の説明ページ
3. `python3 .opencode/scripts/harness_parity_check.py check` を走らせて現在値を取る

## この回の決着

- 読み取りは「合っている」で承認。記録先は新しい親メモへ分離。
- 追記1（ボトルネックの有無）に回答: 要る能力5つのうち4つは3サービスとも止められる。
  欠けは opencode の「会話の終わりで止め返す」1つだけで、代替がある。**技術的な障害はほぼ無い。**
- 追記2（合格ラインの範囲）で節Gを訂正。合格箇所は胸を覆う布のみ、紐は未決着。
- 追記3（手立ての詳細）に回答: 手1〜手4 それぞれに「いまの形／変える形／手元で何が変わるか／
  失うもの／見積り」を書いた。

## まだ決まっていない

- 手1（判定の本体を保管庫へ寄せる）に進むかどうか。2026-08-29 の「独立方針」を
  上書きすることになるので、武田さんの判断が要る

## 追記1（カード回答2・手1 の現実性を測り直した）

武田さんは手1 を承認せず、「現実的に可能かどうか調べる必要がある。各サービスの事実を
根拠にしないと成果に繋がらない」と指摘。その指摘は当たっていた。

- 私の「変換層は小さくて済む」は `prose_guard.py` 1本からの飛躍。あれは「1回の書き込みの中身」、
  brainstorm は「会話1本ぶんの経過」。規模の違う2つを同じ根拠にしていた。
- 測り直し: `brainstorm_guard.py` 関数 2,134 行 ＝ 判定 909(42.6%) ／ 自己試験 894(41.9%) ／
  収集・入出力 331(15.5%)。**共有候補は約85%。**
- ただし収集は **transcript を読む前提**（依存箇所20か所以上）。Codex は自前の状態ファイル、
  opencode は流れるイベントから再構成。**情報源が3通り**で、訳せば済むものではない。
- したがって手1 は3層（収集／事実の記録／判定）。**②事実の記録がまだ無い。**
- **前提が未確認**: Codex は 09-01 の入れ替え以降、この保管庫で動いた記録が無い。
  `lite-state/` も `lite-events.jsonl` も存在せず、09-06 に私が直接起動して初めて生成された
  （確認後に削除）。`tools/logs/prose-guard.log` の `apply_patch` は 08-31 の試験3件が最後。
  コード自体は手動起動で正常動作。`config.toml` の `trusted_hash` の一致は外から確認できない。

### 次の一手（未承認）

`codex exec -C <保管庫> "…"` と `opencode run "…"` を1ターンずつ流し、
`tools/logs/prose-guard.log` と `~/.codex/skills/brainstorm/scripts/lite-state/` が伸びるかを見る。
費用は各1ターン分。判定は「行が増えたか」だけ。
