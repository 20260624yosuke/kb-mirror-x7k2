---
type: build
title: "coloso 並列映像ingest プロジェクト"
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-23
---

# coloso 並列映像ingest プロジェクト

## 目的・背景

ox(opencode) のサーバーエラーで複数の coloso 映像 ingest バッチが停止し、
進捗が会話履歴の中にしか無いため「エクスポート→読み返し→手動復元」が繰り返された
([[coloso-visual-ingest-resume-inventory]] 棚卸し済み)。根本原因は並列性ではなく
**ディスク台帳の欠如**。本プロジェクトは、進捗をディスクに置き、
opencode CLI ワーカーの別プロセスで処理することで、メインセッション死亡からも
`status.sh を読んで続きから再開`できる状態を作る。

## 正本と構成

| ファイル | 役割 |
|---|---|
| `tools/ingest-parallel/init_queue.py` | 全章を走査し 1 章=1 タスクの台帳(tasks/<slug>/task.json)を生成 |
| `tools/ingest-parallel/run.sh <slug>` / `run.sh --next N` | パイプライン実行(3ステージ+ロック+リトライ) |
| `tools/ingest-parallel/status.sh` | 台帳一覧表示(再開時は最初にこれを見る) |
| `tools/ingest-parallel/task_state.py` | 状態変更の唯一の入口(temp+rename 原子書き込み) |
| `tools/ingest-parallel/prompts/stage_{a,b,c}_*.md` | ワーカー指示テンプレート |
| `tools/ingest-parallel/tasks/<slug>/` | task.json・events.log・stage ログ・監査記録(ここが正本) |
| `tools/ingest-parallel/quality-gate.json` | AGENTS.md 1A 品質ゲート |

## パイプライン契約

1ステージ=1つの opencode CLI プロセス(メインセッションと独立)。成功判定は
**rc ではなく成果物の機械検査**(opencode は内容不正でも rc=0 を返すため):

- **A 抽出+盲検**(`stage_a_extract.md`): snapshot→抽出→盲検→manifest(status=draft,
  observations 配列)+recheck_targets.json(max(3, ceil(saved×0.1)) 以上)。
- **B 独立 recheck**(`stage_b_recheck.md`): フレームパスだけ渡された第2読者。
  source・manifest は読ませない。recheck_results.json を出力。
- **C 照合+source 反映**(`stage_c_finalize.md`): 照合 verdict(confirmed/corrected/
  marked-uncertain)→manifest.recheck ブロック→status=complete→source へ
  「映像観測」節挿入→`video_ingest_gate.py check --phase staging`(台帳検査だけ除外した
  新フェーズ)PASS。

## 役割分担と専権事項

- **ワーカー**: 自章の frames dir・tasks/<slug> 配下のみ書ける。
  index.md / log.md / source frontmatter / 他章には触らない。
- **メイン(監査)**: staged タスクを標本照合→index/log 更新→**frontmatter
  visual_ingested と manifest.completed を同一作業で一致付与**→complete gate 再検査→
  audit.json 記録→applied。幽霊 flag(棚卸しで6章発生)の機械的防止。
- **武田さん**: パイロット成果物の受入承認(acceptance-only)。

## 再開手順(セッション死亡時)

1. `tools/ingest-parallel/status.sh` を見る。state 一覧で全体を掴む。
2. failed → `run.sh <slug>` で再ディスパッチ(完了済みステージは .stage_*_ok マーカーで
   スキップされ、抽出済みフレームは再利用される)。staged → メイン監査から再開。
3. 監査判断・ユーザー承認も tasks/<slug>/audit.json とバッチ承認ファイルに追記型で残す
   (会話履歴は使わない)。

## 機械的保証

1. 占有ロック(mkdir 原子性)+stale 解放(PID 死亡/TTL 6h)。二重着工防止
2. タイムアウトは動画総尺から算出(dur×3+900s 等)、rc≠0・検査不合格は指数バックオフで
   最大3回再試行→failed 記録(503 死亡量産対策)
3. staging gate は complete gate から台帳検査を除いたフェーズ(video_ingest_gate.py に追加)
4. 状態遷移は task_state.py のみ(events.log に追記監査線)
5. 1A quality-gate.json(plan PASS 済み)。パイロット3対象群の承認スコープを明記

## 運用方針(量産規約準拠)

- パイロット: ye_jji ch05(修復・4分割動画)/nekojira ch03(資料動画)/hide ch05(新規単一)を
  N=1 直列で実行し、武田さんのレビュー承認を取る
- 承認スコープは対象群ごと(講座×性質)。承認群だけバッチ展開する
- blocked(動画未配置)58章: chan_02 20章・nekojira 25章等。武田さんが動画を置くまで着手しない
- hide ch04 は repair ラベル(source 完成済み・PNG 全滅)。専用復旧タスクとして別途実施

## 使わなかったもの・落とした情報

- **oxloop loop.sh 本体の流用**(planner→arms→verifier ラウンド構造): 1章=1完了条件の
  ingest に修正ラウンドは不要のため不採用。影響: FIXES 繰り越しループが無い=失敗時は
  リトライ3回で failed 落ち。戻す方法: run.sh にラウンド構造を足す(現時点の価値は低いと判断)
- **cwd 物理分離**(oxloop 方式): ingest は wiki への書き込みが前提のため分離できない。
  代わりに章スコープ+ロック+gate の孤児/非破壊検査で置換
- **N>1 の並列**: 三事故の原因が台帳欠如だったため、パイロットでは直列(N=1)のみ。
  承認後に env で並列数を上げる(実装済みの仕様変更は不要)

## 変更履歴

- 2026-08-23 新設。計画はサブエージェント独立レビュー(修正必須9点を反映)を経て
  武田さん承認。Phase 0〜2 を実施中。
- 2026-08-24 パイロット第1弾 hide ch05 が A→B→C 完走+メイン監査で applied。
  基盤バグ3件を修正(タイムアウト未強制/verify_stage SyntaxError/プロセスグループ死亡)。
  デスクトップ側の並行セッション(coloso-batch-resume-handoff)と ye_jji ch05 が計画重複中。

## 関連リンク

- [[coloso-visual-ingest-resume-inventory]] — 本プロジェクトの起点になった棚卸し
- [[oxloop-parallel-agent-loop]] — opencode CLI 実行ノウハウの提供元
- [[video-visual-ingest-design]] — ワーカーが従う設計正本 v2.3
- [[hizurume-visual-ingest-handoff-plan]] — 先行していた講座単位の引き継ぎプラン
