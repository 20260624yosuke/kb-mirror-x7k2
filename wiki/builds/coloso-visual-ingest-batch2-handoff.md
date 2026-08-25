---
type: build
title: "Coloso 映像ingest batch2 引き継ぎ資料(残り全講座・118章・承認済み計画)"
sources: []
status: active
confidence: high
evidence_level: user-stated
created: 2026-08-24
last_reviewed: 2026-08-24
---

# Coloso 映像ingest batch2 引き継ぎ資料(残り全講座)

> この文書は新規セッションのエージェントへの引き継ぎ資料であり、実行計画の正本。
> 武田さんが 2026-08-24 の承認カードで計画を承認(条件: 群パイロットごとの独立レビュー指示文を同梱)。
> 機械品質ゲート `wiki/builds/coloso-visual-ingest-batch2/quality-gate.json` は plan PASS 済み。

## 目的と完成条件

**目的**: 動画あり5講座の残り **118章**(hide 23 / hizurume 24 / marse 18 / sasa 34 / ye_jji 19)へ
video-visual-ingest v2.3 ワークフローで映像観測層を追加する。

**各章の完成条件(すべて機械検証可能)**:

1. source ページに `## 映像観測(フレーム由来)` 節(全フレームを `![[wiki/assets/frames/...]]` 全パス参照・**拡張子 .png 付き**)
2. `wiki/assets/frames/<slug>/` に snapshot.json(または snapshot-pre.json)+manifest.json(status: complete, reader_model, recheck)
3. `tools/video_ingest_gate.py check --phase complete` が **PASS**
4. `index.md` / `log.md` 更新、frontmatter `visual_ingested` 付与、`tools/inbox.py add` で申告

**非対象(今回やらない)**:

- chan_02(動画 0 本)・nekojira(動画 1 本のみ。ch03 は snapshot 済みで動画入手待ち)
- entity / concept の自動更新(群パイロット承認後も凍結継続。別作業として提案する)
- raw 側の文字起こし修正・raw への書き込み

## 監査構造(2026-08-24 の穴塞ぎ・承認済み)

発端: batch1 完了報告で「ひづるめ計画に従えば進められる」と回答したが、同計画が要求する
**ch12 パイロットのユーザーレビュー承認が未実施**だった(log.md:8966)。これを穴として、
承認カードで以下の構造を承認済み:

1. **全体を1つの機械品質ゲートで管理**: `wiki/builds/coloso-visual-ingest-batch2/quality-gate.json`
   (`project_quality_gate.py check <path> --phase plan|batch|complete`)。plan PASS 済み。
   群のパイロット承認が取れたら該当 family に承認記録を書き、`--phase batch` を通してから量産する。
2. **群パイロット制(明示停止点)**: 各講座の先頭1章をパイロット → ゲート complete PASS →
   **武田さんのレビュー承認**(下記レビュー指示文を使用)→ 同群の残りを量産。
   - ひづるめは **ch12 のレビュー(未実施)から開始**。承認後 B1→B2→B3→B4 の順(既存計画の分割を維持)。
   - 他講座のパイロット: hide **ch06** / marse **ch08** / sasa **ch03** / ye_jji **ch06**。
3. **各章ごとに inbox 申告**、群完了ごとに台帳整合確認(index/log/孤児フレーム/flag)。
4. **停止条件**(quality-gate.json と同一): ゲートFAIL / 動画SHA不一致 / 判読不能が群の3割超 /
   並行writer衝突 / raw非変更確認不可 / パイロット未承認。

## 実行順序(新セッションはここから)

1. 本資料+`AGENTS.md`/`CLAUDE.md`+`.claude/skills/video-visual-ingest/SKILL.md`+
   `wiki/builds/video-visual-ingest-design.md`(v2.3)を読む。
2. **ひづるめ ch12 レビューの案内**: 武田さんに下記「レビュー指示文(コピペ用)」を渡し、
   承認 verdict を受取るまで B1 以降に進まない。
3. 承認後、ひづるめ B1 を開始(パイロット ch06 → レビュー → B1 残量産)。並行して他講座パイロットへ着手してよい
   (ただし各群の量産は各パイロット承認後)。
4. 群完了ごとに quality-gate.json の family に承認記録を書き `--phase batch` を通す。
5. 全群完了後: `--phase complete` + 台帳最終確認 + 完了報告。

## レビュー指示文(コピペ用・群パイロットごとに使用)

**設計意図(武田さん指定)**: 計画者=実行者のエージェントの自己報告を信用させないことで、
バイアスを排除し成果物品質を上げる。レビューは **別の新規セッション** に貼って使う。

```text
あなたは coloso 映像ingest パイロットの独立レビュアーです。実行エージェントの報告・manifest の自己記述を信用せず、一次資料(動画)と成果物を自分で直接比較して判定してください。

対象(パイロット1章):
- source: wiki/sources/<slug>.md
- frames: wiki/assets/frames/<slug>/
- 元動画: raw/_coloso/<講座フォルダ>/_attachments/<動画ファイル名>

手順(順に実行し、各手順の結果を記録):
1. 機械検証の再実行: python3 tools/video_ingest_gate.py check --manifest wiki/assets/frames/<slug>/manifest.json --source wiki/sources/<slug>.md --snapshot wiki/assets/frames/<slug>/snapshot.json --phase complete --index index.md --log log.md
   → 実行者の PASS 主張ではなく、自分の実行結果で判定する。FAIL したらその時点で「差し戻し」。
2. 動画との突き合わせ: 観測表の時刻から 5枚以上を無作為に選び、ffmpeg で同時刻のフレームを自分で抽出
   (例: ffmpeg -ss <分:秒> -i <動画> -frames:v 1 /tmp/review-NN.png)して観測文と見比べる。
   - 時刻・被写体・文字が一致するか
   - 観測文に「画面に無い解釈・推論・助言」が混じっていないか
   - 読めるはずの文字を「判読不能」と逃げていないか、逆に無理に読み取っていないか
3. 抽出漏れの検査: 動画を 60秒間隔で数カ所再生相当のフレームを抽出し、観測表に載っていないスライド/画面が
   ないか確認する(20秒等間隔抽出は短いスライドを取りこぼしうる。取りこぼしが知識に関わる場面なら差し戻し材料)。
4. 量の妥当性: 保存枚数が同一画面の重複で過多になっていないか/知識のある場面が欠けていないか。
5. 台帳整合: index.md の行・log.md の記録・frontmatter visual_ingested・manifest completed の一致を確認。
6. 判定: 「承認(この群の量産可)/条件付き承認(修正点を列挙、修正確認後に量産可)/差し戻し」を、
   具体的な修正指示(箇所・修正内容)つきで返す。曖昧な承認はしない。

注意: レビュー中に成果物を修正しないこと(修正は実行セッションの仕事)。発見した問題はすべて列挙する。
```

記入例(ひづるめ ch12 の場合): slug=`coloso-hizurume-ch12-gaze-guidance`、
元動画=`raw/_coloso/2026_05_31_ひづるめ/_attachments/12_01.mp4 と 12_02.mp4(2本とも確認)`。

## 1章あたりの正規手順(v2.3・batch1 実績を反映)

1. dry-run → snapshot(抽出前・retrofit は付けない)→ temp 抽出。
   分割動画は `--page` 反復指定(1章=1 source=1 frames dir=1 manifest)。
2. 盲検読取: **wiki を参照しないサブエージェント**(1体あたり約13枚)。プロンプトテンプレは
   [[hizurume-visual-ingest-handoff-plan]] を使う。統括側は既存観測を読んだ後に自らフレームを読まない。
3. recheck: max(3, 10%切り上げ)枚を別サブエージェントで盲検再読取 → verdict 記録。
   不一致は原寸クロップで確定(corrected)か要確認表記(marked-uncertain)。note 必須。
4. フレーム本保存: `wiki/assets/frames/<slug>/<講座>-chNN[-pP]-MMmSSs.png` — **必ず .png を付ける**
   (表・ファイル・manifest の3か所で一貫。batch1 で4章74ファイルに欠落事故)。
5. manifest 構築 → source 節挿入(5列表=単一動画/6列表=分割動画に動画列)→
   `visual_ingested` + manifest `completed` 付与。
6. ゲート check --phase complete PASS → 台帳更新 → inbox 申告。
   **順序の注意**: flag はゲート PASS 後に付ける。flag 反映後の再検査は本文非破壊が引っかかるため、
   batch1 の前例どおり抽出時 snapshot を `snapshot-pre.json` に退避→現状を `--retrofit` で再記録→
   最終 check(遡及基準)で PASS を確認する。raw/動画 SHA-256 の退避前後一致を必ず機械確認。

## 落とし穴(batch1 2026-08-24 の追加教訓+既存11条)

既存の落とし穴1〜11条([[hizurume-visual-ingest-handoff-plan]])は有効。追加:

12. **読取者の「全N枚読了」報告を鵜呑みしない**: ye_jji p1-12m20s を読み飛ばす事故があった。
    抽出枚数と回収した観測ブロック数を必ず突き合わせる(観測ブロック単位で数える)。
13. **フレーム取り違え(隣接フレームの誤帰属)が実在**: ye_jji で2件。字幕が違う・画面状態が違う
    行は隣の時刻の取り違えを疑い、原寸再読で確定する。
14. **観測/typo バリアント**: 読取結果のパースは「観測/観渻/観渇/観渴/観渨」等の表記揺れに対応する
    こと。completed ファイルを機械パースする場合は全バリアントを grep で事前確認。
15. **サブエージェントの network_error**: 再試行して落ちる場合は本セッションの原寸再読で代替してよいが、
    manifest の recheck.method に代替を明記する(黙って代替しない)。
16. **temp 揮発**: 抽出を temp に置いたら、読取完了前でも冒頭で KB 側 staging へ退避する
    (batch1 は冒頭退避で事故ゼロ。全章完成後に staging を削除)。
17. **並行セッションの log.md 追記**: 追記前に自分のエントリの存在確認をし、他 AI のエントリを
    削除・書き換えない(batch1 後半で gf2 セッションの追記が割り込んだ実例)。
18. **ゲートの盲点**: ゲートは「表と実ファイルの一致」を見るため、両者に同じ欠落(.png 拡張子漏れ等)
    があると通る。完成後の最終検収で人の目(または find での拡張子チェック等の独立チェック)を入れる。

## 現在地(2026-08-25 実測・ch06 レビュー完了後に更新)

- **ひづるめ ch06 パイロット(B1)の独立レビュー完了(2026-08-25)**: 条件付き承認。
  修正点(ev-019 追加: 02:57 の節タイトルカード「私(ひづるめ)の描き方」抽出漏れ補完、
  ev-009 のグリザイユ2 末行「グレー 陰影を細かく」→「グレー 陰影 決められた明度」訂正、
  方式行・manifest・index の 18→19 枚更新)は同日適用済み、gate complete 再 PASS。
  ユーザーの進行指示済み → **B1 残り(07/09/13/14)の量産開始条件を満たす**。
  レビュー手法(11時刻 PSNR 再抽出+10秒間隔全帯域スイープ+シーン変化検出)は
  「20秒間隔の隙間に落ちる 3 秒級スライド」を実検出した。以後のレビューでもスイープ併用を推奨。
- 次の一手: **ひづるめ B1 残り(ch07/09/13/14)** の量産実施。各章完成後に gate complete PASS を確認し、
  独立レビューに回す。他講座パイロット(hide ch06 / marse ch08 / sasa ch03 / ye_jji ch06)は並行着手可。
- 講師別の並行セッション開始指示文は「セッション開始指示文(コピペ用・講師別)」節に追加済み(2026-08-25)。
- 完了(visual_ingested 済み)16章: hide ch02〜05 / hizurume ch11・12 / marse ch04〜07 / sasa ch01・02 / ye_jji ch02〜05
- batch2 対象 118章: 上記「実行順序」のとおり
- blocked: chan_02(動画0本・41md)、nekojira(動画1本・ch03 snapshot 済みで入手待ち)

## セッション開始指示文(コピペ用・講師別)

別セッションを講師ごとに立ち上げ、以下を 1 セッション 1 個貼る。**複数講師を同時並行させる場合の共通ルール**:
共有ファイル(`index.md` / `log.md` / 本資料 / `quality-gate.json`)は書く直前に必ず再読し、追記は最小差分。
他セッションが書いた行の削除・上書きは禁止。衝突を検知したら自分の footprint のみ撤去して停止
(quality-gate.json stop_conditions)。各セッションが触ってよいのは担当講師の章のみ。

### ひづるめ(B1 残り・量産承認済み)

```text
coloso 映像ingest batch2 のひづるめ B1 残り 4 章の量産をお願いします。まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-visual-ingest-batch2-handoff.md

対象(既存 source ページへ ch06 パイロットと同型の手順で映像観測節を追加):
- wiki/sources/coloso-hizurume-ch07-composition.md(動画 07.mp4・単一)
- wiki/sources/coloso-hizurume-ch09-light-shadow-color.md(動画 09_01.mp4+09_02.mp4)
- wiki/sources/coloso-hizurume-ch13-illusion-and-lies.md(動画 13_01.mp4+13_02.mp4)
- wiki/sources/coloso-hizurume-ch14-simplification.md(動画 14_01.mp4 の1本のみ)
動画はすべて raw/_coloso/2026_05_31_ひづるめ/_attachments/ 配下。分割動画は全パートを通しで観測し、パート落ちを manifest で示してください。
各章で dry-run→snapshot(抽出前)→抽出(20秒間隔+文字起こし誘導)→盲検読取→第2読者(max(3,10%))→不一致は原寸再読→manifest→gate complete PASS→index/log 更新→visual_ingested 付与→inbox 申告まで完走。
完成宣言前に動画全帯域を 10 秒間隔でスイープし、観測表に載っていないスライド・画面がないか自己点検すること(20秒間隔の取りこぼしは ch06 で実害確認済み)。
B1 は量産承認済みなので章ごとの承認待ちは不要。全章完成後、独立レビュー用のレビュー指示文を私に渡してください。
ディスク実測を正本にし、ゲート検査と台帳更新(index/log)と inbox 申告を省略しないでください。
```

### hide(パイロット ch06)

```text
coloso 映像ingest batch2 の hide 群パイロット ch06 を実施してください。まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-visual-ingest-batch2-handoff.md

対象: wiki/sources/coloso-hide-ch06-toushin-character.md / raw/_coloso/2026_05_31_hide_01/coloso_hide_06 頭身ごとのキャラクターの特徴と描き分け.md / 動画 raw/_coloso/2026_05_31_hide_01/_attachments/06.mp4(単一)。
パイロット coloso-hizurume-ch06-drawing-types と同型の手順で、既存 source ページへ映像観測節を追加。
dry-run→snapshot(抽出前)→抽出(20秒間隔+文字起こし誘導)→盲検読取→第2読者(max(3,10%))→不一致は原寸再読→manifest→gate complete PASS→index/log 更新→visual_ingested 付与→inbox 申告まで完走。
完成宣言前に動画全帯域を 10 秒間隔でスイープし、観測表に載っていないスライド・画面がないか自己点検すること(20秒間隔の取りこぼしは ch06 で実害確認済み)。
注意: hide 群には文字起こしの無い metadata-only 章があり、そこでは動画が唯一の情報源です。パイロットで「動画だけから観測を起こす手順」を確立し、量産時に適用してください。
完成したら gate PASS・台帳更新まで行い、レビュー指示文(引き継ぎ資料のテンプレ)を私に渡して停止。承認まで hide 群の残りに進まないでください。
このセッションが触ってよいのは hide の章のみ。共有ファイルは書く直前に再読し、追記は最小差分。
```

### マーセ(パイロット ch08)

```text
coloso 映像ingest batch2 の marse 群パイロット ch08 を実施してください。まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-visual-ingest-batch2-handoff.md

対象: wiki/sources/coloso-marse-ch08-focus-first-composition.md / raw/_coloso/2026_05_30_マーセ/coloso_マーセ_08 どこを見せたいのか最初に決めておく.md / 動画 raw/_coloso/2026_05_30_マーセ/_attachments/08.mp4(単一)。
パイロット coloso-hizurume-ch06-drawing-types と同型の手順で、既存 source ページへ映像観測節を追加。
dry-run→snapshot(抽出前)→抽出(20秒間隔+文字起こし誘導)→盲検読取→第2読者(max(3,10%))→不一致は原寸再読→manifest→gate complete PASS→index/log 更新→visual_ingested 付与→inbox 申告まで完走。
完成宣言前に動画全帯域を 10 秒間隔でスイープし、観測表に載っていないスライド・画面がないか自己点検すること(20秒間隔の取りこぼしは ch06 で実害確認済み)。
注意: marse はスライド講義型で同一スライドが長く持続します。同一スライドの重複フレームは保存せず廃棄し、その運用を manifest の note と log に明記してください。
完成したら gate PASS・台帳更新まで行い、レビュー指示文(引き継ぎ資料のテンプレ)を私に渡して停止。承認まで marse 群の残りに進まないでください。
このセッションが触ってよいのは marse の章のみ。共有ファイルは書く直前に再読し、追記は最小差分。
```

### 佐々(パイロット ch03)

```text
coloso 映像ingest batch2 の sasa 群パイロット ch03 を実施してください。まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-visual-ingest-batch2-handoff.md

対象: wiki/sources/coloso-sasa-ch03-growth-mechanism.md / raw/_coloso/2026_05_31_佐々/coloso_佐々_03 成長とは？：努力が結果に変わる仕組み.md / 動画 raw/_coloso/2026_05_31_佐々/_attachments/03.mp4(単一)。
パイロット coloso-hizurume-ch06-drawing-types と同型の手順で、既存 source ページへ映像観測節を追加。
dry-run→snapshot(抽出前)→抽出(20秒間隔+文字起こし誘導)→盲検読取→第2読者(max(3,10%))→不一致は原寸再読→manifest→gate complete PASS→index/log 更新→visual_ingested 付与→inbox 申告まで完走。
完成宣言前に動画全帯域を 10 秒間隔でスイープし、観測表に載っていないスライド・画面がないか自己点検すること(20秒間隔の取りこぼしは ch06 で実害確認済み)。
注意: sasa は 34 章のスライド講義型で観測密度が低めです。少数サンプルで「映像価値なし」と判定する場合も、判定理由を必ず log に記録してください(省略判定の根拠を残す)。
完成したら gate PASS・台帳更新まで行い、レビュー指示文(引き継ぎ資料のテンプレ)を私に渡して停止。承認まで sasa 群の残りに進まないでください。
このセッションが触ってよいのは sasa の章のみ。共有ファイルは書く直前に再読し、追記は最小差分。
```

### ye_jji(パイロット ch06)

```text
coloso 映像ingest batch2 の ye_jji 群パイロット ch06 を実施してください。まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-visual-ingest-batch2-handoff.md

対象: wiki/sources/coloso-ye-jji-ch06-texture-applied.md / raw/_coloso/01_coloso_ye_jji/ye_jji_06. 多様なテクスチャー描写_01〜05.md(+_資料.md) / 動画 raw/_coloso/01_coloso_ye_jji/_attachments/06_01.mov〜06_05.mov(5本分割)。
ch02〜ch05 で確立済みの実績手法(分割読取+第2読者+原寸再読)を踏襲し、既存 source ページへ映像観測節を追加。全パートを通しで観測し、パート落ちがないことを manifest で示してください。
dry-run→snapshot(抽出前)→抽出(20秒間隔+文字起こし誘導)→盲検読取→第2読者(max(3,10%))→不一致は原寸再読→manifest→gate complete PASS→index/log 更新→visual_ingested 付与→inbox 申告まで完走。
完成宣言前に全パートを 10 秒間隔でスイープし、観測表に載っていないスライド・画面がないか自己点検すること(20秒間隔の取りこぼしは ch06 で実害確認済み)。
注意: 字幕焼き込み+ハングル講座です。字幕の変化とスライド/作例の両方を観測し、ハングル手書き等は「判読不能」と正直に記載してください(無理に読まない)。
完成したら gate PASS・台帳更新まで行い、レビュー指示文(引き継ぎ資料のテンプレ)を私に渡して停止。承認まで ye_jji 群の残りに進まないでください。
このセッションが触ってよいのは ye_jji の章のみ。共有ファイルは書く直前に再読し、追記は最小差分。
```

## 使わなかったもの・落とした情報

なし(本資料は新規作成。batch1 の実測値は log.md 2026-08-24 の各 ingest エントリを正本とする)。

## 関連リンク

- [[coloso-batch-resume-handoff]] — batch1 引き継ぎ(完了済み・完了記録入り)
- [[hizurume-visual-ingest-handoff-plan]] — ひづるめ承認済み計画(B1〜B4・落とし穴1〜11条の正本)
- [[video-visual-ingest-design]] — 設計正本 v2.3
- [[coloso-visual-ingest-resume-inventory]] — 8/23 棚卸し(全体地図)
- `wiki/builds/coloso-visual-ingest-batch2/quality-gate.json` — 機械品質ゲート(plan PASS)
