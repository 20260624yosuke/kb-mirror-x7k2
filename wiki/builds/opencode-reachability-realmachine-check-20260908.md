---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-08
---

# opencode の実機確認の手はず（2026-09-08）

作業ディレクトリ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`

**これは実装後の改善確認です。** 変更・試験はすべて済んでいて、直っているはずの状態を
実機で見る作業です（原因調査ではありません）。

## 1. 何がまだ確かめられていないか

自動試験で確かめたのは**検査そのもの**です。

| 済んでいること | どうやって |
|---|---|
| 検査が死んだパスを見つける | 自己試験 9/9・壊し試験 9通り |
| 3ハーネスの判定が揃う | 親メモ 20 枚で 20/20 一致 |
| opencode の `check_parent` が本文を見る | 直接呼んで FAIL を確認 |

**確かめていないのは1点だけです。**

> **opencode を実際に使ったとき、承認カードが本当に止まるか。**

opencode ではプラグイン `.opencode/plugins/skill-gate.js` が、承認カード（`question` 道具）が
出る直前に検査を走らせて FAIL のカードを止めます。**その配線が実際に働くかは、
opencode を起動しないと分かりません。** 私の側では確かめられない部分です。

## 2. やり方は3つあります

| やり方 | 精度 | 武田さんの手間 | 画面の占有 | 危険性 |
|---|---|---|---|---|
| ① 武田さんが操作して結果を教える | 高い | 数分 | 普通に使う画面だけ | 無い |
| ② 私が画面を自動で操作する | 中くらい | ほぼ無い | **前面を占有する** | 誤操作の恐れ |
| ③ 私が短い手順を出し、武田さんが操作し、私が結果を読む | 高い | 数分 | 普通に使う画面だけ | 無い |

**推奨は ③ です。** 見たいのは「実際の画面でカードが止まるか」という一度きりの確認で、
繰り返しも自動判定も要りません。②（自動操作）は反復が多くて判定が客観的なときに向く方法で、
今回は当てはまりません。

## 3. ③ の手順（3ステップ・数分）

使い捨てのメモは**もう置いてあります**。武田さんが用意するものはありません。

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/_probe-realmachine/_index.md`

このメモは本文に**わざと存在しないファイル**を1件書いてあります。
他の会話には影響しません（保管庫全体の監査・台帳・健全性の3つが、置いた後も PASS のままなのを確認済み）。

### ステップ1

VS Code で、いつもどおり統合ターミナルから opencode（muse）を起動します。
作業フォルダは、いつもの
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01` です。

### ステップ2

次の1行を、opencode にそのまま送ります。

```
/brainstorm wiki/analyses/brainstorm/_probe-realmachine/_index.md の実機確認。メモは絶対に直さず、そのまま承認カードを1枚出してください。カードが止まるかを見るのが目的です。
```

> [!warning] 2026-09-08 1回目の失敗を踏まえた修正
> 最初の版では「承認カードを1枚出してください」とだけ書いていました。
> opencode の担当は **brainstorm の規則どおり「記録と現実の食い違いを黙って直す」を実行し、
> 死んだパスを実在するファイルへ置き換えてから通しました。**
> その結果 **「カードが止まるか」は分からないまま**になりました。
> 手順の書き方が悪かったので、「直さずに」を明記しました。

### ステップ3

**カードが出るか、止まるかを見てください。** 期待している動きはこうです。

- **止まるのが正解。** `skill-gate: 承認カードを止めた` のような文言と、
  `パスが実在しません` を含む行が出ます。
- カードが**普通に出てしまったら失敗**です。配線が働いていません。

画面に出た文言を、そのままコピーして教えてください。**判断は私がします。**

## 4. 終わったら

結果を伺ったら、私が使い捨てのメモを消します（フォルダごと）。
消し忘れがないかは、次のコマンドで確かめられます。

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/_probe-realmachine/_index.md
```

## 5. 失敗したときに見る場所

| 症状 | 見る場所 |
|---|---|
| カードが普通に出た | `.opencode/plugins/skill-gate.js` が `muse_brainstorm_check.py` を呼べているか |
| 関係ない文言で止まった | 表示検査（`.opencode/scripts/display_check.py`）が先に止めている |
| opencode が起動しない | 外付け SSD がマウントされているか |

## 6. 関連ファイル（実パス）

- 親メモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md`
- 共通の1本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/reachability_check.py`
- opencode の検査: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/scripts/muse_brainstorm_check.py`
- opencode の関所: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/.opencode/plugins/skill-gate.js`
- 使い捨てのメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/_probe-realmachine/_index.md`
- 揃え方の計画: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/kb-path-existence-parity-plan-20260908.md`
