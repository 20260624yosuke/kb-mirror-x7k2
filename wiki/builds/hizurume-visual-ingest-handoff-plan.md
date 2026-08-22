---
type: build
title: "ひづるめ講座 映像レイヤー追加 ingest 引き継ぎプラン(v2.2 運用)"
status: active
confidence: high
evidence_level: user-stated
created: 2026-08-22
last_reviewed: 2026-08-22
sources: [coloso-hizurume-illustration-course]
---

# ひづるめ講座 映像レイヤー追加 ingest 引き継ぎプラン

> この文書は新規セッションのエージェントへの引き継ぎ資料であり、実行計画の正本。
> 武田さんが 2026-08-22 に v2.2 ワークフロー実運用を了承済み(ye_jji ch02 遡及分のレビュー)。

## 目的と完成条件

**目的**: 文章 ingest 済みのひづるめ講座 26 章のうち映像未取り込みの 25 章へ、
video-visual-ingest v2.2 ワークフローで映像観測層を追加する。

**各章の完成条件(すべて機械検証可能)**:

1. source ページに `## 映像観測(フレーム由来)` 節(evidence_id / 時刻 / frame / 確信度 / 観測 の v2 形式表)
2. `wiki/assets/frames/<slug>/` に snapshot.json + manifest.json(status: complete, reader_model, recheck)
3. `tools/video_ingest_gate.py check --phase complete` が **PASS**
4. `index.md` / `log.md` 更新、frontmatter `visual_ingested` 付与

**非対象(今回やらない)**:

- entity / concept の自動更新(パイロット中は凍結。バッチ承認後に別作業として提案)
- raw 側の文字起こし修正・raw への書き込み
- ch11 の再処理(既に完遂・ゲート適用済み)
- ch02 の分割動画対応は最後のバッチで武田さんに個別相談

## 必読入口(この順で)

1. `AGENTS.md`(Codex)/ `CLAUDE.md`(Claude Code)— 入口規約
2. `.claude/skills/video-visual-ingest/SKILL.md` — ワークフロー本体
3. `wiki/builds/video-visual-ingest-design.md` — 設計正本 v2.2
4. 模範出力: `wiki/assets/frames/coloso-hizurume-ch11-force-field/manifest.json` と
   `wiki/assets/frames/coloso-ye-jji-ch02-contrast/manifest.json`(recheck ブロックの書き方見本)、
   `wiki/sources/coloso-ye-jji-ch02-contrast.md` の映像観測節(表形式見本)
5. モデル能力メモ: `wiki/analyses/ox-video-read-comparison-hizurume-ch11.md`

## 対象対応表(raw ↔ 動画 ↔ source slug)

| 章 | raw md | 動画 | source slug | 映像 |
|---|---|---|---|---|
| 01 はじめに | coloso_ひづるめ_01 はじめに.md | 01.mp4 | coloso-hizurume-ch01-intro | 未 |
| 02 上手い絵は〇割… | coloso_ひづるめ_02 …md | **02_01.mp4 + 02_02.mp4(分割)** | coloso-hizurume-ch02-mindset-over-technique | 未・要個別相談 |
| 03 本の紹介／必需品 | coloso_ひづるめ_03 …md | 03.mp4 | coloso-hizurume-ch03-books-tools | 未 |
| 04 SNS戦略 | coloso_ひづるめ_04 …md | 04.mp4 | coloso-hizurume-ch04-sns-strategy | 未 |
| 05 環境セットアップ | coloso_ひづるめ_05 …md | 05.mp4 | coloso-hizurume-ch05-environment-setup | 未 |
| 06 描き方の種類 | coloso_ひづるめ_06 …md | 06.mp4 | coloso-hizurume-ch06-drawing-types | 未 |
| 07 構図 | coloso_ひづるめ_07 …md | 07.mp4 | coloso-hizurume-ch07-composition | 未 |
| 08 人体 | coloso_ひづるめ_08 …md | 08.mp4 | coloso-hizurume-ch08-anatomy-basics | 未 |
| 09 光と影と色 | coloso_ひづるめ_09 …md | **09_01.mp4 + 09_02.mp4(分割)** | coloso-hizurume-ch09-light-shadow-color | 未 |
| 10 効率的な練習方法 | coloso_ひづるめ_10 …md | **10_01.mp4 + 10_02.mp4(分割)** | coloso-hizurume-ch10-efficient-practice | 未 |
| 11 絵の力場 | (完遂) | 11.mp4 | coloso-hizurume-ch11-force-field | **済**(29枚) |
| 12 視線誘導とサブ視線誘導 | coloso_ひづるめ_12 …md | **12_01.mp4 + 12_02.mp4(分割)** | coloso-hizurume-ch12-gaze-guidance | 未・**パイロット** |
| 13 錯覚と嘘 | coloso_ひづるめ_13 …md | **13_01.mp4 + 13_02.mp4(分割)** | coloso-hizurume-ch13-illusion-and-lies | 未 |
| 14 シンプルとは洗練の極み | coloso_ひづるめ_14 …md | 14_01.mp4(単本) | coloso-hizurume-ch14-simplification | 未 |
| 15 絵画をイラストへ変換 | coloso_ひづるめ_15 …md | **15_01〜15_04.mp4(4 本)** | coloso-hizurume-ch15-painting-to-illustration | 未 |
| 16 一枚絵の速度アップ | coloso_ひづるめ_16 …md | 16.mp4 | coloso-hizurume-ch16-speed-up | 未 |
| 17 実技1-1 暗い絵 | coloso_ひづるめ_17 …md | **17_01.mp4 + 17_02.mp4(分割)** | coloso-hizurume-ch17-dark-painting-1 | 未 |
| 18 実技1-2 絵画技法 | coloso_ひづるめ_18 …md | **18_01.mp4 + 18_02.mp4(分割)** | coloso-hizurume-ch18-painting-technique-2 | 未 |
| 19 実技1-3 仕上げ | coloso_ひづるめ_19 …md | **19_01〜19_04.mp4(4 本)** | coloso-hizurume-ch19-finishing-3 | 未 |
| 20 実技2-1 | coloso_ひづるめ_20 …md | **20_01〜20_03.mp4(3 本)** | coloso-hizurume-ch20-bright-painting-1 | 未 |
| 21 実技2-2 | coloso_ひづるめ_21 …md | **21_01.mp4 + 21_02.mp4(分割)** | coloso-hizurume-ch21-painting-work-2 | 未 |
| 22 実技2-3 | coloso_ひづるめ_22 …md | **22_01.mp4 + 22_02.mp4(分割)** | coloso-hizurume-ch22-negative-check-3 | 未 |
| 23 実技2-4 | coloso_ひづるめ_23 …md | **23_01.mp4 + 23_02.mp4(分割)** | coloso-hizurume-ch23-quality-finish-4 | 未 |
| 24 復習 | coloso_ひづるめ_24 …md | 24.mp4 | coloso-hizurume-ch24-review | 未 |
| 25 過去絵10枚添削 | coloso_ひづるめ_25 …md | **25_01.mp4 + 25_02.mp4(分割)** | coloso-hizurume-ch25-10-artwork-critique | 未 |
| 26 まとめ | coloso_ひづるめ_26 …md | 26.mp4 | coloso-hizurume-ch26-summary | 未 |

注意(2026-08-23 修正): 当初「raw md 内の `[[NN.mp4]]` とフルパスの二重リンクが
dry-run の複数候補エラーの原因」と記載していたが、誤りだった。実際は章そのものが
**複数本の動画に分割されている**(下表の「動画」列参照)。14 章が該当し、v2.3 で
1 ページとして処理する(設計正本の「分割動画の扱い」節)。

## バッチ計画(停止点つき)

1. **パイロット**: ch12 のみ完了 → **ユーザーレビューまで停止**(entity/concept 凍結)
2. 承認後 → **B1 理論系**: ch06, 07, 09, 13, 14
3. **B2 実技1群**(実演価値最大・長尺): ch15, 17, 18, 19
4. **B3 実技2群**: ch20, 21, 22, 23
5. **B4 残り**: ch01, 03, 04, 05, 08, 10, 16, 24, 25, 26(話者中心で映像価値が薄い章は
   少数サンプル判定で省略してよい。省略理由は log に記録)+ **ch02 分割動画の個別相談**
6. 各バッチ末: gate PASS 全章確認 → 報告 → 次バッチ開始前にユーザー確認(明示的承認待ち)

長尺動画(40分超)では interval を 30 秒に緩めてよい。変更時は snapshot の extraction パラメータに反映されるので特別な処理は不要。狙い撃ち(`--at`)は文字起こしの「スライドが出た」「描き始めた」時刻から選ぶ(画面内容の根拠には使わない)。

## 1章あたりの手順(ch11 実績コマンド)

```bash
# 1. dry-run(SHA-256・動画長・抽出予定を確認)
python3 tools/video_frames.py --page "<raw-md>" \
  --video "_attachments/<NN>.mp4" --dry-run --interval 20 [--at MM:SS ...]

# 2. snapshot(gate: 抽出前の同一性記録。retrofit は付けない)
python3 tools/video_ingest_gate.py snapshot --page "<raw-md>" \
  --video "_attachments/<NN>.mp4" \
  --source "wiki/sources/<slug>.md" \
  --out "wiki/assets/frames/<slug>/snapshot.json" \
  --interval 20 [--at ...]

# 3. 抽出(システム一時ディレクトリへ)
python3 tools/video_frames.py --page "<raw-md>" --video "_attachments/<NN>.mp4" \
  --out "<tmp>/hizurume-<NN>/frames" --manifest-out "<tmp>/hizurume-<NN>/manifest.json" \
  --interval 20 [--at ...]
```

4〜7. 盲検読取 → 観測表作成 → フレーム保存 → manifest 作成 → source 節置換 → index/log 更新(下記)。

8. 完成報告前:

```bash
python3 tools/video_ingest_gate.py check \
  --manifest "wiki/assets/frames/<slug>/manifest.json" \
  --source "wiki/sources/<slug>.md" \
  --snapshot "wiki/assets/frames/<slug>/snapshot.json" \
  --phase complete
```

FAIL 項目を潰して PASS してから `visual_ingested` を付ける。

## ブラインド読取プロトコル(2026-08-22 実証済み)

- 読取は **wiki を参照しない新規コンテキスト**(Claude Code なら Task サブエージェント、Codex なら同等機能)が行う。画像パスだけ渡す。
- 1 エージェントあたり約 13 枚。52 枚なら 4 体並列。
- プロンプトテンプレ(実績あり):

```text
あなたは画像読取のみを担当するリサーチタスクです。コードは書かず、ファイルの作成・変更も一切しないでください。
以下の <N>枚の PNG を Read ツールで1枚ずつ開き、画面上で確認できる事実だけを日本語で記録してください:
<絶対パスのリスト>
厳守ルール:
- 上記以外のファイル・ディレクトリは一切読まない。検索(grep/glob)も不要。特に wiki 配下や元動画ページの参照は禁止。
- 画面に表示されていない数値・意味・文脈を推測しない。音声・字幕外の情報には触れない。
- スライド内の文字は読める範囲で原文どおり写す。読めない文字がある場合は「判読不能」と明記する。
- レイアウト、色、オーバーレイ(半透明の色分け表示)、写真/イラストの被写体も簡潔に記す。
- 各フレームに確信度(high/medium/low)を自己評価として付ける。
出力形式(各フレーム1ブロック):
<ファイル名>
確信度: high|medium|low
観測: <画面上の事実>
全<N>枚分を必ず返してください。
```

- 再確認(recheck): 保存予定フレームから **max(3, 10%切り上げ)枚**を別サブエージェントで盲検再読取し、
  正本観測表と照合して manifest の `recheck.entries` へ verdict を記録する。
  - 一致 → `confirmed`
  - 不一致でクロップ等により確定 → `corrected` + note 必須(source 行も修正)
  - 確定不能 → `marked-uncertain` + note 必須 + 該当行に「要確認」表記
- クロップ確定の手順(実績): `ffmpeg -ss <秒> -i <動画> -frames:v 1 -vf "crop=iw*0.58:ih*0.42:0:0,scale=iw*2:-1" -y <tmp>/crop.png` → 新規サブエージェントに字形のみ問う(候補漢字を提示してよいが「見たままの字形描写」を必ず要求)。

## 既知の落とし穴(2026-08-22 の教訓)

1. 手書き風フォントの「力/カ」は読み手ごとに揺れる。見たままを書き、意味レベルでの統一を図らない。
2. 低解像度のまま漢字を確定しない(「端」を「壁」「陰」と誤読した実例)。疑ったら即クロップ。
3. 極小文字(X ハンドル等)は読みが揺れる。重要ならクロップ、重要でなければ「判読不能」または省略。
4. 濃密スライドでは全文写しが漏れうる。文字起こしの時刻情報で狙い撃ち抽出を併用する。
5. 文字起こしが無音・幻聴の区間に画面のみの重要情報があることがある(ch11 06:05 の注釈2件)。狙い撃ちを怠らない。
6. レイヤーパネル番号・ズーム率・ブラシサイズなどの副次情報も live 実演の時系列証拠として価値がある。
7. 誤観察率は 52 枚中 2〜3 枚程度ある。単独読取を鵜呑みにせず、recheck を省略しない。
8. 統括側 LLM が既存観測を読んだ後は自らフレームを読まない(汚染防止)。読取は常にサブエージェント。
9. **節挿入は byte 保持で行う**(2026-08-23 教訓、ch12・ye_jji ch03 の両方で発生)。Python の
   `read_text() → replace → write_text()` は行末正規化等で全文バイトが変わる可能性があり、本文非破壊検査が
   FAIL する。frontmatter 追補(`visual_ingested` 等)は **snapshot の前**に済ませ、観測節は挿入位置の前後だけを
   書き換える方式にする。それでも FAIL した場合は raw・動画の非変更を機械確認したうえで snapshot 基準を
   再取得してよい(2026-08-22 v2.2 運用に対する同日のユーザー了承手順)。

## 報告形式(各バッチ末)

- gate PASS/FAIL の機械検証結果(章別)
- 読取モデル名・保存フレーム数・省略した章と理由
- corrected / marked-uncertain の一覧と note
- 視覚的な正しさを「機械検証済み」と言い換えない

## 変遷

- 2026-08-22: 初版作成。v2.2 運用了承(ye_jji ch02 レビュー)を受け、残り 25 章への展開計画として正本化。
- 2026-08-23: パイロット開始直前に ch12 が 12_01 + 12_02 の分割動画であることが判明。
  全章実調査の結果 25 章中 14 章が分割で、対応表を実態に修正。武田さんの選択により
  設計正本 v2.3(分割動画対応)を先に適用してから、ch12 を 1 ページとして両動画処理する。
