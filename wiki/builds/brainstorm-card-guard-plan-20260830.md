---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-08-30
---

# 承認カード検査（G1）と指摘の棚卸し工程（G4）実装計画

## 0. この計画の位置づけ

- ブレストの正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/askuserquestion-misclick-guard/_index.md`
- 改修対象:
  - `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`
  - `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`
- **対象は Claude 側の brainstorm のみ。** Codex 側（`/Users/takedayousuke/.codex/skills/brainstorm/`）は
  別実体で自動同期されない。今回は触らない。
- これは計画であり、この会話では実装しない。

## 1. 解こうとしている問題

承認カードの選択肢をマウスで誤クリックすると、そのまま推論の方向が確定してしまう。
「クリック送信を無効化して Enter だけにする」設定は存在しない（2026-08-30 実測。
`~/.claude/keybindings.json` は未作成で、そもそもキー割り当てはクリックを対象にしない）。

したがって解き方は「送信手段を縛る」ではなく **「誤クリックしても、その場で言い直せる形にする」**。
方式は X（同じカードの中に確認の質問を並べる）を採用済み。

さらに武田さんの条件として、これを **私の心がけ（文言ルール）にせず、機械が担保する**こと。

## 2. 実装範囲（承認済み）

- **G1**: 確認質問の無い承認カードを機械的に拒否する。
- **G4**: 武田さんの指摘を棚卸しして検査へ変換する工程を、機械が確認する形で仕組みにする。
- G2（申し送りの中身検査）・G3（選択肢に「失うもの」があるかの検査）は**今回入れない**。
- 工程分割（Phase 化）・監査役 LLM の常設は**今回入れない**。

## 3. G1 の設計

### 3.1 発火場所（brainstorm 作動中だけに限る手段）

`SKILL.md` の frontmatter の `hooks` へ `PreToolUse` を1件追加する。

```yaml
  PreToolUse:
    - matcher: 'AskUserQuestion'
      hooks:
        - type: command
          command: 'python3 "$HOME/.claude/skills/brainstorm/brainstorm_guard.py" guard-card'
```

**スキル frontmatter の hooks は、そのスキルが読み込まれている間だけ有効**。既存の
`guard-write --lockdown`（PreToolUse: Write|Edit|NotebookEdit|Bash）と `guard-stop`（Stop）が
同じ場所に置かれており、同じ仕組みに乗る。よって
「brainstorm 以外では動作させない」という武田さんの条件が、設定の置き場所そのもので満たされる。
`~/.claude/settings.json` には**追加しない**（そちらへ書くと常時発火してしまう）。

### 3.2 判定（決定論的・LLM の意味判断を使わない）

`guard-card` は標準入力の hook payload から `tool_input.questions`（配列）を読む。

- **合格条件**: `questions` のうち少なくとも1問の `question` 文字列に、確認の印
  `（確認）` が含まれること。
- 印は固定文字列とし、正規表現の意味解釈や語彙判定は行わない。
  実装側で全角/半角の括弧ゆれを吸収してよいが、**吸収する表記の一覧をコード内に明記**すること。
- 不合格なら `deny()`（既存関数）で拒否し、理由に「確認の質問（`（確認）` を含む質問）を
  1問足して出し直してください」と、現在の質問文の一覧を短く添える。
- `questions` が読めない・payload が壊れている・例外が出た場合は **素通り（allow）**。
  既存スクリプトの方針（どんな失敗でも素通りに倒す）に合わせる。承認を止める副作用の方が重い。
- 判定の結果は既存の `log()` で `guard.log` へ1行残す（後から効いているか数えられるように）。

### 3.3 SKILL.md 側の書式規定

`## 3. 会話を勝手に閉じない` の節へ、カードの書式として次を追記する。

- 承認カードには、**選択の質問に加えて「（確認）上の選択でよろしいですか」の質問を必ず並べる**。
- 確認の質問の選択肢は「はい、この選択でよい」「いいえ、押し間違えた」の2つ。
- 「いいえ」が返ったら、**上の答えを破棄して何も進めず、同じ内容を聞き直す**。

## 4. G4 の設計

### 4.1 メモの書式

親メモに次の節を必須にする（`## 実装への申し送り` の後ろ）。

```markdown
## 機械化した指摘

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
```

- 該当が無い回は本文に `なし` と書けば足りる。**節そのものの欠落だけを止める。**
- 埋めるのは私（LLM）の作業。武田さんに棚卸しをさせない。

### 4.2 検査

`brainstorm_status` を `ready` へ上げようとしたときに検査する。既存の
`_guard_ready_promotion()` / `audit_memo()` の経路へ、`## 機械化した指摘` 節の
**存在チェックのみ**を1件足す（中身の良し悪しは判定しない）。

- FAIL の文言は既存の FAIL と同じ体裁に揃える。
- 既存メモ（`gf2-dusevnyj-bikini-to-helen` など、すでに `ready` のもの）を
  **後から書き換えない**。検査は `ready` へ**上げる操作**にのみ適用する。
  既存の ready メモを触らないことを、実装後に実際に確認すること。

## 5. 完成条件（実機で確認する）

1. brainstorm 作動中に、確認質問を入れずに `AskUserQuestion` を呼ぶと**拒否される**。
2. 確認質問（`（確認）`）を入れたカードは**通る**。
3. brainstorm を使っていない会話では、承認カードが**素通りする**（拒否が出ない）。
4. `## 機械化した指摘` 節が無いメモを `ready` へ上げようとすると**止まる**。節があれば通る。
5. 既存の `ready` メモが書き換わっていない。
6. 上記1〜4が `guard.log` に記録されている。
7. `python3 brainstorm_guard.py audit-handoff --selftest` が従来どおり通る。

## 6. 絶対にやってはいけないこと

- 文言ルールだけで担保して「できた」と報告すること。
- `~/.claude/settings.json` へ `AskUserQuestion` のフックを常駐させること
  （brainstorm 以外でも発火し、武田さんの「現状禁止」に反する）。
- Codex / Kimi / opencode 側のファイルを触ること。
- 既存の `guard-write` 封鎖・`guard-stop`・到達性監査を弱めること。
- G2・G3・工程分割・監査役 LLM を頼まれていないのに足すこと。
- 既存メモの本文を要約・分割すること。

## 7. 想定される失敗と、その扱い

- **`AskUserQuestion` に PreToolUse が発火しない可能性**。未確認。発火しなければ G1 は成立しない。
  実装の1歩目はこの確認とし、発火しなかった場合は**実装を止めて報告**する
  （別方式へ勝手に振り替えない）。
- **誤検知**（正しいカードが止まる）。素通りに倒す方針で軽減するが、起きたら
  印の判定と payload の形を `guard.log` で切り分ける。
- **手数が増える**。X はやり取りを増やさないが、選択肢を読む量は増える。
  使用感が悪ければ Y（次の応答で確認カードを別途1枚）へ切り替える。判断は運用後。
