---
type: build
title: Eグル フォルダ振り分けエンジン(eagle_folder_sort.py)
created: 2026-07-03
sources:
  - eagle-vector-db-personalized-folder-sort-2026-07-02
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-03
---

# Eグル フォルダ振り分けエンジン(eagle_folder_sort.py)

## 目的(取り違え防止)

- **最終目的**: 武田さんの保存意図に沿って、Eグルの画像をフォルダ分けできるワークフロー。
  AIが「武田さんならこの理由で保存していそう」と考え、合うフォルダを提案し、確信が高い
  ものは自動で入れ、怪しいものは保留する。武田さんに数値表を読ませない。
- **中間目的**: AIがフォルダ分け判断に使える材料(事実ストア・類似計算・提案生成)を作る。
- パーソナライズの定義は「フォルダ振り分けの自動化」そのものではなく、**保存理由・傾向性を
  事実データとして抽出できる状態を作ること**(2026-07-03 武田さん明示)。

## 場所・実行方法

- 本体: `tools/eagle_folder_sort.py`(読み取り専用。Eグル本体には一切書き込まない)
- データ: `tools/eagle_sort_data/`(facts.db・reports/・proposals/)
- 実行(NumPyあり Python):

```bash
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
PY=/Users/takedayousuke/.cache/codex-runtimes/codex-primary-runtime/dependencies/python/bin/python3
$PY tools/eagle_folder_sort.py sync        # Eグルを読み facts.db を作り直す
$PY tools/eagle_folder_sort.py propose --limit 20   # 画像付き dry-run 確認HTML
```

## サブコマンド

| コマンド | 役割 | 誰向け |
|---|---|---|
| `sync` | Eグルの metadata.json / vectors.db を読み取り専用でスキャンし `facts.db` を構築 | 裏方 |
| `calibrate` | 既存フォルダをどれだけ復元できるか測る(leave-one-out) | AI/実装者が裏で読む材料。**武田さんに読ませない** |
| `trends-lite` | フォルダ分布・共起・作者上位 | 同上 |
| `propose` | 未整理画像への提案を**画像付きHTML**で出す(dry-run) | **武田さん向け成果物** |

## propose の判定ロジック(v1・2026-07-03)

- 主軸 = 「似ている画像20枚(k=20)のうち何枚が同じフォルダに入っているか」(support)
- そっくり度(コサイン類似度)は品質ゲート。実測分布: 0.35未満=ほぼ他人 / 0.7超=かなり近い
- `confidence`(top_score/total)は教師が複数フォルダ所属のため構造的に低く出る(0.05〜0.14)。
  **判定に使わない**(v1で判明した落とし穴)
- 判定: 自動で入れてよさそう(support≥12 かつ そっくり度≥0.50) / 画像を見て確認したい
  (support≥6) / 保留(それ未満、または そっくり度<0.35)
- 「見た目は近いのにフォルダが割れている」保留は、384次元だけでは保存意図が読めない例として
  HTML上で明示 → CLIP/vision層を足す判断材料になる

## 対象・教師の定義

- 教師 = 意味あるフォルダ3つ以上(約14,255枚)。ただし**既存フォルダは完全な正解ではなく、
  連番管理時代・整理リソース不足が混ざったノイズ混じりの行動ログ**として扱う(2026-07-03)
- propose の対象 = まず意味あるフォルダ0個(約1,047枚)。1〜2フォルダ画像への「追加提案」は
  誤爆リスクが上がるため後段
- 除外フォルダ = 未整理_親フォルダ・動画_保存フォルダ・_in_box_不要 + 子(計51)

## 現状(2026-07-03)

- `sync` / `calibrate` / `trends-lite`: Codex実装・完走確認済み(items 34,948 / vectors 34,904)
- `calibrate --limit 100`: precision 0.240 / top1 0.440 / top3 0.640(小規模・性能結論ではない)
- `propose`: Fable実装。20枚サンプルで生成済み
  (`tools/eagle_sort_data/proposals/propose-dryrun-01.html`、自動1/確認13/保留6)。
  画像リンク116件全て有効を機械確認済み。**武田さんの実機確認(HTMLを見る)は未実施**
- Eグルへの実フォルダ分けは未実装・未実施(意図的。dry-runで意味が通ることの確認が先)

## 運用ルール

- Eグル本体(metadata.json / vectors.db / タグ / フォルダ)には書き込まない。実書き込みを
  導入する段階では Eグル MCP `item_add_to_folders` を使う(HTTP APIはフォルダ変更不可)
- 事実の正本は `tools/eagle_sort_data/facts.db`。Eグルへの書き戻しは投影
- 武田さんへの提示物は画像付きHTML。数値レポートは裏方資料

## 変更履歴

- 2026-07-03: Codexが sync/calibrate/trends-lite を実装。武田さんに数値表を見せる進め方が
  不評となりFableへ引き継ぎ。Fableが propose(画像付きdry-run HTML)を追加、confidence を
  判定から外し support 主軸の判定 v1 を実装
- 2026-07-03: 武田さん実機で画像が全滅(Firefoxはローカルhtmlから同じフォルダ以下の画像しか
  読めない安全ルール。Eグル本体を直接参照していたためブロック)。画像を
  `proposals/<prefix>_files/` へコピーして相対参照する方式に修正、全116参照の実在を機械確認済み
- 2026-07-03: 武田さん確認の結果、初版dry-runは全滅。(1)対象バグ=動画_保存フォルダ・_in_box_不要
  配下と動画ファイルが対象に混入(20枚中10枚)→`load_target_items`で除外修正
  (2)384次元の提案は精度不足と武田さん判定。
- 2026-07-03: v2として**Fableが20枚をvisionで直接見た提案**
  (`proposals/propose-fable-vision-01.html`、自動8/確認7/保留5)を作成。品質上限の確認用。
  発見: 未整理プールの大半はフィギュア商品写真・実写コスプレ・生成AI how-toスクショ・ネタ画像で、
  **フィギュア写真の置き場が現フォルダ体系に無い**(01_形態_立体感参考_01は線画練習置き場と実物確認)。
  384が外れる構造的理由=見た目の近さでは資料種別(フィギュア/実写/スクショ/イラスト)と保存意図を
  区別できない。武田さんのv2確認待ち

## CLIP検索パイロット(段階1・2026-07-04)

- 新規ツール: `tools/eagle_clip_search.py`(既存ファイル無変更・Eagle無書き込み)。
  実行環境 `tools/eagle_clip_env/`(venv+モデル、外付けSSD内)。モデルは SigLIP i18n(多言語)。
- データ: `tools/eagle_sort_data/clip_pilot.db`(1,918枚=未整理寄り1,600+対照群100×4、
  読み込み失敗0)。M2/MPSで数値化完了。
- 出力: `tools/eagle_sort_data/clip_pilot/index.html` から5語(水着/制服/ローアングル/横顔/シュシュ)
  ×日英の検索結果グリッドへ。画像リンク400件欠け0を機械確認。
- 機械集計(上位20枚中の対照群数、偶然なら約1枚): 水着9(日)/12(英)、制服10(日)/14(英)、
  シュシュ6(日)/6(英)、ローアングル1(日)/1(英)。スポット目視: シュシュ1位=手首にフリル飾りの
  画像で妥当、ローアングル1位=ポスターで外れ。
- 暫定所見: 大カテゴリ(服装・小物)は日英とも機能、**構図語(ローアングル)は弱い**。
  横顔は対照群なしのため武田さんの目視待ち。武田さんの判定(使える/微妙/使えない×5語)が
  段階2(全量索引)へ進むゲート。
- 武田さんの最終判定(2026-07-04): 水着=使える、制服=使える → 実装へ進める。
  ローアングル/横顔/シュシュ=使えない → 補強策の検討へ。
- アンサンブル実験(言い換え文の平均): ローアングル・シュシュとも**効果なし**(上位20ヒット数が
  単一文と同じ)。横顔は対応フォルダが存在せず自動採点不可と判明。→ 補強は言い換えでなく
  タイル分割/vision LLM絞り込みへ軸足移動(2026-07-04)。
- 全量索引(段階2a)完了: `eagle_clip_search.py embed --full`(新設、既存コマンド無変更)。
  33,703枚(動画・_in_box_不要除く全ライブラリ)を `clip_full.db`(144MB)へ。読み込み失敗0。
- 閾値較正: 既存フォルダ画像のスコア分布の下位5%点は緩すぎ(全体の84%該当)、
  **中央値を閾値に採用**。新規候補=水着4,037枚・制服2,158枚。
  `tag-review`サブコマンド(新設)で候補上位100枚の画像付き確認HTML/JSONを生成
  (`clip_full/tagreview-mizugi.html` / `tagreview-seifuku.html`、Eagle無書き込み)。
  1位を目視確認: 水着=ビキニのイラスト、制服=セーラー服のイラストで妥当。
  武田さんの100枚全体確認と、実際のタグ書き戻し(100枚パイロット)は次回。
- 新事実: Eagle MCPの `folder_create`/`folder_update` はスマートフォルダに非対応(通常フォルダ専用)。
  スマートフォルダの作成は武田さんの手動操作が必須(自動化できない)。

## 関連リンク

- [[eagle-vector-db-personalized-folder-sort-2026-07-02]](背景分析・vectors.db の仕組み)
- [[eagle-folder-prediction-pilot-2026-06-14]](前回の振り分け予測パイロット・二軸の結論)
- [[x-eagle-free-save-pilot]](保存時のフォルダ判子・X文脈メモの由来)
- [[eagle-personalize-workflow-redesign-2026-07-03]](再設計の正本・実験記録)

## タグ書き戻しパイロット(2026-07-04・実行済み)

- 武田さん承認(水着ページ目視「上位100枚全部水着」)を受け、**Eagleへの初の書き込み**を実行。
- 内容: `clip候補_水着` 100枚 + `clip候補_制服jk` 100枚(MCP `item_add_tags`、タグ追加のみ。
  フォルダ・メモ・レーティング無変更)。両方100/100成功。生成aiフォルダ除外済み候補を使用。
- 検証: `item_get` のタグ検索で `clip候補_水着` がちょうど100件返ることを確認。
- 取り消し: 全item_idを `tools/eagle_sort_data/clip_full/tag_writeback_log_20260704.json` に
  記録済み。`item_remove_tags` で全量取り消し可能。
- 残り: 武田さんのEagle実機確認(タグが見えるか・タグ検索/スマートフォルダで棚になるか)。
