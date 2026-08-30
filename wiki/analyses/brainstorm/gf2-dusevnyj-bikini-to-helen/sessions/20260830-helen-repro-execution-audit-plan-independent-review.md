---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-30
reviewer: gpt-5.6-sol
reasoning_effort: medium
review_mode: independent-read-only
parent: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md
reviewed_plan: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md
---

# Helen 原作再現 実行保証計画 — 独立レビュー 2026-08-30

## レビュー条件

- モデル: `gpt-5.6-sol`
- 推論強度: `medium`
- 計画の結論を支持・否定するよう誘導せず、計画と実コード・JSON・ログを直接比較した。
- 読み取り専用。計画、プロジェクト、Wikiツールの編集はしていない。

## 判定

**修正後に開始可能。現状のまま実装開始は不可。**

S6・S8・G10の対象選定、G10を推定で埋めず `blocked` にする判断、内部ゲートと原作一致を分ける原則は
妥当。一方、中心目的の二重接続には確定した迂回経路があり、`a10_quality_gate.py` は既存の
`quality-gate.json` を破壊的に再生成する危険がある。

## Findings（重大度順）

### Critical 1 — `execution_audit` を削除するとcomplete監査を迂回できる

- 計画: `execution_audit` がある場合だけ共有品質ゲートから監査を呼ぶ一方、manifest欠落をFAILにするとしている。
- 実コード: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/project_quality_gate.py:40`、`:137`、`:195`
- 影響: 任意欄の削除・誤生成・手修正でHelen専用監査が静かに無効になる。
- 必要修正: Helen案件であることを任意欄以外から判定し、対象manifestでは `execution_audit` を必須化する。
  欠落専用の機械可読エラーIDと、非対象プロジェクトの互換試験を追加する。

### Critical 2 — `a10_quality_gate.py` が現行品質台帳を全面上書きする

- 実コード: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/a10_quality_gate.py:26`、`:206`
- 現行台帳: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/quality-gate.json:61`、`:201`、`:210`、`:215`
- 影響: `execution_audit` を足すためa10を実行すると、後から追加された公式2Dアート・原作動画・監査履歴・
  不在主張ゲート・個別ゲートを失う。
- 必要修正: 再生成ではなく検証付きマージへ変更するか、固定テンプレートを完全同期する。
  実行前後で `execution_audit` 以外が同一であることをSHAと構造比較で必須確認する。

### Major 1 — 候補Blend書出し前のguardは既存writerを強制できない

- 既存writer例:
  - `.../06_repro-v51/scripts/f153_specular_candidate.py:221`
  - `.../06_repro-v51/scripts/f162_aces_rrtodt_transfer.py:602`、`:623`
  - `.../06_repro-v51/scripts/f121_unlit_ramp_candidate.py:194`
  - `.../06_repro-v51/scripts/f109_candidate_blend_export.py:67`
- 影響: guardを呼ぶ将来writerだけが止まり、既存writer・複製・新規writerは直接 `save_as_mainfile` できる。
- 必要修正: S6・S8・G10の既存writerと正式採用経路を先に全列挙する。guard自身が入力とwriterを実測SHA化し、
  許可出力パスを返し、書出し後の出力SHAまで記録する二段階契約にする。

### Major 2 — G10がblockedになるだけでは監査の検出力を証明できない

- 現状証拠: `.../06_repro-v51/logs/gate-results.json:212`、`:218`、
  `.../06_repro-v51/scripts/e01_gates.py:359`、`:401`、プロジェクト `quality-gate.json:188`
- 影響: G10は既にFAILし、共有品質ゲートのcompleteも別理由でFAILする。audit_guardを呼ばなくても期待結果が出る。
- 必要修正: 同一fixtureで、監査対照PASS、hook削除、古い証拠、別Blend、open finding、guard許可後だけの
  一時writer出力を1項目ずつ差分試験する。監査固有エラーIDで落ちたことを確認する。
  G10 blockedは実環境のfail-closed確認として残すが、ハーネス通し試験合格とは分離する。

### Major 3 — 状態遷移・鮮度・finding契約が実装可能な精度に達していない

- 実証拠例: `.../06_repro-v51/logs/f128-audit.json:2`、
  `.../06_repro-v51/logs/f152-visual-gates.json:2`、
  `.../06_repro-v51/scripts/f152_visual_compare_gates.py:159`、`:220`
- 不足: 各遷移の肯定証拠、戻り遷移、blocked解除、contestedの決着者、契約とstateの優先順位、
  古さの定義、findingのclose証拠。
- 必要修正: 各状態の必要フィールド・直接証拠・許可直前状態・失効条件・禁止次状態を表にする。
  鮮度は原則として入力・writer・検査器・原作資料・Blendの全SHA一致で判定する。

### Major 4 — 現行証拠の版拘束が弱い

- `f128`・`f152` は現行Blend SHA `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` と一致。
- `gate-results.json` はBlendパスしか持たずSHAがない。2026-08-22の結果で、Blendは2026-08-25更新。
- `f166` はstatusがdoneでも、結果生成後にscriptが更新され、既存正本も現行結果でないと明記している。
- 必要修正: P0で証拠を `current / stale / unbound` に機械分類する。SHAを持たない既存JSONは
  同一パスでも `unbound` とし、completeの根拠にしない。

### Major 5 — 「既存技術計画を変えない」が既存section 9.6と衝突する

- 既存正本: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md:451`、`:453`、`:462`、`:470`
- 衝突: 既存section 9.6は新state/schemaを作らずrun-stateを修復しf166を再走査する。新計画は新state/schemaを作り、
  run-stateを読み取りのみ、f166を変更・再実行しない。
- 必要修正: section 9.6の状態管理部分を後日の決定が置き換えたのか、追加監査として別順序で残るのかを明示し、
  実行順と再開点を一つにする。

### Minor 1 — 共有品質ゲートの回帰試験が変更対象にない

- 既存試験: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/tests/test_project_quality_gate.py:59`、`:67`
- 必要修正: 非対象manifest互換、対象manifest欠落、guard import失敗、guard異常終了、各phase、監査固有IDを追加する。

## 問題なしと確認した項目

- 計画が参照する主要実体は存在する。
- 現行Blend SHAはf128・f152の記録と一致する。
- G10は11枚中7枚、最大誤差0.000390651…で、既存条件どおりFAILしている。
- f166旧結果が現行scriptより古いという前提は正しい。
- 共有品質ゲートは実行確認で `plan=PASS`、`complete=FAIL`。
- 内部ゲート合格を原作一致・実機確認・運用可能と言い換えない境界は適切。
- G10を推定で埋めず `blocked` にする判断自体は現状証拠に合う。

## 次の修正で最低限必要なこと

1. Helen対象manifestで監査を削除不能な必須要素にする。
2. a10による既存品質台帳の破壊を防ぐ。
3. 既存writerと正式採用経路を列挙し、書出し前後のSHA契約へつなぐ。
4. G10 blockedと、ハーネス検出力の差分fixtureを別の完成条件にする。
5. 状態・鮮度・findingを機械実装可能な契約へ具体化する。
6. 既存section 9.6との優先関係を決める。
7. 共有品質ゲートの回帰試験を追加する。

## 使わなかったもの・落とした情報

なし。レビューは読み取り専用で、計画・コード・JSON・ログを変更していない。
