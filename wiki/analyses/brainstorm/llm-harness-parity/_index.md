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

### 2026-08-31 カード回答（4案は選ばれず、問題が置き換わった）

> 機能を増やすって言っても、機械監査で禁止事項を書いてるだけだから同期しないのがおかしいんだよな。
> codexが増えてんのは、俺が成果物がクソだからclaudeとの使用感合わせろって言ったら、勝手に暴走して、
> 一瞬で週の25％のトークンを使った。成果ゼロ。意味がわからない。大問題だけど、どうやって解決したらいいか考えて。
> 心がけは禁止してるから。

> なんかこの使用感も気になるな。丁寧に指示して、段取り組んでるのに、実装させようとしたら、取り違え起きてんだよ。
> 前も、タスク進めろって言ったら、成果物がクソで、理由を詰めたら、計画や段取りはあるけど肝心の実装されてませんって言われた。
> 構造的に問題がある。
> 本当に構造に欠陥がある。
> 別のエージェントにやらせるでも、計画から実装まで同じエージェントが通しでやるでもどっちでもさケースはあるんだからさ、
> こんな欠陥あったらダメだろ。この問題の言語化と、原因の調査、解決を命じる。
> 問題を放置することを禁止します。なぜこうなったかの調査と解決。
> 解決は機械的な監査以外の方法を禁止します。
> 解決法の仕組みが、有効に機能する事実と根拠が揃ってない場合は禁止します。

- 提示した4案（A〜D）は選ばれていない。**問題設定が置き換わった。** 差をどう揃えるかではなく、
  ①中身は禁止事項の集合なのに同期していないこと自体がおかしい ②「合わせろ」の一言で暴走して
  週の25%のトークンを成果ゼロで消費したこと ③計画と段取りは出来るのに実装だけが抜けること、の3つ。
- **心がけ（文言ルール）による解決は禁止。機械的な監査だけ。**
- **有効に機能する事実と根拠が揃っていない仕組みを、解決策として出すことも禁止。**

### 2026-08-31 カード回答2（着手順は決定、新たに「説明が見えない」問題）

主質問の回答: **検査2→検査1（実装抜けを先に塞ぐ）**、確認「はい、この選択でよい」。

第2問の回答は選択ではなく、新しい問題の提起。

> 承認カード以外のお前の説明がこっちで見れない理由を説明して。
> 意図がないなら問題。問題を放置することを禁止します。なぜこうなったかの調査と解決。
> 解決は機械的な監査以外の方法を禁止します。
> 解決法の仕組みが、有効に機能する事実と根拠が揃ってない場合は禁止します。

原因（実測・この会話の記録を見て確認）: **ハーネスが隠しているのではなく、私が本文を1文字も
書いていなかった。** この会話で私が出したのは、ツール呼び出しと承認カードだけ。
brainstorm の規則「会話で設計を育てずファイルへ落とす」「応答は承認カードで終える」を、
「本文を書かない」まで拡大解釈した。**意図はあったが、やり過ぎ。**
`brainstorm_guard.py` の Stop 検査は「カードで終わったか」だけを見ており、
**本文が在るかは見ていない**（`_last_assistant_text` は引き継ぎパスの検査にしか使っていない）。
止める仕組みが無いので、私が黙っても何も起きなかった。

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

### 6. 実装が抜ける構造欠陥（2026-08-31 追加調査・すべて実ファイルを grep して確認）

3つの穴が実在する。すべて「機械が見ていない」ことの確認であり、心がけの話ではない。

**穴1：完成の判定を誰も見ていない。**
`brainstorm_status` は `active` → `ready` → `done` と決めてあるが、
`brainstorm_guard.py`（1929行）に文字列 `done` は **0件**。Codex 側の `codex_adapter.py`（1013行）と
`resume_contract.py`（364行）にも **0件**。検査しているのは `active` と `ready` だけ。
つまり **実装が終わったかどうかを見る仕組みは、両環境とも存在しない。**
実際、現存10枚のメモは active 6・ready 3・done 1 で、**渡したまま閉じていないものが3枚**ある。

**穴2：完成条件が散文で、機械は「節が空でないか」しか見ていない。**
`## 実装への申し送り` の検査は H7 の1本だけで、内容は「節が存在するか」。
`受入` は両環境 0件、`完成条件` は Claude 側3件（すべて説明文の中の語）。
何が出来ていれば完了かがファイルパス・コマンド・期待結果の形になっていないので、
**「計画を書いた」を「進めた」と報告しても検査に通る。**
前回の「計画や段取りはあるけど肝心の実装がされてません」は、この穴を通り抜けた結果。

**穴3：作業量に関所が無い。**
`budget` `cost` `token` `turn_count` は両環境の監査に **0件**。
監査は「書いていいか」だけを見て、「どれだけやったか」を見ない。
Codex の `guard.log` は 8-29=38 件、8-30=75 件、**8-31=334 件**。止める関所が無いまま増えている。

**穴4：KB が持っている監査を、Codex は読んでいない。**
KB 直下の `tools/prose_guard.py`（今日この HTML の書き込みを実際に止めた検査）は、
`~/.claude/settings.json` の PreToolUse に登録されているが、`~/.codex/hooks.json` には **0件**。
**同じ保管庫の同じ規則が、Claude では効いて Codex では効いていない。**
「機械監査で禁止事項を書いてるだけなのに同期しないのがおかしい」は、この意味で正しい。

### 7. なぜこうなったか（原因）

brainstorm は「会話を勝手に閉じない・メモを残す・実装させない」ために作った。設計の重心が
**入口の制御**（書かせない・閉じさせない）にあり、**出口**（実装が本当に行われたか）は最初から
対象外だった。`ready` は「渡してよい」という札で、**渡した結果を戻す経路が無い**。
だから工程を往復するほど、どこまで実際に出来たのかが人間の記憶にしか残らない。
Codex 側が膨らんだのも同じ形で、「合わせろ」に対する作業量の上限が無かった。

## 決まったこと

- **着手順は 検査2 →	検査1**（2026-08-31 カード承認・確認済み）。
  検査2＝ready のメモがある会話で、成果物への書き込みが1件も無いまま終わるのを止める。
  検査1＝`done` へ下げる操作を、完成条件の実行合格で縛る。
  失うものとして提示済み: トークン暴走（検査3）はこの2本では止まらず、先送りになる。
  これは方針の承認であり、実装の承認ではない。実装は別の会話。

### 検査5（新規・2026-08-31 の指摘から）

承認カードを出すターンで、カードの前に本文が無い／極端に短い場合に Stop で止める。
実装可能性の根拠: `brainstorm_guard.py` に `_last_assistant_text(payload)` が既にあり、
Stop フックは最後の発話本文を読めている（現在は引き継ぎパスの検査にしか使っていない）。
**効くという根拠はまだ無い。** 否定・肯定・実機の3試験を通すまで採用しない。

## まだ決まってないこと

- 検査4本（受領証 / 実装ゼロ検出 / 作業量の関所 / 監査の同期）のうち、どれから作るか。
  **4本とも未実装で、効くという根拠は無い。** 根拠が揃うまで採用しない。
- 検査3 の閾値。既存ログの実測分布を見るまで数字を置かない。
- 検査1 で「コマンドを実際に走らせる」範囲。走らせること自体に時間と費用がかかる。
- 週の25%を消費した件の実消費量。まだ確認していない（Codex の記録から取れるかも未確認）。
- 提示した4案（A〜D）の扱い。今回は選ばれていないので保留。差の是正は、検査4の「監査の同期」が
  入れば自動的に一部が片付く可能性がある（未検証）。

## 捨てた案と理由

（まだ無し）

## 直した記録

（まだ無し）

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- 移植の経緯: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- 仕様の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
- 説明 HTML（使用感の差）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260831-claude-codex-usage-gap.html`
- 説明 HTML（実装が抜ける構造欠陥）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260831-implementation-gap-defect.html`

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
