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
4. **技術的な障害はほぼ無い。** 要る能力5つのうち、**3サービスとも「止められる」のは2つ**
   （書き込む前・承認を出す瞬間）。注ぎ直す2つは opencode では `chat.message` と `event` が担当し、
   どちらも戻り値を持たないので **注入はできるが止められない**。
   会話の終わりで止め返すのは opencode ではできない。
   （初版で「4つとも止められる」と書いたのは圧縮しすぎ。2026-09-06 レビューで訂正）
5. **手1（判定の1本化）の現実性。** `brainstorm_guard.py` のトップレベル関数は **2,134 行**。
   そのうち自己試験は、規則「名前が `_st_` か `_st<数字>` で始まる、または `cmd_audit_selftest`」で
   数えて **583 行（27.3%）**。残る 1,551 行の内訳（判定 / 収集）は、**再現できる規則を作れていない**。

   > [!warning] 初版の数字は撤回した
   > 初版は「判定 909（42.6%）／自己試験 894（41.9%）／収集 331（15.5%）／共有候補は約85%」と書いた。
   > 分類の当て方が緩く（名前に `_st` を含むだけで自己試験と数えていた）、再現できない値だった。
   > **「約85%が共有候補」は根拠が無いので取り下げる。** 2026-09-06 レビューの指摘による。

   数字とは別に、構造の事実は残る。収集は **transcript を読む前提**で、Codex は自前の状態ファイル、
   opencode は流れるイベントから再構成しており、**情報源が3通り**。薄い変換層では済まない。
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
| 「要る能力5つのうち4つは3サービスとも止められる」 | 止められるのは2つ。注ぎ直す2つは opencode では戻り値が無く、止められない（レビュー指摘） |
| 「関数の内訳は判定 909／自己試験 894／収集 331、共有候補は約85%」 | 分類が緩く再現できない値だった。**撤回**。確かなのは総行数 2,134 と自己試験 583（規則を明示して再計算） |
| 「`stop()` の block 経路は `errors` の1本」 | 2本ある。`phase == "stopped"` の枝が別にあり、初版の位置指定ではそこが歯止めの外に残る（レビュー指摘） |

---

## 3. 本計画の目的と非対象

### 3.1 目的

**Codex の `/brainstorm` が、会話の終わりで終われなくなる状態を作らないようにする。**

### 3.2 完成条件（機械で判定する）

1. 会話の終わりの合図に「すでに一度止めた」印が立っている再呼び出しでは、adapter が止め返さない。
   これは `stop()` の**2本の経路と `main()` の1本、合わせて3本すべて**について成り立つこと。
2. 内部で予期しない失敗が起きた場合も、同じ印が立っていれば止め返さない。
3. 既存の単体試験 11 件が引き続き通る。
4. 追加の単体試験 **3 件**（試験A・B・C）が通り、試験の総数が 14 件であること。
5. 歯止めが `stop()` と `main()` の**両方の関数の中**にあること（行数ではなく関数ごとに見る）。

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

**`stop()` には止める経路が2本ある。**（初版は1本しか書いていなかった。2026-09-06 レビューで訂正）

- 経路1（234〜238 行）… `phase == "stopped"` の枝。`errors` を作る**前**に、
  `BS_CARD_PROSE_REQUIRED` を直接出して `return 0` する。
- 経路2（239〜244 行）… `errors` を組み立て、空でなければ
  `{"decision": "block", "reason": ...}` を出す。

`main()` にも3本目がある。例外を捕まえ、`command == "stop"` のとき
`{"decision": "block", "reason": "BS_INTERNAL:<例外名>"}` を出す。

**歯止めは3本すべての手前に置く必要がある。**

### 4.2 変更内容

**変更1: `stop()` の冒頭に歯止めを置く。**

`data.get("stop_hook_active")` が真なら、以降の判定へ進まず素通りする（戻り値 0、標準出力なし）。
素通りしたことは既存の `event()` で記録する。

**置く位置は `state = read_state(...)` の直後。** `if not state.get("active")` の判定も、
`waiting_theme` の枝も、`phase == "stopped"` の枝も、`errors` の組み立ても、すべてこれより後ろに来ること。

> [!warning] 初版の位置指定は欠陥版を通す
> 初版は「`errors` の組み立てより前」とだけ書いていた。その条件を満たす位置
> （`errors = []` の直前）に置くと、**経路1（`phase == "stopped"` → `BS_CARD_PROSE_REQUIRED`）が
> 歯止めの外に残る**。中断を確定したあと本文が120字に満たないと止められ、書き直しても足りなければ
> また止められる——武田さんが心配された形そのものが残る。
> しかも初版の試験A・試験Bと `done-when` は、**この欠陥版も合格させる**。
> 2026-09-06 レビューの指摘で、位置指定と試験の両方を直した。

**変更2: `main()` の例外の枝に同じ歯止めを置く。**

`command == "stop"` かつ `data.get("stop_hook_active")` が真なら、`BS_INTERNAL:` を出さずに戻る。

**変更3: 控えを残す。**

変更前の全文を同じフォルダへ `codex_adapter.py.bak-20260906` として残す。

**変更4: 単体試験を2件足す。**

`/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py` に追加する。

**試験は3件**（初版は2件。レビューで1件足した）。

- 試験A（経路2）: 親メモ未選択・カード未発行（＝本来なら `BS_PARENT_REQUIRED / BS_CARD_REQUIRED` で
  止まる状態）で、`stop_hook_active: true` を渡すと標準出力が空であること。
- 試験B（`main()` の枝）: **例外は `read_state` の呼び出しで起こす。**
  `stop_hook_active: true` のときは `BS_INTERNAL:` が出ず、`stop_hook_active` が無いときは出ること。
  （歯止めより後ろで例外を起こすと、`stop()` の歯止めだけで素通りしてしまい、
  変更2 が無くても合格する。だから起こす場所を指定する。）
- 試験C（経路1・**欠陥版を弾くための試験**）: `phase == "stopped"`・`confirmed_via_card` が真・
  最後の発言が120字未満、という `BS_CARD_PROSE_REQUIRED` が出る状態で、
  `stop_hook_active: true` を渡すと標準出力が空であること。
  **この試験だけが、歯止めを `errors` の直前に置いた欠陥版を落とす。**

### 4.3 変更しないこと（実装上の禁止）

- `errors` を作る条件（`BS_PARENT_REQUIRED` / `BS_MEMO_REQUIRED` / `BS_CARD_REQUIRED` /
  `BS_CARD_PROSE_REQUIRED`）の中身。
- 120 字という本文量の閾値。
- `pre_tool` / `post_tool` / `user_prompt` / `session_start` / `session_end` の挙動。

---

## 5. 検証

### 5.1 自動試験

初版の2条件は、どちらも**欠陥版を合格させ**、しかも**試験を1件も足していない状態でも合格した**
（`grep -c` は行数を数えるだけ、`unittest` は 11 件でも 13 件でも OK を返す）。作り直した。

```done-when
path: /Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py
run: python3 -c "import ast; src=open('/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py',encoding='utf-8').read(); t=ast.parse(src); L=src.splitlines(); f={n.name:chr(10).join(L[n.lineno-1:n.end_lineno]) for n in t.body if isinstance(n,ast.FunctionDef)}; print('BRAKE-'+('OK' if 'stop_hook_active' in f.get('stop','') and 'stop_hook_active' in f.get('main','') else 'MISSING'))" ==> BRAKE-OK
run: python3 -c "import subprocess,sys,re; r=subprocess.run([sys.executable,'-m','unittest','tests.test_adapter','-v'],cwd='/Users/takedayousuke/.codex/skills/brainstorm',capture_output=True,text=True); m=re.search(r'Ran (\d+) tests',r.stderr); print('TESTS-'+(m.group(1) if m else '0')+'-'+('OK' if r.returncode==0 else 'FAIL'))" ==> TESTS-14-OK
```

**差別力を実測済み（2026-09-06、変更前のファイルに対して）**: 1行目は `BRAKE-MISSING`、
2行目は `TESTS-11-OK` を返す。どちらも現状では不合格になる＝条件が働いていることを確認した。

### 5.2 この計画で確かめられないこと

- **実会話での発火。** Codex の利用量の回復は半日後。自動試験に通っても
  「実機確認済み」とは呼ばない。
- **`trusted_hash` が現行の `hooks.json` と一致しているか。** 印の作り方が Codex 内部のもので、
  ファイル全体・正規化 JSON・コマンド文字列のいずれとも一致しなかった（レビュー側も12通り試して全滅）。

  **ただし、方式が分からなくても読める事実がある**（2026-09-06 レビューの発見）。
  `~/.codex/config.toml` の `[hooks.state]`（205〜267 行）に登録されている `~/.codex/hooks.json` 由来の
  キーは **すべてグループ番号 0**。`hooks.json` には `Stop` の2つ目のグループ
  （`deliverable_path_guard.py`、83〜91 行）と `PreToolUse` の2つ目のグループ
  （`prose_guard.py`、150〜160 行）があるのに、**`stop:1:0` と `pre_tool_use:1:0` の登録が1件も無い。**
  `config.toml` の更新時刻（9/5 15:55）は `hooks.json`（9/5 19:11）より古い。
  つまり「後から足したグループが未 trust のまま」という説明が立つ。
  これは `prose-guard.log` に Codex 由来の行が 09-01 以降0件であることと整合する。

  **一方、本計画の対象である brainstorm の Stop フックは `stop:0:1` で、登録が残っている。**
  その定義は 08-31 以降3世代の `hooks.json` で1文字も変わっていない。
  §3.3 で `hooks.json` を触らないとしたのは、この点から見ても妥当。
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


---

## 9. 実施結果（2026-09-06・実行の承認後）

### 事前のバックアップ（武田さんの指示で先に確認）

- `~/.codex/skills/brainstorm/` をフォルダ丸ごと
  `~/.codex/skill-backups/brainstorm-pre-brake-20260906` へ退避。
- `diff -r` でファイル一覧・中身とも一致することを確認済み。**ここから丸ごと戻せる。**
- あわせて `codex_adapter.py.bak-20260906` を同じフォルダに残した。
- 09-01 の退避 `brainstorm-pre-lite-20260901-093146` も残っている（重い旧版へも戻せる）。

### 実施した変更

`codex_adapter.py` の差分は2か所だけ（`diff` で確認）。

- `stop()` … `read_state()` の直後に歯止め。`active` の判定も `waiting_theme` も
  `phase == "stopped"` も `errors` も、すべてこれより後ろ。素通り時は `event("stop_brake", …)` を記録。
- `main()` … 例外の枝を `command == "stop" and not data.get("stop_hook_active")` に変更。

試験は3件追加して 11 件から **14 件**へ。

### 壊し試験（この試験で欠陥版を落とせるかの確認）

3通りの壊し方を作って走らせた。**それぞれ対応する試験だけが落ちた。**

| 壊し方 | 落ちた試験 |
|---|---|
| 歯止めを丸ごと削除 | 試験A・試験B・試験C の3件 |
| `main()` 側の歯止めだけ削除 | 試験B の1件 |
| 歯止めを `errors` の直前へ移動（初版の位置指定が許す欠陥版） | **試験C の1件** |

初版の試験（A・B のみ）では3つ目を捕まえられなかった。試験C がそれを塞いでいる。

### 完成条件の結果

| 条件 | 変更前 | 変更後 |
|---|---|---|
| 歯止めが `stop()` と `main()` の両方にある | `BRAKE-MISSING` | **`BRAKE-OK`** |
| 試験が 14 件そろって通る | `TESTS-11-OK` | **`TESTS-14-OK`** |

直接起動での動作も確認した。印が無いときは
`{"decision": "block", "reason": "BS_PARENT_REQUIRED / BS_CARD_REQUIRED"}` を出し、
`stop_hook_active: true` のときは何も出さない。確認で出来た一時ファイルは削除済み。

### この時点で言えること・言えないこと

- **言える**: 実装済み。自動試験済み（14 件、壊し試験 3/3）。
- **言えない**: 実機確認済みとは呼べない。Codex の利用量の回復は半日後で、
  実会話でフックが発火するところをまだ見ていない。
  また §5.2 のとおり、`config.toml` の登録の非対称という別の問題が残っており、
  この修正だけで武田さんの体験が変わるとは断定できない。

## 10. 改訂履歴

- **初版 2026-09-06**（sha256 `ea14ffaa…`）。
- **第3版 2026-09-06**。実行の承認を受けて実装し、§9 に実施結果を追記。
- **第2版 2026-09-06**。独立レビュー（サブエージェント、読み取りのみ）の指摘を反映。
  変更点は6件。
  1. §2.2-4「4つとも止められる」を「止められるのは2つ」へ訂正。
  2. §2.2-5 の行数の内訳と「共有候補は約85%」を**撤回**。分類規則が再現できなかったため。
  3. §4.1 に `stop()` の2本目の block 経路（`phase == "stopped"`）を追記。
  4. §4.2 の歯止めの位置を「`errors` より前」から「`read_state()` の直後」へ限定。
     初版の指定は欠陥版を許し、初版の試験と `done-when` はそれを合格させた。
  5. §4.2 の試験を2件から3件へ。試験B に例外を起こす場所を明記し、
     欠陥版を落とす試験C を追加。
  6. §5.1 の `done-when` を作り直し、**変更前のファイルで不合格になることを実測**して差別力を確認。
  あわせて §5.2 へ `config.toml` のグループ番号の非対称（レビューの発見）を追記。
