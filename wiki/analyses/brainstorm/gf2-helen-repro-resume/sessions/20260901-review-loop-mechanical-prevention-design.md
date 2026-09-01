---
type: analysis
title: レビューループの機械防止設計
status: draft-unapproved
confidence: high
evidence_level: source-backed+inferred
last_reviewed: 2026-09-01
parent: ../_index.md
---

# レビューループの機械防止設計

## 目的

計画を成果物作成へ進めるためのレビューが、レビュー結果の書戻しによって入力SHAを変え、同じ意味内容の再レビューを繰り返すことを機械的に禁止する。担当者が「もう触らない」と注意するだけの運用は採用しない。

## 今回観測した直接原因

1. 一本化計画とcurrentをレビュー入力にした。
2. 3回目レビューがCritical 0 / Major 0 / Minor 0になった。
3. その合格結果をレビュー入力であるcurrentへ書いた。
4. currentのSHAが変わり、同じ意味内容でも最終再レビューが必要になった。

これはモデル性能や利用上限ではなく、レビュー出力からレビュー入力へ戻る辺を許した依存関係の問題である。

## 機械的不変条件

- `review-manifest` はレビュー開始前に、入力の絶対パス・役割・SHA-256を固定する。
- receipt（レビュー受領書）はmanifestの入力集合の外にだけ生成する。
- review開始後、入力集合へのwrite / edit / rename / replaceをguardが拒否する。
- reviewerはreceiptだけを書ける。合否や指摘件数をcurrent・planへ複写しない。
- 合格receipt発行後、同じmanifest SHAへのreview再実行を拒否する。
- 入力変更が必要なら自動再レビューしない。`amendment-required`で停止し、変更理由・変更対象・失うものを提示してユーザーの別承認を要求する。
- 承認後だけ新manifest IDを発行する。旧reviewは履歴として閉じ、新reviewを同一ループの続きではなく別revisionとして扱う。

## このCodex環境での最小実装先

- 実装先: `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py` の `pre_tool`。これは `/Users/takedayousuke/.codex/hooks.json` のPreToolUseへ既に接続済みで、brainstorm中の `apply_patch` と `exec_command` を実際に拒否できる。
- 試験先: `/Users/takedayousuke/.codex/skills/brainstorm/tests/test_adapter.py`。既存の親メモ境界・カード・読取専用command試験へ追加する。
- 永続記録: 選択済み親の `sessions/` に `<review-id>.review-lock.json` を1件置く。中央の第二正本は作らず、対象計画と同じ親から到達可能にする。
- lockの必須欄: `schema_version`、`review_id`、`status=passed`、`receipt_path`、`receipt_sha256`、`locked_inputs[]` の絶対path・role・sha256。
- `apply_patch`: 既存lockの対象pathをAdd / Update / Delete / Moveしようとした時点で拒否する。receipt、親メモ、HTMLなどlock外は変更可能。
- `exec_command`: 読取専用commandは許可する。非読取commandがlock対象の絶対pathを引数に含む場合は拒否する。曖昧な変数・glob・親ディレクトリ指定で対象判定できない書込みは許可せず技術的停止にする。
- lock解除: ファイル削除や手書きstatus変更では解除しない。確認済み承認カードの `review_id` と、新revisionを必要とする理由をadapter stateへ結合した専用 `amend-review` 操作だけを許す。

今回の最小lock対象は、一本化revision 4、current、具体計画の3件。review receiptはlock入力ではなく終端証拠としてSHA拘束し、receipt自身の更新もpassed後は拒否する。親メモと説明HTMLはlock外なので、本題の説明完成に影響しない。

## 最小状態

`draft → frozen → reviewing → passed | findings | technical-stop`

- `passed`から`reviewing`へ戻る遷移は存在しない。
- `findings`は計画修正を自動開始しない。修正承認後に新しい`draft`を作る。
- `technical-stop`は入力内容を変えず、同じmanifestで機構復旧後に再試行できる。

## 必須拒否

| 条件 | 拒否 |
|---|---|
| frozen後に入力ファイルへ書く | `EA_REVIEW_INPUT_FROZEN` |
| receiptを入力集合内へ保存する | `EA_REVIEW_CYCLE_DETECTED` |
| passed済みmanifestを再reviewする | `EA_REVIEW_ALREADY_TERMINAL` |
| 入力変化を検出して自動で新reviewを起動する | `EA_REVIEW_AMENDMENT_APPROVAL_REQUIRED` |
| 合否をcurrent/planへ書き戻す | `EA_REVIEW_RESULT_BACKEDGE` |

## 否定試験

1. 正常: plan/currentをfreezeし、外部receiptへPASSを書き、1回で終端する。
2. 単一変異: reviewerがcurrentのstatusを書き換えようとすると拒否する。
3. 単一変異: receiptをmanifest memberへ追加しようとすると循環検出する。
4. 単一変異: passed済みmanifestをもう一度reviewしようとすると拒否する。
5. 単一変異: 入力を1バイト変えた後に自動再reviewしようとすると承認待ちで停止する。
6. 復元: 入力を元SHAへ戻すと旧receiptの有効性は維持されるが、新reviewは発生しない。
7. lock外: 親メモと説明HTMLは同じturnで更新できる。
8. shell迂回: `sed -i`、Python書込み、`mv`、`cp`、変数・globでlock対象を指すcommandを拒否する。
9. 解除: 自由文、古いカード、確認なし、別review IDでは `amend-review` を拒否する。

## 今回への適用範囲

一本化revision 4、current、具体計画、最終review receiptは現時点で固定し、HTMLと親メモだけを更新する。これは今回の手動終端であり、上記guardが環境へ実装済みという意味ではない。環境共通のguard実装、hook接続、試験は別承認が必要。

## 使わなかったもの・落とした情報

- 合格件数をcurrentへ複写する方式は捨てる。手元ではcurrentだけを読んでもレビュー合否が見えなくなり、receiptへのリンクを辿る必要がある。戻す場合も複写ではなく、currentからreceiptへの参照だけを追加する。
- 自動再レビューは捨てる。入力変更後すぐ再検証できなくなるが、成果物作成を止める無限反復を防ぐ。再開には変更の明示承認が必要。
- 既存ファイルや成果物は削除していない。

## 未決定

- Codex側の最小実装と試験を先に受け入れた後、Claude Code側の `brainstorm_guard.py` へ同じ契約を展開するか。今回同時実装はしない。
- 環境共通化の実装承認。
