---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-30
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/projects-dashboard.md
---

# プロジェクト・ハブ（大きな案件の関連ファイルを1枚に束ねる仕組み）

## 武田さんの考え

> helen原作再現というプロジェクトがあります。v51helenとかそういう名前だった気がしますが、
> 関連ファイルがどの程度紐づけられているかを把握できていません。
> 作業していて思ったのは、大きなプロジェクトになったときは、コンテキストがかさむので、
> セッションが何度も切り替わります。その時、対象プロジェクトの関連ファイルが体系的に
> 紐づけられているハブのような仕組みがないと、このwikiを使っている意味がない気がしました。
> 言語化してください。

（2026-08-29 の brainstorm メモにも同趣旨の発言あり）
> 大元は計画や引き継ぎの資料であって、関連ファイルが全部紐づいてる状態。
> だから、コンテキストの粒度に制限されずに、プロジェクトを進められる環境を作りたい。

## 私の誤り（2026-08-30・武田さんの指摘で撤回）

> 対象プロジェクトを特定せず、憶測で会話を進めるのを禁止します
> Mmdのプロジェクトではなく、ドルフロというゲームのcodeからhelenを原作のまま再現は可能か
> 検証しているプロジェクトです。かなり大きいプロジェクトです。このkbフォルダの中では最大だと思います。

- 私は **MMD/PMX からの作画資料化**（`README-ja.md` が 2026-07-24 で止まっている件）を
  対象案件と取り違えて話した。**撤回する。**
- 対象は **ドルフロ2のゲームコードから Helen を原作のまま再現できるか検証するプロジェクト**
  ＝ `06_repro-v51`（HELEN-REPRO v5.1）。
- また「直近のログを出さないのが気になる」との指摘も受けた。数日前まで LLM に作業させている案件で、
  私は 7月の README を持ち出した。**先に log.md と実作業フォルダの直近を見るべきだった。**

## 問題の再定義（2026-08-30 武田さん本人の言葉）

> ハブというのは例えであって、要は、現行llmは基本的にコンテキストを引き継げない問題があるのに、
> プロジェクトが大きくなって、セッションを切り替えると、それまでのコンテキストはなくなる。
> でも、作成した計画書を添付しただけでは、そのプロジェクトの全容のコンテキストを把握することは
> できないから、事前にセッションを引き継ぐ前に、llmに引き継ぎ資料を用意してと指示を送って、
> 作成した資料を新しいセッションで添付してる。
> この手間が生じるのが、運用上の問題だし、その引き継ぎ資料がどれだけコンテキストを保持しているかも
> わからない。
> Wikiはナレッジ上に構成できるからコンテキストが膨らむ問題を多少はケアできるはずなのに
> それが活かされていないと感じた。

→ 問題は「リンクが無い」ことではない。**引き継ぎ資料を毎回その場で人が指示して作らせており、
その資料の網羅度が測れない**こと。

## 実測B（2026-08-30・HELEN-REPRO v5.1 に限定して測り直した）

- **引き継ぎ資料が案件内に7枚ある。** 合計 4,124行（wiki 側4枚）＋作業フォルダ側3枚。
  | 場所 | 行数 | 状態 |
  | --- | --- | --- |
  | `wiki/builds/gf2-helen-repro-v51-run.md` | 1737 | 実行記録の正本 |
  | `wiki/builds/gf2-helen-repro-v51-handoff.md` | 1582 | 引き継ぎ正本（8/27 更新） |
  | `wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md` | 570 | 別セッション向け |
  | `wiki/builds/gf2-repro-and-swimsuit-conversation-handoff-20260827.md` | 235 | 会話分割用 |
  | `06_repro-v51/reports/HANDOFF.md` | 313 | 冒頭に「このファイルは古い」 |
  | `06_repro-v51/reports/NEXT-SESSION-PROMPT.md` | 131 | 冒頭に「旧次回プロンプト」 |
  | `06_repro-v51/reports/HANDOFF-2026-08-20.md` | 10 | wiki へ移動済みのポインタ |
- **機械可読の現在位置はすでに存在する**: `06_repro-v51/run-state.json`。
  `current_step` / `passed_gates` / `failed_gates` / `next_action` / `blocked` / `history`(113件) を持つ。
  ただし **129,913文字（226KB）** あり、そのまま新セッションへ渡せない。
  さらに **最終更新 2026-08-26 20:55** で、wiki 側の 8-29〜8-30 の作業が反映されていない。
- log.md の直近: 8-26 以降も helen 系の作業が 8-30 まで続いている（futa / 水着 / 体メッシュ実測 /
  胸の型 / 承認の粒度）。つまり **案件は生きているのに、機械可読の現在位置だけ4日ずれている。**

## 実測A（2026-08-30・紐づけの状況。対象取り違えの前に測った分・数値自体は有効）

- 「v51helen」の正式名は **HELEN-REPRO v5.1**。wiki 側の正本は
  `wiki/builds/gf2-helen-repro-v51-run.md`（実行記録）と
  `wiki/builds/gf2-helen-repro-v51-handoff.md`（引き継ぎ）。
- wiki 内で helen に言及するページは **54枚**（builds 23 / analyses 23 / sources 4 /
  concepts 1 / _attachments 2）。
- そのうち **他ページから1度もリンクされていないページが16枚**。
  例: `wiki/builds/gf2-character-repro-pipeline.md`（工程の親のはずのページ）、
  `wiki/analyses/gf2-helen-body-shape-variants-20260829.md`、
  `wiki/analyses/gf2-helen-plan-audit-design-20260829.md`。
- **helen（および gf2 プロジェクト）の entity ページは存在しない。** `wiki/entities/` に該当なし。
- `index.md` は 1449行。helen 関連は **4つの別セクションに分散**（3D資料化 / Builds / Analyses ほか）し、
  各セクション内は**追記順**。プロジェクト単位の塊になっていない。
- `wiki/analyses/projects-dashboard.md` は **2026-07-09 が最終更新で、helen の記述がゼロ**。
  進行中プロジェクト一覧として機能していない。
- 実作業フォルダは
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz`
  （**ファイル 13,602 個**、直下に 00_source〜07_futa-helen と reports）。
  事実上のハブは `README-ja.md` だが **最終更新 2026-07-24** で、8月に作った
  `06_repro-v51` / `07_futa-helen` の記述が無い。

## 問題の言語化（この段階の私の整理・要承認）

1. **入口が無い。** 「HELEN-REPRO v5.1 を再開して」と言われた新しいセッションが、最初に読むべき
   1枚が決まっていない。今は grep で 54枚を掘り当てるところから始まる。
2. **束ねる単位が「カテゴリ」で「プロジェクト」ではない。** index.md は source/build/analysis で
   割っているため、1案件の資料が構造的に分散する。
3. **リンクが放射状でなく点在。** 16枚が孤立。リンクを辿る前提が成立していない。
4. **wiki の外（13,602 個の実ファイル）との橋が古い。** wiki 側から実フォルダの現況へ辿れない。
5. **鮮度の判定手段が無い。** どれが現行でどれが超過去かを、ページを開かずに判別できない。

## 武田さんの考え（2026-08-30・カード回答）

> 承認しません。選択肢1と2を選びたいです。

→ 「新しい1枚を作る」か「既存の正本に節を足す」かの二択ではなく、**両方**という指示。
片方だけでは、①ハブから出ていく道（案1）か ②既存ページからハブへ戻る道（案2）の
どちらかが欠ける。両方やれば双方向になる。

## 実測C（2026-08-30・武田さんの指示「まず対象の全容を先に測ってほしい」への回答）

対象は `06_repro-v51`（HELEN-REPRO v5.1）。全文は
`wiki/_attachments/project-hub-index/20260830-helen-repro-v51-overview.html`。

- 規模: **6,193ファイル / 6.2GB**（scripts 258 / logs 241 / ledger 113）。親フォルダ 13,602 の半分弱。
- 現在位置: `current_step: E` / **40回目** / completed A〜E / **GATE 13 PASS + 1 FAIL(G10)** / history 113件。
- 成果物 `blends/helen-h0157-repro.blend` は **2026-08-25 17:29 / 19.3MB / SHA `04ef8b79…`**（wiki 記録と一致）。
- 止まっている理由は4種類（原作入力の回収不能 / 原因未特定の欠陥 / 武田さんの4択待ち /
  LLM 実行可能な残り3件）。さらに手前に 2026-08-29 の「縮小計画のレビュー待ち」。
- **記録の分裂**: 実データ最終更新 **2026-08-26 22:49**、`run-state.json` は同日 **20:55** で停止、
  wiki 側の引き継ぎ4枚は **08-27〜08-29**。8-27 以降の4日間、成果物は1バイトも動いていない。

## 決まったこと

- **案1と案2を両方採る**（2026-08-30 武田さん指示）。ハブを新設し、かつ既存ページ側にも手を入れる。

## まだ決まってないこと

- 既存ページ側に置くのを **ハブへの1行ポインタだけ**にするか、**一覧そのものを複製**するか。
  → 私の宣言: **ポインタだけにする**。複製は二重管理になり、必ず片方が古くなる
  （README-ja.md が 2026-07-24 で止まったのと同じ失敗）。違えば直してほしい。
- ハブの中身を **手で書く**のか、**スクリプトで生成する**のか（後者なら何を根拠に紐づけるか）。
- 実フォルダ側（13,602 個）をどこまでハブに載せるか。
- 既存 54枚を遡って紐づけるか、今後の案件からにするか。

## 捨てた案と理由

（まだ無し）

## 直した記録

- 2026-08-30 `20260830-project-hub-problem.html` の対象取り違えを訂正（冒頭に訂正欄、問題4 を
  正しい対象で測り直したものへ差し替え）。数値のうち wiki 全体を対象に測った分はそのまま有効。

## 未了・障害

- **成果物 Inbox への申告が通らない。** `python3 tools/inbox.py add ...` が brainstorm 封鎖に
  引っかかる（`tools/inbox.py` を成果物への書き込みと判定）。これは既知の課題1（封鎖側のパス抽出）
  とは別の、**呼び出すスクリプト自体を書き込み対象と誤判定する**症状。
  `20260830-helen-repro-v51-overview.html` は **Inbox 未申告**。

## セッションメモ（子）

（まだ無し）

## 再開の入口（実パス）

- このメモ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md`
- 説明用HTML: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-project-hub-problem.html`

## 実装への申し送り

（承認前。未記入）

## 関連リンク

- [[gf2-helen-repro-v51-handoff]] — `wiki/builds/gf2-helen-repro-v51-handoff.md`
- [[gf2-helen-repro-v51-run]] — `wiki/builds/gf2-helen-repro-v51-run.md`
- [[brainstorm-gf2-dusevnyj-bikini-to-helen]] — `wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
