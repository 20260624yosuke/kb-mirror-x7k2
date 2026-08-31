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

### 2026-08-31 方針変更（計画と実装を分けるのをやめる）

> 心がけは不要です。機械監査で制御して、二度と再発しないようにしてください。
> 実装と計画は分けると私の意図でそうしましたが、思った以上に使用感が悪いので変えます。
> 実装はどのエージェントで行うかは、私の指示がない限り勝手にllmが判断することを禁止します。
> このエージェントで実装までします。

- **brainstorm の「実装は別の会話へ渡す」を撤回。** 実装先は武田さんが指示する。
  LLM が「別会話へ渡す」と勝手に決めることを禁止。
- **この会話で実装まで行う。** 上の発言を封鎖の解除の明示とみなし、bypass を実行した
  （セッション 7a865522、24時間で自動失効）。
- 検査5（カード前に本文が無いのを止める）は、心がけではなく機械監査として実装する。

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
  これは方針の承認。実装先は同日の方針変更で「この会話で行う」に変わった
  （旧記述『実装は別の会話』は失効。2026-08-31 に修正）。

### 検査5（新規・2026-08-31 の指摘から）

承認カードを出すターンで、カードの前に本文が無い／極端に短い場合に Stop で止める。
**2026-08-31 実装済み**（`cmd_guard_stop` 発火点③）。否定・肯定の自動試験は通ったが、
**実機での発火は未確認**。

## まだ決まってないこと

- **未着手は無し。** 検査1〜5と誤検知修理・封鎖の条件化・監査の同期を、Claude と Codex の
  両方に実装した（自動試験のみ合格、**実機未確認**）。
- 残るのは実機確認。特に Codex の `guard-stop-content` が adapter 経由で実際に呼ばれるか。
- `~/.codex/AGENTS.md` が 0 バイトのままである影響。今日は触っていない。
- （決着済み）検査3 の閾値 → 実測分布から 200 に決定。
- 検査1 の `run:` を実行することによる時間と費用。今は1件120秒・最大10件で頭打ちにしただけ。
- 週の25%を消費した件の実消費量。まだ確認していない（Codex の記録から取れるかも未確認）。
- 提示した4案（A〜D）の扱い。今回は選ばれていないので保留。差の是正は、検査4の「監査の同期」が
  入れば自動的に一部が片付く可能性がある（未検証）。

## 捨てた案と理由

（まだ無し）

## 直した記録

- 2026-08-31 `brainstorm_guard.py` の `SKILL.md` 側 description と本文にあった「実装はしない」を、
  「実装先は武田さんが決める」へ変更。武田さんの 2026-08-31 の方針変更の反映。
- 2026-08-31 自己試験の最終行が「第1層〜第3層」のままだったのを第4層まで含む表記へ修正。
- 2026-08-31 「決まったこと」に残っていた『実装は別の会話』を、同日の方針変更に合わせて修正。
- 2026-08-31 「検査5は未実装」「4本とも未実装」という記述を、実装済みの現状に合わせて修正。

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- 移植の経緯: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- 仕様の正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
- 説明 HTML（使用感の差）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260831-claude-codex-usage-gap.html`
- 説明 HTML（実装が抜ける構造欠陥）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/llm-harness-parity/20260831-implementation-gap-defect.html`

## 実装への申し送り

### 2026-08-31 この会話で実装した3件（自己試験 PASS・実機未確認）

対象ファイル: `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`、
`/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`。

1. **検査5（カード前の本文）** — `cmd_guard_stop` に発火点③を追加。承認カードを出しているのに
   カードの外の本文が `MIN_PROSE_CHARS`（120字）未満なら閉じさせない。カードの選択肢の文字数は
   数えない（`_prose_of` が tool_use を除外）。記号・空白も数えない（`#` の連打で水増しできない）。
2. **誤検知の修理** — `_looks_write_command()` を新設。`2>&1` を書き込みとみなすのをやめ、
   リダイレクト記号は**引用符の外だけ**で見る。書き込みの動詞（rm / cp / mkdir 等）は引用符の
   中も見る。今日6回出た誤検知（`ls` `wc` `find`）の直接の原因。
3. **封鎖の条件化** — 封鎖は、親メモの frontmatter に
   `implementation_agent: separate-session` がある場合だけ効く。無ければ封鎖しない。
   「実装を別会話へ回す」判断を LLM が勝手にしないための機械化。
   **この案件のメモにはこの行を書かない**（＝この会話で実装する、が武田さんの指示）。

**やってはいけないこと**: `implementation_agent` の行を私の判断で書き足すこと。
`MIN_PROSE_CHARS` を下げて検査5を骨抜きにすること。誤検知修理を口実に、書き込み動詞の検出を
外すこと。

### 2026-08-31 続けて実装した2件（承認済みの検査2→検査1）

4. **検査2（実装ゼロ検出）** — `cmd_guard_stop` に発火点④を追加。`ready` のメモを**実際に読んだ**
   会話で、成果物への書き込みが1件も無いまま閉じようとしたら止める。書き込みの判定は宣言ではなく
   transcript の実際のツール呼び出しを数える（`_deliverable_writes`）。wiki やメモへの書き込みは
   成果物に数えない。実装しない回の逃げ道は固定の印 `実装なし: <理由>`（理由10字以上）で、
   印だけでは通らない。読んでいない会話・`ready` が無い会話には掛からない。
5. **検査1（受領証）** — `cmd_guard_write` に発火点⑤を追加。`done` へ下げる操作を、
   `## 実装への申し送り` の ```done-when``` ブロックの条件を**実際に実行して**合格するまで拒否する。
   条件は `path:`（実在すること）と `run: コマンド ==> 期待する文字列`。最大10件・1件120秒。
   `run` に `sudo` `rm ` `curl` `>` などは書けない（メモの文字列をそのまま実行するため）。

**根拠の状態**: 第4層に否定試験と肯定試験を28件追加し、`audit-handoff --selftest` は
第1層〜第4層すべて PASS。**実機での発火はまだ観測していない。**

### 2026-08-31 Codex 側への移植（承認済み）

Claude 側の5件と、KB 監査の同期（検査4）を Codex へ入れた。

- `/Users/takedayousuke/.codex/skills/brainstorm/scripts/brainstorm_guard.py` に、Claude 版と同じ
  定数と関数を移植（`_looks_write_command` / `_prose_of` / `_card_turn_prose` /
  `_deliverable_writes` / `_parse_done_when` / `_run_done_conditions` / `_guard_done_promotion`）。
  Codex 版の Stop は adapter が独自に持つため、検査5と検査2は新しい入口
  `guard-stop-content` に分け、adapter の `stop()` の冒頭から呼ぶ配線にした。
- 第4層の自己試験23件を Codex 側にも追加。第1層〜第4層すべて PASS。
- **検査4（監査の同期）**: KB の `tools/prose_guard.py` は Claude の Write/Edit しか見ておらず、
  Codex の `apply_patch` は素通りしていた。`apply_patch` を読めるようにし
  （`_apply_patch_targets` / `inspect_patch`）、`~/.codex/hooks.json` の PreToolUse へ登録した。
  実際に3件の入力で確かめた（版の印なし=拒否 / 版の印あり=通過 / コードは対象外=通過）。
- 変更前のファイルは `.bak-20260831` として同じ場所に残してある
  （`brainstorm_guard.py` / `codex_adapter.py` / `hooks.json` / Codex 版 `SKILL.md`）。

**未確認**: Codex 側も実機での発火は観測していない。`guard-stop-content` が adapter 経由で
実際に呼ばれるかは、次に Codex で brainstorm を使ったときに `guard.log` で確かめる。

### 2026-08-31 検査3（作業量の関所）— 6本目、両環境に実装

「合わせろ」の一言で暴走し、週の25%を成果ゼロで消費した件への歯止め。

- 1つの会話での書き込み操作が **200 件**を超えたら、そこで一度止める。止めっぱなしにはせず、
  続けてよいかを承認カードで聞き、答えをもらえば段が上がってそのまま続く。
- **閾値の根拠（実測）**: この保管庫の会話記録 111 セッションで、書き込み系ツール呼び出しの
  回数を数えた分布は 中央値 66 ／ 75% 143 ／ **90% 218** ／ 95% 247 ／ 最大 717。
  90 パーセンタイルに近い 200 を段の高さにした。ふだんの作業では発火せず、上位1割の長い作業で
  1度だけ止まる。推測ではなく数えた値。
- 読み取りだけの操作は数えない（`_candidate_paths` が空なら素通り）。
- 試験3件（201件目で止まる／カードに答えれば続く／読み取りは数えない）を Claude 側に、
  2件を Codex 側に追加。両方 PASS。

### 2026-08-31 実機確認とそこで見つかった2つの欠陥

**確認できたこと（実機・ログに残っている）**

| 検査 | 実機の証拠 |
|---|---|
| 誤検知の修理 | bypass を外した状態で `ls -la … 2>&1`／`python3 -c "print('a','->','b')"`／`wc -l …` が3件とも通った。同じコマンドは今日の午前に `lockdown DENY` になっていた |
| 検査1（受領証・否定側） | `2026-08-31 23:39:03 audit done DENY memo=_index.md fails=1` |
| 検査1（受領証・肯定側） | `23:42:13 audit done pass memo=_index.md conds=2`。直前に `selftest PASS` の行があり、フックが `run:` のコマンドを本当に実行したことが残っている |
| 検査3（作業量の関所） | 当セッションの `worklimit/7a865522….json` が実際に作られ、書き込み操作のたびに増えた |

**欠陥1: フックの制限時間を超えると素通りする（実機で発覚・修正済み）**

`run:` に時間のかかるコマンドを書くと、フック自体が制限時間（10秒）で打ち切られ、
**拒否されずに書き込みが通った**。`DONE_RUN_TIMEOUT = 120` は、フックが10秒で殺される以上
意味を持っていなかった。対処は2つ。1件あたり60秒・全体150秒の予算を置き、予算切れは
**不合格**にする（通さない）。あわせて `~/.claude/settings.json` と `~/.codex/hooks.json` の
PreToolUse の制限時間を 180 秒へ上げた。試験も追加（予算3秒で9秒かかる `run:` → 拒否）。

**欠陥2: スキルが登録するフックは会話の再開で消える（実機で発覚・修正済み）**

`guard-stop`（検査5と検査2が入っていた）と `guard-write --lockdown` は、スキル側が登録する
フックだった。ログを見ると `guard-stop` は **18:11 を最後に途絶えていた**。利用制限で会話が
中断し再開したあと、スキルのフック登録が失われたため。**再開後は検査5が効いていなかった。**
対処として、検査5と検査2を `guard-stop-content` という独立した入口に分け、常駐側
（`~/.claude/settings.json` の Stop）へ登録した。判定の中身は `_content_block_reason` に
1本化してあるので、どちらの入口から呼ばれても同じ。

**まだ確認できていないこと**: 封鎖の条件化（`lockdown off`）は、上の理由でこの会話では
フックが動いておらず観測できなかった。検査5・検査2の実機発火も、常駐へ移した直後なので未観測。

### 終わったら次に取る承認

5本すべてが実会話で発火したことを `guard.log` で確認したうえで、残る検査3（作業量の関所）と
検査4（監査の同期＝Codex 側への展開）へ進む承認。

確認する行:

- 検査5 … `BLOCK card-without-prose`
- 検査2 … `BLOCK no-implementation`
- 検査1 … `audit  done DENY` または `audit  done pass`
- 封鎖の条件化 … `lockdown off (no separate-session memo)`
- 誤検知の修理 … 読み取りだけのコマンドで `lockdown DENY` が出ないこと

## 機械化した指摘

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
| 同じ名前のスキルで環境ごとに中身が食い違う | する | できる（外部仕様の項目が両 SKILL.md に在るかの照合） | 未決定（今回の承認待ち） |
| 読み取り専用コマンドを封鎖が止める | していた（今日6件） | できる | `_looks_write_command()`。2026-08-31 実装・第4層で試験 |
| カードだけ出して本文を書かない | する | できる | `guard-stop` 発火点③（検査5）。2026-08-31 実装・第4層で試験 |
| 実装先を LLM が勝手に別会話へ回す | する | できる | 封鎖を `implementation_agent` の記録に条件づけ。2026-08-31 実装 |
| 計画だけ作って実装せずに閉じる | している | できる | `guard-stop` 発火点④（検査2）。2026-08-31 実装・第4層で試験 |
| 完成条件を満たさずに done にする | する | できる | `guard-write` 発火点⑤（検査1）。2026-08-31 実装・第4層で試験 |

## 関連リンク

- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-guard-fix-handoff-20260829]] — `wiki/builds/brainstorm-guard-fix-handoff-20260829.md`

## セッションメモ（子）

- 親: このファイル。子はまだ無し。
