---
type: analysis
sources: []
status: active
confidence: high
evidence_level: inferred
last_reviewed: 2026-07-09
---

# 進行中プロジェクト ダッシュボード

> 武田さんが「今どのプロジェクトがどこまで進んでいるか」を1か所で把握するための一覧。
> 状態は `log.md`・各 build ページ・引き継ぎ計画・`llm-uploads/`(他LLMの経緯まとめ)から集約
> (evidence_level: inferred)。詳しい経緯は各行のリンク先(build 正本など)を参照。
> **ここは要約のみ**。触ったら最終更新日を直し、状態が変わった行を更新する。

## このダッシュボードの保ち方(後継 AI / セッション開始時)

- **正の情報源は `/Users/takedayousuke/llm-uploads/`**(武田さんが他LLMの経緯まとめを
  タイムスタンプ付きで投入する場所)。`raw/_llm_logs/` ではない(あちらは2件で停止)。
- Claude Code の生ログ(`~/.claude/projects/.../*.jsonl`、**今日だけで147MB**)と
  Codex の `~/.codex/logs_2.sqlite`(**212MB**)は、量が桁違いで費用対効果が悪い。
  **routine では読まない**。特定の抜けを追うときだけ、その1セッションを狙い撃ちで見る保険。
- 運用: 下の「取り込み状況」の時刻以降に `llm-uploads/` へ入った新規分だけを読み、
  状態が動いた行を更新して時刻を進める(過去分の全件さらいはしない方針=武田さん 2026-06-21)。

### 取り込み状況(llm-uploads)

- **2026-06-21 まで確認済み**(最近7日 = 13件を照合。すべて既存プロジェクトに対応し、
  取りこぼしプロジェクトは無し)。次回はこれより後に入った新規分を反映する。
- **2026-06-26**: 武田さん指定の `20260626-105500-Obsidian-Canvas-UI軽量化…` を**個別 ingest**(指定の1件のみ)。
  → 下「進行中」に「Obsidian Canvas のUI軽量化」を新規登録。**06-22〜06-26 の他の新規分の一括照合は未実施**
  (上の baseline は据え置き。次回の全件照合はこのままここから再開する)。

### Google Tasks dry-run 根拠メモ(2026-07-09)

- 取得日時: `2026-07-09 20:25:10 +0900`
- コマンド:
  `python3 tools/google_tasks_to_obsidian.py --client-secret ~/.config/google-tasks-quickadd/client_secret.json --token ~/.config/google-tasks-quickadd/token.json --source-list-title '進行中プロジェクト' --limit 200 --no-browser`
- 結果:
  `source_list=進行中プロジェクト tasks=43 execute=False`
- 全文の正本は Google Tasks。本ページには file-back 用の**クラスタ要約だけ**残す。

## Google Tasks 上の主クラスタ(2026-07-09)

| クラスタ | 現在見えている束 | 正本 / メモ |
|---|---|---|
| Obsidian Canvas/UI | Pureref化、選択ノード接続、サムネ更新、ズーム視認性、右クリックUI、動画ビュワー、一覧整列が主塊 | [[obsidian-canvas-ui-lightweight-plan-2026-06-26]] を起点。個別 build 正本が無い案は、まだ Google Tasks 上の未整理メモ段階 |
| X Eagle / Eagle画像整理 | 重複処理、検索力、保存UI、フォルダ表示、画像整理 | [[x-eagle-free-save-pilot]] / [[eagle-dedup-merge-2026-07-07]] |
| LLM運用 | トークン消費、ルーティーン停止、Google Tasks/メモ一元化、週次スケジューリング | [[llm-cheap-model-execution-workflow]] が実行ゲートの正本。個別ルーティーン設計は別途未整理 |
| KeyClack | 打鍵音アプリ本体、`Cmd+Space` 時の無音 | [[keyclack]] |
| その他 | 資料作成ワークフロー、創作還元、エスキース一覧化など | project 化前の混合メモ。必要になったものだけ build / analysis へ昇格 |

## 進行中(次の一手がある)

| プロジェクト | 状態 | 次の一手 | 待ち |
|---|---|---|---|
| Coloso 7講座の横断統合(wiki知識) [[synthesis-backlog-2026-06]] | 模範ページ「影の設計」[[shadow-design]] 作成済み(Phase 2 承認済み)。残テーマは優先度付きで待機 | 次テーマ「視線誘導」[[shi-sen-yu-dou]] の統合に着手 | 武田さんの着手順 承認 + 担当体制(2026-06-22 以降 Opus+Codex) |
| Obsidian Canvas のUI軽量化(描画/メモリ軸) [[obsidian-canvas-ui-lightweight-plan-2026-06-26]] | 重い参照Canvas(485ノード/展開3.3GB)のカクつき原因を特定。非破壊の縮小プラン(方式B推奨/A)を提示済み・**未実行** | 方式(B/A)・目標長辺(1280/1600/1000)・パイロット対象を決定 | 武田さんの方式決定 →(raw/着手前に)再承認 |
| X→Eagle 保存拡張 [[x-eagle-free-save-pilot]] | `manifest.json` / 署名済みXPI / 公開 `updates.json` は 0.5.38 を確認。ただし起動中 helper `/health` は `version: 0.5.17`、ユーザー実機では「重複処理がされない」と失敗報告あり。**配布済みと実機反映済みを分離して扱う段階** | Firefox実機の版、保存入口、helper lookup、フォルダ追加プラグイン起動状態を切り分ける | 武田さんの実機確認 or 実機ログ共有 |

## 保留(外部待ち・本人の判断待ち)

| プロジェクト | 状態 | 待ち / 再開条件 |
|---|---|---|
| Karabiner で Enter 入替(LLMチャット) [[llm-chat-enter-guard|Karabiner Enter 入替]] / [[llm-chat-enter-guard]] | Karabiner 16.0.0 インストール済み | Mac の再起動(再起動後に有効化を実機確認) |
| BetterDisplay 擬似解像度(M27f) [[betterdisplay-m27f-pseudo-resolution]] | 2560×1440 化を設定済み | 次回 Mac 再起動時に自動復元するか・フォント変更が反映されるか未検証 |
| Fable5 引き継ぎ Phase 3(メンテナー指南書+入口整備) [[llm-maintainer-handoff-plan]] / [[llm-maintainer-handbook]] | ドラフト修正済み(Codex レビュー5件反映) | Fable 5 が再び使えるか、別担当で再開すると明示決定するまで停止 |

## 直近で完了(参考・進行中ではない)

| プロジェクト | 完了状況 |
|---|---|
| Google Tasks クイック追加 [[google-tasks-quickadd]] | Raycast 経由の実呼び出しを動作検証OK(2026-06-21 武田さん) |
| Mac 本体容量の掃除 `mac-storage-cleanup-2026-06` | 空き 90% まで回復し本人判断で一区切り(2026-06-21「とりあえず大丈夫」)。残りの保留項目(Notion / Claude vm 削除など)は当面見送り |
| クリスタ自動バックアップ 外付け移設 `clipstudio-backup-external-symlink` | 末端試験 合格(2026-06-22)。クリスタが symlink 経由で外付けへ新規バックアップを実書き込み(DocumentBackup 14:31 / InitialBackup 12:17)。外付け未マウント中は作られない依存は残る |
| 画面配置復元(Raycast) [[window-layout-state-restore|画面配置復元]] / [[window-layout-state-restore]] | 実機 apply で復元成功(2026-06-21)。取り残し1窓(ノート切替によるタイトル不一致)は既知制限 |
| Canvas 参照ツール「左端揃え」 [[canvas-reference-tools|Canvas 左端揃え]] / [[canvas-reference-tools]] | v0.5.0 実機OK。Canvas→クリスタ貼り付けも使用感良好 |
| 複数サイト画像検索(一括) [[multi-site-image-search]] | Raycast からの実呼び出し含め実機確認済み |
| 影の設計 統合ハブ [[shadow-design]] | 講座横断の模範統合ページとして作成済み(統合プロジェクト全体は上の「進行中」に継続) |

## 関連リンク

- [[current-projects-todo-clarifications-2026-06-15]] — 状態訂正メモ(本ダッシュボードの前身。意図の詳細はこちら)
- [[synthesis-backlog-2026-06]] — wiki知識統合の優先順位付きバックログ
- [[llm-maintainer-handoff-plan]] — 引き継ぎ計画の正本
- [[llm-cheap-model-execution-workflow]] — 廉価LLMに渡す前の実行ゲート正本
