---
type: build
title: 承認の粒度を機械で扱えるようにする — 引き継ぎ資料
status: active
confidence: high
evidence_level: source-backed
created: 2026-08-29
last_reviewed: 2026-08-29
tags: [harness, approval, quality-gate, plan-audit, handoff]
---

# 承認の粒度を機械で扱えるようにする — 引き継ぎ資料

> [!important] これを読むあなたへ
> **この1枚だけで作業できるように書いてあります。** ほかの会話の記憶は要りません。
> Obsidian の二重角かっこ記法（開けないリンク）は使っていません。ファイルはすべて実パスです。
> 分からない用語は section 1 に説明があります。

## 0. 最初に

**作業ディレクトリ（以下の相対パスの起点）**

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
```

**この依頼は 2026-08-29 に武田さん（このナレッジベースの持ち主）が出したものです。本人の言葉:**

> ここではしない。別のセッションで問題を解決させるから、意図が伝わるように指示を考えて。
> コンテキストがないllmに渡すことになるのでその前提を無視した内容を禁止します。

**やること**: 「承認済みなのに実行されない」という事故が起きたので、同じことが再発しないように
**記述と機械の両方**を直す。作業は section 4 の課題1・課題2の2つです。

## 1. 用語（この文書で使うもの）

| 語 | 意味 |
|---|---|
| 承認 | 武田さんが「これでいい」と明示的に言うこと。無回答・沈黙は承認ではない |
| 方針の承認 | 「どのやり方を採るか」への承認。まだ作ってよいとは言っていない |
| 実行の承認 | 「実際に作ってよい」という承認。方針の承認とは別 |
| approximation（近似版） | 原作の忠実な再現ではなく、推定で埋めた版。忠実版と分けて別に承認を取る決まりになっている |
| 品質ゲート | プロジェクト直下の `quality-gate.json` を機械が読んで、計画・量産・完成の各段階で合否を出す仕組み |
| 機械監査 | 計画書とコードの食い違い、関所の効かなさを機械が調べる仕組み。`tools/plan_audit.py` |
| 関所 | 不合格を止めるための検査。「壊した版を実際に落とせるか」を確かめて初めて有効と言える |

## 2. 何が起きたか（事故の中身）

ヘレンというキャラクターに水着を着せる 3D の案件があります。その中で、
**「カップ（胸を覆う立体）を作り直す」という作業が 2026-08-29 に承認されていたのに、
実装セッションはそれをやらずに止まりました。**

原因を実ファイルで調べた結果、**実装側の判断ミスではなく、運用の穴**でした。

| # | 事実 | 出どころ（実パス） |
|---|---|---|
| 1 | 実装への申し送りの**完成条件4項目に、その作業が入っていなかった** | `wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md` の `## 実装への申し送り` |
| 2 | 計画書の該当節は**見出しが「（2026-08-29 承認）」なのに、本文の最終行だけが「approximation として別途承認を取ること」** | `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md` の section 3.1 |
| 3 | 「別途承認を取る」は計画書の3か所にあるが、**いつ・誰が・どの形で取るかが誰にも割り当てられていない** | 同上（section 0.5 / 3.1 / 8.4） |
| 4 | 全プロジェクト共通規則が「完成条件を満たしたら停止する」と定めている | `CLAUDE.md` の「ふるまいの優先順位」1 の「実行」 |

つまり **見出しは「承認済み」と読め、本文は「まだ承認が要る」と読める。** 武田さんは見出しどおりに
読み、実装は完成条件どおりに止まった。**どちらも規則に従っていて、それでも噛み合わなかった。**

## 3. なぜ機械で直すのか（文言ルールでは足りない）

武田さんは以前から「文言ルールだけで担保して『できた』と報告しない」と繰り返し指示しています。
理由は、規則を増やしても次の LLM が読み飛ばせば同じことが起きるからです。
**だから課題1（記述）と課題2（機械）は必ずセットで行ってください。**

## 4. やること

### 課題1: 承認の粒度を、文書の書き方として固定する

**満たすこと**

1. 計画書や引き継ぎ資料の見出しに「承認」と書くときは、**それが方針の承認か実行の承認かを
   見出し自体に書く。** 本文の最後だけに但し書きを置かない。
2. 実装への申し送りに **`### 終わったら次に取る承認`** という節を必ず置く。
   完成条件（達成条件の一覧）だけでは、達成したあとの分岐が無く、実装は報告して止まる以外にできない。
3. すでに直してある実例が2つあるので、**それを見本にしてください**（あなたが作る必要はない）。
   - `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md` の section 3.1（見出しと承認状態の書き方）
   - `wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
     の `### 終わったら次に取る承認`
4. あなたが直すのは **書き方の規則を置く場所**です。次の2つのファイルに、上の1と2を
   「今後こう書く」という形で追記してください。
   - `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md` の `## 5. 承認が出たら` の節
   - `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
     （スキルの仕様の正本。SKILL.md を変えたらここも合わせる）

**やってはいけないこと**

- 規則を長く書かない。既存の節へ数行足すだけにする。武田さんは長文と規則の増殖を嫌います。
- 既存の規則を消さない。

### 課題2: この案件の品質ゲートを作り、承認の状態を機械が読めるようにする

**背景（実測）**: この案件だけ `quality-gate.json` がありません。兄弟プロジェクトには5件あります。

```
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/quality-gate.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-mityl-motion/quality-gate.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-sharkry-touch-to-sabrina/quality-gate.json
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-sharkry-touch-to-sabrina-v4/quality-gate.json
```

`CLAUDE.md` の「1A 高リスク成果物の品質ゲート」は、この種の案件に `quality-gate.json` を
必須と定めています。**ひな型と判定器はすでにあります。**

- ひな型: `tools/quality-gate.template.json`
- 判定器: `tools/project_quality_gate.py`
- 使い方: `python3 tools/project_quality_gate.py check <対象>/quality-gate.json --phase <plan|batch|complete>`

ひな型の `families[]` には、必要な欄がすでに全部あります（`approval_scope` /
`mode`（`faithful` か `approximation`）/ `approximation_approved` / `approved_by` / `approved_at` /
`approval_evidence` / `accepted_gaps`）。**新しい欄を発明しないでください。**

**満たすこと**

1. `quality-gate.json` を、この案件の作業フォルダ直下に置く。場所は
   `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/quality-gate.json`
   （成果物の出力先がここなので、ここが自然です。別の場所が適切だと判断したら、理由を添えて
   武田さんに聞いてください）。
2. `families[]` に、**2026-08-29 時点で確定している承認を、事実どおりに**書く。
   - カップの作り直し … `mode: "approximation"`、`approximation_approved: true`、
     `approved_at: "2026-08-29"`、`approval_scope` に **「カップだけ。上衣の下端と腰の布の接続は含まない」**
   - 上衣の下端と腰の布の接続 … `approximation_approved: false`（**まだ承認されていない**）
3. `tools/plan_audit.py` に **A9** を足す。**満たす条件**は次のとおり。
   - 成果物の来歴（面ごとの `donor` / `helen` / `fitted` / `created`）を読み、
     **`created` が 0 でないのに、その工程の `approximation_approved` が `true` でないなら FAIL** にする。
   - 来歴の数え方はすでに実装されています。`tools/helen_swimsuit_fit_p.py` の `provenance()` と、
     `--approved-created` という引数を見てください。
   - 実装のやり方はあなたが決めてください。既存の A1〜A8 の書き方に合わせること。

**検出力の確認（これをやらないと完了ではありません）**

このナレッジベースには「検出力を確かめていない試験を『合格した』の根拠にしない」という
絶対規則があります（`CLAUDE.md` と、計画書 section 7 の8番）。**A9 を足したら、必ず次を確かめてください。**

1. `quality-gate.json` の `approximation_approved` を `false` に書き換えた版を作り、
   **A9 が FAIL を返すこと**を実際に確認する。
2. 承認された枚数と違う `created` を申告した版で、**A9 が FAIL を返すこと**を確認する。
3. 確認できたら元に戻す。**確認していない状態で「実装しました」と報告しないこと。**

**完了の判定**

```
python3 tools/plan_audit.py
python3 tools/project_quality_gate.py check output/gf2-helen-swimsuit/quality-gate.json --phase plan
```

- `plan_audit.py` が **9 / 9 PASS**（現状は 8 / 8 PASS。A9 を足すので 9 になる）。
- 品質ゲートが `PASS (plan)` を返す。
- 上の「検出力の確認」1と2で、**壊した版が実際に FAIL したことを報告に書く。**

## 5. 絶対にやってはいけないこと

1. **`plan_audit.py` の既存の A1〜A8 を弱めない。** 通すために条件を緩めない。
2. **検出力を確かめていない関所を「実装した」と報告しない。**
3. **成果物の中身（`tools/helen_swimsuit_fit_p.py` の適合や判定の中身）を変えない。**
   この依頼は承認の扱いを直すものです。合否の物差しそのものには触りません。
4. **原本を書き換えない。**
   `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/intermediate`
   の中身と、既存の Blend は読み取り専用です。
5. **既存の合格線（数値）を動かさない。** 動かすと「合格させるために線を動かした」ことになります。
   この案件では過去に2回、それで合格を撤回しています。
6. **レンダリングした画像を見て判断しない。** この案件では LLM の画像判読を封印しています
   （方針の正本: `wiki/builds/llm-vision-review-suspension-policy.md`）。

## 6. 既知の落とし穴

- **`tools/plan_audit.py` は1回およそ 45秒かかります。** 中で適合計算を何度も走らせるためです。
  途中で止めないでください。
- **`brainstorm` というスキルが動いている会話では、`tools/` への書き込みが機械的に止まります。**
  止められたら、それは「その会話は考えをまとめる段階」という意味です。
  **この依頼は実装なので、`brainstorm` を起動していない新しい会話で作業してください。**
- そのブロックには**読み取りだけのコマンドまで止めてしまう誤検知**があります（既知の課題）。
  別の引き継ぎ資料があります:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-guard-fix-handoff-20260829.md`
  **この依頼ではそれを直さないでください。** 別件です。

## 7. 背景として読んでよいもの（読まなくても作業はできます）

| 実パス | 何が書いてあるか |
|---|---|
| `wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md` | 水着案件の計画書。事故の現場 |
| `wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/sessions/20260829-p-implementation-and-decision-point.md` | 事故の調査記録（この依頼が生まれた経緯） |
| `wiki/_attachments/helen-swimsuit-status/20260829-helen-swimsuit-decision-point.html` | 案件の現状説明（人が読む用） |
| `CLAUDE.md` | このナレッジベース共通の規則。とくに「1A 高リスク成果物の品質ゲート」 |

## 8. この依頼が見ていないもの

- **ほかの案件にも同じ穴があるかは調べていません。** 今回直すのは水着案件と、書き方の規則だけです。
- **A9 が「承認の範囲（どの工程か）」を正しく突き合わせられるかは、あなたの実装しだいです。**
  工程名の対応づけを間違えると、承認されていない工程を承認済みとして通してしまいます。
  検出力の確認2は、そこを見るための試験です。
- 承認そのものを機械が取ることはできません。**機械にできるのは「承認が記録されていない作業を
  止めること」だけです。**
