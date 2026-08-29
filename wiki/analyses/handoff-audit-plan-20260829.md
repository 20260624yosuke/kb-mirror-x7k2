---
type: analysis
title: brainstorm 引き継ぎ到達性の機械監査（実装計画）
status: active
confidence: medium
evidence_level: source-backed+inferred
created: 2026-08-29
last_reviewed: 2026-08-29
revision: 3
approval: 設計2点のみ承認済み（発火点=渡す瞬間 / 方式=宣言＋走査）。実装は未承認。
review: 2026-08-29 独立レビュー（別エージェント・opus）を2回実施。1回目 重大3/中6/小5、2回目 実装前必須7件。全件反映して revision 3。
tags: [brainstorm, harness, audit, handoff]
---

# brainstorm 引き継ぎ到達性の機械監査（実装計画）

> [!warning] 未承認
> 本書は実装前の計画である。コードは1行も書いていない。

## 0. 作業ディレクトリと関連ファイルの実パス

**作業ディレクトリ（以降の相対パスの起点）**

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
```

本文中の `[[slug]]` は Obsidian の記法で、実体は `wiki/` 配下の `<slug>.md`。

| 役割 | 実パス |
|---|---|
| 改修対象・監査スクリプト | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` |
| 改修対象・スキル定義 | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` |
| 動作ログ（既存） | `/Users/takedayousuke/.claude/skills/brainstorm/guard.log` |
| フック登録の実体 | `/Users/takedayousuke/.claude/settings.json` |
| スキルの正本 wiki ページ | `wiki/builds/brainstorm-skill.md` |
| 事故が起きた実ログ | `/Users/takedayousuke/llm-uploads/20260829-082928-セッションを新しくするので-エージェントがタスクを再開するのに必要なファイルパス.md` |
| 適用する考え方（GPT との検討） | `/Users/takedayousuke/llm-uploads/20260828-223742--AI開発における-レビュー-検証ボトルネック-を現在のプロジェクト計画へ適用す.md` |
| 監査対象になる既存メモ（active） | `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` |
| 監査対象**外**の既存メモ（done。参考） | `wiki/analyses/brainstorm-brainstorm-skill-design.md` |
| 事故の現場になった計画書 | `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md` |

## 1. 直す不備（実測で特定した事実）

2026-08-29 のセッションで、brainstorm で作った計画書を新セッションへ引き継ぐ際、
計画書に関連ファイルの実パスが無く、`[[slug]]` だけが書かれていた。新セッションの LLM は
`[[slug]]` を解決できないため、関連ファイルへ到達できない状態だった。武田さんの指摘で初めて発覚した。

実ファイルを読んで確認した原因は次の3点。**いずれも「書き忘れ」ではなく「検出器の不在」である。**

1. `brainstorm_guard.py` が持つ関所は3つのみ — `guard-write --lockdown`（成果物封鎖）、
   `guard-write --unread`（申し送り未読ブロック）、`guard-stop`（カード無し終了の停止）。
   **メモや計画書の中身を検査するコマンドは存在しない。**
2. `--unread` は `live_memos(cwd, ("ready",))` に限定されている。今回の引き継ぎは
   `brainstorm_status: active` のまま行われたため、**一度も発火していない**（`guard.log` に該当拒否なし）。
3. `SKILL.md` のひな型に、関連ファイルの実パスを書く欄が無い。`## 実装への申し送り` は自由記述で、
   空欄でも `[[slug]]` だけでも通る。実際、事故時点でこの節は空欄だった。

適用する考え方は上記 GPT 資料の §12「人間が一度した指摘を、二度させない形へ変換する」と
§13「検証を LLM の自主性に依存させない」。本件はこれが未実施の箇所にあたる。

## 2. 承認済みの設計方針（2026-08-29・承認カード）

- **発火点は「渡す瞬間」だけ。** ①`brainstorm_status` を `ready` へ上げる編集時、
  ②アシスタントの応答がメモ・計画書のパスや `[[slug]]` を含んだまま終わろうとした時（Stop フック）。
  却下した案: 毎ターン監査（執筆途中で頻繁に中断する）／`ready` 昇格時のみ（今回の事故が active
  のままだったので防げない）。
- **方式は宣言＋走査の両方。** frontmatter に `entry_paths:` を必須化して実在照合し、
  加えて本文の `[[slug]]`・パス文字列も全部照合する。
  却下した案: 走査のみ（何も書かなければ照合対象ゼロで素通り）／宣言のみ（本文の切れリンクが通る）。

## 3. 監査の定義

新コマンド `audit-handoff` を `brainstorm_guard.py` に追加する。判定はすべてファイル実在と
テキスト照合のみで行い、LLM の意味判断は使わない。

### 3.1 監査対象の決め方（revision 3 で再修正）

revision 2 は「cwd で絞らない」としたが、これは**過剰な修正だった**。実測で active なメモは
`brainstorm-gf2-dusevnyj-bikini-to-helen.md` の1件のみであり、絞りを外すとこの1件が
**全プロジェクト・全セッションでヒット**する。しかもこのメモは現時点で H2 が5件 FAIL するため、
「PASS なら無音」という前提が成立しない。

置き換えた規則:

```
対象メモ = 次のいずれかを満たす brainstorm_status が active / ready のメモ
          (a) 既存の Memo.covers(cwd) が真（＝cwd がメモの scope 内）
          (b) 直前のアシスタント発話が、そのメモの実パス・ファイル名・slug、または
              そのメモの entry_paths に挙がったパスを含む（＝引き継ぎとして名指しされた）

副次対象 = 各メモの entry_paths のうち実在する .md ファイル（KB 外も含む）
```

(b) があるので、cwd が scope 外のフォルダで引き継ぎを行っても捕まる。(a) があるので、
無関係なプロジェクトの会話は対象0件で無音のまま抜ける。revision 2 の穴はこれで塞がる。

**H1〜H5 の適用範囲**

- 副次対象には H1〜H4 を適用する。
- **H5（相互リンク）は KB 内の副次対象にのみ適用する。** KB 外のファイル（外部 LLM の出力ログ等）は
  こちらが書き足して直す性質のものではなく、双方向を要求すると直せない FAIL が恒久的に残るため。
  実測でも `/Users/takedayousuke/llm-uploads/20260828-223742--…適用す.md` はメモのパスを含まず、
  H5 だけが必ず落ちることが確認されている。
- H6・H7 はメモ本体のみに適用する。

**H1〜H4 は frontmatter を含む生テキスト全体**を対象にする（`_parse_frontmatter` は body から
frontmatter を落とすため、body だけを見ると `scope:` や `related:` のパス・リンクを取り逃す）。

### 3.2 検査項目

相対パスの起点は `KB_ROOT` に固定する。

| 記号 | 検査内容 | 判定方法 | 事故での該当 |
|---|---|---|---|
| H1 | `[[slug]]` の実在 | 本文から `[[slug]]` を抽出し `KB_ROOT/wiki/**/<slug>.md` の実在を確認 | 切れリンクの検出 |
| H2 | `[[slug]]` への実パス併記 | H1 で実在した各 slug について、同じファイル中に**ディレクトリを含む形**が別途現れるか。裸のファイル名は不可 | **直撃点** |
| H3 | 本文中パスの実在 | 3.3 の規則で抽出したパスがすべて実在するか | 存在しないパスの提示 |
| H4 | 作業ディレクトリの宣言 | 相対パスを1件以上含むファイルは、本文のどこかに `KB_ROOT` の絶対パス文字列を含むこと | 起点が無かった |
| H5 | メモ ↔ KB 内副次対象の相互実パス | 双方が相手の実パス文字列を含むこと | 片方向しか無かった |
| H6 | `entry_paths` の存在・書式・実在 | 配列として読め、1件以上あり、全件実在すること。**glob は書けない**（実在照合できないため書式違反とする） | 欄そのものが無かった |
| H7 | 申し送りが空でない | `## 実装への申し送り` 節の本文が非空 | 空欄だった |

### 3.3 パス抽出の規則（revision 3 で 新規4・新規5 を修正）

revision 1 の「空白で終端する」規則は**この KB では必ず壊れる**。KB ルートに半角スペースが2つ
含まれるため（`SSD_M.2_Realtek RTL9210 NVME Media_`）。既存コードで同じ誤爆が起きた実例が
`guard.log` に残っている（`lockdown DENY tool=Bash path=/Volumes/SSD_M.2_Realtek`）。

1. **構造から取る**。コードフェンス内の行全体、インラインコードの中身、Markdown リンクの括弧内、
   表のセル。区切りが明確なので空白を含んでよい。
   **ただし取り出した文字列は、次の 2 のパス接頭辞テストを必ず通す。** これを書き忘れると
   `` `brainstorm_status: ready` `` のような全インラインコードがパス候補になる（revision 2 の穴）。
2. **接頭辞テストと貪欲一致**。既知の接頭辞（`/Volumes/`, `/Users/`, `/tmp/`, `/private/`、および
   KB 相対の `wiki/`, `tools/`, `raw/`, `index.md`, `log.md`）で始まるものだけをパス候補とする。
   絶対パスは**その出現位置から行末まで貪欲に取り**、実在しなければ末尾から1語ずつ削って再判定する。
   実在する最長のものを採用し、どこまで削っても実在しなければ FAIL。
3. **1行に複数のパスがあり得る**。行全体を1トークンにせず、**接頭辞の出現ごとに独立して**貪欲一致を
   掛ける。revision 2 の規則では、1行目のパスが実在した時点で確定し、同じ行の2つ目の壊れたパスが
   素通りすることがレビューの実測で確認されている。
4. **glob（`*` を含む）は実在照合の対象外**。`tools/fit_method_*.py` のような書き方は正当。
5. **プレースホルダは H1・H3 の両方で除外する**。`<` `>` `…` を含むトークン、および
   `slug` / `page-slug` / `name` / `テーマslug`。revision 2 はこの除外を H1 にしか掛けておらず、
   `wiki/…/<slug>.md` のような**説明文自身が FAIL する**ことがレビューの実測で確認されている。
6. **除外行**: 同一行に「未作成」「未実装」「予定」「これから」「作らない」「存在しない」を含む行は
   H3 の対象外。
7. **表のセルは `|` で分割してから**接頭辞テストを掛ける（`index.md` / `log.md` のような
   連結を防ぐ）。

### 3.4 出力仕様

- 標準出力に `H2 wiki/builds/xxx.md: [[yyy]] の実パスが本文に無い` の形式で1件1行。
  H3 の FAIL では**採用したトークンではなく、問題のあるパス文字列そのもの**を出す。
- 終了コード: FAIL 0件で `0`、1件以上で `1`。
- 引数: 無指定で対象メモを監査。`--memo <path>` で個別指定（**その entry_paths も辿る**）。
  `--selftest` で自己試験。
- 監査関数は `root` を引数で受け取り、`KB_ROOT` 定数に直接依存しない。

## 4. 発火点の実装

### 4.0 レビューで判明した前提の誤り

revision 1 は「`settings.json` は変更しない」としていたが**誤り**だった。実測でわかったこと:

- `settings.json` の hooks 登録は `inject-full` / `inject-light` / `guard-write --unread` の3つのみ。
  **`guard-stop` も `--lockdown` も登録されていない**（登録は SKILL.md の frontmatter のみ）。
- `guard.log` の `guard-stop` 行12行はすべて 2026-08-28 20:43 の同一秒3バッチ＝自己試験の合成入力。
  同じ SKILL.md 登録の `lockdown` は 21:51〜21:53 に本番行があるのに、その時間帯の `guard-stop` は0行。
  **Stop フックは本番で一度も発火していない。**
- 事故当日 2026-08-29 のログは `inject` 27行のみ。

### 4.1 発火点① — `ready` 昇格の阻止（挿入位置を厳密に指定する）

既存 `cmd_guard_write` の `--unread` 経路は次の順で早期 return する。

```
L386  if bypass_active(session_id): return 0
L390  if mode == "lockdown": …; return 0
L408  memos = live_memos(cwd, ("ready",))
L410  if not memos: return 0          ← ★
```

**`ready` へ昇格する瞬間は、まだ `ready` のメモが0件**である（今まさに active→ready に上げようと
している）。したがって ★ より後ろに置くと**原理的に一度も発火しない**。

**挿入位置: L386（bypass 判定）の直後、L390（lockdown 分岐）より前。条件は `mode == "unread"`。**

- `mode == "unread"` に限る理由: `--unread` は `settings.json` 登録で常駐しており、スキルを
  読み込んでいないセッションでも効く。`--lockdown` は SKILL.md 登録でスキル作動中のみ。両方に
  置くと同じ編集で二重に発火する（`guard.log` に lockdown/unread のペア行が実在）。
- bypass 中は発火点①も止まる（L386 が前段のため）。これは意図した挙動として記録しておく。

作動条件と処理:

1. `tool_name` が `Write` / `Edit` / `NotebookEdit` / `Bash` で、対象がメモファイル
   （`MEMO_DIR/brainstorm-*.md`）で、書き込む内容に `brainstorm_status: ready` を含むこと。
   キー名は `Write`=`content`、`Edit`=`new_string`、`NotebookEdit`=`new_source`。
   `Bash` は既存 `_candidate_paths` の解析を流用する（`sed -i` 経由の昇格を取り逃さないため）。
2. **編集後の姿**を再現して監査する。`Edit` は現ファイルの `old_string` を `new_string` へ置換した
   結果。置換に失敗したら現ファイルの内容で代用する。
   **H5 だけは、メモ側が編集後・副次対象側がディスク上の現物という混合評価になる**。
   実害は小さいが、そう評価していることを明記しておく。
3. FAIL があれば `deny()` で拒否。理由には FAIL 一覧と直し方を書く。

### 4.2 発火点② — 引き継ぎ時の停止（新コマンド `guard-stop-handoff`）

既存 `guard-stop`（カード無し終了の停止）とは**別コマンドに分ける**。`guard-stop` をそのまま
常駐登録すると、brainstorm を使っていない会話にまで「承認カードで終われ」という規則がかかるため。

`/Users/takedayousuke/.claude/settings.json` の `Stop` へ追加する:

```
python3 "$HOME/.claude/skills/brainstorm/brainstorm_guard.py" guard-stop-handoff
```

処理:

1. `stop_hook_active` が真なら即 return（無限ループ防止）。
2. active / ready のメモが無ければ即 return。
3. 直前のアシスタント発話を取る（`last_assistant_message`、無ければ transcript 末尾から自前で拾う）。
   実測で 14MB の transcript の読み込みは 0.04 秒、イベント化まで 0.11 秒程度。
4. **3.1 の (a)(b) で対象メモを絞る。ここで0件なら return。**
   これにより、無関係なプロジェクトの会話は無音で抜ける（revision 2 の最大の穴）。
5. 発話に `[[` または対象メモ・副次対象の実パス文字列が含まれるときだけ監査する。
6. FAIL があれば `{"decision": "block", "reason": …}` を出して return。
   **reason には「このメモを勝手に編集して直そうとしないこと。引き継ぎ元の会話で直すか、
   武田さんへ報告すること」を必ず含める。** 止められたセッションが KB のメモを書き換えに行く経路が
   あり（そのセッションでは成果物封鎖が効いていない）、歯止めが文言しかないため。
7. フックの timeout（10秒）で落ちた場合、および外付け SSD 未マウントで `MEMO_DIR.is_dir()` が
   False の場合は、**メモ0件＝素通り**になる。これは意図した挙動。

### 4.3 圧縮対策の再注入にも載せる

`build_injection()` が拾う節のハードコード
（`"武田さんの考え", "決まったこと", "まだ決まってないこと", "実装への申し送り"`）に
`## 再開の入口（実パス）` を足し、`entry_paths` も併記する。忘れると、書いた実パスが圧縮後の
再注入に乗らない。

### 4.4 安全弁

- 監査関数が例外を投げたらログだけ残して **PASS 扱い**（既存方針に一致）。
- 発火・拒否・素通りはすべて `guard.log` に1行残す。
- **bypass の正規手順を書いておく**（revision 2 は「案内しない」としたが、誤検知が実在する以上
  逃げ道が文書上ゼロになるのは不適切）。拒否メッセージには**まず直し方**を書き、
  「同じ FAIL が2回続き、内容を確認しても誤検知と判断できる場合に限り」`bypass` を案内する。
  bypass は24時間・全監査一括解除である旨も併記する。
- Codex / Kimi 用の `cmd_check` にも監査を組み込む。

## 5. 自己試験（revision 3 で 1.4・新規8 を修正）

**第1層 — 監査の検出力**

- 一時ディレクトリに擬似 KB を作り、`LLM_WIKI_ROOT` を差し替えたサブプロセスで実行する。
  `KB_ROOT` は env 由来で `MEMO_DIR` もそこから派生するため、**対象選定ごと試験できる**。
- ケースは8つ。正常ケース1つ（FAIL 0件でなければ偽陽性として試験失敗）と、H1〜H7 をそれぞれ
  1つだけ壊したケース7つ（対応する記号の FAIL が出ることを確認。他の記号だけ出て当該記号が
  出ない場合は試験失敗）。
- **正常ケースには必ず次を含める**: 空白を含むパス、1行に複数パス、glob 表記、
  `<slug>` 等のプレースホルダを含む説明文。いずれも revision 1/2 の規則では誤爆した形。

**第2層 — 配線の試験**

- 合成 JSON を stdin に流して `guard-write --unread`（`ready` 昇格の Edit）と
  `guard-stop-handoff`（引き継ぎ発話）を実際に呼び、`deny` / `block` の JSON が出ることを確認する。
  正常なメモでは出ないことも確認する。
- **`settings.json` を読んで `guard-stop-handoff` が `Stop` に登録されているかを検査する。**
  これは数行で書ける機械検査であり、実機（第3層）へ回す必要がない。revision 2 はこれを第3層へ
  回しており、重大1 と同じ欠陥を検出できないままだった。
- **自己試験は `guard.log` を汚さない。** `LOG_PATH` は現状 `HERE/guard.log` 固定で env 非対応。
  env（`BRAINSTORM_GUARD_LOG`）で差し替えられるようにする。revision 2 のままだと、合成行が本番ログに
  混ざり、4.0 の判定根拠（「20:43 の12行は合成、本番は0行」）と同じ判別を将来もう一度やることになる。

**第3層 — 実機（自動化できない）**

登録後、本番セッションで `guard-stop-handoff` の行が `guard.log` に実際に出るかを確認する。
次に `/brainstorm` を使うときにしか確認できない。自己試験が全部緑でも、この行が出るまで
「発火する」とは書かない。

## 6. 変更するファイル

| # | 実パス | 変更内容 | 規模 |
|---|---|---|---|
| 1 | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` | `audit-handoff` / `guard-stop-handoff` 新設、`--unread` へ発火点①、`build_injection` へ節追加、`LOG_PATH` の env 対応、自己試験3層 | 追加 約300行 |
| 2 | `/Users/takedayousuke/.claude/settings.json` | `Stop` に `guard-stop-handoff` を追加 | 1ブロック追加 |
| 3 | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` | ひな型に `entry_paths:`（インデント付き `- ` 形式・glob 不可）と `## 再開の入口（実パス）` を追加、監査の説明を追記 | 追記のみ |
| 4 | `wiki/builds/brainstorm-skill.md` | 正本へ仕様追記 | 追記のみ |
| 5 | `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` | `entry_paths:` を追記し、**現時点で H2 が5件 FAIL する箇所に実パスを併記して直す** | frontmatter＋本文 |
| 6 | `index.md` / `log.md` | 台帳更新 | 追記のみ |

`entry_paths` の書式は**インデント付きの `- ` 形式に固定**する。既存 `_parse_frontmatter` は
非インデントの `- item` を黙って捨て、インラインフロー `[a, b]` を文字列として読むため。
H6 には「配列として読めなければ書式違反として FAIL」を含め、無言 PASS を作らない。
SKILL.md のひな型に `entry_paths` の**実在しない例を書かない**（消し忘れが H6 FAIL になるため、
ひな型では空欄＋書き方の説明のみにする）。

## 7. この計画が減らす武田さんの確認項目

- 「渡したファイルから関連ファイルへ辿れるか」の目視確認（今回は武田さんの指摘が唯一の検出手段だった）。
- 「申し送りが埋まっているか」の確認。
- 「提示されたパスが実在するか」の確認。

増える確認項目は**無い**見込み。revision 2 では「無関係セッションで出た block の切り分け」が
増える可能性があったが、3.1 の (a)(b) 方式でその経路を閉じた。

## 8. この計画が見ていないもの

- **書かれた内容が正しいかは検査しない。** 到達できることだけを見る。
- **受け取り側では走らない。** 監査は送り出す側の関所。
- **1ターンにつき1回しか効かない。** `stop_hook_active` により、1度 block した直後の Stop は
  監査ごと素通りする。「直しました」と返して止まれば、実際に直っていなくても2回目で通る。
- **口頭・要約だけの引き継ぎ、武田さんが自分でパスをコピーして別窓に貼る場合**はフックを通らない。
- **`entry_paths` の網羅性そのものは検査できない。** 挙げ忘れたファイルは分からない。
- **glob 表記の中身は検査しない。**
- **KB 外の副次対象は片方向しか見ない**（H5 を適用しないため、外部ファイル側からメモへ戻れなくてよい）。
- 誤検知の可能性は残る。逃げ道は `bypass`（24時間・全監査一括解除）で、粒度は粗い。
- **実機での発火確認は次に `/brainstorm` を使うときになる。** `guard.log` に本番の
  `guard-stop-handoff` 行が出るまでは「発火する」と書かない。

## 9. 独立レビューの結果と処置

別エージェント（opus）に、計画と実コードの不一致を重点にレビューさせた。バイアスのかかる指示は
出していない。2回実施。

### 9.1 1回目（revision 1 に対して・重大3/中6/小5）

| 指摘 | 内容 | 処置 |
|---|---|---|
| 重大1 | `guard-stop` は本番で一度も発火していない（実測） | 受理。`settings.json` へ `guard-stop-handoff` を新規登録 |
| 重大2 | 空白入りパスで抽出が壊れる。既存コードで誤爆実績あり | 受理。抽出規則を書き直し。2回目の実測で FAIL 0件に改善を確認 |
| 重大3 | 定型文の `[[slug]]` リテラルが H1 を必ず落とす | 受理。2回目の実測で3ファイルとも H1 FAIL 0件を確認 |
| 中1 | H2 が裸のファイル名で PASS | 受理。ディレクトリを含む形を必須化 |
| 中2 | `done` メモへの `entry_paths` 追記は検査されない | 受理。変更対象から取り下げ |
| 中3 | `cwd` が scope 外だと素通り | **過剰に処置していた。revision 3 で (a)(b) 方式へ再修正**（9.2 参照） |
| 中4 | frontmatter 書式で無言誤判定 | 受理。書式固定＋書式違反を FAIL に |
| 中5 | H4 が frontmatter を含むか未定義 | 受理。生テキスト全体と明記 |
| 中6 | 自己試験が配線を試験しない | 受理。第2層・第3層を追加。**さらに 9.2 で登録有無の検査を追加** |
| 小1 | Bash 経由の昇格を取り逃す | 受理 |
| 小2 | `NotebookEdit` のキー名が誤り | 受理（`new_source`）。実害なし |
| 小3 | 発火点①の二重発火 | 受理。`--unread` 側のみ |
| 小4 | 1ターン1回しか効かない | 受理。8 に明記 |
| 小5 | 挿入位置で transcript 未読み込み | 受理。別コマンド化 |
| 追加4件 | `build_injection` / KB 外副次対象 / `cmd_check` / `bypass` 粒度 | 受理（bypass のみ部分受理） |

なお2回目のレビューで、1回目に懸念された transcript 読み込みコストと `[[` の誤爆は
**実測により否定され、撤回された**（14MB で 0.11 秒、直近25本の transcript で `[[` の誤爆0件）。

### 9.2 2回目（revision 2 に対して・実装前必須7件）

| 指摘 | 内容 | 処置 |
|---|---|---|
| 新規1 | cwd 絞り撤廃で「無音」前提が破綻。active メモ1件が全セッションでヒットし、しかもそれが H2 FAIL 5件 | 受理。3.1 を (a)(b) 方式へ再修正 |
| 新規2 | block された無関係セッションが KB メモを書き換えに行く（そこでは封鎖が効かない） | 受理。新規1 で経路を閉じ、加えて block の文言で明示的に禁止（4.2-6） |
| 新規3 | 発火点①の挿入位置が未指定。`if not memos: return 0` より後ろだと永久に不発火 | 受理。L386 直後・lockdown 分岐より前・`mode == "unread"` 限定と明記（4.1） |
| 新規4 | 3.3-1 に接頭辞テストが無く、全インラインコードがパス候補になる。計画書自身が H3 で3件 FAIL | 受理。接頭辞テストを必須化し、プレースホルダ除外を H3 にも適用（3.3-1/3.3-5） |
| 新規5 | 1行に複数パスがあると2つ目以降が素通り（実測で PASS を再現） | 受理。接頭辞の出現ごとに独立して貪欲一致（3.3-3） |
| 新規6 | KB 外副次対象に H5 を適用すると直せない FAIL が恒久化 | 受理。H5 は KB 内に限定（3.1） |
| 新規8 | 自己試験が本番 `guard.log` を汚し、第3層の判定根拠を壊す | 受理。`LOG_PATH` を env 対応に（5・6） |
| 1.4 | 第2層がフック登録の有無を検査せず、重大1 が再発しうる | 受理。`settings.json` の登録検査を第2層へ追加 |
| 新規7 | section 0 の表記が 3.1/6 と食い違い | 受理。表記訂正 |
| 見落とし1 | H6 と glob の関係が未定義 | 受理。entry_paths に glob 不可と明記（3.2 H6・6） |
| 見落とし2 | `--memo` 指定時に副次対象を辿るか未定義 | 受理。辿ると明記（3.4） |
| 見落とし3 | 発火点①の H5 が編集後／編集前の混合評価 | 受理。そう評価すると明記（4.1-2） |
| 見落とし4 | bypass を案内しないのに誤検知は実在する | 受理。条件付きで正規手順を書く（4.4） |
| 見落とし5 | timeout・SSD 未マウント時の挙動が未定義 | 受理。素通りと明記（4.2-7） |
| 見落とし6 | section 7 が revision 1 のまま | 受理。増える確認項目は無い旨を追記（7） |

**未処置として残す指摘**: `bypass` の粒度分割（監査だけを解除する仕組み）。実際に常用が起きたら分割する。

## 10. 関連リンク

- スキルの正本: `wiki/builds/brainstorm-skill.md`（[[brainstorm-skill]]）
- 事故の現場: `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`（[[gf2-helen-swimsuit-fit-plan-20260829]]）
- 対象メモ: `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md`（[[brainstorm-gf2-dusevnyj-bikini-to-helen]]）
- 考え方の適用: `wiki/analyses/llm-review-bottleneck-applied-2026-08-28.md`（[[llm-review-bottleneck-applied-2026-08-28]]）
