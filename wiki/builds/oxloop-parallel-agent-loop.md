---
type: build
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-23
---

# oxloop — 並列マルチエージェント実行ループ

## 目的・背景

複数の作業単位(アーム)を opencode CLI で並行実行し、検証役が合格/修正を判定して完了までループする仕組み。
類似システムの設計テキストのみが存在し実ファイルは無かったため([[oxloop-skill-text-was-unimplemented]]
的な経緯)、2026-08-23 にゼロから実装した。実装前にサブエージェントへ計画レビューさせ、
**opencode CLI の実測**(v1.18.21: 非ログイン PATH 外/`--format json` で sessionID 取得可/内容不正でも
終了コード0/サンドボックスなし/macOS に timeout・flock なし)を反映して設計を修正した。

確定した設計原則(武田さん承認): **判定権限はプロンプト(LLM)ではなく loop.sh が持つ**。
LLM の合格主張・VERIFIED 作成は信用せず、機械条件でのみ完了とする。

## 構成

| ファイル | 役割 |
|---|---|
| `tools/oxloop/loop.sh` | 本体。`loop.sh <task.md> [アーム数] [--verifier-model M]` |
| `tools/oxloop/prompts/{planner,worker,verifier}.md` | 3役の指示。verifier は REPORT 形式(確定版)を埋め込み |
| `tools/oxloop/tasks/<タスクID>/` | 実行ごとの作業場(R<N>/arms・work・logs + STATE.log + REPORT.md + VERIFIED) |
| `tools/oxloop/tests/*.md` | 試験用タスク |

詳細仕様・機械的保証一覧・限界は [[oxloop-readme]] = `tools/oxloop/README.md` を正本とする。

## 使い方

```bash
tools/oxloop/loop.sh tools/oxloop/tests/t2-parallel.md 3
```

- task.md に `---ARM---` 区切りを直書きすると planner を省略(コスト減・分解が決定的)
- 完了時、REPORT.md が成果物 Inbox へ自動申告され、武田さんの通常確認導線に乗る

## 機械的保証(設計の核)

1. **VERIFIED の発行者は loop.sh**: verifier 終了コード0 × 全アーム rc=0 × PROGRESS.log 全員非空 ×
   REPORT 必須節 grep 合格、の全条件でのみ作成
2. **アームの物理分離**: cwd=`R<N>/work/<NN>/`。成果物は work 内にのみ存在し統合は loop.sh
3. タイムアウト内蔵(watchdog kill / macOS に timeout コマンドがないため自前実装)
4. 失敗経路は STATE.log へ状態遷移として記録(PLAN_FAIL/FIXES_PARSE_FAIL/MAXROUNDS_EXHAUSTED 等)
5. REPORT.md の「実行記録」表(sessionID・生ログパス)は loop.sh が自動付与(verifier に書かせない)

## テスト結果(2026-08-23 実施)

| 試験 | 結果 |
|---|---|
| t1-single(単一アーム縦貫) | VERIFIED・約2.5分。REPORT は確定形式どおり生出力値付き |
| t2-parallel(planner+3並列) | VERIFIED・Inbox 自動申告まで成功・約9.5分。3ツール実動作確認 |
| 強制FIXES(OX_FORCE_FIXES=1, MAXROUNDS=2) | FIXES繰り越し→ラウンド消費→exit 6 を確認 |

試験中に発見・修正した不具合: パス空白分割でアーム数が爆発(ls 非quote)/cwd 外読取の自動拒否で
worker 全滅(アーム定義をプロンプトへ直接埋め込みへ変更)/watchdog sleep 孤児がパイプを掴み
ハング見え/watchdog 発火時の二重 FAILCOUNT/KB_ROOT 取得階層誤りで Inbox 申告されない。

### 使わなかったもの・落とした情報

- **planner 必須設計**: 直書きモード併用に変更。影響: 分解品質を人間が事前確認できる。戻す方法: なし(planner は残存)
- **opencode のモデル固定**: 既定 `x-preview-f-free`(無料枠)。verifier 別モデル指定は可能だが未検証

## 変更履歴

- **2026-08-23 新設**。3段階試験合格。実運用は未開始(武田さんの実際のタスクでの初回使用待ち)

## 関連リンク

- [[deliverable-inbox]] — 完了報告の申告先
- [[llm-project-quality-gate]] — 高リスク成果物の別ゲート(oxloop とは独立に効く)
