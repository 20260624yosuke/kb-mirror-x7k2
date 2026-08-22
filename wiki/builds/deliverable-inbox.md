---
type: build
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-23
---

# 成果物 Inbox(deliverable-inbox)

## 目的・背景

opencode TUI など**チャット内リンクが押せないハーネス**でも、成果物を確実に確認できる導線を作る。
リンク押下はハーネス側の表示機能依存のため機械的保証にならず(2026-08-23 実測: opencode で無反応)、
「制度・ルール文面」でなく「機械的なスクリプト」で解決する方針を武田さんが明示した。

発端の経緯: 自動オープン案(LLMがタスク完了時に窓を開く)→却下。武田さんは LLM の処理に常時
張り付いておらず、意図しない前面ウィンドウは不快。→「タスク管理術の inbox 枠のように、確認待ち
成果物を把握・管理したい」という構想へ発展し、文脈凍結(開いた後に何を見るべきか分かる)+フォルダ階層
表示+新着順併記で方針確定(選択肢 B)。実装前にサブエージェントによる批判的レビューを実施し、
指摘6件を反映して実装した。

## 構成

| ファイル | 役割 |
|---|---|
| `tools/inbox.py` | 正本 CLI。`add` / `list` / `open` / `done` / `board` |
| `tools/inbox.jsonl` | 追記専用イベントログ。fcntl.flock 排他・破損行は無視して継続 |
| `inbox-dashboard.md`(KBルート直下) | 機械生成ボード。鮮度表示+新着順+フォルダ階層ツリー+vault 外ノード。毎回全再生成され手編集不可 |
| `tools/llm_wiki_inbox.sh` / `llm_wiki_inbox_done.sh` | Raycast 正本2本(ボード表示/処理済み化) |
| `tools/install_llm_wiki_inbox.sh` | Raycast 実体(`~/.config/raycast-scripts/`)と CLI 入口(`~/.local/bin/llm-wiki-inbox`)の配置 |
| `AGENTS.md` / `CLAUDE.md` / `KIMI.md`「成果物 Inbox」節 | LLM への申告指示(3正本すべてに同文系) |

## 使い方

### LLM 側(申告)

```
python3 "<KBルート>/tools/inbox.py" add "<絶対パス>" --origin <claude-code|codex|kimi|opencode> --task "<依頼の要約>" --note "<見て判断すべき点>"
```

- 窓は開かない。同一パスの未処理分がある場合は弾かれ既存短IDが返る。
- 項目 ID はタイムスタンプ由来の短ID(例: `i0823a4f`)。位置番号を使わないのは、複数ハーネス並行時に
  番号ずれで別成果物を誤開放/誤完了化する事故防止(レビュー指摘)。

### ユーザー側(確認)

1. Raycast「**成果物Inboxを開く**」→ 未処理ボード(inbox-dashboard.md)を Obsidian でオープン。
   vault 内項目は wikilink(押せる)、vault 外は絶対パス表記。
2. 必要なものだけ開いて中身を確認。
3. Raycast「**成果物Inbox処理済み**」で短ID入力 → done 化(未処理件数が減る)。CLI なら `llm-wiki-inbox done i0823a4f`。

`open` コマンドは開くだけで処理済みにしない(「開いた=確認済み」の誤完了化防止)。

## 設計上の要点

- **イベントログ型**: add も done も追記のみ。状態は同一IDの最新イベントから算出。履歴保持・
  複数ハーネス同時書き込み・破損耐性を構造的に満たす。
- **ボードオープンは inbox.py 内で完結**: 既存 `open_in_obsidian.py` は `wiki/raw` 配下限定のため
  KBルート直下の dashboard を解決できない(open_in_obsidian.py:43 `SEARCH_DIRS` 制約)。
  URI 生成式は共用(VAULT_NAME を import)し、dashboard の起動は自前で `/usr/bin/open` する。
- **外部SSD未マウント時**: Raycast ラッパーが日本語メッセージを出して正常終了(無言失敗の防止)。

## 使わなかったもの・落とした情報

- **自動オープン**(タスク完了時に LLM が窓を開く案): 却下。武田さんが画面張り付きではないため
  不快になる。影響: 成果物完成の瞬間に窓が出ず、自分のタイミングでボードを開く運用になる。
  戻す方法: `llm-wiki-inbox open --all`(未処理全オープン)を報告時に呼ぶだけ。
- **open 時の自動 done**: 採用案から削除。ダッシュボード内リンクで確認した場合に pending が
  永久残留する問題と、「開いただけで完了扱い」になる誤消し問題の両方を避けるため。
  影響: 確認後にもう1操作(done)が必要。戻す方法: `cmd_open` 内への done イベント追記復活。
- **vault 外ファイルの file:// リンク**: Obsidian 内での押下可否が未検証のため、絶対パス表記のみ。
  押せたほうが良い場合は今後検証。

## 後回し(実害発生後に対応予定)

- 同一パス再 add 時の note/task 更新(upsert)。現状はエラー案内のみ。
- `open --all` の窓嵐対策(件数上限や事前確認)。ユーザー能動実行なので様子見。
- done 履歴による jsonl 肥大化のアーカイブ分離。

## 変更履歴

- **2026-08-23 新設**。単体動作試験済み(add の重複弾き/存在チェック/done/open 分岐/Obsidian URI 形式/
  階層レンダリング/空状態ボード/Raycast ラッパー配置)。Raycast からの実機確認は未実施(武田さん作業)。
- **2026-08-23 同日修正**。ボードの wikilink が無反応との報告を受け、パス形式
  (`[[wiki/…/名前|名前]]`)から日頃の実績があるスラグ形式(`[[ページ名]]`)へ統一。
  同名 md が複数ある場合のみパス形式へ自動フォールバック(`md_stem_counts`)。
  ボード冒頭に「行の見え方」凡例を追加(wikilink 行=押せる/パス行=`llm-wiki-inbox open <短ID>` で開く)。

## 関連リンク

- [[obsidian-direct-open-entrypoint]] — 既存のページ直開き導線。個別ページの手動オープン担当として併存
- [[obsidian-bridge-chatgpt-mirror]] — 公開ミラーは default deny 方式のため本機能のファイルは公開対象外
