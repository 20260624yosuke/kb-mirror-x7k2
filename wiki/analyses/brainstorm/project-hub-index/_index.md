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

## 実測（2026-08-30・すべて実ファイルを直接読んだ）

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

## 決まったこと

（まだ無し）

## まだ決まってないこと

- ハブを **1案件1枚の新しいページ種別**にするのか、既存の build 正本ページに節を足すだけにするのか。
- ハブの中身を **手で書く**のか、**スクリプトで生成する**のか（後者なら何を根拠に紐づけるか）。
- 実フォルダ側（13,602 個）をどこまでハブに載せるか。
- 既存 54枚を遡って紐づけるか、今後の案件からにするか。

## 捨てた案と理由

（まだ無し）

## 直した記録

（まだ無し）

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
