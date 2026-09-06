---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-06
sources: []
---

# 保管庫のLLM体験を揃える — 第1歩「会話の終わりの歯止め」計画（2026-09-06）

作業ディレクトリ（相対パスの基準）:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

親メモ（正本）:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`

---

## 1. この計画の位置づけ

これは「保管庫のLLM体験をどのサービスでも同じに出す」という大きなテーマの **第1歩だけ** を扱う。
テーマ全体の手立ては4つあり（後述）、本計画はそのどれでもなく、**先に塞ぐべき穴1つ**を対象とする。

- 方針の承認: 2026-09-06 取得済み（「歯止めを先に入れる」）。
- 実行の承認: **未取得。** 本計画のレビュー後に取る。

## 2. 経緯（2026-09-06 の1会話ぶん）

### 2.1 出発点

武田さんの問い（原文）:

> このプロジェクトのllm体験がすごくいい。（中略）opus5の性能がすごいという話ではなく、
> ハルシネーションを軽減しつつ難しいタスクを段階的に処理できている。（中略）
> これは性能というよりも、俺の意図や指示の元であるコンテキストの粒度を上手くコントロールしたからだと思う。
> （中略）kbフォルダ内でサービスごとにエージェントの挙動が変わるのは不快なんだよね。
> claudeが無限に使えるわけじゃないから、ここがボトルネック。
> どうすればこのllm体験の再現性をkbフォルダで安定化できると思う？

### 2.2 測って分かったこと（時系列）

1. **効いていたものは保管庫側にあった。** ヘレン案件で効いた6件（穴の台帳・明言の逐語保存・
   合格線を原作から毎回計算・検査の壊し試験・目的の関所・1コマンドの監査）は、
   すべて保管庫の中の Python と JSON。Claude の中には無い。
2. **分岐しているのは各サービスが自分のフォルダに持つ判定。** 保管庫に置いた検査
   （`tools/prose_guard.py` / `tools/deliverable_path_guard.py` / 共有の context-harness）は
   Claude と Codex が同じ1ファイルを呼んでいて分岐が無い。一方 brainstorm の判定は3実装。
   `python3 .opencode/scripts/harness_parity_check.py check` は **FAIL・10件**。
3. **`/brainstorm` の本文が3種類。** Claude 238 行／Codex 77 行／opencode 66＋11 行。
   Codex 版には「巨大な監査台帳や、通常作業を横断する許可レジストリは作らない」と明記されている。
   つまり Codex は指示どおり小さいものを作っただけで、失敗ではない。
4. **技術的な障害はほぼ無い。** 要る能力5つのうち4つは3サービスとも止められる。
   欠けは opencode の「会話の終わりで止め返す」1つで、`event` フックが戻り値を持たないため。
   ただし `permission.ask` と `tool.execute.before` は出力を書き換えられるので止められる。
5. **手1（判定の1本化）の現実性。** `brainstorm_guard.py` の関数 2,134 行の内訳は
   判定 909（42.6%）／自己試験 894（41.9%）／収集・入出力 331（15.5%）。共有候補は約85%。
   ただし収集は **transcript を読む前提**で、Codex は自前の状態ファイル、opencode は
   流れるイベントから再構成しており、**情報源が3通り**。薄い変換層では済まない。
6. **Codex は 09-01 の入れ替え以降、この保管庫で動いた記録が無い。**
   `~/.codex/skills/brainstorm/scripts/` に `lite-state/` も `lite-events.jsonl` も存在しなかった
   （09-06 に直接起動して初めて生成。確認後に削除）。`tools/logs/prose-guard.log` の
   `apply_patch` 行も 08-31 が最後。**コード自体は手動起動で正常動作する。**

### 2.3 武田さんの記憶2件と、その裏取り

> codexで最初に実装したbrainstormは、監査の無限ループに入って、心配になった。

> Opencodeはvscodeの総合ターミナルから呼び出してつかてったんだけど、推論過程のテキストの表示が埋もれて、
> 俺から確認できなかった。（中略）Ui上で俺が確認できるのは承認カードの中だけだった。

裏取りの結果:

- **無限ループ**: 会話の終わりで止める仕組みには歯止め（`stop_hook_active` を読む処理）が要る。
  `~/.claude/skills/brainstorm/brainstorm_guard.py` に **3か所**、`tools/deliverable_path_guard.py` に
  **1か所**。`~/.codex/skills/brainstorm/scripts/codex_adapter.py` に **0**。
  情報が来ていないわけではなく、`tools/context_harness/evidence/openai-hooks.md` 13行目に
  「`Stop` は `turn_id`、`stop_hook_active`、`last_assistant_message` を受け取る」と記録がある。
  さらに `main()` の例外の枝（`BS_INTERNAL:`）にも抜け道が無い。
- **埋もれる件**: 検査5 は「本文が書かれたか」を見て「本文が読めたか」を見ていない。
  `.opencode/instructions/display.md` 第2節は「前提：この会話はデスクトップアプリで見ている」で、
  武田さんが居たのは VSCode の統合ターミナル。**別の画面を前提にルールが作られていた。**

### 2.4 私（メインエージェント）が今日訂正した自分の誤り

| 誤り | 訂正 |
|---|---|
| 「変換層は小さくて済む」を `prose_guard.py` 1本から一般化した | あれは「1回の書き込みの中身」、brainstorm は「会話1本ぶんの経過」。規模が違う |
| 「合格線を通したのは機械ではなく武田さんの目」 | 武田さんの言う合格箇所は胸を覆う布の一点で、紐は最初から未決着。範囲を取り違えた |
| 「Codex と opencode に歯止めが0か所（＝2本直す）」 | opencode は Stop 型の関所を持たないので対象外。**直すのは Codex の1本** |

---

## 3. 本計画の目的と非対象

### 3.1 目的

**Codex の `/brainstorm` が、会話の終わりで終われなくなる状態を作らないようにする。**

### 3.2 完成条件（機械で判定する）

1. 会話の終わりの合図に「すでに一度止めた」印が立っている再呼び出しでは、adapter が止め返さない。
2. 内部で予期しない失敗が起きた場合も、同じ印が立っていれば止め返さない。
3. 既存の単体試験 11 件が引き続き通る。
4. 追加の単体試験 2 件（上記1と2）が通る。

### 3.3 今回やらないこと（非対象）

- 手1（判定の1本化）。本計画は分岐を1件増やす方向であり、それは承知のうえで先に穴を塞ぐ。
- 検査5 を画面ごとに測り分けること。次の方針承認で扱う。
- opencode 側の変更。**Stop 型の関所が無いため `stop_hook_active` は当てはまらない。**
- Claude 側の変更。
- `~/.codex/hooks.json` と `~/.codex/config.toml` の変更。
  信頼済みの印（`trusted_hash`）の作り方が外から確かめられていないため、
  触るとかえって発火しなくなる恐れがある。
- 実会話での発火確認。Codex の利用量が回復するのは半日後。**本計画は自動試験までで止まる。**
- 止める条件そのものの緩和。**合格線は動かさない。**

---

## 4. 変更の仕様

### 4.1 対象ファイル（1本のみ）

```
/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
```

現状 257 行。関連する既存の実装は次の2か所。

- `stop(data)` … 会話の終わりの判定。`errors` を組み立て、空でなければ
  `{"decision": "block", "reason": ...}` を標準出力へ出す。
- `main()` … 例外を捕まえ、`command == "stop"` のとき
  `{"decision": "block", "reason": "BS_INTERNAL:<例外名>"}` を出す。

### 4.2 変更内容

**変更1: `stop()` の冒頭に歯止めを置く。**

`data.get("stop_hook_active")` が真なら、以降の判定へ進まず素通りする（戻り値 0、標準出力なし）。
素通りしたことは既存の `event()` で記録する。

置く位置は `state = read_state(...)` の直後、`if not state.get("active"): return 0` より前でも後でもよいが、
**`errors` の組み立てより前**であること。

**変更2: `main()` の例外の枝に同じ歯止めを置く。**

`command == "stop"` かつ `data.get("stop_hook_active")` が真なら、`BS_INTERNAL:` を出さずに戻る。

**変更3: 控えを残す。**

変更前の全文を同じフォルダへ `codex_adapter.py.bak-20260906` として残す。

**変更4: 単体試験を2件足す。**

`/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py` に追加する。

- 試験A: 親メモ未選択・カード未発行（＝本来なら `BS_PARENT_REQUIRED / BS_CARD_REQUIRED` で止まる状態）で、
  `stop_hook_active: true` を渡すと、標準出力が空であること。
- 試験B: `stop()` の内部で例外が起きる状況を作り、`stop_hook_active: true` のときは
  `BS_INTERNAL:` が出ないこと。同じ状況で `stop_hook_active` が無いときは出ること
  （＝歯止めが効いているのであって、例外処理を消したのではないことを示す）。

### 4.3 変更しないこと（実装上の禁止）

- `errors` を作る条件（`BS_PARENT_REQUIRED` / `BS_MEMO_REQUIRED` / `BS_CARD_REQUIRED` /
  `BS_CARD_PROSE_REQUIRED`）の中身。
- 120 字という本文量の閾値。
- `pre_tool` / `post_tool` / `user_prompt` / `session_start` / `session_end` の挙動。

---

## 5. 検証

### 5.1 自動試験

```done-when
path: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
run: grep -c stop_hook_active /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py ==> 2
run: python3 -c "import subprocess,sys; r=subprocess.run([sys.executable,'-m','unittest','tests.test_adapter'],cwd='/Users/takedayousuke/.codex/skills/brainstorm',capture_output=True,text=True); print('TESTS-OK' if r.returncode==0 else 'TESTS-FAIL')" ==> TESTS-OK
```

### 5.2 この計画で確かめられないこと

- **実会話での発火。** Codex の利用量の回復は半日後。自動試験に通っても
  「実機確認済み」とは呼ばない。
- **`trusted_hash` が現行の `hooks.json` と一致しているか。** 印の作り方が Codex 内部のもので、
  ファイル全体・正規化 JSON・コマンド文字列のいずれとも一致しなかった。
  したがって「09-01 以降フックが発火していない」原因が、この印なのか、単に Codex を
  この保管庫で使っていないだけなのかは **未確定**。
- 上の未確定があるため、**この修正だけで武田さんが体験する無限ループが消えるとは断定できない。**
  消えるのは「歯止めが無いという構造上の欠陥」であって、発火経路の問題は別。

## 6. 危険と戻し方

| 危険 | 対処 |
|---|---|
| 歯止めを広く取りすぎて、止めるべき場面でも止まらなくなる | 条件を `stop_hook_active` の1つに限定する。他の条件は足さない |
| 例外の枝の歯止めが、本物の不具合を隠す | `event()` に記録を残す。`stop_hook_active` が無いときは従来どおり止める（試験Bで担保） |
| 変更でファイルを壊す | `codex_adapter.py.bak-20260906` を同じ場所に残す。上書きで戻せる |
| 他のサービスへ波及する | 対象は Codex の1ファイルのみ。他は非対象として明記済み |

## 7. この先の順番（本計画の外）

1. 本計画（歯止め）
2. 検査5 を画面ごとに測り分ける（opencode ではカードの中身を見る）— 方針承認から
3. 手1（判定の1本化）— 2026-08-29 の「スキル本体は LLM ごとに独立」を上書きする判断が要る

## 8. 関連する実パス

```
/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py
/Users/takedayousuke/.codex/skills/brainstorm/SKILL.md
/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/deliverable_path_guard.py
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/context_harness/evidence/openai-hooks.md
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/plugins/skill-gate.js
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/harness_parity_check.py
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/kb-experience-reproducibility/20260906-kb-experience-reproducibility.html
```
