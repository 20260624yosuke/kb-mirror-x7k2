---
type: analysis
title: brainstorm 引き継ぎ到達性の機械監査（実装計画）
status: active
confidence: medium
evidence_level: source-backed+inferred
created: 2026-08-29
last_reviewed: 2026-08-29
revision: 2
approval: 設計2点のみ承認済み（発火点=渡す瞬間 / 方式=宣言＋走査）。実装は未承認。
review: 2026-08-29 独立レビュー（別エージェント・opus）を1回実施。重大3件・中6件・小5件を反映して revision 2。
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
| 監査対象になる既存メモ（done） | `wiki/analyses/brainstorm-brainstorm-skill-design.md` |
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

### 3.1 監査対象の決め方

```
対象メモ   = brainstorm_status が active / ready の全メモ（cwd で絞らない）
副次対象   = 各メモの entry_paths のうち、実在する .md ファイル（KB 外も含む）
```

副次対象には H1〜H5 を適用する。H6・H7 はメモ本体のみに適用する。

**cwd で絞らない理由**: 既存の `Memo.covers()` は `scope` に cwd が含まれるかで絞るが、引き継ぎの発話が
どのフォルダで行われるかは事前に分からない。実際 `guard.log` には `inject no-live-memo cwd=/Users/takedayousuke`
の行が多数あり、cwd が scope 外だとメモが0件になることが実測できる。監査は PASS なら無音なので、
広く拾って困らない。

**H1〜H4 は frontmatter を含む生テキスト全体**を対象にする（`_parse_frontmatter` は body から
frontmatter を落とすため、body だけを見ると `scope:` や `related:` に書かれたパスとリンクを取り逃す）。

### 3.2 検査項目

相対パスの起点は `KB_ROOT` に固定する（`scope[0]` は使わない。scope は複数あり得るため）。

| 記号 | 検査内容 | 判定方法 | 事故での該当 |
|---|---|---|---|
| H1 | `[[slug]]` の実在 | 本文から `[[slug]]` / `[[slug\|表示]]` を抽出し、`KB_ROOT/wiki/**/<slug>.md` の実在を確認 | 切れリンクの検出 |
| H2 | `[[slug]]` への実パス併記 | H1 で実在した各 slug について、同じファイル中に **ディレクトリを含む形**（`wiki/…/<slug>.md` または絶対パス）が別途現れるか。**裸の `<slug>.md` は不可**（新セッションはどのフォルダか分からず開けないため） | **直撃点。`[[slug]]` だけで実パスが無かった** |
| H3 | 本文中パスの実在 | 抽出した絶対パス・KB 相対パスがすべて実在するか | 存在しないパスの提示を防ぐ |
| H4 | 作業ディレクトリの宣言 | H3 で相対パスを1件以上抽出したファイルは、本文のどこかに `KB_ROOT` の絶対パス文字列を含むこと | 起点が書かれていなかった |
| H5 | メモ ↔ 副次対象の相互実パス | メモが各副次対象の実パス文字列を含み、かつ各副次対象がメモの実パス文字列を含むこと | 片方向しか無かった |
| H6 | `entry_paths` の存在と実在 | frontmatter に `entry_paths:` があり、1件以上あり、全件が実在すること | 欄そのものが無かった |
| H7 | 申し送りが空でない | `## 実装への申し送り` 節の本文が非空であること | 事故時点で空欄だった |

### 3.3 パス抽出の規則（revision 2 で全面的に書き直した）

revision 1 の「空白で終端する」規則は**この KB では必ず壊れる**。KB ルートのパスに半角スペースが
2つ含まれるため（`SSD_M.2_Realtek RTL9210 NVME Media_`）、`/Volumes/SSD_M.2_Realtek` で切れて
実在しない扱いになる。既存コードで同じ誤爆が起きた実例が `guard.log` に残っている
（`2026-08-28 21:52:43 guard-write lockdown DENY tool=Bash path=/Volumes/SSD_M.2_Realtek`）。

置き換えた規則:

1. **まず構造から取る**。コードフェンス内の行全体、インラインコード `` `…` `` の中身、
   Markdown リンク `[表示](パス)` の括弧内、表のセル。ここは区切りが明確なので空白を含んでよい。
2. **裸の本文中の絶対パス**は、既知のルート接頭辞（`/Volumes/`, `/Users/`, `/tmp/`, `/private/`）から
   始めて**行末まで貪欲に取り**、実在しなければ**末尾から1語ずつ削って再判定**する。実在する最長の
   ものを採用する。どこまで削っても実在しなければ FAIL。これで空白入りパスが救われる。
3. **KB 相対パス**は `wiki/`, `tools/`, `raw/`, `index.md`, `log.md` で始まるトークン。起点は `KB_ROOT`。
4. **glob（`*` を含む）は実在照合の対象外**。`tools/fit_method_*.py` や `brainstorm-*.md` のような
   書き方は正当な記述であり、FAIL にしない。
5. **除外行**: 同一行に「未作成」「未実装」「予定」「これから」「作らない」「存在しない」を含む行は
   H3 の対象外。
6. **H1 の除外**: インラインコード・コードフェンスの中に現れる `[[…]]` は説明用のリテラルなので
   対象外。加えて `slug` / `page-slug` / `name` はプレースホルダとして除外する。
   （revision 1 のままだと、`本文中の [[slug]] は Obsidian の記法` という定型文自身が必ず FAIL した。
   実際にこの計画書・gf2 メモ・gf2 計画書の3ファイルすべてで発生することをレビューで実測している。）

### 3.4 出力仕様

- 標準出力に `H2 wiki/builds/xxx.md: [[yyy]] の実パスが本文に無い` の形式で1件1行。
- 終了コード: FAIL 0件で `0`、1件以上で `1`。
- 引数: 無指定で全 live メモを監査。`--memo <path>` で個別指定。`--selftest` で自己試験。
- 監査関数は `root` を引数で受け取り、`KB_ROOT` 定数に直接依存しない形にする。

## 4. 発火点の実装（revision 2 で配線を変更）

### 4.0 レビューで判明した前提の誤り

revision 1 は「既存の `guard-write` / `guard-stop` に相乗りするので `settings.json` は変更しない」と
書いていた。**これは誤り。** 実測でわかったこと:

- `settings.json` の hooks に登録されているのは `inject-full` / `inject-light` / `guard-write --unread` の
  3つだけ。**`guard-stop` も `--lockdown` も登録されていない**（登録は SKILL.md の frontmatter のみ）。
- `guard.log` の `guard-stop` 行は12行すべてが 2026-08-28 20:43 の同一秒3バッチ＝自己試験の合成入力。
  **本番セッション由来の `guard-stop` 行は0件。** 同じ SKILL.md 登録の `lockdown` は 21:51〜21:53 に
  本番行があるのに、その時間帯の `guard-stop` は0行。**Stop フックは本番で発火していない。**
- 事故当日 2026-08-29 のログは `inject` 27行のみ。

したがって revision 1 の発火点②は、実装しても動かない関所だった。

### 4.1 発火点① — `ready` 昇格の阻止（`guard-write --unread` 側に置く）

**`--unread` 側にのみ置く。** 理由: `--unread` は `settings.json` 登録なので常駐しており、
スキルを読み込んでいないセッションでも効く。`--lockdown` は SKILL.md 登録でスキル作動中しか
存在しない。両方に置くと同じ編集で二重に発火する（`guard.log` に lockdown/unread のペア行が実在）。

1. `tool_name` が `Write` / `Edit` / `NotebookEdit` / `Bash` で、対象がメモファイル
   （`MEMO_DIR/brainstorm-*.md`）に該当し、書き込む内容に `brainstorm_status: ready` を含む場合に作動。
   - `Write` は `content`、`Edit` は `new_string`、`NotebookEdit` は `new_source`（`content` /
     `new_string` は入らない。revision 1 の記述は誤り。ただし対象は `.md` なので実害はない）。
   - `Bash` は既存 `_candidate_paths` の解析を流用し、コマンド文字列に `brainstorm_status: ready` を
     含む場合を拾う（`sed -i` やヒアドキュメント経由の昇格を取り逃さないため）。
2. **編集後の姿**を再現して監査する。`Edit` は現ファイルの `old_string` を `new_string` へ置換した
   結果。置換に失敗したら現ファイルの内容で代用する。
3. FAIL があれば `deny()` で拒否。理由には FAIL 一覧と**直し方**を書く。`bypass` は案内しない（4.4 参照）。

### 4.2 発火点② — 引き継ぎ時の停止（新コマンド `guard-stop-handoff` を `settings.json` へ登録）

既存 `guard-stop`（カード無し終了の停止）とは**別コマンドとして分ける**。理由: `guard-stop` を
そのまま常駐登録すると、brainstorm を使っていない会話にまで「承認カードで終われ」という規則が
かかってしまう。分ければ、カード規則はスキル作動中のまま、到達性監査だけを常駐させられる。

```
Stop:
  - hooks:
      - type: command
        command: python3 "$HOME/.claude/skills/brainstorm/brainstorm_guard.py" guard-stop-handoff
        timeout: 10
```

処理:

1. `stop_hook_active` が真なら即 return（無限ループ防止）。
2. active / ready のメモが1件も無ければ即 return（brainstorm を使っていない会話では無音・無コスト）。
3. 直前のアシスタント発話を取る。`last_assistant_message` が無ければ transcript の末尾から拾う
   （この読み込みは `guard-stop-handoff` の中で自前で行う。既存 `cmd_guard_stop` の `events` 読み込みを
   当てにしない）。
4. その発話に `[[` または KB 内・メモ・計画書の実パス文字列が含まれるときだけ監査する。
5. FAIL があれば `{"decision": "block", "reason": …}` を出して return。

### 4.3 圧縮対策の再注入にも載せる

`build_injection()` が拾う節は `("武田さんの考え", "決まったこと", "まだ決まってないこと", "実装への申し送り")`
のハードコード。ここに `## 再開の入口（実パス）` を足し、`entry_paths` も併記する。
これを忘れると、せっかく書いた実パスが圧縮後の再注入に乗らない。

### 4.4 安全弁と、その粒度

- 監査関数が例外を投げたらログだけ残して **PASS 扱い**（既存方針「どんな失敗でも素通りに倒す」に一致）。
- 発火・拒否・素通りはすべて `guard.log` に1行残す。
- **拒否メッセージに `bypass` を案内しない。** 既存 `bypass` は24時間・全監査一括解除であり、
  常用されると関所そのものが無効化される。監査 FAIL の直し方は「実パスを併記する」「`entry_paths` を
  書く」であって、いずれも数十秒で直せる。どうしても通らないときの `bypass` は既存のまま残すが、
  メッセージでは積極的に案内しない。
- Codex / Kimi 用の `cmd_check` にも監査を組み込む（フックの無い環境では新コマンドが呼ばれないため）。

## 5. 自己試験（revision 2 で配線の試験を追加）

revision 1 の自己試験は監査関数しか試験しておらず、**実際に壊れていた配線（4.0）を1つも検出できない**
設計だった。「H1〜H7 全部 OK」でも事故は止まらない。これは section 5 自身が避けたいと書いていた
「構造上必ず合格を返す試験」に該当する。次の2層に分ける。

**第1層 — 監査の検出力**

- 一時ディレクトリに擬似 KB を作る。`KB_ROOT` は環境変数 `LLM_WIKI_ROOT` で差し替えられる既存の
  作りをそのまま使い、**サブプロセスで env を差し替える**。これにより監査関数だけでなく
  **対象選定（`live_memos` / `MEMO_DIR`）ごと試験できる**。事故原因の2番目は対象選定の欠陥だった。
- ケースは8つ。正常ケース1つ（FAIL 0件でなければ試験失敗＝偽陽性の検出）と、H1〜H7 をそれぞれ
  1つだけ壊したケース7つ（**対応する記号の FAIL が出ることを確認**。他の記号だけ出て当該記号が
  出ない場合は試験失敗）。
- **空白を含むパスのケースを正常ケースに必ず入れる**（この KB の実際のパス形状。revision 1 の
  規則ならここで落ちる）。

**第2層 — 配線の試験**

- 合成 JSON ペイロードを stdin に流して `guard-write --unread`（`ready` 昇格の Edit）と
  `guard-stop-handoff`（引き継ぎ発話）を実際に呼ぶ。既存コードに同じ方式の試験ハーネスの痕跡がある
  （`guard.log` 20:43 の合成バッチ）。
- 壊れたメモを置いた擬似 KB で、`deny` / `block` の JSON が実際に出ることを確認する。
- 正常なメモでは出ないことも確認する。

**第3層 — 実機（自動化できない）**

`settings.json` へ `Stop` を登録したあと、**本番セッションで `guard-stop-handoff` の行が
`guard.log` に実際に出るか**を確認する。これは次に `/brainstorm` を使うときにしか確認できない。
自己試験が全部緑でも、この行が出るまで「発火する」とは書かない。

## 6. 変更するファイル

| # | 実パス | 変更内容 | 規模 |
|---|---|---|---|
| 1 | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` | `audit-handoff` / `guard-stop-handoff` 新設、`--unread` へ発火点①、`build_injection` へ節追加、自己試験2層 | 追加 約280行 |
| 2 | `/Users/takedayousuke/.claude/settings.json` | **`Stop` に `guard-stop-handoff` を追加**（revision 1 では「変更しない」としていたが誤りだった） | 1ブロック追加 |
| 3 | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` | ひな型に `entry_paths:`（インデント付き `- ` 形式に固定）と `## 再開の入口（実パス）` を追加、監査の説明を追記 | 追記のみ |
| 4 | `wiki/builds/brainstorm-skill.md` | 正本へ仕様追記 | 追記のみ |
| 5 | `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` | `entry_paths:` を追記（実在するパスのみ） | frontmatter |
| 6 | `index.md` / `log.md` | 台帳更新 | 追記のみ |

revision 1 にあった「`brainstorm-brainstorm-skill-design.md` にも `entry_paths` を追記」は**取り下げる**。
このメモは `brainstorm_status: done` で監査対象外であり、足しても永久に検査されないため。

`entry_paths` の書式は**インデント付きの `- ` 形式に固定**する。既存 `_parse_frontmatter` は
非インデントの `- item` を黙って捨て、インラインフロー `[a, b]` を文字列として読むため、
書式が崩れると H6 が無言で誤判定する。H6 には「`entry_paths` が配列として読めなければ
書式違反として FAIL」を含める（無言 PASS を作らない）。

## 7. この計画が減らす武田さんの確認項目

- 「渡したファイルから関連ファイルへ辿れるか」の目視確認（今回は武田さんの指摘が唯一の検出手段だった）。
- 「申し送りが埋まっているか」の確認。
- 「提示されたパスが実在するか」の確認。

## 8. この計画が見ていないもの（レビュー結果を反映して加筆）

- **書かれた内容が正しいかは検査しない。** 到達できることだけを見る。中身が古い・誤っている場合は通る。
- **受け取り側では走らない。** 監査は送り出す側の関所。新セッションが渡されたファイルを開いても
  検査は走らない。
- **1ターンにつき1回しか効かない。** `stop_hook_active` により、1度 block した直後の Stop は
  監査ごと素通りする。「直しました」と返して止まれば、実際に直っていなくても2回目で通る。
- **口頭・要約だけの引き継ぎ、武田さんが自分でパスをコピーして別窓に貼る場合**は、フックを通らない。
- **`entry_paths` の網羅性そのものは検査できない。** 挙げたパスが実在するかは見るが、
  挙げ忘れたファイルがあるかは分からない。
- **glob 表記の中身は検査しない**（`tools/fit_method_*.py` が実際に6本あるかは見ない）。
- 誤検知の可能性は残る。逃げ道は既存の `bypass`（24時間・全監査一括解除）のみで、粒度は粗い。
- **実機での発火確認は次に `/brainstorm` を使うときになる。** 自己試験が全部緑でも、
  `guard.log` に本番の `guard-stop-handoff` 行が出るまでは「発火する」と書かない。

## 9. 独立レビューの結果と処置（2026-08-29）

別エージェント（opus）に、計画と実コードの不一致を重点にレビューさせた。バイアスのかかる指示は
出していない。重大3件・中6件・小5件の指摘。処置は次のとおり。

| 指摘 | 内容 | 処置 |
|---|---|---|
| 重大1 | `guard-stop` は本番で一度も発火していない（実測） | 受理。`settings.json` へ `guard-stop-handoff` を新規登録（4.0 / 4.2） |
| 重大2 | 空白入りパスで抽出が壊れる。既存コードで誤爆実績あり | 受理。抽出規則を全面的に書き直し（3.3） |
| 重大3 | 定型文の `[[slug]]` リテラルが H1 を必ず落とす | 受理。コード内・プレースホルダを除外（3.3-6） |
| 中1 | H2 が裸のファイル名で PASS してしまう | 受理。ディレクトリを含む形を必須化（3.2 H2） |
| 中2 | `done` メモへの `entry_paths` 追記は永久に検査されない | 受理。変更対象から取り下げ（6） |
| 中3 | `cwd` が scope 外だと対象0件で素通り | 受理。cwd で絞らない（3.1） |
| 中4 | `_parse_frontmatter` の制約で `entry_paths` が無言誤判定 | 受理。書式固定＋書式違反を FAIL に（6） |
| 中5 | H4 が frontmatter を含むか未定義 | 受理。生テキスト全体と明記（3.1） |
| 中6 | 自己試験が配線を試験しない＝検出力ゼロ | 受理。第2層・第3層を追加（5） |
| 小1 | Bash 経由の `ready` 昇格を取り逃す | 受理。Bash も対象に（4.1） |
| 小2 | `NotebookEdit` のキー名が誤り | 受理。`new_source` に訂正（4.1）。実害なし |
| 小3 | 発火点①が lockdown / unread で二重に走る | 受理。`--unread` 側のみに置く（4.1） |
| 小4 | 1ターンに1回しか効かないことが未記載 | 受理。8 に追記 |
| 小5 | 挿入位置では transcript が未読み込み | 受理。別コマンド化し自前で読む（4.2-3） |
| 追加 | `build_injection` が新設節を運ばない | 受理。節リストに追加（4.3） |
| 追加 | 副次対象が KB 内 .md 限定で、KB 外の関連ファイルを見ない | 受理。KB 外も対象に（3.1） |
| 追加 | Codex / Kimi 経路（`cmd_check`）が無防備 | 受理。`cmd_check` に組み込み（4.4） |
| 追加 | `bypass` が24時間・全解除で恒久無効化になりうる | 部分受理。拒否メッセージで案内しない（4.4）。粒度の分割は今回やらない |

**未処置として残す指摘**: `bypass` の粒度分割。理由は、監査 FAIL の直し方が数十秒で済む内容であり、
`bypass` を使う場面が想定しにくいため。実際に常用が起きたら分割する。

## 10. 関連リンク

- スキルの正本: `wiki/builds/brainstorm-skill.md`（[[brainstorm-skill]]）
- 事故の現場: `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`（[[gf2-helen-swimsuit-fit-plan-20260829]]）
- 対象メモ: `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md`（[[brainstorm-gf2-dusevnyj-bikini-to-helen]]）
- 考え方の適用: `wiki/analyses/llm-review-bottleneck-applied-2026-08-28.md`（[[llm-review-bottleneck-applied-2026-08-28]]）
