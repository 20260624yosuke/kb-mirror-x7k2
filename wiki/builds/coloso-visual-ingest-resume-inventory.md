---
type: build
title: "Coloso 映像ingest 中断タスク棚卸し(2026-08-23)"
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
---

# Coloso 映像ingest 中断タスク棚卸し(2026-08-23)

## 目的

ox(opencode) サーバーエラーで停止した複数の coloso 映像 ingest バッチについて、
**ディスク上の実物**(source・frames・manifest)を実測し、中断タスクの正確な残量と
壊れた状態を列挙する。並列 ingest 基盤を設計する前の現状把握。

## 調査方法

- `wiki/sources/coloso-*.md` 190 件の frontmatter `visual_ingested` 有無を実走査。
- 各 source 内のフレーム参照パスと `wiki/assets/frames/<slug>/` の実ファイル存在を突合。
- 死亡セッションの特定には opencode DB(`~/.local/share/opencode/opencode.db`, 272 セッション)と
  武田さん作成のエクスポート JSON(`/Users/takedayousuke/00_in box pro/`)を読んだ。

## 集計

| 状態 | 章 | 内容 |
|---|---:|---|
| A. 完全健康 | 9 | 観測節・フレームPNG・manifest が全て整合 |
| B. 壊れた状態(修復対象) | 6+1 | flag・観測・証拠のどれかが欠落 |
| C. 未着手(動画あり) | 約118章 | 5講座分。台帳初期化の本体 |
| D. blocked(動画なし) | 約45章 | chan_02 全20 sec・nekojira ほぼ全体。raw に動画が無い |

### A. 完全健康(9章)

hide ch02・ch03 / hizurume ch11・ch12 / marse ch04 / sasa ch01 / ye_jji ch02・ch03・ch04。
manifest status=complete、source 参照 PNG 全在、ゲート適用済み。

### B. 壊れた状態(修復判断が必要)

| slug | 実態 | 必要な復旧 |
|---|---|---|
| coloso-hide-ch04-body-basics | 2026-07-15 完成扱い。source に観測表 7 件あるが **参照 PNG 7 枚全滅**・manifest 無し | フレーム再抽出で証拠画像のみ復元(source 本文は無変更) |
| coloso-ye-jji-ch05-texture-basic | PNG 14 枚抽出済み・flag 付与済みだが観測節・manifest 無し | 盲検読取の続きから再開(抽出分は再利用可) |
| coloso-marse-ch05-fetish-face | flag のみ。観測節・PNG・manifest 全部無し | flag 撤去してやり直しが安全 |
| coloso-marse-ch06-fetish-upper-body | 同上 | 同上 |
| coloso-marse-ch07-fetish-lower-full-body | 同上 | 同上 |
| coloso-sasa-ch02-insight-memo | flag のみ。同上 | 同上 |

参考: nekojira ch03 は snapshot 済み・抽出前(flag 無し)。B に近い形で再開可能。

注: hide バッチ死亡セッション(ses_fd404d2c…)の最終報告は「別ウィンドウが
marse ch04〜07・ye_jji ch05 を処理済み」と主張していたが、ディスク実態は marse ch05〜07 が空だった。
**死亡セッションの会話上の完了主張は信用できず、ディスク実測だけが正本**という今回の最大の教訓。

### C. 未着手(動画ローカルあり・約118章)

| 講座 | 未着手章 | ローカル動画 | 備考 |
|---|---:|---:|---|
| hizurume | ch01〜10・13〜26 の 24 章 | 45 本 | 承認済み引き継ぎプランあり([[hizurume-visual-ingest-handoff-plan]]) |
| sasa | ch03〜36 の 34 章 | 59 本 | |
| hide | ch01・ch05〜27 の 24 章 | 34 本 | |
| ye_jji | ch01・ch06〜23 の 18 章 | 53 本 | 分割動画(ch05 は 3 部構成等)あり |
| marse | ch01〜03・ch08〜22 の 18 章 | 26 本 | |

章ごとの動画対応表(1 章=何本のどの動画か、重複ファイル `25_01 1.mov` 系の扱い)は
未作成。台帳初期化の最初の工程で機械的に作る。

### D. blocked(動画なし・約45章)

- chan_02: sec01〜20 の 20 章。raw に動画 0 本。chan バッチセッション(ses_fd3f09fdb…)も
  「動画を `_attachments/` に置いてから再依頼」と結論済み。
- nekojira: ch01〜02・ch04〜26。ローカル動画は ch03 の資料動画 1 本のみ。
  → 動画入手は武田さん側の作業。置かれるまで着手しない。

## 教訓(設計への入力)

1. **会話履歴は正本にならない**: 272 セッション中「停止調査と再開」系が大量発生。
   エクスポート→読み返し→手動復元のコストが繰り返し支払われた。
2. **幽霊 flag 問題**: 死亡直前の書き込み(`visual_ingested` のみ付与)がディスクに残り、
   「完了」と「未完了」の機械判別を壊す。進捗 state は完了時点での一括付与ではなく、
   段階ごとの追記型にする必要がある。
3. **証拠画像は source 本文より壊れやすい**: hide ch04 のように本文完璧でも PNG 紛失で
   監査線が切れる。フレーム存在チェックをゲートに組み込む価値がある。
4. **併走ウィンドウの二重作業リスク**: 死亡セッション同士が同じ章に触れており
   衝突が現実に起きていた。章単位の排他制御(誰が今どの章を持っているかの記録)が必要。

## 次の処理方針(未承認・案)

1. B の修復を第0タスクとし、並列 ingest 基盤([[oxloop-parallel-agent-loop]] 流用 +
   ディスク台帳)の設計計画へ接続する。
2. D は動画が置かれるまで queue に入れない(blocked ラベルのみ)。
3. C はパイロット→武田さんレビュー→承認群だけ本処理(AGENTS.md 量産規約どおり)。

## 関連リンク

- [[oxloop-parallel-agent-loop]] — 流用候補の並列実行基盤
- [[hizurume-visual-ingest-handoff-plan]] — hizurume の承認済み計画
- [[coloso-ingest-coverage-audit]] — raw 対応表(文章 ingest 済みの根拠)
- [[video-visual-ingest-design]] — 映像 ingest 設計正本
