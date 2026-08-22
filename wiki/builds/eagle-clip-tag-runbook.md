---
type: build
title: Eagle 100枚棚展開 runbook(CLIP候補→確認→タグ書き戻し)
created: 2026-07-05
sources:
  - eagle-personalize-workflow-redesign-2026-07-03
status: superseded
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-06
---

> [!warning] 2026-07-06 プロジェクト断念
> 武田さんがプロジェクト断念を決定。**新規カテゴリの追加実行は行わない**。既存棚
> (水着・制服jk等、実機確認済み)の運用参照用にのみ本ページを残す。根本原因は
> [[eagle-personalize-workflow-redesign-2026-07-03]] の断念節を参照。

# Eagle 100枚棚展開 runbook(断念済み・記録用)

服装などのカテゴリを「clip候補_◯◯_01/_02…」の100枚連番タグとしてEagleに棚化する定型手順。

> **タグの意味定義(2026-07-05 メイド服試行を受けて明文化)**: `clip候補_◯◯` は
> 「その系統の視覚的な近縁を広めに集めた候補棚」であり、厳密な分類ではない。
> 厳密な棚は武田さんの手作業フォルダ(05_服装_◯◯等)が正本。CLIPは意味の近所を
> 拾う仕組みなので、周辺衣装(例: メイド服ならフリルエプロン系・アイドル衣装)の混入は
> 仕様の範囲。混入が「明白な別物(実写・スクショ・無関係)2割超」の場合のみ作り直す。
**どのAI環境(Claude Sonnet/Fable、Codex GPT-5.4等)でも、この文書だけで実行できる**ことを目標に書く。
実績: 水着・制服で一周済み(2026-07-04、100/100成功・武田さん実機確認済み)。

## 前提(環境)

- 作業ディレクトリ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
- Python: `tools/eagle_clip_env/bin/python`(CLIP環境入り venv)。実行前に
  `export HF_HOME="$PWD/tools/eagle_clip_env/hf_cache"` を設定
- 索引: `tools/eagle_sort_data/clip_full.db`(33,703枚、2026-07-04作成)
- Eagleアプリが起動していること(書き戻し時)
- Eagle書き込み手段(どちらか):
  - Claude: MCP `mcp__eagle-mcp__item_add_tags`
  - Codex等MCPなし環境: `node .claude/skills/eagle/scripts/eagle-api-cli.js call item_add_tags --json '{"ids": [...], "tags": ["タグ名"]}'`(動作確認済み 2026-07-05)

## 安全規則(全環境共通・絶対)

1. Eagleへの操作は**タグ追加のみ**。フォルダ移動・削除・メモ変更・レーティング・ゴミ箱は禁止
2. 書き込み**前**に、対象item_id全件をログJSON(下記書式)に保存する
3. 書き込みは武田さんが確認ページを見て承認した後のみ
4. タグ名は `clip候補_` 接頭辞を厳守(既存タグ空間を汚さない)
5. 取り消しは `item_remove_tags`(MCP)または CLI の同名ツールで、ログのitem_id全件に対して実行

## 手順(1カテゴリ=15〜30分、うち武田さんの手間は数分)

### ① 候補生成(dry-run・Eagle無変更)

```bash
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
export HF_HOME="$PWD/tools/eagle_clip_env/hf_cache"
tools/eagle_clip_env/bin/python tools/eagle_clip_search.py tag-review \
  --tag "clip候補_メイド服" \
  --english "an anime illustration of a girl wearing a maid outfit with apron and frills" \
  --folder-keyword "メイド服" \
  --slug tagreview-maid --chunks 3 --numbered
```

- 出力: `tools/eagle_sort_data/clip_full/tagreview-maid_01.html`(スコア1〜100位)、`_02`、`_03` + 同名JSON
- 「生成ai」フォルダ所属画像は自動除外される(コード内蔵)
- チャンク数の目安: 新規候補総数(コマンドが表示)÷100。まず2〜3チャンクから

### ② AI事前判定(実行AIが画像を見る)

各チャンクのHTML内サムネイル(`tagreview-*_files/`)を実行AIが目視し、明白な外れ
(実写・スクショ・別衣装)を数える。**外れが2割を超えるチャンクは武田さんに出さず**、
検索文の調整または閾値引き上げ(--percentile 60〜75)で作り直す。

### ③ 武田さんの確認

- 確認ページをブラウザで開く(`open <htmlパス>` または Finderで Cmd+Shift+G →右クリック→ブラウザ)
- 依頼文の例: 「メイド服の_01と_02を開きました。流し見して、明らかに違う画像が多いか
  少ないかだけ教えてください」
- 判定基準: 大体合ってる→承認 / 外れが目立つ→そのチャンクは見送り

### ④ タグ書き戻し(承認後のみ)

1. ログJSONを先に保存: `tools/eagle_sort_data/clip_full/tag_writeback_log_YYYYMMDD.json`
   書式: `{"executed_at":…, "batches":[{"tag":"clip候補_メイド服_01","item_ids":[…全件…]}], "status":…}`
   (実例: `tag_writeback_log_20260704.json`)
2. チャンクごとに書き込み(タグ名はチャンクJSONの`tag`をそのまま使う)
3. 検証: タグ検索(`item_get` tags指定)で件数がチャンク枚数と一致するか確認
4. ログの status を executed に更新

### ⑤ 武田さんの棚化(手動・数クリック)

Eagleサイドバー右クリック→スマートフォルダ作成→条件: タグ=`clip候補_メイド服_01`。
(スマートフォルダ作成はAPIで自動化できない。実測済み)

### ⑥ 記録

`log.md` に1エントリ(カテゴリ・チャンク数・書き込み件数・ログファイル名)。
[[eagle-folder-sort]] の変更履歴に1行。

## 服装カテゴリ対応表(初期分)

| カテゴリ | --folder-keyword | --english | 信頼度予想 |
|---|---|---|---|
| メイド服 | メイド服 | an anime illustration of a girl wearing a maid outfit with apron and frills | 🟡(2026-07-05実測: 周辺衣装が混ざる。_03は見送り候補) |
| バニー | バニー | an anime illustration of a girl in a bunny girl leotard costume with bunny ears | 🟢(2026-07-06実測: p60 `_01/_02` 書き戻し成功、`_03` 見送り) |
| ドレス | ドレス | an anime illustration of a girl wearing an elegant dress or gown | 🟢(2026-07-06実測: `_01/_02` 書き戻し成功、`_03` は参考候補) |
| チーパオ | チーパオ | an anime illustration of a girl wearing a Chinese qipao cheongsam dress | 🟢(2026-07-06実測: `_01/_02` 書き戻し成功、`_03` は参考候補) |
| サキュバス | サキュバス | an anime illustration of a succubus girl with demon horns, wings or tail | 🟡(2026-07-06実測: `_01/_02/_03` 書き戻し成功。概念混じり) |
| シスター | シスター | an anime illustration of a girl in a nun outfit with habit | 🔴(2026-07-06実測: p70でも混入が残り、今回は採用なし) |
| 浴衣 | 浴衣 | an anime illustration of a girl wearing a Japanese yukata kimono | 🟢(2026-07-06実測: `_01/_02` 書き戻し成功、`_03` は参考候補) |
| タイツ | タイツ | an anime illustration of a girl wearing black tights or pantyhose | 🟡(2026-07-06実測: `_01/_02/_03` 書き戻し成功。部分要素) |
| 下着 | 下着 | an anime illustration of a girl in underwear or lingerie | 🟡(2026-07-06実測: `_01/_02/_03` 書き戻し成功。nsfw境界が曖昧) |
| パーカー | パーカー | an anime illustration of a girl wearing a hoodie or sweatshirt | 🟡(2026-07-06実測: p60 `_01/_02` 書き戻し成功、`_03` 見送り) |

- 信頼度: 🟢=水着・制服の実測(候補ページの外れ僅少)と同型の見込み / 🟡=②の事前判定を厳しめに /
  🔴(構図・小物系)=このrunbookの対象外(補強方式が別途必要)
- 実測後、この表の信頼度を実績値に更新していくこと

## 索引の差分更新(鮮度運用・2026-07-06 追加)

`clip_full.db` は作成時点(2026-07-04)のスナップショット。新規保存画像を棚に入れるには差分更新が必要。

```bash
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
export HF_HOME="$PWD/tools/eagle_clip_env/hf_cache"
tools/eagle_clip_env/bin/python tools/eagle_folder_sort.py sync          # facts.db再構築(数分)
tools/eagle_clip_env/bin/python tools/eagle_clip_search.py embed --full  # 計算済みIDはスキップ=新規分のみ(数分)
```

- 実行タイミング: **拡張バッチの前に必ず** + 週1目安。ミドル級の定型作業
- 実行後、log.md に新規数値化枚数を1行記録

## モデル・環境ポリシー(2026-07-05 改訂)

- **環境・モデル・推論レベルは武田さんがタスクごとに選ぶ**。固定ルールはない
- 目安: **手順が文書化済みの反復作業(本runbook全工程)=ミドル級で十分**
  (Claude Sonnet 5 / Codex GPT-5.4、推論低〜中)。
  **文書を書き換える判断(設計変更・閾値変更・原因調査・品質監査・迷い画像の再判定)=上位級**(Fable等)
- Codex GPT-5.4での実行は見込みありだが**初回未検証**。初回は1カテゴリ通し→結果を上位級が
  抜き取り監査(20枚)してから常用する。5.4 miniは判定ブレを確認してから
- 迷い画像の扱い: 実行AIが②③で判断に迷った画像はタグを付けず「迷いリスト」
  (ログJSONに `unsure_item_ids`)として残し、上位級セッションで再判定

## 関連リンク

- [[eagle-folder-sort]](エンジン全体のbuild正本)
- [[eagle-personalize-workflow-redesign-2026-07-03]](設計の経緯・実測値)
- [[canvas-ingest-eagle-feedback-guide]](Canvas側のEagleフィードバック指南書)
