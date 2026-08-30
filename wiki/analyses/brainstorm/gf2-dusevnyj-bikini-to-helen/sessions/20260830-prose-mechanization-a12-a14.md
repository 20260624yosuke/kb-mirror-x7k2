---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-30
---

# 表現の機械化3本の実装記録（A12 / A13 / A14・2026-08-30）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

親メモの「実装への申し送り」先頭節（2026-08-30 実行の承認・表現の機械化3本）を実装した記録。

## 武田さんの条件

> 機械的な監査で弾く以外の解決法を禁止します

> プロジェクトの進展に悪影響が出るような整合性の取れていない仕組みは禁止です

つまり「私が気をつける」で閉じるのは禁止で、同時に誤検知で作業が止まる作りも禁止。
この2つを両立させるため、**対象を武田さんが読む文書だけに絞り**、コードと本人の言葉は検査しない。

## 作ったもの

| 実パス | 役割 |
|---|---|
| `wiki/builds/terminology.json` | 用語の辞書。正式名称・許可する別表記・禁止する言い換え |
| `tools/terminology_check.py` | 辞書の禁止語を見る。対象範囲と「見ない場所」の定義もここが正本 |
| `tools/doc_version_check.py` | 説明ページの版（`doc-topic` / `doc-status` / `doc-superseded-by`） |
| `tools/vague_predicate_check.py` | 比較の基準が無い述語（対象語は3語のみ） |
| `tools/prose_guard.py` | 上の3本をまとめて呼び、PreToolUse で書き込みを止める |
| `tools/plan_audit.py` | A12 / A13 / A14 を追加（11項目 → 14項目） |

フックの登録は `~/.claude/settings.json` の `PreToolUse` へ **1行だけ**追加（matcher `Write|Edit`）。
既存の `guard-write --unread` の行は書き換えていない。退避は
`/Users/takedayousuke/.claude/settings.json.bak-20260830-prose-guard`。

## 実機で止まった記録（完成条件3）

`tools/logs/prose-guard.log` に残っている。会話の中で実際に起きたのは次の2件。

1. 21:09:16 `_probe-deny.html` への書き込みを **4件の指摘で拒否**
   （禁止語1・比較の基準が無い述語1・版の印2）。
2. 21:13:23 `_probe-duplicate.html` への書き込みを **1件の指摘で拒否**
   （同じ話題で現行版が2枚になる）。

同じログに、正しく書けた3件（新しい説明ページの作成1件、差し替え表示の追記2件）が
`pass` として残っている。**止めるだけでなく通ることも実機で確認した。**

既存の封鎖が壊れていないことは `brainstorm_guard.py audit-handoff --selftest` が
第1層〜第3層すべて PASS で示している（settings.json を変更した後に実行）。

## 検出力（変異試験）

`plan_audit.py` は **14 / 14 PASS**。3本の内訳は次のとおりで、
**壊した版を落とすこと**と**正しい版を落とさないこと**の両方を毎回確かめている。

- A12: 5例（説明ページの散文・メモの散文で検出／コード内・引用行・正式名称は素通り）
- A13: 7例（印なし・状態が不正・現行版2枚・後継なし・後継が実在しない を検出／正しい2例は素通り）
- A14: 7例（基準の無い述語3例を検出／数値あり・比較の語あり・時間の三十分・コード内は素通り）

実行記録: `output/gf2-helen-swimsuit/run-20260830-prose-guard.txt`

## 説明ページの是正（完成条件6）

- 旧: `wiki/_attachments/helen-swimsuit-status/20260830-three-approvals-explained.html`
  → `doc-status: superseded`、冒頭に差し替え表示、題名に【差し替え済み】を追加。**本文は上書きしていない。**
- 新: `wiki/_attachments/helen-swimsuit-status/20260830-three-approvals-decided.html`
  → 現行の結論だけを書いた1枚（腰・透過の布・足先）。

この旧ページには禁止語が9箇所ある（`terminology_check.py` で実測）。既存ページへの一括修正は
規約で禁止されているため、そのまま残している。

## 指摘の台帳

`output/gf2-helen-swimsuit/review-findings.json` へ F006（用語）・F007（ページの版）・F008（述語）を追加。
8件すべてが `converted` または `human-kept` で、A10 は PASS。

## 見ていないもの

- **チャットの本文は見ていない。** 止められるのは私が書くファイルだけ。Stop フックで応答を弾けるかは未確認で、
  調べる前に武田さんの承認を取る。
- 辞書に載っていない言い換え、対象語3語以外の曖昧な述語は弾けない。
- 文章の分かりやすさそのものは測っていない。数値を書いてさえいれば規則は通る。
- 既存の説明ページ13枚には印が無い。次に触ったときに付ける。

## 次に取る承認

1. `vague_predicate_check.py` の対象語を増やすか（増やすほど書き込みが止まる場面が増える）。
2. チャット本文へ適用するために Stop フックの挙動を調べてよいか。
3. 水着の作業（工程O1／カップ）へ戻る順番。
