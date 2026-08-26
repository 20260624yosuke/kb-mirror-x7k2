# hide 講座 映像ingest 引き継ぎ(正本)

このファイルは hide 講座の残り映像 ingest をどのセッション・どの AI でも再開できるようにする正本引き継ぎ書。
新しいセッションは冒頭でこのファイルを読み、**完了した章はこのファイルの状態表を必ず更新してから終わること**。

## 進め方(確定済み・再議論しない)

1. 正本仕様は `wiki/builds/video-visual-ingest-design.md` v2.3 + SKILL(`.claude/skills/video-visual-ingest/`)。
2. **1動画パート = 区切り**。抽出 → 盲検読取(サブエージェント8枚前後のバッチ) → バッチ返却ごとに
   `read-p<part>-<batch>.md` へ退避 → 保存対象だけ frames ディレクトリへコピー → 再確認 max(3,10%) →
   manifest 更新 → `gate check --phase staging` PASS。
3. 章の全パート完走で `check --phase complete` PASS → `visual_ingested` 付与 → index/log 更新 →
   inbox.py add → **この HANDOFF.md の状態表と次の一手を更新**。
4. サブエージェントが `Upstream request failed` で落ちたら同じプロンプトで再実行。連続失敗時はバッチを半分に割る。
5. 一時フレーム置き場: `/var/folders/mx/08ffsjl11dnc3yxc_76clv940000gn/T/opencode/<章>-frames/`(章完走後に削除)。
6. snapshot は章ごとに全動画を `--video` 反復指定で一度取る。temp 消失時は SHA-256 を snapshot と照合して再抽出してよい。

## 対象(raw/_coloso/2026_05_31_hide_01/)

| 章 | source slug | 動画 | 合計 | 状態 |
|---|---|---|---|---|
| ch15 頭部 | coloso-hide-ch15-head-structure-simplification | 3本 | 約47分 | ✅ 完了(54枚・2026-08-26) |
| ch16 首 | coloso-hide-ch16-neck-structure | 16.mp4 | 約20分 | ⬜ 未実施 |
| ch17 胸郭・鎖骨・肩甲骨 | coloso-hide-ch17-thorax-clavicle-scapula | 2本 | 約29分 | ⬜ 未実施 |
| ch18 胸 | coloso-hide-ch18-chest-structure | 18.mp4 | 約19分 | ⬜ 未実施 |
| ch19 背中 | coloso-hide-ch19-back-structure | 19.mp4 | 約19分 | ⬜ 未実施 |
| ch20 肩 | coloso-hide-ch20-shoulder-structure | 2本 | 約21分 | ⬜ 未実施 |
| ch21 腰・お尻 | coloso-hide-ch21-pelvis-hips | 2本 | 約33分 | ⬜ 未実施 |
| ch22 腕 | coloso-hide-ch22-arm-structure | 22_01.mov + 22_02.mov | 約34分 | ⬜ 未実施 |
| ch23 手 | coloso-hide-ch23-hand-structure | 23_01.mov + 23_02.mov | 約26分 | ⬜ 未実施 |
| ch24 脚 | coloso-hide-ch24-leg-structure | 24_01.mov + 24_02.mov | 約23分 | ⬜ 未実施 |
| ch25 足 | coloso-hide-ch25-foot-structure | 25.mov | 約15分 | ⬜ 未実施 |
| ch26 自然なポーズ | coloso-hide-ch26-natural-pose-points | 26.mov | 約14分 | ⬜ 未実施 |

- 動画なしで対象外: ch01 / 07 / 08 / 09 / 10 / 27(raw ページに動画リンクがない)。
- 完了済み: ch02〜06、11〜14。
- mov の章(ch22〜26)も手順は同一(video_frames.py が解決する)。実績未確認なので最初の dry-run で要確認。

## 作業フォルダ

- バッチ退避: `wiki/builds/coloso-visual-ingest-batch2/hide-batch3/<chapter>/`
- 実績参照: 同配下 `ch15/progress.md`(方式の実例)

## 次の一手

**ch16 首(mp4 1本・約20分)** から開始する。
