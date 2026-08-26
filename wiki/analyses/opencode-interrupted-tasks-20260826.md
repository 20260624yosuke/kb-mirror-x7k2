---
type: analysis
title: "opencode 中断タスクの全容把握とマーセch12救出（2026-08-26）"
status: active
confidence: high
evidence_level: source-backed
sources: []
created: 2026-08-26
last_reviewed: 2026-08-26
---

# opencode 中断タスクの全容把握とマーセ ch12 救出（2026-08-26）

## 経緯と目的

PC 再起動のたびに opencode のセッションが中断され、「何をやっていて何が止まったか」が掴めないのが問題。履歴 DB（`~/.local/share/opencode/opencode.db`、アクティブ925セッション・未完了 TODO 342件）を実測して現行タスクの全容を確定した。

## 確定した事実

### 全体像

- Coloso 映像 ingest: **7講座190章中28章済み**（ye_jji 10/25・hide 8/29・マーセ 8/23・ひづるめ 8/28・佐々 2/37・nekojira 0/27・chan_02 0/21）
- coloso-intake 新規5講座: 移植は**全部完了**（ixy_2 23/ixy 82/ne-on 40/晃田ヒカ22ペア/雨傘ゆん118）。文字起こしは **134/285 本完了**
- GF2 Helen 系: K4第2回視覚判定が待ちの頂点（合格なら Helen 系の出口）。repro-v51 解析は f159〜f161 完遂・f162/f163 未着手。char-extract はコード修正途中で承認カード未提出

### 判断・決定

1. **A-2 文字起こし151本は保留に決定**（武田さん「リソースを回せない・プライオリティ低い」）。実測根拠： 1本中央5.1〜6.4分（ファイルmtime実測）＝純計算13〜15時間、GPU占有＋メモリ約6GB（M1・16GBのため並列化不可）。記録済み → [[coloso-intake-design]] 変遷節
2. **A-1 既存講座の映像読み取りは続行可**（vision計算はクラウド側で Mac ほぼ無負荷）と説明し武田さんが承認。4本（ye_jji／hide／マーセ／ひづるめ）を**別セッションで並行進行中**

### ボトルネックの原因特定（質問への回答）

- **Coloso 作業時の CPU 負荷**: `tools/video_frames.py` がフレーム1枚ごとに ffmpeg を個別起動する設計のため。マーセch12スイープ＝211回起動×シーク捨てデコード×PNGエンコード。設計 v2.3 の要件（同一性・来歴・ゲート）には速度が入っていなかった。改善案（selectフィルタ一括抽出化・出力契約は互換維持）を提示したが**未実装**
- **フォルダ→Obsidian 移動の遅さ**: LLM が1ファイルずつ推論往復していたことが原因。この問題は **coloso-intake 設計v2（8/25）の機械パイプライン化で既に解決済み**。残る時間は whisper の純計算のみ

### マーセ ch12 救出（二重死亡からの回収）

- 実態確定： **ch12-01 盲検57枚＋sweep91枚完走／ch12-02 盲検71枚完走／ch12-02 sweep120枚未完**。「何も分からない」ではなく7割読めていた
- DB の task tool 出力から18バッチ63,189文字を救出 → `wiki/builds/coloso-visual-ingest-batch2/marse-ch12-rescue/`（rescue-summary.md + recovered-readings.json）
- 残作業： staging再構築（フレーム本体は再起動で消滅）→ ch12-02 sweep → source反映 → manifest/recheck/gate → visual_ingested

## 現在進行中（別セッション4本・2026-08-26 時点）

| セッション | 対象 | 再開起点 |
|---|---|---|
| ① | ひづるめ B1群 | review/2026-08-25-hizurume-b1-review.md の修正指示 A〜C 実行→修正確認レビュー |
| ② | マーセ ch12 | rescue-summary.md の再開手順どおり |
| ③ | ye_jji ch12 | 抽出165枚済み・盲検読取から |
| ④ | hide ch14 | 盲検読取続き（01m00s/01m20s から） |

## 武田さんの手元に残っている判断

- K4第2回視覚判定（コンタクトシート round2）
- migrate-opencode.sh 実行
- 各種検収（ひづるめ修正確認レビューの verdict・intake 代表ページ目視など）

## 不確実・要確認

- 別セッション4本がまた中断した場合、救出は自動ではない（DB からの手動救出手順は今回の rescue-summary.md が雛形）
- ffmpeg 一括抽出化は提案止まり。次に映像ingestを量産する前に導入判断が要る

## 関連リンク

- [[coloso-intake-design]] / [[video-visual-ingest-design]] / [[coloso-visual-ingest-batch2-handoff]]
- 成果物： `opencode-task-dashboard.html`（不採用・認知負荷過多のため）、`wiki/builds/coloso-visual-ingest-batch2/marse-ch12-rescue/rescue-summary.md`
