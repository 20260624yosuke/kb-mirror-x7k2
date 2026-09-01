---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-01
page_kind: current-state
project_id: gf2-helen-repro-v51
project_name: HELEN-REPRO v5.1（ドルフロ2のゲームコードから Helen を原作のまま再現）
machine_sections: [2, 6, 7]
llm_sections: [1, 4, 5]
state_source: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
state_source_updated: 2026-08-26T20:55:15+09:00
generated_at: 2026-09-01
human_view: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/gf2-helen-repro-v51/20260901-helen-current-state.html
sources: []
---

# HELEN-REPRO v5.1 — 現在位置

> [!info] このページの使い方
> **新しい会話は、この1枚だけを読めば再開できる**ことを目指した雛形です（2026-09-01・型の見本として形の承認済み）。
> 節2・6・7 は **機械が書く区画**（`run-state.json` と実ファイルの実測）。LLM は手で書き換えません。
> 節1・4・5 は **LLM が会話から書き起こす区画**。節3 は両方が書きます。
> 武田さんが見る面は `human_view` の HTML です。この md は機械と LLM が読む正本です。

> [!warning] 雛形であることの明示
> **これは1枚目の試作で、2026-09-01 に「型の見本」として形の承認を得ました。** 既存の引き継ぎ4枚が「自分が正本」と主張している状態は、
> **まだ直していません**（別タスクとして分離済み）。この計画の範囲では、
> 既存4枚を旧扱いにする書き換えは行いません。

## 節1 いま何をしているか（LLM が書く・3行以内）

原作入力（照明・階調表・衣装材質）の回収が止まっており、**工程Eで停止中**。
LLM が手を動かせる残りは実質0件で、次は**武田さんが4つの候補から1つ選ぶ**段階。
成果物の blend は 2026-08-25 以降1バイトも変わっていない。

## 節2 現在位置（機械が書く）

<!-- MACHINE:section2:start -->
| 項目 | 値 | どこから来たか |
| --- | --- | --- |
| 記録の最終更新 | 2026-08-26T20:55:15+09:00 | この記録の last_updated |
| いまの工程 | E | この記録の current_step |
| 次にやること | 6項目: 1_原作入力の回収 / 2_回収後の機械処理 / 3_見た目の報告 / 4_工程の停止点 / 5_次の候補(武田さんの選択待ち) / 参照 | この記録の next_action |
| 止まっているもの | 4件: マント cloth1_lod0_Fight の再現 / 胸・スカートの伸びた辺（胸46本・スカート156本） / 顔の伸びた辺（122本） / 胸・背中の火傷状の筋 | この記録の blocked |
| 成果物 | 2026-08-25 17:29 / 19,295,445 バイト / `04ef8b79b3fa5b64…` | `helen-h0157-repro.blend` |
| 記録ファイル | 2026-09-01 11:49 / 228,544 バイト | `run-state.json` |

**この節は機械が書いています**（生成 2026-09-01 11:50）。手で書き換えないでください。
<!-- MACHINE:section2:end -->

## 節3 止まっている理由（機械と LLM・1行1件。片付いた行は消える）

| 何が止まっているか | 種別 | 状態 |
| --- | --- | --- |
| 原作入力（照明・階調表・衣装材質）の回収 | 外部要因 | CDN 直接取得は 403 の区別がつかず確定不能。残る道は backup volume の読める化か、将来の配信待ち |
| G10（silkstock の ramp 割当て） | 関所の不合格 | renderer から material への対応が無く、推測は禁止。**工程G・完了へは進めない** |
| 胸・スカートの伸びた辺（胸46本・スカート156本） | 原因未特定 | 骨の親の推定ミス・切り詰め・畳まれた骨への追従はすべて実測で除外済み |
| 次の一手の選択 | **武田さん待ち** | 節4 の4候補から1つ |

**片付いたので消した行**（追記のみにしないための記録）

- ~~胸下のイボイボ~~ — 2026-08-18 に武田さんの明示で**完了**。`run-state.json` には
  古い「未着手」が残っており、2026-08-30 に私がそれを拾って誤報告した。
- ~~鎖のジャギー~~ — f34 で**対応済み**。ただし原作の画素範囲までは未達。
- ~~きらきら層~~ — **原作準拠では実装不能**と判明（材質19,869件が gitter を参照せず、
  衣装材質672件は全部 `_UseGlitter=0.0`）。材質バンドルの入手待ちへ移した。

## 節4 次の選択肢（LLM が書く・武田さんが選ぶ）

`run-state.json: next_action` の「5_次の候補(武田さんの選択待ち)」より。

| 候補 | 中身 | 選ぶと失うもの |
| --- | --- | --- |
| A | SH から Blender ライティングを再構成する別計画を立てて承認を取る | 別計画の作成と承認に1往復。原作一致の保証は無い（推定を含む） |
| B | backup volume の読める化を試す（欠損15件＋root の回収） | 成功しなければ時間だけが減る。FDA の確認が未了 |
| C | 工程F（武田さんの目視判断）へ進む | 原作入力が欠けたままの見た目で判断することになる |
| D | ここで凍結し、派生案件（水着化・ふたなり化）へ移る | 原作再現は未完のまま止まる |

**注**: この表は `run-state.json` の候補を LLM が読みやすく並べ直したものです。
文言の正本は `run-state.json` 側。

## 節5 武田さんの判断（LLM が書く・古いものは失効させる）

| いつ | 判断 | 状態 |
| --- | --- | --- |
| 2026-08-18 | 胸下のイボイボは**完了**とする | 有効 |
| 2026-08-20 | （`run-state.json: 武田さんの決定_2026_08_20` に記録あり） | 有効・要約は原文を参照 |
| 2026-08-31 | helen の整理は**後で別エージェントへ投げる**。整合性を崩すのは禁止 | 有効 |
| 2026-08-31 | KB 全体の仕組み化（軸②）を**先に**完成させる | 有効 |

**失効**

- ~~2026-08-30「LLM が手を動かせる残りが3件ある」~~ → 私の誤読。実行可能な残りは実質0件。

## 節6 関連ファイル（機械が書く・実パス）

<!-- MACHINE:section6:start -->
| 何 | 規模・最終更新 | 実パス |
| --- | --- | --- |
| 作業フォルダ | 6,193 ファイル / 2026-09-01 11:49 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51` |
| 成果物フォルダ | 148 ファイル / 2026-08-26 15:45 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends` |
| ふたなり化（派生） | 298 ファイル / 2026-09-01 11:48 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/07_futa-helen` |
| キャラ抽出の安定化（派生） | 10,153 ファイル / 2026-09-01 11:48 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract` |
| 水着化（派生） | 86 ファイル / 2026-09-01 11:48 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit` |
| 実行記録（wiki） | 156,467 バイト / 2026-08-27 18:41 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md` |
| 引き継ぎ（wiki） | 152,873 バイト / 2026-08-27 18:41 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md` |

**網羅である根拠はありません。** ここに出るのは `common.related` に書かれたものだけで、機械が自分で探した結果ではありません（2026-09-01 武田さんの指摘）。

**この節は機械が書いています**（生成 2026-09-01 11:50）。
<!-- MACHINE:section6:end -->

## 節7 履歴の所在（機械が書く・全文は読ませない）

<!-- MACHINE:section7:start -->
| 記録 | 量 | 場所 |
| --- | --- | --- |
| スクリプト | 323 本 | `scripts` |
| 実行ログ | 243 本 | `logs` |
| 台帳 | 621 本 | `ledger` |
| 実行の全文（wiki） | 156,467 バイト | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-run.md` |
| 引き継ぎの全文（wiki） | 152,873 バイト | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md` |

**全文は読ませません。** どこを見ればよいかだけを示します。

**この節は機械が書いています**（生成 2026-09-01 11:50）。
<!-- MACHINE:section7:end -->

## 未確認・限界

- **節6「関連ファイル」が網羅である根拠はありません（2026-09-01・武田さんの指摘）。**
  ここに並べたパスは、私が既に知っているものを並べたものです。ファイル数と最終更新は実測ですが、
  **「他に関連フォルダが無い」ことは確かめていません。** 網羅性は、完成条件②の自動生成を
  入れるときに機械で担保します。この雛形は**型の見本**としての位置づけです。
- ~~これは1枚目の雛形で、形の承認を得ていません。~~ → **2026-09-01 に形の承認を取得**
  （武田さん「これはあくまでも型の見本ということであるのなら、次に進んでいい」）。
- 節2・6・7 は今回**手で書いた測定結果**です。自動生成の仕組みはまだありません
  （完成条件②はこれから）。次に触るときは値が古くなっている可能性があります。
- 既存の引き継ぎ4枚の「自分が正本」という主張は**まだ残っています**。
- 節5 の 2026-08-20 の判断は、原文の要約を作らず所在だけを示しています
  （武田さん本人の言葉を要約して曲げないため）。

## 関連リンク

- [[gf2-helen-repro-v51-handoff]] — `wiki/builds/gf2-helen-repro-v51-handoff.md`
- [[gf2-helen-repro-v51-run]] — `wiki/builds/gf2-helen-repro-v51-run.md`
- 仕組みの正本メモ — `wiki/analyses/brainstorm/project-hub-index/_index.md`
