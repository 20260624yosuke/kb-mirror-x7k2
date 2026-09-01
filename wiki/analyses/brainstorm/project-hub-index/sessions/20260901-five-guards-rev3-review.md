---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-01
---

# 5検査 計画 rev.3 の再々レビュー結果（2026-09-01・独立レビュー3回目）

計画 rev.3 の「後の工程1: rev.3 への再々レビューをサブエージェントで行い、結果を武田さんに見せる」の実施記録。
対象: `wiki/builds/brainstorm-five-guards-plan-20260831.md`（本文＋rev.2＋rev.3）。
レビュー役: 独立サブエージェント（Opus 5・計画を擁護しない指示・書き換え禁止）。

## 所見

> **rev.3 のまま実行の承認に進むべきではない。** critical 3件（うち2件は rev.3 が新規に作り込んだもの、
> 1件は rev.3 の事実誤認）、major 5件、minor 5件。

## critical 3件

### C-1 穴4 の判定は、既存の「良い版」の大半を落とす（計画の実測主張が自己矛盾）

計画本文は「既存の良い版3枚がすべて通ることを実測済み」と書いているが、**3枚とも落ちる**。
落ちる原因は、計画が穴4の実例として名指しした `mb-lead` を、その3枚が全部使っていること。

**私（メインエージェント）も独立に数え直して同じ数字を得た（2026-09-01）**:
正本 CSS `.agents/skills/html/design-system/document.css` の `mb-` クラス定義は **28個**、
対象 `wiki/_attachments/**/*.html` は **54枚**。

| 下位検査 | 54枚中の不合格 |
| --- | --- |
| 実在しないクラス | **17枚** |
| 要素との一致（`blockquote.mb-quote` 等） | 11枚 |
| 骨格（`mb-page`/`mb-wrap`/`mb-main`/`mb-glossary`） | **8枚** |

さらに、対象範囲 `wiki/_attachments/**/*.html` は `design-system/component-samples.html` 3枚と
説明文書でない単体HTML（`skin-genre-viewer.html`）まで含む。

### C-2 穴1（rev.3 N-2）は brainstorm の毎ターン動作とかみ合わず、恒常的に書き込みを止める

- N-2 のトリガーは「メモに新しい引用ブロックがあるのに `【優先順位】` 印つきカードの回答が無ければ止める」。
- ところが brainstorm の中核動作は「毎ターン、武田さんの発言をメモへ書き足す」＝**ほぼ毎ターン引用が増える**。
- 帰結は二択でどちらも壊れる: 毎ターン印を付ける→**恒真**（何も判別しない・文言ルールに戻る）／
  付けない→**成果物を書くたびに優先順位カードを1枚挟む**運用になる。
- さらに `Memo.covers()` は scope 配下の cwd を全部対象にするため、KB ルートを scope に含む
  active/ready メモが常時5件ある現状では、**どれか1枚に引用が増えるだけで KB 内の全セッションの
  HTML 書き込みが止まる**（並行会話どうしの相互ブロック）。

### C-3 N-6 の事実誤認 — `_guard_done_promotion` は**配線済み**

rev.3 N-6 は「未配線なので完成条件5の対象外」と書いたが、実際は
`brainstorm_guard.py:756` の `guard-write --unread` から呼ばれており、自己試験にも合格ケースが13本ある。
「未配線＝畳んでよい」と読んだ実装者が削除すると、`done` 昇格の完成条件検査が消える。

## major 5件（要旨）

- **M-1**: `【優先順位】` カードは**閉じた（dismiss）場合も** `tool_result` に質問文が復唱されて記録される。
  rev.3 の判定式（印つきカードへの回答が記録にあるか）は **dismiss を合格にする**。
  KB CLAUDE.md の状態遷移ゲート「カードを閉じただけは承認の証拠ではない」に反する。
  → `[User dismissed` を含まないことまで判定式に書く。全文検索は恒真になるので禁止。
- **M-2**: 「成果物」の定義が既存コードと二重化。`ALLOWED_WRITE_ROOTS` に `wiki` が入っているため、
  `wiki/_attachments/**/*.html` は既存判定では**成果物ではない**。HTML だけ書いたターンで、
  既存の「実装ゼロ」検査が止め、同時に新設の穴2 がパス記載を要求する＝同一ファイルを両分類する。
- **M-3**: 「brainstorm 以外の通常作業には発火しない」は成立しない。KB ルートを scope に含む
  active/ready メモが常時5件あるため、`/llm-wiki` や `/transcript` の作業まで対象になる。
  → ゲートを「そのセッションで brainstorm スキルが起動されたか」に変える案が出ている。
- **M-4**: 「そのターン」の定義が計画に無い（`transcript_events()` はターン境界を落としている）。
  「会話ぜんぶ」で実装すると、最初のターンで書いた HTML のパスを以後全ターンに書き続けないと閉じられない。
- **M-5**: 穴3のカウンタを PreToolUse で増やすと、**拒否された Edit・再試行まで +1** される。
  T=2 の根拠は「実行された tool_use」を数えた値なので母集団が違い、最初の追記で止まりうる。
  永続状態に有効期限が無い点も未検証。対象が Edit のみになったため実効母集団は 48操作ではなく **Edit 15操作**。

## minor 5件（要旨）

- m-1: 内蔵先が「コマンド名」指定で関数レベルが曖昧（`_content_block_reason` か `cmd_guard_stop_content` か）。
- m-2: **逃げ道の記載漏れ3件**。`bypass` フラグ（24時間・`guard-write` 冒頭で即 return）、
  `stop_hook_active`（一度ブロックした後は全検査素通り）、環境変数（`LLM_WIKI_ROOT` 等）で無効化できる。
- m-3: 穴5 の「取得した記録」に、どのツール呼び出しを合格根拠にするかが無い。
- m-4: 穴2 の実パス照合が、グローバル CLAUDE.md の「作業ディレクトリ内は相対パスのリンク」規則と衝突する。
  → 絶対パス **または** KB ルートからの相対パスの両方を受理すると明記すべき。
- m-5: 完成条件2「5つとも guard.log に発火行が出る」の達成手順が計画に無い。

## 確認して問題なしと明示された項目

- N-5 の CSS パスは実在（`.claude/skills/html` は `.agents/skills/html` へのシンボリックリンク）。**計画の記述は正しい。**
- 各成果物フォルダの `design-system/` は3つとも正本と md5 一致。現時点でこの下位検査は**恒偽**。
- N-1 の事実関係（`settings.json:79` = `guard-stop-content`・`settings.json:163` = `guard-write --unread`・
  スキル側フックが再開で消える docstring）は**正確**。rev.2 の内蔵先誤りの訂正として妥当。
- rev.2 m-1（自己試験は4層）・m-2（系列 `WWAAEEAAW`）は正しい。
- 既存の自己試験 `audit-handoff --selftest` は**第1層〜第4層すべて PASS**（完成条件4のベースライン）。
- 穴4 の CSS 骨格の記述（`aside.mb-glossary` / `blockquote.mb-quote` / 正しくは `mb-lede`）は正しい。

## レビュー役が示した「承認へ進むための最小条件」

1. C-3（`_guard_done_promotion` 未配線の誤記）を訂正する — 1行。
2. C-1 の誤検知を54枚で数え直し、穴4 の3つの下位検査それぞれの扱い（止める／報告のみ）を決める。
3. C-2 の「新しい引用」を、メモ全体の差分ではなく**専用節に限定**して定義し直す。
4. M-1（dismiss を数えない）・M-4（ターンの定義）・M-5（カウンタ増加点）を判定式として書き込む。
5. M-2・M-3 のどちらかの解き方を選ぶ。

**すべて「計画の文言を足す／数える」作業で、実装は不要。**

## 関連リンク

- 計画正本 — `wiki/builds/brainstorm-five-guards-plan-20260831.md`
- 親メモ — `wiki/analyses/brainstorm/project-hub-index/_index.md`
