---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-01
page_kind: current-state
project_id: gf2-helen-swimsuit
project_name: Helen 水着化（Dusevnyj の上衣を Helen の体へ）
machine_sections: [2, 6, 7]
llm_sections: [1, 4, 5]
state_source: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-state.json
generated_at: 2026-09-01
human_view: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260901-projects-current-state.html
sources: []
---

# Helen 水着化（Dusevnyj の上衣を Helen の体へ） — 現在位置

> [!info] このページの使い方
> **新しい会話は、この1枚だけを読めば再開できる**ことを目指したページです（2026-09-01 作成）。
> 節2・6・7 は **機械が書く区画**。`tools/current_state.py` が書き直します。手で書き換えないでください。
> 節1・4・5 は **LLM が会話から書き起こす区画**。節3 は両方が書きます。
> 武田さんが見る面は `human_view` の一覧ページです。

## 節1 いま何をしているか（LLM が書く・3行以内）

General（基本の形）での検証を進めている段階。判定は **General で4項目・Flat で6項目が不合格**。
記録を1本に持たない案件のため、状態は同フォルダの各 json に分かれている
（この案件の `run-state.json` は共通の欄の受け皿として 2026-09-01 に新規作成した）。

## 節2 現在位置（機械が書く）

<!-- MACHINE:section2:start -->
| 項目 | 値 | どこから来たか |
| --- | --- | --- |
| 記録の最終更新 | 2026-09-01 12:08 | その場で測った（. の最新） |
| いまの工程 | 2026-08-31 夕（胸を General へ・腕を表示へ。明言 S001 / S003） | visible-set-swimsuit.json の updated |
| 次にやること | **未設定** | 値として書かれている |
| 止まっているもの | 6項目: note / V3 / V4 / W7 / W8 / W11 | build-exception.json の outstanding_check_failures |
| 記録ファイル | 2026-09-01 12:08 / 3,104 バイト | `run-state.json` |

**この節の断り書き**

- 記録の最終更新: この案件は記録を1本に持たないため、フォルダ内で最も新しいファイルの時刻を機械が測る
- いまの工程: 工程名ではなく状態の記述。この案件は工程番号を持っていない
- 次にやること: 機械が読める『次にやること』の欄は無い。正本は source_of_truth の brainstorm メモ
- 止まっているもの: 同ファイルが『放置ではなく、次の承認事項として残っているもの』と自称している

**この節は機械が書いています**（生成 2026-09-01 12:08）。手で書き換えないでください。
<!-- MACHINE:section2:end -->

## 節3 止まっている理由（機械と LLM・1行1件。片付いた行は消える）

| 何が止まっているか | 種別 | 状態 |
| --- | --- | --- |
| V3 胴体帯の「肌＋水着」割合 | 検査の不合格 | 0.908（合格線 0.98）。原因は長手袋が肩まで来ていること（明言 S003 で腕を表示にした結果） |
| V4 接触 | 検査の不合格 | 0.164（下限 0.313）。原因はカップの小ささ。最深は −36.31mm で上限の内側 |
| W7 ひも先端の位置 | 検査の不合格 | ヘレンの Neck_M より +7.3mm。ドナーは +28.7mm で差 21.5mm（上限 20.0） |
| 判定 General | 不合格4件 | G4a中央 / G4b深い / G9b二面角 / G9a厚み |
| 判定 Flat | 不合格6件 | 上記＋G3b / G4c |

**出典**: `build-exception.json: outstanding_check_failures`（同ファイルが「放置ではなく、
次の承認事項として残っているもの」と自称）／`judge_general.json` / `judge_flat.json` の `fails`。

## 節4 次の選択肢（LLM が書く・武田さんが選ぶ）

**機械が読める「次にやること」の欄がこの案件にはありません。**
正本は brainstorm メモ `wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
（`common.source_of_truth` に実パスあり）。

記録から読み取れる範囲では、上の V3・V4・W7 が**次の承認事項として残っている**。

**この節は空欄のままにしてあります。** 推測で候補を作ると、helen で起こした
「候補を作り出す」事故を繰り返すためです。正本メモを読んだうえで書き起こす必要があります。

> [!warning] この節の禁止
> 正本の記述を**増やしても減らしても言い換えてもいけない**。並び順と読みやすさの調整までとする
> （2026-09-01 に helen で、候補を1つ落として1つ作り出す事故を起こしたため）。

## 節5 武田さんの判断（LLM が書く・古いものは失効させる）

| いつ | 判断 | 状態 |
| --- | --- | --- |
| 2026-08-30 | **S001**「最終的に欲しいのは flat の状態にレンダリングされた水着。しかし General という基本的な形にレンダリングできないなら flat にできるはずがないので、現段階では検証として General を進める」 | 有効 |
| 2026-08-31 | **S003** 腕を表示にする（V3 の不合格の原因になっている） | 有効 |
| 2026-08-31 | 骨の角度の扱い: 原因の特定と機械化を承認。**扱いそのものの是非は次の承認事項** | 有効 |

**出典**: `explicit-statements.json`（武田さんの明言を id 付きで貯めるファイル）／`bone-angle-handling.json: approved_by`。

## 節6 関連ファイル（機械が書く・実パス）

<!-- MACHINE:section6:start -->
| 何 | 規模・最終更新 | その案件の現在位置 |
| --- | --- | --- |
| 作業フォルダ | 86 ファイル / 2026-09-01 12:08 | 工程 2026-08-31 夕（胸を General へ・腕を表示へ。明言 S001 / S003） ／ 記録 2026-09-01 12:08 |
| 下見レンダ | 48 ファイル / 2026-08-31 17:00 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/blend-probe` |
| 正本メモ（brainstorm） | 328,175 バイト / 2026-09-01 00:06 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md` |
| 本体（helen 原作再現） | 6,193 ファイル / 2026-09-01 11:49 | 工程 E ／ 記録 2026-08-26T20:55:15+09:0… |

**網羅である根拠はありません。** ここに出るのは `common.related` に書かれたものだけで、機械が自分で探した結果ではありません（2026-09-01 武田さんの指摘）。

**この節は機械が書いています**（生成 2026-09-01 12:08）。
<!-- MACHINE:section6:end -->

## 節7 履歴の所在（機械が書く・全文は読ませない）

<!-- MACHINE:section7:start -->
| 記録 | 量 | 場所 |
| --- | --- | --- |
| 武田さんの明言 | 3,381 バイト | `explicit-statements.json` |
| 未処理の検査失敗 | 2,952 バイト | `build-exception.json` |
| 解決台帳 | 11,261 バイト | `resolution-ledger.json` |
| レビュー所見 | 12,830 バイト | `review-findings.json` |

**全文は読ませません。** どこを見ればよいかだけを示します。

**この節は機械が書いています**（生成 2026-09-01 12:08）。
<!-- MACHINE:section7:end -->

## 未確認・限界

- **このページは 2026-09-01 に新規作成した1枚目です。** 節1・4・5 は記録から読み取って書いており、
  会話で決まった内容が抜けている可能性があります。
- 節6 が網羅である根拠はありません。`common.related` に書かれたものだけが出ます。
- 機械区画の値は、`tools/current_state.py` を走らせた時点のものです。

## 関連リンク

- 仕組みの正本メモ — `wiki/analyses/brainstorm/project-hub-index/_index.md`
- 4案件の一覧 — `wiki/_attachments/project-hub-index/20260901-projects-current-state.html`
