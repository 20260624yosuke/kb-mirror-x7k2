---
type: build
title: Helen H0157 — 新しいエージェントの入口（2026-09-09）
status: active
confidence: high
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-09
supersedes_entry: gf2-helen-h0157-u0-u3-next-agent-task-entry.md
---

# Helen H0157 — 新しいエージェントの入口（2026-09-09）

**この1枚を最初に読む。他の資料はここから辿る。**

この文書の目的は2つある。ゴールまでの現在地を渡すことと、
**この案件で繰り返し起きている失敗を、次の担当者が繰り返さないようにすること**である。
後者のほうが重い。前任者は 2026-09-09 に同じ失敗を1日で3回起こしている。

---

## 0. 30秒で読む現在地

- **ゴール**: 寮の Helen の見た目を Blender 上で再現し、開いて確認できる blend を渡すこと。
- **合否を決めるのは武田さんの目**。機械の一致は合格の証拠にならない（実例は §4）。
- **いま止まっている場所**: 候補を開いて F12 を押す1手。2026-09-08 12:40 から待ち。
- **ただしその1手の段取りは muse が書いたもの**なので、採用する前に §3 を読むこと。

---

## 1. いちばん大事な注意 — この案件の汚染源

2026-09-03 12:30 から 09-08 12:50 まで、opencode で **muse-spark-1.3** というモデルが
この案件を担当した。**符号は書けた。判断のほうは、武田さんの評価では成果物に結びつかなかった。**
武田さんの評価は「成果物への直接の貢献は一切なかった」。

muse が書き換えたファイルは **269 件**。一覧はここにある。

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/muse-authored-files.json
```

**採用の方針（2026-09-09 に武田さんが選んだもの）**

- muse の**測定値**と**再実行できる符号**は残す。再実行して合えば使える。
- muse の**判断・結論・要約**は疑う。根拠に使うなら、muse 由来だと明記する。

**この方針は機械で守られている。** muse のファイルを出所の印なしで引用すると、
会話が閉じられない（§6 の出所の門）。

### muse 由来だと知らずに引用しやすいもの（実際に前任者が引用した）

| ファイル | 中身 | 注意 |
|---|---|---|
| `logs/f197-allframes-gap.json` | 合格条件についての判断 | **muse の判断。武田さんの発言としての裏付けが取れていない。`provenance_warning` を読むこと** |
| `blends/_candidate-postchain/WORKING-LINE.json` | 3手の段取り | muse が 2026-09-08 11:57 に作成。段取りとして使ってよいが、muse の判断である |
| `wiki/builds/helen-h0157-handoff-20260908.md` | 引き継ぎ資料 | muse が作成。実測部分と推論部分が本文で分けて書かれている（`実測`／`記録`／`推論`の印がある） |
| `quality-gate.json` | 品質の関所 | muse が触れている |

---

## 2. ゴールと完成条件

目的は、寮の Helen の見た目を Blender 上で再現することである。
成果物は、開いて確認できる blend であること。手順の学習を要求しないこと。

**完成条件はまだ確定していない。** 武田さんが 2026-09-05 に述べた考え方は、
最終的なゴールは複合的（質感・光の再現などを合わせたもの）であり、
1点が通っただけでは合格ラインとは限らない、というものだった。
**これを具体的な合否条件へ落とす作業は、まだ済んでいない。**
合格条件を勝手に決めないこと。決めるのは武田さんである。

---

## 3. いま止まっている1手

作業線の候補は、指の作り直しと等級の結線が載ったものである。

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/_candidate-postchain/helen-h0157-repro__04ef8b79b3fa5b64_f195-opencheck.blend
```

- sha256 の先頭: `763393dd1d325c2bf69966b7`（19.3MB・2026-09-08 12:40）
- 開く枠は **49**。目印は `49指` と `206顔`
- 案内: 49枠で指、206枠で顔、F12 で等級
- 指・ノード・キー・カメラ・LUT は触っていない

**この1手は武田さんの目でしか進まない。** 返してもらうのは2点だけ。
顔が白く飛んでいないか。指が原作の形に見えるか。

この候補と段取りは muse の作業に由来する。採用の前に sha256 を測り直すこと。

---

## 3.5 入力の所在が 2026-09-09 に変わった（PC再起動をまたいで観測）

**着手前に必ず所在を測り直すこと。**

同日の同じ会話で、ゲームのキャッシュ側
`~/Library/Containers/com.haoplay.game.ios.exilium/Data/Documents/LocalCache/Data/AssetBundles_IOS`
から 9,565 件を列挙し、248MB の目録（56,846 記録）を復号していた。
PC を再起動したあと、同じ経路に `os.stat` を当てた戻りは **errno 2（ENOENT）** だった。
権限の遮断を示す errno 1（EPERM）とは別の値である。

- **外付けSSD 上のアプリ側 4,511 件は、同じ時刻に読めている**（同じ方法での陽性対照）
- したがって「読み取りの方法が壊れた」ではなく、「その経路の中身の状態が変わった」と観測された
- 原因はこの観測では特定していない

測定の記録:

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f200-cache-side-availability.json
```

**この影響で、監査の再実行が落ちている 40 件がある。**
いずれも `scripts/` 配下 10 本がキャッシュ側の版番号 `2.12.4517` を直書きで参照していることに由来する。
版を直書きせず、実在する版を探す形へ直す必要がある。

---

## 4. 繰り返している失敗（読まないと必ず踏む）

### 4.1 「探索は尽きた」と書いて、あとで覆る

記録に残っているものだけで何度も起きている。
`ledger/negative-claims-legacy.json` に凍結された宿題が 10 件ある。
共通の型は **問いの立て方を1つだけ選び、その角度で得られた結果を、断定へ言い換える**ことである。

前任者は 2026-09-09 に3回やっている。
「ローカルには無いのでゲームを動かすしかない」「未開封が1.8億バイトある」
「未開封はほぼ尽きた」。3つとも撤回した。
最後のものは、撤回の直後に、誰も走査していなかった 248MB の目録から
探していた2本の名前が出てきた。

**対策は機械で入っている（§6）。だが機械は言い換えを全部は捕まえない。**
「無い」と書きたくなったら、まず別の角度を1つ立てること。

### 4.2 機械の一致を、見た目の合格と取り違える

指の作り直し（f184）で、機械では30本すべて原作の連鎖と 0.00 度で一致した。
**武田さんの目視では不合格だった。**
原因は、それ以前の検査が骨の位置だけを測っていて、向きを一度も比べていなかったこと。

**目視と測定が食い違ったら、目が優先。**

### 4.3 要約を一次資料として扱う

`ledger/local-corpus-coverage.json` の要約（「内部構造未展開」）を根拠に、
前任者は「まだ誰も中を見ていない」と判断した。
実際にはその 8 分前に書かれた `ledger/h0157-gff-container-scan-v1.json` が
同じ領域を復号済みだった。要約のほうには出典が空で入っていた。

**要約を見たら、必ずその下の詳細記録に当たること。**

### 4.4 陽性対照を置かないと、自分の不具合を「発見」と誤認する

前任者は目録の復号で、先頭バイトを平文長と解釈する不具合を出した。
その結果、探していた2本も陽性対照も 0 件になった。
**陽性対照を置いていたので、「無い」ではなく「探し方が壊れている」と分かった。**

**探索の結果を報告する前に、有るはずのものを同じ方法で探して、実際に当たることを確かめること。**

---

## 5. 最初に読む正本（この順）

1. この文書
2. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/_candidate-postchain/WORKING-LINE.json` — 3手の段取り（muse 作成）
3. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/helen-h0157-handoff-20260908.md` — 案件の詳細（muse 作成・印つきで実測と推論を分けている）
4. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/negative-claims.json` — 「無い」と言うために要る証拠の書き方
5. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md` — 親メモ

### 2026-09-09 に作った記録

- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260909-muse-contamination-audit.html` — muse が何を書いたかの調査
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260909-audit-blind-spot-and-repeat-pattern.html` — 繰り返しの照合と監査の死角
- `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f198-gff-unopened-accounting.json` — 未開封部分の会計

---

## 6. 機械で守られていること（心がけではない）

会話を閉じるときに走る。破ると閉じられない。

| 検査 | 何を止めるか | 場所 |
|---|---|---|
| 否定主張の門 | 登録の無い「無い」「原理的にできない」を、指示書・引き継ぎ資料・親メモ・説明ページに書くこと | `tools/absence_claim_scope_gate.py` |
| 出所の門 | muse の判断を印なしで引用すること。ログ写しに無い文言を武田さんの発言として引くこと | `tools/provenance_gate.py` |
| 成果物のパス | 判断を求めるのにパスを書かないこと。引き継ぎ資料や説明ページを更新してパスを出さないこと | `tools/deliverable_path_guard.py` |
| 監査の健全性 | 監査自身が壊れたまま閉じること。自己試験が通れば拡張として自動で通る | `tools/audit_integrity_check.py` |

いずれも `KB/tools/` にある。自己試験は `--mutation-test` または `--selftest` で走る。

**「無い」と書けないときの逃げ道は用意されている。**
探索範囲を名指しした弱い言い方（例: 具体的なファイル名を挙げて「調べた範囲では見つからなかった」）
なら、証拠一式なしで通る。**言葉の強さと証拠の強さを釣り合わせること。**

---

## 7. 承認済みで未着手の作業

武田さんが 2026-09-09 に許可したもののうち、まだ手を付けていないもの。

1. 保存領域2台（`/Volumes/HDD_バックアップ` と `/Volumes/HDD_バックアップ_macbookpro`）の
   走査。**2026-09-09 に一度「該当0件」と報告したが、その報告は取り消した。**
   使ったコマンドが `timeout` で始まっており、この Mac に `timeout` が入っていないため
   `command not found` となり、空のパイプが `wc -l` で 0 を返していた。
   検索そのものが走っていなかった。
   `timeout` を外して同じ `find` を流したところ、両ボリュームから `*.bundle` が **1,458 件**
   返っている（同じ `find` で `backup_manifest.plist` が 9 件返ることを陽性対照として確認）。
   **この 1,458 件の素性は未確認。**着手する担当者は、まずここから調べること。
   記録は `06_repro-v51/logs/f201-backup-volume-rescan.json`（作成予定）
2. 否定主張 9 件への区画申告の書き足し（`covered_classes` / `excluded_classes`）
3. 再実行が壊れている主張の修理（登録された探索コマンドが実行時に落ちるもの）

---

## 8. やってはいけないこと

- 原本 `blends/helen-h0157-repro.blend`（sha `04ef8b79b3fa5b64…`）への書き込み。読み取りと照合だけ
- 合格条件を自分で決めること
- 機械の検査が通ったことを「完成」と報告すること
- muse の判断を、出所を明かさずに根拠にすること
- 隔離場所 `blends/_quarantine-20260908/` のものに触ること

---

## 9. 使わなかったもの・落とした情報

- **捨てたもの**: 2026-09-09 に作った作業指示書
  `06_repro-v51/reports/WORK-ORDER-20260909-open-gff-containers.md`。
  独立レビューで Critical 3 件・Major 6 件が出て撤回した。冒頭に撤回の見出しがある
- **手元でどう変わるか**: この指示書を実行していたら、既に済んでいる作業を
  やり直すことになっていた。3D の見た目はどちらにせよ変わらない
- **戻せるか**: 文書なので戻せる。ただし前提が誤っているため、戻して使うことは勧めない

---

## 10. 関連

- [muse 汚染調査](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260909-muse-contamination-audit.html>)
- [監査の死角と繰り返し](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260909-audit-blind-spot-and-repeat-pattern.html>)
- [引き継ぎ資料 2026-09-08](</Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/helen-h0157-handoff-20260908.md>)
