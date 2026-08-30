---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-30
brainstorm_status: active
scope:
  - /Users/takedayousuke/.claude
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/askuserquestion-misclick-guard/_index.md
  - /Users/takedayousuke/.claude/settings.json
background_paths:
  - /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md
  - /Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py
---

# 承認カードの誤クリック対策

## 武田さんの考え

> アスクユーザークエスチョンの承認カードが出た時、その選択の送信コマンドを Enter じゃないと
> 機能しないようにしたいんだけど可能？ 誤クリックで推論の方向がずれるのが不快

- 困りごとは「クリックが即送信されること」そのものではなく、**誤クリックで推論の方向がずれる**こと。

## 決まったこと

### 2026-08-30 武田さんの指示（A/B/C は承認せず）

> ならわかった。選択カードがこれでよろしいですか？っていう確認を必ずもう一枚増やすようにしてほしい。
> 承認待ちって複数で出せるじゃん。
> 対象はこの brainstorm のスキルだけで行います。それ以外での動作は現状禁止します。

- **確認カードを必ずもう1枚出す。** 選択したら、そのまま進まず「これでよろしいですか」を挟む。
- **適用範囲は brainstorm スキルの中だけ。** 他の作業・他のスキルでの承認カードには入れない
  （現状禁止）。

## まだ決まってないこと

- 確認カードの出し方が2通りあり、誤クリックへの効き目が違う（下の「確認カードの2通り」）。
- PostToolUse フックが `AskUserQuestion` で実際に発火するか（**未確認**。確認は実装側の第1歩）。

## 事実確認（2026-08-30・ファイルを直接読んだ）

1. **「クリック送信を無効化して Enter だけにする」設定は存在しない。**
   `~/.claude/keybindings.json` は未作成。そもそも keybindings.json が扱うのは
   **キーの割り当て**であって、カードのマウスクリックは対象外。クリックを殺す設定項目は無い。
2. 現行の `~/.claude/settings.json` に `AskUserQuestion` を対象にしたフックは**無い**
   （`AskUserQuestion` の文字列は brainstorm スキル側のファイルにしか出てこない）。
3. よって「送信手段を Enter に限定する」は**不可**。実現できるのは
   **誤クリックしても方向が確定しない（復唱して確認するまで私が動かない）**という形の担保。

## 候補（それぞれ失うもの付き）

- **A. PostToolUse フックで「復唱→確認まで進むな」を機械化**
  `AskUserQuestion` の直後に、私の文脈へ「選んだ内容を1行で復唱し、承認されるまで作業を進めるな」を
  自動で差し込む。誤クリックしても、次の私の発言は復唱だけなので、その場で言い直せる。
  失うもの: 誤クリックしていない時も **毎回1往復増える**。カードの手数が2倍になる。
- **B. 承認カードを使わず、番号付きの文章で選ばせる**
  クリック対象が消えるので誤クリックが原理的に起きない。
  失うもの: brainstorm の `guard-stop`（カード無しで会話を閉じるのを機械的に止める仕組み）が
  効かなくなる。**会話を勝手に閉じる事故が戻る。**
- **C. 選択肢の1番目を必ず「まだ決めない／説明して」にする**
  誤クリックの当たりどころを無害な肢にする。フックもスクリプトも不要。
  失うもの: 推奨案を1番目に置く現行の書き方が崩れ、毎回1クリック余分。
  そして**誤クリック自体は防げない**（2番目以降を誤爆したら同じこと）。

## 確認カードの2通り（2026-08-30・要決定）

`AskUserQuestion` は1回の呼び出しで最大4問を並べられる。ここから2通りの実装がある。

- **X. 同じカードの中に確認の質問を並べる（1回のやり取りで完結）**
  1問目「どれにするか」、2問目「上の選択でよろしいですか」を同時に表示する。
  やり取りは増えない。ただし2問とも同じ画面にあるので、**手が滑った流れでそのまま2問目も
  押してしまうと素通りする**。効き目は弱い。
- **Y. 選択が届いた次の応答で、確認カードを1枚出す（カード2枚）**
  「◯◯を選ばれました。これで進めます。よろしいですか」を別のカードとして出す。
  画面が一度切り替わり、選んだ内容が文章で目に入るので、**誤クリックに気づける**。
  そのぶん毎回1往復増える。

## 捨てた案と理由

- **A/B/C（復唱フック・番号選択・1番目を無害肢）→ いずれも不採用**（2026-08-30 武田さん）。
  代わりに「確認カードをもう1枚」で担保する。

- キーバインド設定でクリック送信を殺す案 → **不可能**。上の事実確認1のとおり、
  クリックはキー割り当ての対象外で、無効化する設定項目が存在しない。

## 直した記録

（なし）

## 再開の入口（実パス）

- このメモ: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/askuserquestion-misclick-guard/_index.md
- フック登録先: /Users/takedayousuke/.claude/settings.json
- brainstorm 規則本体: /Users/takedayousuke/.claude/skills/brainstorm/SKILL.md

## 実装への申し送り

（承認後に書く）

## セッションメモ（子）

（なし）

## 関連リンク

- brainstorm スキル正本: wiki/builds/brainstorm-skill.md
