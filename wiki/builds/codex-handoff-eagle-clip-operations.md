---
type: build
title: Codex引き継ぎ — Eagle 100枚棚展開 / Canvas ingest 実行タスク
created: 2026-07-05
sources:
  - eagle-clip-tag-runbook
  - canvas-ingest-eagle-feedback-guide
  - eagle-meta-tags-design
status: superseded
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-06
---

> [!warning] 2026-07-06 プロジェクト断念・第2期キューは中止
> 武田さんがプロジェクト断念を決定。**第2期キュー(M4/W2等)の新規実行は行わない**。
> 根本原因は [[eagle-personalize-workflow-redesign-2026-07-03]] の断念節を参照。
> 第1期(服装100枚棚展開・実機確認済み)とC1(Canvas還元・実施済み)は実績として本文を残す。

# Codex引き継ぎ — Eagle 100枚棚展開 / Canvas ingest(断念済み・記録用)

武田さんがこのファイルパスをCodexに渡すだけで作業を開始できる自己完結の引き継ぎ資料。
対象タスクは2系統。どちらも**手順書が完備された反復作業**であり、GPT-5.4(推論低〜中)で
実行可能な想定(初回は非miniを推奨。結果は後で上位モデルが抜き取り監査する)。

## 背景(1分で分かる版)

- Eagleライブラリ(画像33,703枚)に対し、CLIP(ローカル画像検索モデル)の索引が構築済み
  (`tools/eagle_sort_data/clip_full.db`)
- 「clip候補_水着」「clip候補_制服jk」の各100枚タグ書き戻しまで一周完了し、武田さんが
  Eagle実機でスマートフォルダ化済み(2026-07-04)
- 目的はEagle画像の保存後の検索・再分類運用を作ること。詳細な経緯は
  [[eagle-personalize-workflow-redesign-2026-07-03]]

## タスクA: 服装カテゴリの100枚棚展開

**手順書(正本・必読)**: `wiki/builds/eagle-clip-tag-runbook.md`

- 武田さんから「◯◯カテゴリを展開して」と指示されたら、runbookの①〜⑥を順に実行する
- カテゴリ→検索文→folder-keyword の対応表はrunbook内にある
- 要点: ①候補生成(dry-run) ②自分でサムネイルを目視し外れ2割超なら作り直し
  ③武田さんに確認ページを見せる ④**承認後のみ**タグ書き戻し(事前ログ必須) ⑤検証 ⑥記録

### 一括バッチ指示(2026-07-05 武田さん承認済み)

**メイド服_01/_02級の精度(=周辺衣装込みの候補棚として妥当)が見込めるカテゴリは、
runbook対応表の残り全カテゴリを順に処理してよい**。処理キュー:

1. バニー → 2. ドレス → 3. チーパオ → 4. シスター → 5. 浴衣 → 6. パーカー →
7. サキュバス → 8. タイツ → 9. 下着

- 品質基準はメイド服の実績が物差し: 周辺衣装の混入は許容(タグの意味定義=視覚近縁の候補棚)。
  **明白な別物(実写・スクショ・無関係)が2割超のチャンクだけ**見送りまたは作り直し
- チャンク数は候補総数÷100で判断(上限3)。スコア下位チャンクの質が落ちたら
  メイド服_03の前例どおりそのチャンクを見送る
- 確認(③)は複数カテゴリまとめて武田さんに出してよい(1カテゴリずつ待たなくてよい)。
  ただし書き戻し(④)は承認が出たカテゴリのみ
- 各カテゴリ完了ごとにrunbook対応表の信頼度列を実績で更新

## タスクB: Canvas ingest + 実戦_使用済みタグ

**手順書(正本・必読)**: `wiki/builds/canvas-ingest-eagle-feedback-guide.md`

- 優先1(2026_07の水着系Canvas 3個)から。1バッチ2〜3個
- canvas-ingest skill の正本は `skills/canvas-ingest/SKILL.md`(dry-run→本処理の流れ厳守)
- ingest後、sidecarのsha256確定(confirmed)分にのみ `実戦_使用済み` タグを付与

## タスクキュー第2期(2026-07-06 武田さん承認済み・設計正本は [[eagle-meta-tags-design]])

タスクA拡張バッチの完走後、武田さんから「キューの次へ」と指示されたら以下を**上から順に**実行する。
各タスク=1セッション目安。設計判断が要る箇所は必ず [[eagle-meta-tags-design]] を読む。

### F1: 索引の差分更新(鮮度)

runbook「索引の差分更新」節(下記追記済み)のとおり `sync` → `embed --full` を実行し、
新規に数値化された枚数を log.md に記録する。

### M1: `tools/eagle_meta_tags.py` 新規実装(辞書生成+答え合わせ)

- **新規ファイル**。既存ツールの編集は引き続き禁止。torch不要、`tools/eagle_sort_data/facts.db`
  のみ読む(素のpython3で動くこと)
- サブコマンド:
  - `dict-build`: フォルダ名が `作品` / `キャラ` / `絵柄` を含む手作業フォルダ(実測: 48/42/81)
    ごとに、既存メンバーの author_id 分布を集計し、辞書下書き
    `tools/eagle_sort_data/meta_dict.json` を生成。書式:
    `{"棚名": {"axis": "作品|キャラ|絵柄", "folder_ids": [...], "author_rules": {"@id": 件数},
    "keyword_rules": []}}`(keyword_rules は空枠のまま出す。充填は上位級の仕事)
  - `calibrate`: 各フォルダの既存メンバーの2割をランダムに隠し、残り8割で作った辞書で
    当てられるかを測定。棚ごとの的中率を表で出力
- 完了したら**ここでキューを止め**、武田さんに「M1完了・Fable監修待ち」と報告する。
  M2(辞書の監修・キーワード充填・的中率の採否判定)はFableの仕事

### M3: `meta-review` 実装+絵柄パイロット(M2完了済み 2026-07-06・実行可)

- **辞書は監修済み**: `meta_dict.json` は Fable が M2 で確定済み。`_meta._spec` に使用規則が
  書いてある。要点: **adopt=true の棚のみ対象** / author照合は **author_rules_pruned** を使う
  (author_rules は生データなので使わない) / keyword_rules はファイル名+annotation の部分一致
  (大文字小文字無視) / **keyword_mode=with-work-context のキーワードは、同じ画像が対応作品
  (ブルアカ等)の規則にも当たる場合のみ有効**(同名キャラ誤爆防止)。
  生データのバックアップは `meta_dict_raw_20260706.json`(dict-buildで再生成可)
- `eagle_meta_tags.py` に `meta-review` サブコマンドを追加: 確定辞書を全画像に適用し、
  該当フォルダ**未所属**の画像を100枚連番チャンクHTML/JSONで出力。書式・画像コピー方式は
  既存 `tagreview-*.html/json` と同一(rank_rangeの代わりに規則名を記録)
- タグ名は `候補_絵柄_◯◯_01` 形式(`clip候補_` ではない。新形式は設計正本で承認済み)
- パイロット対象: **絵柄の adopt 4棚(ひづるめ・モ誰・MX2J・米山舞)**。的中率実測は
  ひづるめ1.00 / MX2J 1.00 / モ誰0.95 / 米山舞0.50 — 米山舞のチャンクは②の事前判定を厳しめに
- dry-run→武田さん確認→承認後書き戻し(runbook④〜⑥と同じ)

### C1: Canvas ingest(既存タスクBの優先1)

タスクBの手順のまま実行(2026_07水着系3個→実戦_使用済みタグ)。

### W1: `tools/eagle_pool_judge.py` 新規実装+ローアングルパイロット

- CLIPで `tag-review --chunks 3` を slug `poolreview-lowangle-<日付>` で生成(=上位300枚のプール)
- 実行AIが全300枚を1枚ずつ、[[eagle-meta-tags-design]] の「判定基準書: ローアングル」に従い
  yes/no/unsure 判定し、判定JSON `{"item_id": "yes|no|unsure", ...}` を保存
- **新規ファイル** `eagle_pool_judge.py`: チャンクJSON+判定JSONを突き合わせ、yes分だけの
  確認HTML+書き戻しリストを生成(素のpython3)
- yes件数と確認HTMLを武田さんに報告して停止。ゲート判定(収量50枚以上+Fable抜き取り9割)は
  上位級が行う。**ゲート通過前にタグを書き戻さない**

### M4 / W2(ゲート通過後のみ・上位級の指示があってから)

- M4: 作品・キャラ軸の展開。**絵柄軸は対象外**(2026-07-06 武田さん判定: M3絵柄パイロットは
  技術的には当たったが「作者はファイル名・既存フォルダで探せる」ため運用価値薄で見送り・
  書き戻しなし。作品・キャラは続行と明示)。キャラは同名誤爆率の実測後に一括展開
- W2: シュシュ等の弱カテゴリ横展開(W1と同手順)

### 第2期の追加安全規則

- 新規スクリプト2本の作成は許可(安全規則6の「既存ツール編集禁止」はそのまま)
- 新規スクリプトも書き込み機能は持たせない(dry-run生成まで。Eagle書き込みは従来どおり
  CLI `item_add_tags` を承認後に実行)
- タグ接頭辞は `clip候補_`(CLIP由来)/`候補_作品|キャラ|絵柄_`(規則由来)/`実戦_使用済み` のみ

## 環境(Codex用)

```bash
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
# CLIP実行(候補生成):
export HF_HOME="$PWD/tools/eagle_clip_env/hf_cache"
PY=tools/eagle_clip_env/bin/python           # torch+open_clip入りvenv(numpyもここにある)
# Eagle書き込み(MCPが無い環境用のCLI。動作確認済み):
node .claude/skills/eagle/scripts/eagle-api-cli.js list                     # ツール一覧
node .claude/skills/eagle/scripts/eagle-api-cli.js call item_add_tags \
  --json '{"ids": ["ITEM_ID", ...], "tags": ["タグ名"]}'                     # タグ付与
node .claude/skills/eagle/scripts/eagle-api-cli.js call item_get \
  --json '{"tags": ["タグ名"], "limit": 200}'                               # 検証用
```

- Eagleアプリが起動していないとCLIは失敗する。失敗したら武田さんにEagle起動を依頼
- canvas_ingest.py は素のpython3で動く(numpy不要)。CLIP系だけ上記venvを使う

## 安全規則(絶対・違反したら作業停止)

1. Eagleへの操作は **item_add_tags のみ**。item_update / item_move_to_trash /
   folder系 / tag_merge 等は使用禁止
2. 書き込み前に対象item_id全件を `tools/eagle_sort_data/clip_full/tag_writeback_log_YYYYMMDD.json`
   へ保存(書式は既存の `tag_writeback_log_20260704.json` を踏襲)
3. 書き込みは武田さんの承認後のみ。「確認ページを見せる→承認の言葉をもらう」を飛ばさない
4. raw/ ディレクトリは読み取り専用
5. タグ名は `clip候補_◯◯_NN`(タスクA)と `実戦_使用済み`(タスクB)のみ。新しいタグ体系を発明しない
6. 既存ツールファイル(`tools/eagle_clip_search.py` / `tools/canvas_ingest.py`)の**編集は禁止**。
   不具合や設計疑問が出たら止めて報告(下記エスカレーション)

## エスカレーション(止めて報告する条件)

- 候補ページの外れが2割超で、検索文調整でも改善しない
- スクリプトがエラーで止まる / Eagle CLIが繋がらない
- 手順書に書かれていない判断が必要になった
→ その時点で作業を止め、状況を武田さんに報告。設計変更は上位モデル(Fable)のセッションで行う

## 記録義務

- 各バッチ完了時に `log.md` へ追記(書式: `## [YYYY-MM-DD] query | <内容>`。既存エントリ参照)
- 触ったファイル・書き込み件数・ログファイル名を必ず列挙
- [[eagle-clip-tag-runbook]] の対応表の信頼度列を実績で更新してよい(それ以外の手順書本文は変えない)
