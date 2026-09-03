---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-03
parent: ../_index.md
---

# 2026-09-03 U1独立検証受領書（やり直し分）

立場は独立検証：実装者の成功報告を根拠にせず、候補・試験・実コードを直接読んだ。
基準は新版の現在位置ページ（`ec641b37...`）＋他11入力の固定版＋環境3ファイル。

## 確認1：stage成果物の再読込

- 報告4点（u1-implementation-report / static-validation / writer-scan-summary / candidate-sha256）は実在し、内容は実装者の申告どおり。
- 候補25件のSHA照合を自分の手で実行：24件一致、1件不一致。
- 不一致の1件は `stage/project/quality-gate.json` で、原因は自分が行った基準更新（旧 `a04df291...` → 新 `89b0c6c6...`）。
  内訳は kb-current の SHA・サイズ（19943→22059）・時刻（2026-09-01→2026-09-03T12:20:20+09:00）と集合SHA（旧 `212b9b07...` → 新 `48a1ebb2...`、候補の guard と同一式で再計算）。
- 旧報告書（candidate-sha256.json）の当該1行は証拠保全のため書き換えない。新旧の対応は本受領書に記録する。
- 正規の `quality-gate.json`・共有hooks・Blend本体には触れていない。

## 確認2：20試験の再実行

- 自分の手で実行。最初は9失敗＋1エラーだった。
- 原因は旧基準の残留（`EA_KB_SNAPSHOT_STALE: kb-current` → 解消後に `set_sha256 mismatch`）。実装者の報告が誤りだったのではなく、基準が9月3日に変わったため。
- 上記の基準更新後に20件すべてPASS。試験の中身は guard・state・hook・rollback を実際に呼び出す作り（例：`test_state_rejected_blocked...` は transition/status/technical_stop を実実行）。

## 確認3：75件の分類（追加21件の判定）

- 75件全件の実コードを静的照合（Blend保存命令の有無）し、代表5件（f110 / f27 / f17 / f81 / f137）を精読した。
- 追加21件はいずれも直接のBlend保存（`save_as_mainfile` / `save_mainfile`）を持たない。内訳は、Blenderを別工程で起動して読み取る・一時コピーで隔離実行する・描画PNGやJSONを出す、検証用の作り。
- 走査器の `blender_subprocess` / `blend_copy_or_move` 基準が広すぎて、検証用まで writer として拾う誤検出になっている。
- 判定：21件は writer ではなく reader/renderer として再分類が必要。自動昇格は off のまま（計画どおりで正しい）。

## 確認4：試験の質の注記（Minor・受領可否には影響しない）

- `test_p3a_synthetic_does_not_clear_real_g10` は fixture の自己申告値（`synthetic` / `audit_expected` / `may_promote_real_g10`）を読み取る作り。
  実 G10 blocked の実測も併記しているため合格扱いは維持するが、将来の改善点として記録する。

## 未確認（本受領書の限界）

- 状態ファイルへの直接書き換えに対する改ざん検知（guard の transition 自体は証拠を要求することを試験で確認済み）。
- 内側スクリプトの推移的保存の全件確認（例：f81 が呼ぶ f79 等）。f81 自体の判定には影響しない。
- ブラウザーでのHTML表示確認。

## 判定：受領不可（正規導入へは進めない）

理由は1点：21件の誤分類の確定が残るため。品質ゲートの停止（`EA_WRITER_CLASSIFICATION_REVIEW_REQUIRED`）は正常動作として維持する。
前回（Codex側）の「Majorで停止」と同じ結論に、実ファイルの証拠つきで独立に到達した。

## 要求する限定修正（実装者の役割）

候補 `writers.json` の21件を non-writer（reader/renderer）へ再分類し、registry と gate を再実行すること。
修正後に本検証者が再検証する。正規導入・Blend変更・U2/U3は含まない。

## 証拠の境界

本受領書はU1検証の結果であり、監査導入・実hook到達・原作一致・Blend完成の証拠ではない。

## 追記：限定修正の実施と再検証（2026-09-03・承認済み）

カードで「この会話で限定修正」と「はい、この選択でよい」を受領して実施した。範囲は一時作業場（stage）のみ。正規の台帳・共有設定・Blend本体は無変更。

- 受領書 `stage/project/06_repro-v51/audit/writer-review-receipt.json` を新規作成（SHA `398acda212504b07248817f235f98f2779158c0619afbfbc40b623e3ee998b90`）。75件の1件ずつの判定つき：54件維持（P0のまま）＋21件は non-writer（reader/renderer/verifier）。自動昇格は off のまま。
- `stage/.../audit/state.json` の writer分類欄を accepted へ（証拠SHA・検証者ID・履歴行つき）。
- `stage/.../audit/review-findings.json` の EA-P0-005（writer分類の独立確認待ち・Critical）を closed へ（証拠SHA・検証者IDつき）。他のMajor所見は手をつけていない。
- 再実行の結果：品質ゲート plan が PASS（修正前は `EA_WRITER_CLASSIFICATION_REVIEW_REQUIRED` の所期停止 → 一時 `EA_OPEN_CRITICAL_FINDING` → P0-005クローズで PASS）。自動試験20件も再実行して PASS。
- 判定の更新：分類未確定は解消。正規導入の可否は別の承認（導入前後のSHA記録と復元試験つきの原子操作）に委ねる。
