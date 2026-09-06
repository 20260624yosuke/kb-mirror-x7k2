---
type: analysis
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-06
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
  - /Users/takedayousuke/.agents
  - /Users/takedayousuke/.claude/skills/brainstorm
  - /Users/takedayousuke/.codex/skills/brainstorm
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260906-kb-experience-reproducibility.html
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/harness_parity_check.py
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/CLAUDE.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/AGENTS.md
  - /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md
  - /Users/takedayousuke/.codex/skills/brainstorm/SKILL.md
---

# 保管庫のLLM体験を、どのサービスでも同じに出す（2026-09-06 開始）

**引き算のメモではなく足し算のメモ。** 「差が不快だから禁止する」という向きの記録は
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
（[[llm-harness-parity]]、状態 active）にある。こちらは
**ヘレンの案件で良かったものを、他のサービスでも出すには何が要るか**だけを扱う。

## 武田さんの考え
### 2026-09-06 良かったほうから問い直す（フォークした会話）

> このセッションはフォークしたもので、ここからはkbフォルダでのllm運用の話をしたいと思う。
> 話したい内容は、このプロジェクトのllm体験がすごくいいということ。
> 仕組みとして気に入っている。意図を汲み取って欲しいんだけど、opus5の性能がすごいという話ではなく、
> ハルシネーションを軽減しつつ難しいタスクを段階的に処理できている。正直llmが、
> 今回の成果物のようなクリエイティブな合格ラインを通過できるのはすごいと感じた。
> これは性能というよりも、俺の意図や指示の元であるコンテキストの粒度を上手くコントロールしたからだと思う。
> この感覚は俺が使っているllmの中でclaudeがダントツでいい。
> でも俺の思想としては、llmの各種サービスやモデルってエージェントっていう一つの単位だから、
> kbフォルダ内でサービスごとにエージェントの挙動が変わるのは不快なんだよね。
> claudeが無限に使えるわけじゃないから、ここがボトルネック。
> 現に今使ってる/brainstormのスキルも他のサービスのllmだと上手く動作しないし、適応させようと調整を実行させても、
> 俺がなんで/brainstormを使ったかを汲み取れない。
> どうすればこのllm体験の再現性をkbフォルダで安定化できると思う？

- これまでのこのメモは「差が不快だから禁止したい」という**引き算**の問い。今回は
  「良かったものを他でも出したい」という**足し算**の問い。同じ対象だが向きが逆。
- 武田さんの仮説: 効いていたのは **モデルの性能ではなく、文脈の粒度の制御**。
- ボトルネックは Claude の利用量。だから「Claude だけで良い」は答えにならない。
- サービスごとに挙動が変わること自体が不快。**エージェント＝1単位**という思想。

### 2026-09-06 カード回答（読み取りは合意・追記3件）

主質問: **「合っている（この枠で続ける）」**。記録先: **「新しい親メモに分ける」**（このファイル）。

追記1（ボトルネックの有無について）

> この部分は、そもそもclaudeとcodexだと挙動が違う。現状の差異も調整をcodex側で試させた結果。
> でも上手くいってないらしい。技術的に最終的な使用感は似たようにできると直感してるから、
> 疑問に感じるんだよね。ボトルネックになる部分なんてないのでは？

追記2（合格ラインの指す範囲。私の言い直しへの訂正）

> なるほど、俺の言い方に語弊があるかもしれない。
> でも、このプロジェクトの進み方じゃなかったら現行のllmがヘレンに水着を着せるという、
> 成果物としての基準を、妥協ではあるが一応合格ラインにできることはなかったと思う。
> あと俺の言ってる合格箇所は、バストにビキニを着せる部分のみであって、乳首を隠す布の部分の話をしてた。
> 紐とかは、まだ詰まってるのは周知の事実。

追記3

> 手立ての候補と、失うもの。ここらへんの前後の説明をもう少し詳しく聞きたい。

**追記2 は私の言い直しが的を外していたということ。** 武田さんが「合格ライン」と呼んでいたのは
**胸を覆う布**の一点で、紐は最初から未決着として扱っていた。私が挙げた却下3件はすべて紐と手順の話で、
胸の布の話ではない。よって「合格線を通したのは機械ではなく武田さんの目」という私の書き方は、
**武田さんが指していた範囲には当てはまらない**。胸の布は G12a が ×1.442 で合格している。
訂正して、この節を正とする。

### 2026-09-06 カード回答2（承認せず・事実の確認を先に）

> 承認しません。意見を言います。
> ###
> 「手1 に進むか。進む場合、2026-08-29 に承認いただいた『スキル本体は LLM ごとに独立』を
> 上書きすることになります。上書きの根拠は、そのときの理由（本体を共有しても変換層が要るだけ）が
> prose_guard.py の実物で反証されている点です。」
> この部分は現実的に可能かどうか調べる必要があります。
> codexでのbrainstromの挙動は確かに変だったのは覚えているので、
> 各サービスの事実を確認してそれを根拠に方針を固めないと、成果に繋がりません。

**指摘は当たっている。** 私は `prose_guard.py` 1本の成功から「変換層は小さくて済む」を一般化した。
`prose_guard.py` が扱うのは「1回の書き込みの中身」だけで、brainstorm の検査が要るのは
「会話1本ぶんの経過」。**規模の違う2つを同じ根拠にした。**
以下は、その指摘を受けて測り直した結果。

## 2026-09-06 実測（すべてこの日に実ファイルを読んで確認）

### A. KB フォルダに置いた検査は一致し、各ハーネスのフォルダに置いた検査は一致していない

これは理屈ではなく、いまの配線を読んだ結果。

| 検査の置き場所 | 呼んでいるハーネス | 分岐 |
|---|---|---|
| `tools/prose_guard.py`（KB の中） | Claude の PreToolUse ／ Codex の PreToolUse | 無し（同じ1ファイル） |
| `tools/deliverable_path_guard.py`（KB の中） | Claude の Stop ／ Codex の Stop | 無し（同じ1ファイル） |
| `~/.agents/context-harness/current/context_harness.py`（共有） | Claude 9イベント ／ Codex | 無し（同じ1ファイル） |
| brainstorm の判定本体 | Claude=`~/.claude/skills/brainstorm/brainstorm_guard.py` ／ Codex=`~/.codex/skills/brainstorm/scripts/codex_adapter.py` ／ opencode=`.opencode/scripts/muse_brainstorm_check.py` | **3実装に分岐** |

`python3 .opencode/scripts/harness_parity_check.py check` は 2026-09-06 現在 **FAIL・10件**。
検査1(done受領証)・検査2(実装ゼロ検出)・検査3(作業量の関所)・handoff到達性が Codex と opencode の
両方に無く、検査5(本文量)と guard-card確認質問が Codex に無い。

### B. `/brainstorm` の本文が3か所で長さから違う

- Claude 版 `~/.claude/skills/brainstorm/SKILL.md` … 238 行
- Codex 版 `~/.codex/skills/brainstorm/SKILL.md` … **77 行**
- opencode 版 `.opencode/commands/brainstorm.md` 66 行 ＋ `.opencode/instructions/brainstorm-body.md` 11 行

Codex 版の末尾には次の一文がある。

> 巨大な監査台帳や、通常作業を横断する許可レジストリは作らない。必要な機械保証は、親マーカー、
> 二問カード、状態保存、承認前の書込み拒否、圧縮後再注入に限定する。

つまり **Codex は「意図を汲み取れていない」のではなく、機械を持たないと書いてある別の文書を読んでいる。**
武田さんの「なんで /brainstorm を使ったかを汲み取れない」の機械的な原因はここ。

### C. `llm-wiki` スキルも2つに分かれている（今回発見・これまで未記録）

- `~/.agents/skills/llm-wiki/SKILL.md` … 8,953 バイト（2026-05-17）
- `~/.claude/skills/llm-wiki/SKILL.md` … 9,642 バイト（2026-05-20）
- 見出しの並びは完全に一致。中身は SKILL.md で 39 行、reference.md で 150 行ぶん違う。
- `AGENTS.md` は Codex を `~/.agents/skills/llm-wiki/` へ、`CLAUDE.md` は Claude を
  `~/.claude/skills/llm-wiki/` へ送っている。**同じ名前の別の規約を読ませている。**

### D. 分岐しない置き方の先例が、すでにこの保管庫にある

`.claude/skills/html` は `.agents/skills/html` への **symlink**（inode 852375 で同一）。
複製ではないので、書き換えても分岐しようがない。opencode 側も `.opencode/commands/html.md` で
`.agents/skills/html/SKILL.md` を正本と名指ししている。**html だけは3ハーネスで1実体。**

### E. 分岐は事故ではなく、承認済みの方針の結果

- 2026-08-29 承認: **「スキル本体は LLM ごとに独立。共通本体の一元化は行わない」**
  （理由として記録されているのは「フックの入出力形式が環境ごとに違うため、本体を共有しても
  変換層が要るだけ」）。
- 2026-09-01 承認: Codex 版を小さい版へ置換（旧版は `~/.codex/skill-backups/brainstorm-pre-lite-20260901-093146` へ退避）。

**8-29 の理由づけは、いまや `tools/prose_guard.py` の存在で反証されている。** あれは KB に本体を置き、
Codex 側の `apply_patch` を読む関数を1本足しただけで両方から呼べている。変換層は「要るだけ」ではなく
「小さくて済んだ」。方針を見直す根拠はここ。

### F. ヘレン案件で効いていたものの内訳（良かった理由の分解）

| 効いたもの | 実体 | 移せるか |
|---|---|---|
| 進み具合を「未測定の側面の数」で数えた | `gf2-helen-swimsuit-goal-map.json` | 移せる（JSON） |
| 武田さんの言葉を逐語で番号付き保存 | `output/gf2-helen-swimsuit/explicit-statements.json` | 移せる（JSON） |
| 合格線を毎回原作から計算し、コードに焼かない | G検査群 | 移せる（Python） |
| 検査自体を壊して捕まえられるか試す | 各検査の mutation test | 移せる（Python） |
| 目的を忘れることを機械で禁止 | `tools/purpose_guard.py` P2a〜P2d ＋ `plan_audit.py` A25 | 移せる（Python） |
| 合否を1コマンドで言い切る | `tools/plan_audit.py`（A1〜A25） | 移せる（Python） |

**要点: 上の6つは全部 KB フォルダの中に居て、Python と JSON でできている。どのサービスでも動く。**
効いていたものの本体は、Claude の中には無かった。

### G. 「合格ラインを通したのは誰か」— 私の言い直しは的を外していた（訂正済み）

> [!warning] この節の初版は訂正された
> 初版で私は「クリエイティブな合格線を通したのは機械ではなく武田さんの目」と書いた。
> **武田さんの追記2により、これは指している範囲が違うと分かった。** 武田さんが「合格」と
> 呼んでいたのは**胸を覆う布（乳首を隠す部分）だけ**で、紐は最初から未決着として扱っていた。
> 現行説は下の「訂正後」。初版の主張は、範囲を紐に限れば今も成り立つ。

**訂正後（現行説）**

- 武田さんの言う合格箇所は **バストにビキニを着せる部分＝乳首を隠す布**。この一点。
- そこは合格している。カップの寸法の検査 G12a が **×1.442 で合格**（09-06 に段階の読み間違いを
  直して合格になった。それ以前は ×1.099 で永久不合格だった）。
- 私が挙げた却下3件（紐が細い／方法の承認前に成果物を汚した／目的を忘れた）は、
  **すべて紐と手順の話**であって、胸の布の話ではない。
- 武田さんの評価は「妥協ではあるが一応合格ラインにできた。この進み方でなければできなかった」。

**初版の観察のうち、いまも有効な部分**

- 却下3件のうち、目的の件だけが機械になり、**紐の断面と「方法の承認前に成果物へ触る」の2件は
  いまも文章の申し送りのまま**＝武田さんが禁止した「心がけ」の状態。ここは変わらない。
- 保管庫全体で、機械に変換した指摘は **121 件記録・29 件（24%）が未実装**
  （`## 機械化した指摘` の表を全メモで数えた値）。台帳としては完全で、取り付けが遅れている。


## 2026-09-06 手立ての候補（一覧。詳しい説明は下の「手立ての詳しい説明」）

| 手 | 中身 | 失うもの |
|---|---|---|
| 手1 | 判定の本体を KB の `tools/` へ移し、各ハーネスには payload を訳す薄い層だけ置く。先例は `prose_guard.py` と html の symlink | 2026-09-01 に「Codex を軽くする」と決めた判断を捨てることになる。重い検査が全部載れば、あのときのトークン消費が再発しうる。**本体の共有と、呼ぶ側で重さを選べることを、対で決める必要がある** |
| 手2 | 締めのフックが無い環境では、**承認カードを出す瞬間**を関所にする。opencode の `skill-gate.js` は既にこれをやっている | カードを出さずに閉じる回は素通りする。Stop フックの完全な代わりにはならない |
| 手3 | 台帳4点（明言・穴・検査・監査1本）を新プロジェクトの雛形にする | 小さい作業にも儀式が乗る。全部の案件に要るものではない |
| 手4 | Claude を「枠を設計する工程」に温存し、枠の中の実行を他のサービスへ回す | 引き継ぎ費用と取り違え（このメモに既に記録がある）。ただしこれは**ふるまいの差ではなく工程の差**なので、武田さんの「エージェント＝1単位」の思想とは衝突しない |

推奨は **手1 →	手2**。手1 は先例が同じ保管庫の中にあり、B と C の分岐を構造として消せる。
手2 は opencode の構造的な限界（会話の終わりを掴むフックが無い）に触れるので、
何を諦めるかを決めてからでないと着手できない。

## 2026-09-06 実測2：ボトルネックは技術側にあるのか（追記1への回答）

3つのサービスが実際に持っているフック（差し込み口）を、型定義と設定ファイルから全部読んだ。
brainstorm の使用感に要る能力は5つある。「観測できる」と「止め返せる」は別ものとして数えた。

| 要る能力 | Claude Code | Codex | opencode |
|---|---|---|---|
| 書き込む前に止める | PreToolUse（止まる） | PreToolUse（止まる） | `tool.execute.before`（止まる） |
| 承認を出す瞬間に止める | フック（止まる） | フック（止まる） | `tool.execute.before` の `question`（止まる） |
| 会話の始めに台帳を注ぎ直す | SessionStart / UserPromptSubmit | 同じ2つ | `chat.message` |
| 圧縮の前後で注ぎ直す | PreCompact / PostCompact | 同じ2つ | `event` の `session.compacted` |
| **会話の終わりで止め返す** | Stop（拒否を返せる） | Stop（拒否を返せる） | **できない** |

- opencode の `event` フックは `session.idle`（会話が止まった合図）を**受け取れる**が、
  型が `Promise<void>` で戻り値を持たないため、**押し返せない**。観測専用。
- 一方 `permission.ask` と `tool.execute.before` は出力を書き換えられるので、**止められる**。

**結論: 武田さんの直感どおり、技術的なボトルネックはほぼ無い。** 5つのうち4つは3者とも止められる。
唯一の欠けは opencode の「会話の終わりで止め返す」1つだけで、それも
**「ユーザーに問う瞬間で止める」に置き換えれば同じ働きになる**（opencode の起動路は既にそうしている）。

### では、なぜ Codex での調整は上手くいっていないのか

能力の問題ではなく、**渡した指示の問題**だった。2026-09-01 の置換で Codex 側に入れた本文には、
こう書いてある。

> 巨大な監査台帳や、通常作業を横断する許可レジストリは作らない。必要な機械保証は、親マーカー、
> 二問カード、状態保存、承認前の書込み拒否、圧縮後再注入に限定する。

Codex は言われたとおり小さいものを作った。**失敗ではなく、仕様どおりの結果。**
`harness_parity_check.py` が出す不合格10件は、そのとき落とした検査そのもの
（検査1・検査2・検査3・検査5・guard-card・handoff到達性）。

### 押さえておくこと

- Codex に「Claude と合わせろ」と言うと、**同じものを書き直そうとする**。8-31 に週の25%を
  成果ゼロで消費したのはこの形。合わせる作業は「書き直させる」ではなく
  **「同じ1ファイルを呼ばせる」**でなければ再発する。
- 逆に言えば、`prose_guard.py` と `deliverable_path_guard.py` と `context_harness.py` の3本は
  **既にその形で動いていて、一度も分岐していない**。前例は自分の保管庫の中にある。

## 2026-09-06 手立ての詳しい説明（追記3への回答）

### 手1　判定の本体を保管庫へ寄せる

**いまの形。** 同じ規則が3つのファイルに別々に書いてある。
`~/.claude/skills/brainstorm/brainstorm_guard.py`、
`~/.codex/skills/brainstorm/scripts/codex_adapter.py`、
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/muse_brainstorm_check.py`。
規則を1つ直すと、3か所を直さないと揃わない。実際に揃っていないのが不合格10件。

**変える形。** 判定の中身を保管庫の `tools/` に1本置き、各サービスには
「そのサービスのフックの入力を、共通の形に訳して渡す」だけの薄い層を置く。
`prose_guard.py` が既にこの形で、Codex の書き込み形式を読む関数を1本足しただけで両方から呼べている。

**手元で何が変わるか。** 規則を1回直せば3つのサービスで同時に効く。
`/brainstorm` が Codex でも opencode でも Claude と同じ場所で止まるようになる。

**失うもの。** 2026-09-01 に「Codex を軽くする」と決めた判断を捨てることになる。
重い検査が全部載れば、あのときのトークン消費が戻る可能性がある。
だから**本体の共有と、呼ぶ側で重さを選べること（どの検査を走らせるかをサービスごとに指定できる）
を、対で決める必要がある**。片方だけやると、8-31 の暴走と同じ形になる。

**要る作業の見積り。** 未見積り。判定本体の行数は Claude 側だけで約1,900行あり、
そのうち何割が「フックの形に依存する部分」かは数えていない。

### 手2　会話の終わりの関所を、ユーザーに問う瞬間へ移す

**なぜ要るか。** opencode だけは会話の終わりで押し返せない（上の表）。
いまの検査5（カードの外の本文が短いと止める）と検査2（実装ゼロで閉じるのを止める）は、
会話の終わりに置いてあるので、opencode では原理的に効かない。

**変える形。** 同じ判定を「承認カードを出す直前」で走らせる。
そこは3サービスとも止められる。opencode の起動路は既にこれをやっている。

**手元で何が変わるか。** opencode でも、本文の無いカードや実装ゼロの締めが止まるようになる。

**失うもの。** カードを出さずに閉じる回は素通りする。
brainstorm は「承認まではカードで終わる」規則なので実害は小さいが、ゼロではない。

### 手3　台帳4点を新しい案件の雛形にする

**中身。** ヘレンで効いた4つ（明言の逐語台帳／まだ測っていない側面の台帳／
合格線を原作から毎回計算する検査／合否を1コマンドで言い切る監査）を、
空の雛形として用意し、新しい案件はそこから始める。

**手元で何が変わるか。** いまは案件ごとに、長い議論の末にこの形へ辿り着いている。
雛形があれば最初からこの形で始まる。

**失うもの。** 小さい作業にも儀式が乗る。全部の案件に要るものではないので、
「どの規模から使うか」を決めないと、短い依頼が重くなる。

### 手4　Claude を枠の設計に温存する

**中身。** 台帳と測り方を決める工程（強い推論が要る）を Claude が担い、
枠の中の実行（検査を走らせて不合格を消す）を他のサービスへ回す。

**手元で何が変わるか。** Claude の利用量が、設計の回だけに集中する。

**失うもの。** 引き継ぎの費用と、取り違えの再発。
「計画と段取りはあるが実装されていない」は 8-31 に実際に起きていて、
そのために検査2を作った。**ふるまいの差ではなく工程の差**なので、
「エージェントは1単位」という武田さんの思想とは衝突しない。

**要る作業の見積り。** 未見積り。案件のうち強い推論が要る工程が何割かは数えていない。

## 2026-09-06 実測3：手1 は現実的か（カード回答2への回答）

### 1. 共有できる割合は数えられる

Claude 側の判定本体（`brainstorm_guard.py`）を関数ごとに分類した。関数の総行数 2,134 行。

| 区分 | 行数 | 割合 |
|---|---|---|
| 判定（サービスに依らない） | 909 | 42.6% |
| 自己試験（同上） | 894 | 41.9% |
| 収集・入出力（サービス依存） | 331 | 15.5% |

判定と自己試験は原理的にどのサービスでも同じなので、**約85%が共有の候補**になる。

### 2. ただし残りの15%は「薄い変換層」ではない

**Claude の収集は、会話記録ファイル（transcript）を読む前提**で書かれている。
`transcript_path` に依存する箇所が 20 か所以上あり、検査5（本文量）・検査2（実装ゼロ）・
カードの検出・引き継ぎの到達性は全部そこから facts を取っている。

- **Codex にはそのファイルが無い。** 代わりにフックが呼ばれるたび、自前の状態ファイル
  （`lite-state/<hash>.json`）へ積み上げている。
- **opencode にも無い。** 流れてくる `message.part.updated` から助手の発言を組み立てている。

**同じ形式の3通りではなく、情報源そのものが3通り。** ここが 8-29 の「変換層が要るだけ」という
言い方が指していた実体で、私の反証は的が小さすぎた。

### 3. したがって手1 は2層ではなく3層になる

| 層 | 中身 | いまの状態 |
|---|---|---|
| ① 収集 | そのサービスの流儀で事実を集める | サービスごとに必要。**すでに3通り存在する** |
| ② 事実の記録 | 集めた事実を共通の形で置く | **まだ無い**。Codex と opencode は私物の形で持ち、Claude は持たない |
| ③ 判定 | 事実を読んで合否を出す | 3実装に分かれている。**ここが1本にできる** |

つまり手1 の作業は「本体を移す」ではなく、**②の形を決めて、①を3つそろえ、③を1本にする**。
①は捨てられない。②は新規。③が本命。

### 4. そして前提が取れていない — Codex は 09-01 以降この保管庫で動いた記録が無い

- `~/.codex/skills/brainstorm/scripts/` に `lite-state/` も `lite-events.jsonl` も **存在しなかった**
  （2026-09-06 に私が直接起動して初めて作られた。確認後に削除済み）。入れ替えから5日間、
  **実会話の記録が1件も無い。**
- `tools/logs/prose-guard.log` を見ても、Codex 由来（`apply_patch`）の行は 08-31 23:04 の
  試験3件が最後で、**09-01 以降は0件**。
- **コードは壊れていない。** 手で `session-start` / `user-prompt` / `stop` を流したら、注入も
  停止判定（`BS_PARENT_REQUIRED / BS_CARD_REQUIRED`）も正しく出た。
- `~/.codex/config.toml` の `[hooks.state]` には `~/.codex/hooks.json` の全イベントが
  `trusted_hash` 付きで登録されている。ただしその hash の作り方は Codex 内部のもので、
  ファイル全体・正規化JSON・コマンド文字列のどれとも一致しなかった。
  **信頼済み hash が古いかどうかは、外からは判定できない。**

### 5. 武田さんが覚えている「Codex の挙動が変」は、09-01 より前の重い版の話

現行の小さい版は実会話で1度も動いた記録が無いので、**良いとも悪いとも言えない（未測定）**。
ここを未測定のまま方針を決めると、8-31 の「作ったが1度も発火していなかった」を繰り返す。

### 6. 結論

手1 は、共有できる割合（85%）から見れば現実的。ただし
**「各サービスがこの保管庫で実際にフックを発火させているか」を先に確かめないと、根拠にならない。**
いま確かなのは Claude だけ（今日の `prose-guard.log` に 18:07〜18:14 の行がある）。

**次に要るのは1ターンの探り。** `codex exec -C <保管庫> "…"` と `opencode run "…"` を
1回ずつ流し、`prose-guard.log` と `lite-state/` が伸びるかを見る。どちらの CLI も非対話で動く。
費用は各1ターン分。

## 決まったこと

- 2026-09-06 読み取りの承認。**効いていたのは保管庫側の台帳と検査で、分岐しているのは
  各サービスが自分のフォルダに持つ判定のほう。**この枠で続ける。
- 2026-09-06 記録先はこの新しい親メモ。`llm-harness-parity` には1行の案内だけを残した。

## まだ決まってないこと

- **手1 に進むかどうか。判断の前に、各サービスが実際にフックを発火させているかの確認が要る**
  （2026-09-06 カード回答2）。いま確かなのは Claude だけ。
- 1ターンの探りを実行するかどうか（`codex exec` と `opencode run` を1回ずつ。費用は各1ターン分）。
- 手1 と対で決める「呼ぶ側で重さを選べる」の作り方。
- 手2 の適用範囲（検査5と検査2だけか、会話の終わりの検査を全部移すか）。
- 手3 の発動条件（どの規模の案件から雛形を使うか）。
- 手4 の見積り（強い推論が要る工程の割合）。

## 捨てた案と理由

- **`prose_guard.py` 1本を根拠に「変換層は小さい」と結論する** … 2026-09-06 に武田さんの指摘で撤回。
  あれが扱うのは「1回の書き込みの中身」で、brainstorm が要るのは「会話1本ぶんの経過」。
  規模の違う2つを同じ根拠にしていた。正しい見立ては上の実測3。
- **Codex に「Claude と合わせろ」と言って書き直させる** … 2026-08-31 に実行して、
  週の25%を成果ゼロで消費した。同じ規則を別々に書かせている限り再発する。
  代わりに「同じ1ファイルを呼ばせる」（手1）を採る。

## 直した記録

- 2026-09-06 `wiki/_attachments/kb-experience-reproducibility/design-system/` を新設し、
  `figure-zoom.js` を含めて置いた。見え方の変化は「図を押すと拡大できる」だけ。
- 2026-09-06 説明ページを `wiki/_attachments/llm-harness-parity/` から
  `wiki/_attachments/kb-experience-reproducibility/` へ移した。成果物Inbox の古い申告
  `i0906a35` は処理済みにして、移動後のパスで申告し直した。

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`
- 説明ページ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260906-kb-experience-reproducibility.html`
- 分岐を数える監査: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/harness_parity_check.py`
- 引き算側の記録（差の禁止）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- 移植の経緯: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`

## 実装への申し送り

**まだ実装段階ではない。** 手1〜手4 のどれにも実行の承認は出ていない。
方針が決まったらここへ完成条件を書く。

## 機械化した指摘

### 2026-09-06 分

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
| 同じ名前のスキルが3ハーネスで別の長さ・別の中身になる | している（238/77/77 行） | できる（`harness_parity_check.py` が既に FAIL 10件を出している） | **既存の `harness_parity_check.py` を関所に昇格**（いまは走らせたときだけ見る道具）。未決定・今回の承認待ち |
| `llm-wiki` スキルが `~/.agents/` と `~/.claude/` で二重化し、規約が別々を指す | している（8,953 / 9,642 バイト） | できる（同名スキルの実体が2つ以上あれば FAIL） | **同名スキルの実体数の検査**。未実装 |
| 方法の承認前に成果物へ触る | している（09-06 に2回） | できる（承認カードの記録より前の成果物書き込みを数える） | 未実装。いまは文章の申し送りのまま |
| 布の断面が処理で細る | した（−29%） | できる（処理の前後で断面を測る） | ヘレン案件側で対応済み（案B で断面一致）。汎用の検査にはしていない |

## 関連リンク

- [[llm-harness-parity]] — `wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`

## セッションメモ（子）

- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/sessions/20260906-experience-reproducibility.md`
