---
type: build
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-01
page_kind: current-state
project_id: gf2-char-extract
project_name: ドルフロキャラ抽出の安定化（gf2-char-extract）
machine_sections: [2, 6, 7]
llm_sections: [1, 4, 5]
state_source: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/run-state.json
generated_at: 2026-09-01
human_view: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260901-projects-current-state.html
sources: []
---

# ドルフロキャラ抽出の安定化（gf2-char-extract） — 現在位置

> [!info] このページの使い方
> **新しい会話は、この1枚だけを読めば再開できる**ことを目指したページです（2026-09-01 作成）。
> 節2・6・7 は **機械が書く区画**。`tools/current_state.py` が書き直します。手で書き換えないでください。
> 節1・4・5 は **LLM が会話から書き起こす区画**。節3 は両方が書きます。
> 武田さんが見る面は `human_view` の一覧ページです。

## 節1 いま何をしているか（LLM が書く・3行以内）

Dusevnyj の固有色の調査が完了し、**対処案の承認カードを出したところで停止**（2026-08-26 深夜）。
武田さんの指摘「原作の固有色を無視しているから、よく探してほしい」への回答として、
テクスチャ自体は暖色で正常なのにレンダだけ冷色になる原因を、後処理ゼロの直出しと特定した。

## 節2 現在位置（機械が書く）

<!-- MACHINE:section2:start -->
| 項目 | 値 | どこから来たか |
| --- | --- | --- |
| 記録の最終更新 | 2026-08-26T22:45:00+09:00 | この記録の updated_at |
| いまの工程 | step2-pilot-dusevnyj-intrinsic-color-investigation-complete-post-port-proposal-awaiting-approval | この記録の current_step |
| 次にやること | 【2026-08-26 深夜】武田さんへ対処案の承認カードを出して止まる（出した）。推奨A案: セレクト画面実値port＝コンポジタ常駐でfilmicトーンマップ(v51 f138実績チェーン・filmToe0.47系)… | この記録の next_resumption_point |
| 止まっているもの | **未設定** | 値として書かれている |
| 記録ファイル | 2026-09-01 12:08 / 53,009 バイト | `run-state.json` |

**この節の断り書き**

- 次にやること: current_action は『いま何をしたか』の記録で意味がずれるため、next_resumption_point を参照する
- 止まっているもの: この案件の記録にはこの意味の欄が無い。推測で埋めていない。

**この節は機械が書いています**（生成 2026-09-01 12:08）。手で書き換えないでください。
<!-- MACHINE:section2:end -->

## 節3 止まっている理由（機械と LLM・1行1件。片付いた行は消える）

| 何が止まっているか | 種別 | 状態 |
| --- | --- | --- |
| 対処案 A/B/C の選択 | **武田さん待ち** | 承認カードは出済み（2026-08-26 深夜） |
| 品質ゲート batch / complete | 未実施 | `plan` は 2026-08-23 に PASS |

**今回の範囲から外してあるもの**: 照明・輪郭線・ramp・アニメーション・キャラ間の衣装流用。

## 節4 次の選択肢（LLM が書く・武田さんが選ぶ）

`run-state.json: next_resumption_point` の原文より。

| 候補 | 正本の文言 |
| --- | --- |
| A（推奨） | セレクト画面の実値を移植。後処理を常駐させ filmic トーンマップ（v51 f138 の実績チェーン）＋色調整（コントラスト +0.10／彩度 ×1.234）を出力 blend へ追加。**LUT は適用しない**（セレクト画面では無効のため） |
| B | ラウンジの値（ColorLookup の寄与 1.0 込み）で v51 に完全一致させる |
| C | 後処理なしの照明色補正（**原作の根拠なし・非推奨**） |

承認されたら実装 → 検証 → シート再生成 → 目視の承認カード。その後 Step3 のバッチ計画へ。

**選ぶと失うもの**: C は原作の根拠が無いため、以後「原作準拠」と言えなくなる。

> [!warning] この節の禁止
> 正本の記述を**増やしても減らしても言い換えてもいけない**。並び順と読みやすさの調整までとする
> （2026-09-01 に helen で、候補を1つ落として1つ作り出す事故を起こしたため）。

## 節5 武田さんの判断（LLM が書く・古いものは失効させる）

| いつ | 判断 | 状態 |
| --- | --- | --- |
| 2026-08-23 | 方針v2 を承認（在庫台帳 → ドライバ → パイロット2体 → バッチ。光/ramp は除外） | 有効 |
| 2026-08-23 | 計画v2.1 を承認（独立レビュー反映＋引き継ぎ運用）。**実装開始** | 有効 |
| 2026-08-24 | 監査修正「全部やる」（LOD・可視性の不具合修正／テクスチャ適用／Dusevnyj 全パーツの組み合わせ） | 有効 |
| — | **読むだけの対象**: `gf2-helen-starlit-waltz/` 配下、ゲームデータ一式 | 有効 |

## 節6 関連ファイル（機械が書く・実パス）

<!-- MACHINE:section6:start -->
| 何 | 規模・最終更新 | その案件の現在位置 |
| --- | --- | --- |
| 作業フォルダ | 10,153 ファイル / 2026-09-01 12:08 | 工程 step2-pilot-dusevnyj-intrinsic-color-investigation-complete-post-port-proposal-awaiting-approval ／ 記録 2026-08-26T22:45:00+09:0… |
| 引き継ぎ（wiki） | 111,180 バイト / 2026-08-27 18:39 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-char-extract-handoff.md` |
| 計画 | 14,003 バイト / 2026-08-24 00:24 | `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/reports/PLAN-CHAR-EXTRACT-2026-08-23.md` |
| 本体（helen 原作再現） | 6,193 ファイル / 2026-09-01 11:49 | 工程 E ／ 記録 2026-08-26T20:55:15+09:0… |

**網羅である根拠はありません。** ここに出るのは `common.related` に書かれたものだけで、機械が自分で探した結果ではありません（2026-09-01 武田さんの指摘）。

**この節は機械が書いています**（生成 2026-09-01 12:08）。
<!-- MACHINE:section6:end -->

## 節7 履歴の所在（機械が書く・全文は読ませない）

<!-- MACHINE:section7:start -->
| 記録 | 量 | 場所 |
| --- | --- | --- |
| 作業フォルダ | 10,153 本 | `.` |

**全文は読ませません。** どこを見ればよいかだけを示します。

**この節は機械が書いています**（生成 2026-09-01 12:08）。
<!-- MACHINE:section7:end -->

## 残タスク（他の会話から持ち込まれたもの）

### 2026-09-03 タイムラインの図をこの案件にも作る（武田さん指示・未着手）

**この会話ではやらない。この案件を進めるときに、武田さんと詳細を詰めてから作る。**

武田さんの言葉（2026-09-03、水着版ヘレンの会話にて）:

> 同系統はドルフロ抽出プロジェクトは複雑にそれぞれ広がってるから、今後はこのタイムライン
> 作って欲しい。ここでやるんじゃなくて、それぞれプロジェクトを進めるときに、詳細を
> エージェントと話できるように、残タスクとして記録しておいて。

- **目的**: 規模が大きくなって「いまどこにいるか」が分からなくなるのを防ぐ（認知負荷を下げる）。
- **形**: 横軸が日付、縦に3段。同日の承認で「C: 3段すべて」が選ばれている。
  1. 武田さんの判断（承認・差し戻し）
  2. 成果物が作り直された回
  3. 動く検査の本数（累計）
- **見本**: `wiki/_attachments/helen-swimsuit-status/20260903-choker-restored-and-exception-scope.html`
  の 1 章。図はインライン SVG で、クリックすると拡大する
  （`wiki/_attachments/helen-swimsuit-status/design-system/figure-zoom.js`）。
- **機械の関所**: `tools/doc_timeline_check.py`。`doc-status: current` の説明ページに
  `<meta name="doc-timeline" content="lanes:3">` とタイムラインの図が無いと、
  `tools/prose_guard.py` が**書き込みの時点で止める**。検出力は `tools/plan_audit.py` の A15。
  いまの対象フォルダは `wiki/_attachments/helen-swimsuit-status` **だけ**なので、
  この案件で使うときは `doc_timeline_check.ATTACH` の対象フォルダを増やすところから始める。
- **詰めること（武田さんと相談する）**: この案件で「作り直した回」「検査の本数」に
  当たるものが何か。案件によっては別の段のほうが役に立つ可能性がある。

## 未確認・限界

- **このページは 2026-09-01 に新規作成した1枚目です。** 節1・4・5 は記録から読み取って書いており、
  会話で決まった内容が抜けている可能性があります。
- 節6 が網羅である根拠はありません。`common.related` に書かれたものだけが出ます。
- 機械区画の値は、`tools/current_state.py` を走らせた時点のものです。

## 関連リンク

- 仕組みの正本メモ — `wiki/analyses/brainstorm/project-hub-index/_index.md`
- 4案件の一覧 — `wiki/_attachments/project-hub-index/20260901-projects-current-state.html`
