---
type: analysis
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-07
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
  - /Users/takedayousuke/.agents
  - /Users/takedayousuke/.claude/skills/brainstorm
  - /Users/takedayousuke/.codex/skills/brainstorm
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260907-codex-hook-firing-check.html
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
（llm-harness-parity メモ＝ `wiki/analyses/brainstorm/llm-harness-parity/_index.md`、状態 active）にある。こちらは
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

### 2026-09-06 カード回答3（探りも承認せず・記憶から2つの事実）

> 承認しない。
> ###
> codexは今トークンを使い切って、次の回復が、半日後。
> opencodeは今museが無料で使えるから使ってるんだけど、今リミットきて、まだ期間が続いてて回復するなら半日後になる。
> ###
> 俺の記憶では、codexで最初に実装したbrainstormは、監査の無限ループに入って、心配になった。
> あまりにもclaudeと挙動が違って。
> Opencodeはvscodeの総合ターミナルから呼び出してつかてったんだけど、推論過程のテキストの表示が埋もれて、
> 俺から確認できなかった。llmは回答自体は生成してたけど、Ui上で俺が確認できるのは承認カードの中だけだった。
> 意味わかりますかね？
> Claudeでは/htmlのスキルで俺がレビューするときの回答の認知負荷を下げてるけど、
> それがopencodeではui的に実現しなかったし、そもそも承認カード以外の文脈がないから選択のしようがなかった。
> ちなみにuiでは見れないけど、会話中断して、html作ってたやつ出させると、
> 確かにそのセッションに関するhtmlを作ってたからuiで埋もれてるんだと感じた。

- **探りは実行しない。** Codex・opencode とも利用量を使い切っており、回復は半日後。
- この2つの記憶が、探りより強い手がかりになった。以下の実測4はすべて、記憶を頼りに
  コードと記録済みの実測資料を読んで確かめたもの。**新しくトークンは使っていない。**

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

- `~/.codex/skills/brainstorm/scripts/` の下に、状態のフォルダ（`lite-state`）も
  イベントの記録（`lite-events.jsonl`）も **存在しなかった**
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

## 2026-09-06 実測4：記憶の2件を、コードで裏取りした

### 1. 無限ループには構造上の原因があり、いまも残っている

会話の終わりで止める仕組みには、**歯止め**が要る。止めた結果もう一度終わろうとしたときに、
また止めると永久に終われないからだ。Claude の側はこれを持っている。

| 実装 | 歯止め（`stop_hook_active` を読む箇所） |
|---|---|
| `~/.claude/skills/brainstorm/brainstorm_guard.py` | **3 か所** |
| `tools/deliverable_path_guard.py` | **1 か所** |
| `~/.codex/skills/brainstorm/scripts/codex_adapter.py` | **0** |
| `.opencode/scripts/muse_brainstorm_check.py` | **0** |

> [!warning] この表の読み方を 2026-09-06 に訂正した
> `0` が2つ並ぶが、**穴なのは Codex だけ**。opencode はそもそも会話の終わりで押し返せず、
> Stop 型の関所を持っていない（実測2）。止めているのはカード（`question` 道具）を出す瞬間で、
> そこは道具の呼び出しが1回断られるだけなので、終われなくなる形にはならない。
> `skill-gate.js` には行き止まり避けが既に書かれている
> （「初回カードは方向確認のため成果物ゼロを許す」「止めたカードで目印を進めない」）。
> **`stop_hook_active` は opencode には当てはまらない。直す対象は Codex の1本。**

**そして Codex 側にその情報が来ていないわけではない。** 実測資料
`tools/context_harness/evidence/openai-hooks.md` の13行目に、実 Codex セッションで採取した結果として
「`Stop` は `turn_id`、`stop_hook_active`、`last_assistant_message` を受け取る」と記録されている。
**受け取っているのに読んでいない。** 環境の制限ではなく、実装の抜け。

さらに `codex_adapter.py` の `main()` は、**内部で例外が起きたときも Stop を止める**
（`BS_INTERNAL:<例外名>`）。ここにも抜け道が無いので、一度壊れると終われない。

**これは旧版だけの話ではない。** 上の表は 09-01 に入れ替えた現行の小さい版を数えた値。
武田さんが心配された形は、いまも同じ構造で残っている。

### 2. これが手1 の本当の根拠（私が最初に挙げた「行数」ではない）

歯止めは、共有できる85%のほうに属する判定の一部。**なのに3つの実装のうち1つにしかない。**
つまり手1 の意味は「コードが減る」ではなく、
**安全のための仕掛けが、書いた1つの実装の外へ広がらないこと**の解消にある。
`stop_hook_active` を読む処理は Claude 側で 3 か所あって、他の2実装には0か所。
同じことは今後の検査でも起きる。

### 3. opencode の「埋もれる」は、検査が測る対象を外している

武田さんの観察: 回答は生成されているのに、画面で読めるのは承認カードの中だけ。
中断して出させたら HTML はちゃんと作られていた。**生成の問題ではなく、表示の問題。**

ここで検査5（カードの外の本文が短いと止める）の作りを見ると、問題が分かる。

- 検査5 が見ているのは **本文が書かれたか**。
- 見ていないのは **本文が読めたか**。
- opencode + VSCode の統合ターミナルでは、本文は書かれるが埋もれる。
  **つまり検査5 は合格するのに、武田さんは何も読めない。**

検査5 は Claude の画面（本文が端末にそのまま出る）を前提に作られている。
**同じ検査を他へ持って行っても、測っている対象がその画面に合っていない。**

### 4. 表示ルールが、武田さんの居た画面と違う画面を前提にしていた

`.opencode/instructions/display.md` の第2節は、こう始まる。

> 前提：この会話はデスクトップアプリで見ている。回答本文はMarkdown描画される。

一方、武田さんが使っていたのは **VSCode の統合ターミナルから呼んだ opencode**（TUI）。
描画の仕方が別もの。**表示ルールは、武田さんが居なかったほうの画面に合わせて作られていた。**
`opencode-display-audit` の「まだ決まってないこと」にある
「質問カードの選択肢内にパスを書く必要が出た場合の書式（現状は短名で回避）」は、
この食い違いの現れ。回避していたものが、本体の問題だった。

### 5. ここから出る設計の結論

**検査は3つとも同じにする。説明をどこへ置くかは画面ごとに変える。**

| | Claude Code | opencode（VSCode の統合ターミナル） |
|---|---|---|
| 本文が読める場所 | 端末にそのまま出る | 埋もれる |
| カードの役割 | 選択肢だけ。本文はカードの外 | **唯一読める面。要約もここに要る** |
| 検査5 の測り方 | 本文の長さ（いまのまま） | 本文の長さでは意味がない。**カードの中身**を見る必要がある |

これは「サービスごとにふるまいを変える」ことではない。**ふるまい（何を止めるか）は同じで、
説明の置き場所だけが画面に合わせて変わる。**武田さんの「エージェントは1単位」とは衝突しない。

## 2026-09-07 実測5：config.toml の登録漏れの調査（読み取りのみ・設定は触っていない）

### 0. まず、私の前の報告が誤りだった

> [!warning] 訂正
> 実測3-4 で「Codex は 09-01 以降この保管庫で動いた記録が無い」と書いた。**誤り。**
> Codex のセッション記録（`~/.codex/sessions` と `~/.codex/archived_sessions` の 403 件）を
> 作業ディレクトリごとに数えたところ、**08-28 以降この保管庫で 41 回動いていた**。
> 小さい版へ入れ替えた 09-01 09:31 より後だけでも **9 回**、最後は 09-02 02:55。
> 無いのは「Codex を使った記録」ではなく、**「フックが発火した記録」**。区別を誤っていた。

### 1. それでも状態ファイルは1件も作られていない

小さい版へ入れ替えた後の 9 セッションで、`lite-state/` も `lite-events.jsonl` も作られていない。
**フックが実際に呼ばれていない。** これは「使っていないから」ではない。

> [!warning] 2026-09-07 訂正（実測6）
> 最後の2文は**誤り**。`lite-state/` は「フックが呼ばれたら出来る」ものではなく、
> **入力が `$brainstorm` / `/brainstorm` で始まったときだけ**出来る（`codex_adapter.py` の
> `user_prompt()` が唯一の作成点。`stop()` は状態が無ければ何も書かずに 0 を返す）。
> そして Codex のセッション記録 403 件を全部当たったところ、**起動語で始まる入力の最後は
> 2026-09-01 01:00** で、小さい版へ入れ替えた 09-01 09:31 より**前**。入れ替え後に
> brainstorm は Codex で一度も呼ばれていない。状態ファイルが無いのは当然で、
> ここから「フックが呼ばれていない」は導けなかった。詳細は下の実測6。

### 2. 信頼済みの登録に、はっきりした欠けがある

`~/.codex/config.toml` の `[hooks.state]` に登録されている `hooks.json` 由来のキーを全部照合した。

| フック | キー | 登録 |
|---|---|---|
| `context_harness.py`（各イベント） | `*:0:0` | あり |
| `codex_adapter.py`（brainstorm） | `stop:0:1` ほか | **あり** |
| `deliverable_path_guard.py` | `stop:1:0` | **無し** |
| `prose_guard.py`（`apply_patch`） | `pre_tool_use:1:0` | **無し** |

- 登録されているのは **グループ番号 0 のものだけ**。1 は1件も無い。
- 逆に、`config.toml` にあって `hooks.json` に無い古い登録は **0 件**。
- `config.toml` の更新は 09-05 15:55、`hooks.json` は 09-05 19:11。**設定のほうが古い。**

### 3. ただし、これだけでは説明が付かない

brainstorm の `stop:0:1` は **登録がある**のに、9 セッションで1件も発火していない。
つまり **信頼済みの登録の有無だけでは、発火しない理由を説明できない。**
外から確かめられる材料はここで尽きる。

### 4. 保管庫側の関所2本は、実は一度も Codex で動いていない

`tools/logs/prose-guard.log` の Codex 由来（`apply_patch`）の行は 3 件だけで、
すべて 2026-08-31 23:04:14 の同一秒で、対象として記録されているのは
当時の確認用の仮ファイル名（`_probe.html`。いまは存在しない）。
**配線の確認で流した合成の入力であって、実運用ではない。**

> [!warning] 実測1 の言い方も訂正する
> 「保管庫に置いた検査は Claude と Codex が同じ1ファイルを呼んでいて分岐が無い」と書いたが、
> **正しくは「同じ1ファイルを呼ぶように書いてある」**。Codex 側で実際に動いたのを見たことは一度も無い。
> 分岐が無いのは設定の話で、ふるまいの話ではなかった。

### 5. 歯止めは「無かった」のではなく「失われた」

09-01 より前の重い版のコードが、当時のセッション記録の中に残っている。そこには

```
reentry=bool(data.get("stop_hook_active"))
```

があり、`tests/test_parent_selection_fault.py` という専用の試験も存在した
（`stop_hook_active=retry` を渡す形）。**重い版は `stop_hook_active` を読んでいた。**
09-01 の小さい版への書き直しで、それが落ちた。
**今日入れた歯止めは、新機能ではなく、失われた性質の復旧。**

さらに重い版には `BS_CARD_PREFLIGHT_MISSING` という技術的停止があり、その再開手順の文言は
「PreToolUseの信頼状態と実発火を確認し、明示再開後に親選択の事前検査を再試行する」。
**Codex 側は当時から「フックが信頼されず発火しないかもしれない」ことを知っていた。**

### 6. 信頼を与える方法は、外から自動でできない

- `codex doctor` を走らせたが、フックの信頼状態は報告項目に無い（22 項目を確認）。
- `codex` に `hooks` サブコマンドは無い。
- 一次資料（`tools/context_harness/evidence/openai-hooks.md`）には
  「CLIでは `/hooks` から確認・trust する」とある。**対話中の画面での操作。**

**つまりここから先は、武田さんの手が要る。** 私が自動でできる範囲は尽きた。

## 2026-09-07 実測6：手順①の前提が誤っていた（読み取りのみ・設定は触っていない）

半日後の手順を実行する前に、手順そのものが成り立つかを先に確かめた。**成り立っていなかった。**
以下はすべて 2026-09-07 15:00 前後に実ファイル・実データベース・実バイナリを読んだ結果。

### 1. `lite-state` は「フックが動いた印」ではない

`~/.codex/skills/brainstorm/scripts/codex_adapter.py` を読んだ。状態フォルダを作る場所は
**1か所だけ**で、`user_prompt()` の中にある。

- `user_prompt()` は、入力が `INVOKE_RE`（`/brainstorm` `$brainstorm`
  `[$brainstorm](...)` のいずれかで**始まる**）に当たらず、かつ状態がまだ無いときは
  `return 0` で何も書かずに終わる。
- `stop()` も `session_start()` も `session_end()` も、状態が無ければ何も書かずに 0 を返す。
  記録用の `event()` すら、状態がある場合しか呼ばれない。

つまり **「Codex を開いて何でもよいので1行打つ」では、フックが完璧に動いていても
`lite-state/` は出来ない。** 手順①の見分けは、そのままでは使えない。

### 2. 入れ替え後、brainstorm は Codex で一度も呼ばれていない

`~/.codex/sessions` と `~/.codex/archived_sessions` の記録を全部（`*.jsonl`）走査し、
役割が `user` の入力テキストを `INVOKE_RE` と同じ正規表現で照合した。

- 起動語で**始まる**入力は全期間で **11 件**。
- その最後は **2026-09-01 01:00**。小さい版へ入れ替えた **09-01 09:31 より前**。
- 入れ替え後の 9 セッションには **0 件**。

**状態ファイルが無いのは当然だった。** 実測5-1 の「フックが実際に呼ばれていない」は、
この事実を確かめずに書いた推測で、成り立たない。

### 3. Codex のフックは、実際には発火している

`~/.agents/context-harness/state/tasks/` の記録 196 件を全部読み、イベントを数えた。

| 製品 | SessionStart | UserPromptSubmit | Stop | SessionEnd |
|---|---|---|---|---|
| codex-desktop | 196 | 235 | **182** | 136 |
| codex-cli | 28 | 25 | **20** | 21 |
| claude-code | 59 | 19 | 3 | 58 |

- codex-desktop の **Stop はこの保管庫で 2026-09-01 22:22（JST）に発火**している。
  これは小さい版へ入れ替えた 09-01 09:31 より**後**。
- `context_harness.py` は `hooks.json` の Stop グループの **0 番目**、
  `codex_adapter.py` は同じグループの **1 番目**。**同じ Stop イベントで並んでいる。**

**したがって「Codex では Stop フックが動かない」も成り立たない。** 動いている。
0 番目が動いて 1 番目だけが動かない証拠は、いまのところ1件も無い。

### 4. 一方、グループ1（`deliverable_path_guard` と `prose_guard`）は本当に動いていない

- `~/.codex/config.toml` の `[hooks.state]` を全件照合し直した。登録は
  **すべてグループ番号 0**。グループ 1 は依然 **0 件**。
- `tools/logs/prose-guard.log` の `apply_patch` 由来は **3 件のみ**、すべて
  2026-08-31 23:04:14 の配線確認。実運用は 0。

**実測5-2 のこの部分は、そのまま正しい。**

### 5. 武田さんが使っている面は、CLI ではなく VS Code 拡張

`~/.codex/state_5.sqlite` の `threads` を数えた（2026-08-01 以降・サブエージェント除く）。

| 面（`source`） | この保管庫での件数 | 最後 |
|---|---|---|
| `vscode` | **69** | 2026-09-02 03:04 |
| `exec` | 19 | 2026-08-14 21:57 |
| `cli` | 3 | 2026-09-01 00:41 |

そして `/hooks` の画面は、Codex のバイナリの中で
`tui/src/bottom_pane/hooks_browser_view.rs` という **TUI（端末の対話画面）側**に置かれている。
**VS Code 拡張から `/hooks` を開ける確証は無い**（バイナリ内の文字列からの判断で、画面では未確認）。

手順②「Codex の対話画面で `/hooks` と打つ」は、**端末で `codex` を起動する必要がある**。
普段の面のままでは実行できない可能性が高い。

### 6. 09-02 以降、この保管庫で Codex は動いていない

`threads` に 2026-09-02 03:04 より後のスレッドは無い。`config.toml` の更新時刻が
09-07 14:56 になっているのは ChatGPT.app が起動中だからで、**信頼登録は増えていない**
（グループ1は 0 件のまま）。

### 7. ここから出る結論

- 手順①は **`$brainstorm` で始まる1行**に直せば成立する。それ以外の入力では判定できない。
- 手順②は **端末の `codex`**（VS Code 拡張ではなく）で行う必要がある。
- 手順③（歯止めの実機確認）は、①を直せばそのまま使える。
- **「Codex ではフックが動かない」という見立ては、いったん取り下げる。** 動いていないと
  確実に言えるのはグループ1の2本だけで、原因は信頼登録が無いこと。

## 2026-09-07 半日後にまとめてやること（手順・そのまま実行できる形）

> [!warning] 2026-09-07 15:00 この節の①と②は、そのままでは実行できない
> 実行前に前提を確かめたところ、**①の見分けが成り立たず、②の画面が普段の面に無い**ことが
> 分かった（上の実測6）。直した手順は下の「2026-09-07 直した手順（未承認）」にある。
> 以下は当時のまま残す。

武田さんの選択: **信頼の確認と、歯止めの実機確認を1回でやる**（利用量の回復後）。
順番が大事。**①を先にやらないと②の結果が読めない。**

### ① 発火しているかを、先に見分ける（Codex を1回だけ動かす）

Codex をこの保管庫で開き、何でもよいので1行打って閉じる。そのあと私が次を読む。

- `~/.codex/skills/brainstorm/scripts/` の下に `lite-state`（フォルダ）が出来ているか
  （出来ていれば brainstorm のフックは発火している）
- `tools/logs/prose-guard.log` に新しい行が増えたか
  （増えていれば `prose_guard.py` は発火している。増えなければ未信頼の疑いが強まる）

**この2つで、発火しているフックとしていないフックが分かれる。**

### ② 信頼状態を画面で見る（武田さんの操作・モデルは呼ばない）

Codex の対話画面で `/hooks` と打ち、一覧を見せていただく。見たいのは次の4行の状態。

- `context_harness.py`（各イベント）
- `codex_adapter.py`（brainstorm）
- `deliverable_path_guard.py`（Stop・2つ目のグループ）
- `prose_guard.py`（PreToolUse・2つ目のグループ・matcher は `apply_patch`）

**信頼を与える操作は武田さんが行う。** 安全に関わる承認なので、私は代行しない。

### ③ 歯止めの実機確認

①で brainstorm のフックが発火していることが確認できたら、`$brainstorm` を1回使い、
承認カードの前に本文を書かずに閉じようとする。止まったあと、もう一度閉じようとして
**2回目は止まらない**ことを見る。`lite-events.jsonl` に `stop_brake` の行が残る。

**①で発火していなければ、③は成立しない。** その場合は②の結果を先に片付ける。

### 私がその場で読むもの

いま実在するのは次の2つ。

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/logs/prose-guard.log
/Users/takedayousuke/.codex/config.toml
```

残る2つは **フックが発火したときに初めて出来るもの**なので、いまは存在しない。
置き場所は次のフォルダで、名前は `lite-state`（フォルダ）と `lite-events.jsonl`（ファイル）。

```
/Users/takedayousuke/.codex/skills/brainstorm/scripts/
```

**この2つが出来ているかどうかが、①の見分けそのもの。**

## 2026-09-07 直した手順（未承認・実測6を反映）

変えたのは①の入力と、②を行う場所だけ。順番と狙いは変えていない。

### ①' 発火しているかを見分ける（Codex を1回だけ動かす）

**この保管庫で Codex を開き、`$brainstorm 発火確認` とだけ打って、答えが返ったら閉じる。**
何でもよい1行では判定できない（実測6-1）。起動語で始まることが条件。そのあと私が次を読む。

置き場所は次のフォルダ。**中の2つはフックが発火したときに初めて出来るので、いまは実在しない。**

```
/Users/takedayousuke/.codex/skills/brainstorm/scripts/
```

- `lite-state`（フォルダ）が出来ているか
- `lite-events.jsonl`（ファイル）に `user_prompt` の行があるか

**出来ていれば、brainstorm の Stop フックは信頼されて発火している。**
出来ていなければ、そこで初めて「登録はあるのに発火しない」が確定する。

### ②' 信頼状態を画面で見る（端末の `codex`・武田さんの操作）

**VS Code 拡張ではなく、端末で `codex` を起動して `/hooks` を打つ。**
`/hooks` の画面は TUI 側にあり、拡張から開ける確証が無い（実測6-5）。見たいのは次の4行。

- `context_harness.py`（各イベント）
- `codex_adapter.py`（brainstorm）
- `deliverable_path_guard.py`（Stop・2つ目のグループ）
- `prose_guard.py`（PreToolUse・2つ目のグループ・matcher は `apply_patch`）

**信頼を与える操作は武田さんが行う。** 安全に関わる承認なので、私は代行しない。

### ③ 歯止めの実機確認（変更なし）

①' で発火が確認できたら、`$brainstorm` を1回使い、承認カードの前に本文を書かずに閉じようとする。
止まったあと、もう一度閉じようとして **2回目は止まらない**ことを見る。
`lite-events.jsonl` に `stop_brake` の行が残る。

### 私がその場で読むもの（変更なし）

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/logs/prose-guard.log
/Users/takedayousuke/.codex/config.toml
/Users/takedayousuke/.codex/skills/brainstorm/scripts/
```

最後のフォルダの中を見て、`lite-state`（フォルダ）と `lite-events.jsonl`（ファイル）の有無を判定する。
この2つは発火して初めて出来るものなので、いまは実在しない。

## 決まったこと

- 2026-09-06 読み取りの承認。**効いていたのは保管庫側の台帳と検査で、分岐しているのは
  各サービスが自分のフォルダに持つ判定のほう。**この枠で続ける。
- 2026-09-06 記録先はこの新しい親メモ。`llm-harness-parity` には1行の案内だけを残した。

### 2026-09-07 直した手順の承認：①' だけ先に

カードの回答は「①' だけ先に（推奨）」＋「はい、この選択でよい」。

- **実施するのは ①' のみ。** この保管庫で Codex を開き、`$brainstorm 発火確認` とだけ打って閉じる。
  そのあと私が `lite-state/` と `lite-events.jsonl` を読んで、発火の有無を判定する。
- **②'（端末で `codex` を起動して `/hooks`）は今回は行わない。** 失うものとして提示済み:
  関所2本（`deliverable_path_guard.py` と `prose_guard.py`）の信頼登録の件は未着手のまま残る。
- ③（歯止めの実機確認）は ①' の結果を見てから。
- **武田さんの操作待ち。** この時点では実行も中断もしていない（状態: 入力待ち）。

### 2026-09-06 実行の承認と、実施の完了（自動試験まで）

- 武田さんの条件「実装に入る前に、今の状態に戻せるようにバックアップが効く状態か確認してから」を先に実施。
  `~/.codex/skills/brainstorm/` をフォルダ丸ごと `~/.codex/skill-backups/brainstorm-pre-brake-20260906`
  へ退避し、`diff -r` で一致を確認した。
- 実装は計画書第2版のとおり。`codex_adapter.py` の2か所のみ変更、試験は 11 件から 14 件へ。
- **壊し試験 3/3。** 歯止めを丸ごと削除／`main()` 側だけ削除／`errors` の直前へ移動（初版が許す欠陥版）の
  3通りで、それぞれ対応する試験だけが落ちた。
- 完成条件: `BRAKE-MISSING` から **`BRAKE-OK`**、`TESTS-11-OK` から **`TESTS-14-OK`**。
- **実機確認済みではない。** Codex の利用量の回復は半日後。
- 計画書（第3版・実施結果つき）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/kb-agent-parity-stop-brake-plan-20260906.md`

### 2026-09-06 独立レビューで見つかった、私の計画の欠陥

サブエージェント（読み取りのみ、私の結論は伝えず事実照合のみ指示）が6件を指摘。すべて反映した。

| 指摘 | 内容 |
|---|---|
| **最重要** | 初版の位置指定では `phase == "stopped"` の block 経路が歯止めの外に残り、**初版の試験と完成条件はその欠陥版を合格させた** |
| 完成条件 | `unittest` は 11 件でも 13 件でも OK を返すので、試験を1件も足さなくても合格した |
| 試験B | 例外を起こす場所を指定しないと、`main()` 側の歯止めが無くても合格する |
| `grep -c` | 行数を数えるだけで、2行とも同じ関数の中にあっても合格する |
| 行数の内訳 | 「共有候補は約85%」は分類が緩く再現できない。**撤回** |
| 能力の表 | 「5つのうち4つは3サービスとも止められる」は誤り。止められるのは2つ |

**新しく見つかった事実**: `~/.codex/config.toml` の信頼済み登録はすべてグループ番号 0 で、
`hooks.json` の2つ目のグループ（`deliverable_path_guard.py` と `prose_guard.py`）の登録が1件も無い。
`config.toml` の更新時刻は `hooks.json` より古い。**「後から足したぶんが未承認のまま」**という説明が立ち、
`prose-guard.log` の Codex 由来が 09-01 以降0件であることと整合する。
一方、今回の対象である brainstorm の Stop フックは登録が残っており、定義も3世代変わっていない。

### 2026-09-06 方針の承認：歯止めを先に入れる

カードの回答は「歯止めを先に入れる（推奨）」＋「はい、この選択でよい」。
**これは方針の承認であり、実行の承認ではない。** 実行は別のカードで取る。
失うものとして提示済み: 3実装に分かれたまま直すので、分岐が1件増える。次の検査でも同じことが起きる。

## まだ決まってないこと

- **手1 に進むかどうか。判断の前に、各サービスが実際にフックを発火させているかの確認が要る**
  （2026-09-06 カード回答2）。いま確かなのは Claude だけ。
- **1ターンの探りは実行しない**（2026-09-06 カード回答3。Codex・opencode とも利用量を使い切り、
  回復は半日後）。実測4 でコードから原因が取れたため、探りの優先度は下がった。
- （決着）歯止めは単独で先に入れた。対象は Codex の1本のみ。
- （手順は用意済み・実施待ち）半日後に①発火の見分け ②`/hooks` の確認 ③歯止めの実機確認を1回で行う。
  **2026-09-07 15:00 更新: ①②は前提が崩れたので直した（上の「直した手順」）。直した手順の承認が未取得。**
- **①' と ②' は Codex を起動する操作で、私からは実行できない。** どちらを武田さんが行うか未決。
- （調査は完了）登録の欠けは確定したが、**それだけでは発火しない理由を説明できない**。
  信頼を与えるには `/hooks` を対話画面で操作する必要があり、**武田さんの手が要る**。
  どのやり方を採るかが未決（実測5-6）。
- 検査5 を画面ごとに測り分ける形（opencode ではカードの中身を見る）を採るかどうか。
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

- 2026-09-07 実測5-1 の「フックが実際に呼ばれていない」に訂正の注記を付けた。実ファイル
  （`codex_adapter.py`）と実記録（Codex セッション 403 件）を読んだ結果と食い違っていたため。
  見え方の変化は「半日後に実行する手順の①が変わる」こと。戻すには注記と実測6の節を削除する。

- 2026-09-06 `wiki/_attachments/kb-experience-reproducibility/design-system/` を新設し、
  `figure-zoom.js` を含めて置いた。見え方の変化は「図を押すと拡大できる」だけ。
- 2026-09-06 説明ページを `wiki/_attachments/llm-harness-parity/` から
  `wiki/_attachments/kb-experience-reproducibility/` へ移した。成果物Inbox の古い申告
  `i0906a35` は処理済みにして、移動後のパスで申告し直した。
- 2026-09-07 到達性監査 H1 の指摘対応（Kimi Code・武田さんの明示依頼）。`[[llm-harness-parity]]` は
  リンク先がフォルダ型メモ（`_index.md`）で slug が解決しないため、実パス併記済みの
  素の表記へ変更（2箇所）。Obsidian でも未解決リンクだったものが通常の文字になるだけで、
  実パスによる誘導は変わらない。戻すには `[[llm-harness-parity]]` に書き戻す。

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`
- 説明ページ（最新・2026-09-07）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260907-codex-hook-firing-check.html`
- 説明ページ（2026-09-06）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260906-kb-experience-reproducibility.html`
- 分岐を数える監査: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/harness_parity_check.py`
- 計画書（歯止め・実施結果つき）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/kb-agent-parity-stop-brake-plan-20260906.md`
- **次の会話が最初に読むのは「2026-09-07 半日後にまとめてやること」の節。**
- 引き算側の記録（差の禁止）: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- 移植の経緯: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`

## 実装への申し送り

### 2026-09-06 歯止めの追加（方針は承認済み・実行は未承認）

**完成条件**

Codex の adapter が、会話の終わりを一度止めたあとの再呼び出しでは止め返さないこと。
内部の失敗で止めるときも同じ抜け道を持つこと。既存の試験 11 件が引き続き通ること。

**変更するファイル（1本だけ）**

`/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py`

- `stop()` の冒頭で `stop_hook_active` が真なら、判定へ進まず素通りさせる（記録は残す）。
- `main()` の例外の枝（`BS_INTERNAL:`）も同じ条件で素通りさせる。
- 変更前を `codex_adapter.py.bak-20260906` として同じ場所に残す。
- 試験を2件足す（一度止めたあとは止めない／内部の失敗でも一度止めたあとは止めない）。

**絶対にやってはいけないこと**

- Claude 側（`~/.claude/skills/brainstorm/`）を触ること。今回の対象ではない。
- opencode 側（`.opencode/`）を触ること。**opencode は Stop 型の関所を持たないので、
  `stop_hook_active` は当てはまらない。**「0 が2つあるから2本直す」は誤り。
- `~/.codex/hooks.json` と `~/.codex/config.toml` を触ること。信頼済みの印が変わると、
  かえって発火しなくなる恐れがある（印の作り方は外から確かめられていない）。
- 保管庫の `tools/` を触ること。
- 歯止めを入れるついでに、止める条件そのものを緩めること。**合格線は動かさない。**

**捨てた案と理由**

- 手1（判定の1本化）の中でまとめて直す … 穴が塞がるまでが長い。今回は先に塞ぐ。
- 探りを流して実機で確かめてから直す … 利用量が半日戻らない。原因はコードから取れている。

```done-when
path: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
run: grep -c stop_hook_active /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py ==> 2
run: python3 -c "import subprocess,sys; r=subprocess.run([sys.executable,'-m','unittest','tests.test_adapter'],cwd='/Users/takedayousuke/.codex/skills/brainstorm',capture_output=True,text=True); print('TESTS-OK' if r.returncode==0 else 'TESTS-FAIL')" ==> TESTS-OK
```

### 終わったら次に取る承認

**歯止めは 2026-09-06 に完了（自動試験まで）。** 次に取る承認は2つのどちらか。

1. `~/.codex/config.toml` の登録の非対称を調べるか（`prose_guard.py` と
   `deliverable_path_guard.py` が Codex で発火していない原因の可能性）。
2. 検査5 を画面ごとに測り分ける（opencode ではカードの中身を見る）。

手1（判定の1本化）はその後。**手1 に進むなら、行数の根拠は撤回済みなので、
「安全の仕掛けが1つの実装の外へ広がらない」ほうを根拠にする。**

## 機械化した指摘

### 2026-09-06 分

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
| 同じ名前のスキルが3ハーネスで別の長さ・別の中身になる | している（238/77/77 行） | できる（`harness_parity_check.py` が既に FAIL 10件を出している） | **既存の `harness_parity_check.py` を関所に昇格**（いまは走らせたときだけ見る道具）。未決定・今回の承認待ち |
| `llm-wiki` スキルが `~/.agents/` と `~/.claude/` で二重化し、規約が別々を指す | している（8,953 / 9,642 バイト） | できる（同名スキルの実体が2つ以上あれば FAIL） | **同名スキルの実体数の検査**。未実装 |
| 方法の承認前に成果物へ触る | している（09-06 に2回） | できる（承認カードの記録より前の成果物書き込みを数える） | 未実装。いまは文章の申し送りのまま |
| 布の断面が処理で細る | した（−29%） | できる（処理の前後で断面を測る） | ヘレン案件側で対応済み（案B で断面一致）。汎用の検査にはしていない |

## 関連リンク

- llm-harness-parity メモ — `wiki/analyses/brainstorm/llm-harness-parity/_index.md`
- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`

## セッションメモ（子）

- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/sessions/20260906-experience-reproducibility.md`
