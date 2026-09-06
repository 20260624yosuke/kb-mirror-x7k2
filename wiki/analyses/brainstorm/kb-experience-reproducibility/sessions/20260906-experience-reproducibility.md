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

## 追記2（カード回答3・記憶2件をコードで裏取り）

探りは中止（Codex・opencode とも利用量切れ、回復は半日後）。代わりに武田さんの記憶を手がかりに
コードと記録済み実測を読んだ。**新規のトークン消費なし。**

### 無限ループ

- 会話の終わりで止める歯止め（`stop_hook_active` を読む処理）の数:
  Claude の `brainstorm_guard.py` **3か所** ／ `tools/deliverable_path_guard.py` **1か所** ／
  `codex_adapter.py` **0** ／ `.opencode/scripts/muse_brainstorm_check.py` **0**。
- Codex に情報が来ていないわけではない。`tools/context_harness/evidence/openai-hooks.md` 13行目に
  実セッション採取として「`Stop` は `turn_id`、`stop_hook_active`、`last_assistant_message` を受け取る」。
  **受け取っているのに読んでいない。** 環境の制限ではなく実装の抜け。
- `codex_adapter.py` の `main()` は内部例外でも Stop を止める（`BS_INTERNAL:`）。抜け道なし。
- **09-01 の現行の小さい版で数えた値。旧版だけの話ではない。**

### 埋もれる件

- 検査5 は「本文が書かれたか」を見て、「本文が読めたか」を見ていない。
  opencode + VSCode 統合ターミナルでは **合格しつつ武田さんは何も読めない**。
- `.opencode/instructions/display.md` 第2節は「前提：この会話はデスクトップアプリで見ている」。
  武田さんが居たのは VSCode の統合ターミナル（TUI）。**別の画面を前提にルールが作られていた。**
- `opencode-display-audit` の宿題「カードの選択肢内にパスを書く書式（短名で回避）」が、
  実は本体の問題だった。

### 設計の結論

**検査は3つとも同じ。説明の置き場所だけ画面ごとに変える。**
opencode ではカードが唯一読める面なので、カードが要約を持つ必要がある。
ふるまいは同じなので「エージェントは1単位」と衝突しない。

## 追記3（実装まで完了・2026-09-06 夜〜09-07）

### 実施

- 計画書 `wiki/builds/kb-agent-parity-stop-brake-plan-20260906.md` を作成（第1版）。
- サブエージェントに独立レビューを依頼（読み取りのみ、私の結論は伝えず事実照合のみ指示）。
  **指摘6件。最重要は「初版の位置指定が欠陥版を許し、初版の試験と完成条件がそれを合格させる」。**
- 第2版へ反映 → 武田さんの実行承認（条件: 先にバックアップの確認）→ 実装。
- バックアップ: `~/.codex/skill-backups/brainstorm-pre-brake-20260906`（フォルダ丸ごと・`diff -r` で一致確認）
  ＋ `codex_adapter.py.bak-20260906`。
- 実装: `codex_adapter.py` の2か所。試験 11→14 件。**壊し試験 3/3。**
- 完成条件: `BRAKE-MISSING`→`BRAKE-OK`、`TESTS-11-OK`→`TESTS-14-OK`。

### 調査（config.toml の登録漏れ）

- 信頼済み登録はグループ 0 のみ。`stop:1:0`（パスの関所）と `pre_tool_use:1:0`（文書の関所）が無い。
- **ただし brainstorm の `stop:0:1` は登録があるのに発火していない。外からの材料はここで尽きた。**
- 信頼付与は `/hooks`（対話画面）でしかできない。`codex doctor` に項目なし、サブコマンドも無い。

### 私の誤りの訂正（この会話で4件）

1. 「変換層は小さくて済む」を `prose_guard.py` 1本から一般化した。
2. 「合格線を通したのは機械ではなく武田さんの目」— 武田さんの指す範囲は胸の布のみで、紐は未決着。
3. 「Codex と opencode に歯止めが0か所（2本直す）」— opencode は Stop 型の関所を持たず対象外。
4. **「09-01 以降 Codex が動いた記録が無い」— 誤り。この保管庫で 41 回、入替後も 9 回動いていた。**
   無いのはフックが発火した記録。
5. （レビュー指摘）「共有候補は約85%」— 分類が再現できず**撤回**。
6. （レビュー指摘）「5つのうち4つは3サービスとも止められる」— 止められるのは2つ。

### 分かった重要な事実

**今日の歯止めは新機能ではなく復旧。** 09-01 より前の重い版は `stop_hook_active` を読んでいて
（`reentry=bool(data.get("stop_hook_active"))`）、専用の試験ファイルもあった。
09-01 の書き直しで落ちた。当時の版には `BS_CARD_PREFLIGHT_MISSING` という停止もあり、
その文言は「PreToolUseの信頼状態と実発火を確認し」。**当時から発火の不安は認識されていた。**

### 次の会話が最初にやること

親メモの `## 2026-09-07 半日後にまとめてやること` を読む。①発火の見分け ②`/hooks` の確認
③歯止めの実機確認の順。①を飛ばすと②③の結果が読めない。
