---
type: analysis
title: brainstorm 引き継ぎ到達性の機械監査（実装計画）
status: active
confidence: medium
evidence_level: source-backed+inferred
created: 2026-08-29
last_reviewed: 2026-08-29
revision: 1
approval: 設計2点のみ承認済み（発火点=渡す瞬間 / 方式=宣言＋走査）。実装は未承認。
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
対象メモ   = live_memos(cwd, ("active", "ready"))
副次対象   = 各メモの entry_paths のうち、KB_ROOT 配下に実在する .md ファイル
```

副次対象には H1〜H5 を適用する。H6・H7 はメモ本体のみに適用する。

### 3.2 検査項目

相対パスの起点は `KB_ROOT` に固定する（`scope[0]` は使わない。scope は複数あり得るため）。

| 記号 | 検査内容 | 判定方法 | 事故での該当 |
|---|---|---|---|
| H1 | `[[slug]]` の実在 | 本文から `[[slug]]` / `[[slug\|表示]]` を抽出し、`KB_ROOT/wiki/**/<slug>.md` の実在を確認 | 切れリンクの検出 |
| H2 | `[[slug]]` への実パス併記 | H1 で実在した各 slug について、**同じファイル本文中に `<slug>.md` という文字列**が別途現れるか（相対・絶対どちらの表記でも可） | **直撃点。`[[slug]]` だけで実パスが無かった** |
| H3 | 本文中パスの実在 | 抽出した絶対パス・KB 相対パスがすべて実在するか | 存在しないパスの提示を防ぐ |
| H4 | 作業ディレクトリの宣言 | H3 で相対パスを1件以上抽出したファイルは、本文のどこかに `KB_ROOT` の絶対パス文字列を含むこと | 起点が書かれていなかった |
| H5 | メモ ↔ 副次対象の相互実パス | メモが各副次対象の実パス文字列を含み、かつ各副次対象がメモの実パス文字列を含むこと | 片方向しか無かった |
| H6 | `entry_paths` の存在と実在 | frontmatter に `entry_paths:` があり、1件以上あり、全件が実在すること | 欄そのものが無かった |
| H7 | 申し送りが空でない | `## 実装への申し送り` 節の本文が非空であること | 事故時点で空欄だった |

### 3.3 パス抽出の規則（誤爆を避けるため意図的に狭くする）

- **絶対パス**: `/Volumes/`, `/Users/`, `/tmp/`, `/private/` で始まり、空白・全角文字・
  `` ` `` `"` `'` `|` `)` `]` `,` のいずれかで終端するトークン。
- **KB 相対パス**: 区切り直後に現れる `wiki/`, `tools/`, `raw/`, `index.md`, `log.md` で始まるトークン。
- 上記以外の文字列はパスとみなさない（日本語混じり文での誤爆防止）。
- **除外**: 同一行に「未作成」「未実装」「予定」「これから」「作らない」「存在しない」のいずれかを
  含む行は H3 の対象外とする（「まだ無い」と明記してあるものを FAIL にしないため）。

### 3.4 出力仕様

- 標準出力に `H2 wiki/builds/xxx.md: [[yyy]] の実パスが本文に無い` の形式で1件1行。
- 終了コード: FAIL 0件で `0`、1件以上で `1`。
- 引数: 無指定で cwd の live メモを監査。`--memo <path>` で個別指定。`--selftest` で自己試験。

## 4. 発火点の実装

### 4.1 発火点① — `ready` 昇格の阻止（`cmd_guard_write` 内）

`guard-write` は現在 lockdown / unread の2モードで呼ばれる。**両モードの先頭**に次を挿入する。

1. `tool_name` が `Write` / `Edit` / `NotebookEdit` で、`file_path` が `MEMO_DIR/brainstorm-*.md` に該当し、
   `content` または `new_string` に `brainstorm_status: ready` を含む場合のみ作動。
2. **編集後の姿**を再現して監査する。`Write` は `content` をそのまま、`Edit` は現ファイルの
   `old_string` を `new_string` へ置換した結果を使う。置換に失敗した場合は現ファイルの内容で代用する。
3. FAIL があれば `deny()` で拒否。理由に FAIL 一覧・直し方・`bypass` コマンドを載せる。

これは既存の lockdown 判定（メモは書き込み許可）より**前**に置く。順序を誤ると素通りする。

### 4.2 発火点② — 引き継ぎ時の停止（`cmd_guard_stop` 内）

`stop_hook_active` による早期 return の**直後**、既存のカード判定より**前**に次を挿入する。

1. `live_memos(cwd, ("active", "ready"))` が空なら何もしない。
2. 直前のアシスタント発話テキスト（`last_assistant_message`、無ければ transcript 末尾）に
   次のいずれかが含まれるときだけ作動する。
   - `[[` を含む
   - `MEMO_DIR` / `KB_ROOT/wiki/builds` / `KB_ROOT` の絶対パス文字列を含む
3. 監査 FAIL があれば `{"decision": "block", "reason": ...}` を出して return する。
   reason には FAIL 一覧と「引き継ぎ先が関連ファイルへ到達できない」旨を書く。
4. PASS なら従来のカード判定へ進む。

**カード判定より前に置く理由**: 承認カードで終わる応答であっても、その中で引き継ぎパスを
提示しているなら止めるべきだから。

### 4.3 共通の安全弁

- 既存の `bypass_active(session_id)` が真なら監査を丸ごとスキップする。
- 監査関数が例外を投げた場合はログだけ残して **PASS 扱い**にする（既存方針「どんな失敗でも
  素通りに倒す」に一致）。
- 発火・拒否・素通りはすべて `guard.log` に1行残し、後から効いたかを数えられるようにする。
- Stop フックの block は既存と同じく `stop_hook_active` により2周目で抑止され、無限ループしない。

## 5. 自己試験（検出力の証明）

`audit-handoff --selftest` を実装する。**「答えの分かっている試験が構造上必ず合格を返す」**
という失敗（別案件の G10 で実際に起きた）を繰り返さないため、次の形にする。

- 一時ディレクトリに擬似 KB（`wiki/analyses` / `wiki/builds`）を作る。
- 監査関数は `root` を引数で受け取る形にし、`KB_ROOT` 定数に依存させない（差し替え可能にする）。
- ケースを8つ作る。
  - **正常ケース1つ** → FAIL が0件でなければ試験失敗（偽陽性の検出）。
  - **H1〜H7 をそれぞれ1つだけ壊したケース7つ** → **対応する記号の FAIL が出ることを確認**する。
    他の記号だけ出て当該記号が出ない場合は試験失敗とする。
- 出力は `H1 OK` … `H7 OK` / `正常ケース OK` と総合判定。終了コードで PASS/FAIL を返す。
- 実装後にこれを実行し、**実測結果を武田さんへ報告する。**

## 6. 変更するファイル

| # | 実パス | 変更内容 | 規模 |
|---|---|---|---|
| 1 | `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py` | `audit-handoff` 新設、発火点2箇所、自己試験 | 追加 約200行 |
| 2 | `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` | ひな型に `entry_paths:` と `## 再開の入口（実パス）` を追加、監査の説明を追記 | 追記のみ |
| 3 | `wiki/builds/brainstorm-skill.md` | 正本へ仕様追記 | 追記のみ |
| 4 | `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md` | `entry_paths:` を追記（実在するパスのみ） | frontmatter |
| 5 | `wiki/analyses/brainstorm-brainstorm-skill-design.md` | `entry_paths:` を追記 | frontmatter |
| 6 | `index.md` / `log.md` | 台帳更新 | 追記のみ |

`/Users/takedayousuke/.claude/settings.json` は**変更しない**。既存の `guard-write` / `guard-stop`
の呼び出しに相乗りするため、新しいフック登録は不要。

## 7. この計画が減らす武田さんの確認項目

- 「渡したファイルから関連ファイルへ辿れるか」の目視確認（今回は武田さんの指摘が唯一の検出手段だった）。
- 「申し送りが埋まっているか」の確認。
- 「提示されたパスが実在するか」の確認。

## 8. この計画が見ていないもの（正直な限界）

- **書かれた内容が正しいかは検査しない。** パスが実在し到達できることだけを見る。中身が古い・
  誤っている場合は通る。
- **`[[slug]]` を1つも書かず、パスも1つも書かず、`entry_paths` だけ埋めた**メモは H1〜H5 が
  対象ゼロで通る。H6 が最低限の担保になるが、entry_paths の網羅性そのものは検査できない。
- **発火点②は「アシスタントの発話にパスや `[[` が含まれる」ことを条件にする。** 引き継ぎを
  口頭のみで示唆した場合は発火しない。
- 誤検知の可能性は残る。逃げ道は既存の `bypass` コマンドのみ。
- 実機での発火確認は、次に `/brainstorm` を使うときになる。本計画では自己試験までしか確認できない。

## 9. 関連リンク

- スキルの正本: `wiki/builds/brainstorm-skill.md`（[[brainstorm-skill]]）
- 事故の現場: `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`（[[gf2-helen-swimsuit-fit-plan-20260829]]）
- 対象メモ: `wiki/analyses/brainstorm-gf2-dusevnyj-bikini-to-helen.md`（[[brainstorm-gf2-dusevnyj-bikini-to-helen]]）
- 考え方の適用: `wiki/analyses/llm-review-bottleneck-applied-2026-08-28.md`（[[llm-review-bottleneck-applied-2026-08-28]]）
