---
type: analysis
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
---

# Wiki 全体 lint レポート(棚出しのみ) — 2026-08-23

> ox-alpha(opencode ハーネス)による `/llm-wiki lint` 相当の機械走査 + 目視確認。
> **修正は一切行っていない**(棚出しのみ)。修正可否は武田さんの判断待ち。
> 走査中も別セッション(Coloso 映像 ingest バッチ)が稼働していたため、件数は本時点のスナップショット値。

## 要約

| 項目 | 件数 | 判定 |
|---|---|---|
| 対象ページ(wiki/) | 1,062 | — |
| legacy(frontmatter 4項目のいずれか欠け) | **370(34.8%)** | 既知方針どおり「触るとき追補」継続を推奨 |
| 鮮度切れ(last_reviewed 90日超) | **0** | KB 最古の last_reviewed が 2026-05-26 のため出ようがない |
| リンク切れ(ページ→ページ・実害) | **1 slug / 2 箇所** | `clipstudio-backup-external-symlink` が未作成 |
| 埋め込み画像切れ | **7 枚 / 1 ページ** | hide ch04 の参照 PNG 全滅([[coloso-visual-ingest-resume-inventory]] の「壊れた状態」と一致=既知) |
| index 不整合 | 重複 0 / 幽霊 0 / **未収載 1** | 整合性は良好 |
| 完全孤立ページ | **2** | どこからもリンク無し |
| 矛盾あり warning(wiki 内) | **3 ページ** | 下の一覧 |

## 母数

wiki 1,062 ページ = source 472 / concept 404 / build 68 / analysis 54 / entity 36 / meme 28。
うち `visual_ingested` 付き 10 ページ(映像 ingest 済の機械判別用フラグ)。

## 1. frontmatter 不足(legacy)

- `status` 無し **359** / `confidence` 無し **370** / `evidence_level` 無し **227** / `last_reviewed` 無し **227**
- 内訳: sources **245** / concepts **105** / memes **11** / entities **8** / builds **1** / analyses **0**
- analyses が全数完備なのは良い状態。sources と concepts に集中しているが、AGENTS.md の既定
  (一括変換せず、触ったタイミングで追補)を続ければ自然に減る。急ぎの対応は不要。

## 2. 鮮度

- `last_reviewed` 付き **835** ページ。最古は [[llm-wiki-ai-precision-schema]] の **2026-05-26**。
- 90日超(= 2026-05-25 以前)は **0 件**。KB 自体が 2026-05 下旬開始のため、鮮度切れは構造的にまだ発生していない。

## 3. リンク切れ(ページ→ページ)

- **実害 1 件**: `wiki/builds/gfl2-external-data-mount.md` から `[[clipstudio-backup-external-symlink]]`
  へのリンク ×2。リンク先ページが存在しない(削除されたのか最初から無かったのかは未確認)。
- 実害なしの見かけ上の切れ 12 リンク / 6 ページ: `llm-maintainer-handbook.md`・
  `llm-maintainer-handoff-plan.md`(文中の用語例 `[[リンク]]` 等)、`deliverable-inbox.md`・
  `obsidian-direct-open-entrypoint.md`(書式例 `[[page-slug]]`)、`obsidian-bridge-chatgpt-mirror.md`
  (`[[llm-wiki]]`=別ボルト参照)、`oxloop-parallel-agent-loop.md`(`[[oxloop-readme]]` 等 =
  tools/ 配下の実ファイルへの言及)。いずれも本文の説明文であり修正不要。
- 技術メモ: 表内の `[[file.png\|alias]]` エスケープ記法(nekojira ch03 等)は Obsidian で正常に
  解決される。単純な正規表現走査では誤検出するので、今後 lint を機械化する場合は考慮が必要。

## 4. 埋め込み画像切れ

- [[coloso-hide-ch04-body-basics]] の参照フレーム PNG **7 枚がディスク上に存在しない**。
  [[coloso-visual-ingest-resume-inventory]] が記録した「壊れた状態 6 章」の hide ch04 分と一致(既知事項)。
  修復は映像 ingest 再開時に行うのが妥当で、本レポート単独での対応はしない。
- [[video-visual-ingest-design]] の 1 件は設計ドキュメント内のテンプレート例(`&lt;source-slug&gt;/...`)で除外。

## 5. index 整合

- エントリ **1,061**、重複 **0**、幽霊(実ファイル無し)**0**、未収載 **1**
  (`coloso-parallel-ingest-project`)。全体として非常に健康。

## 6. 孤立・参照構造

- **完全孤立(どこからもリンク無し)2 件**:
  - [[x-eagle-free-save-pilot]]
  - [[coloso-parallel-ingest-project]]
- **index/log からしか参照されていない 231 件**: builds 10 / concepts 4 / analyses 14 / sources 203。
  sources の 203 は X クリップ等の末端 source で通常の姿。実質的な注意対象は builds/concepts/analyses の 28 件
  (他ページから一度も引かれたことがない = 知識グラフから到達できない状態)。
- Obsidian の backlink パネルでの確認を推奨するが、機械集計では以上のとおり。

## 7. 矛盾マークの現状

`> [!warning] 矛盾あり` を含む wiki 内ページ **3 件**:

- [[firefox-x-profile-scroll-jump-root-cause-2026-08-03]](analyses)
- [[x-eagle-free-save-pilot]](builds)
- [[coloso-ye-jji-ch01-intro]](sources)

加えてルート規約(AGENTS.md / CLAUDE.md / log.md)内に同一マークがあるが、これは規約・ログ上の言及。
矛盾以外の `[!warning]` 注意書きは 24 ページ(主に gf2/mmd 系 builds)あり、これは異常ではなく注意喚起として正常。

既知の講座間分岐(影比率 7:3 vs 6:4、高彩度の置き場所など)は [[synthesis-backlog-2026-06]] に
整理済みで、新たに検出されてはいない(全文対照までは実施していない。制限事項参照)。

## 8. スキーマ逸脱候補

frontmatter の `sources:` が wiki 外のパスや URL を指しているケースが **13 参照 / 2 ページ**:

- [[context-harness]] — `tools/context_harness/...` やローカル絶対パス、外部 URL を `sources:` に記載
- [[plan-gate-skill]] — `tools/plan_gate/...` や AGENTS.md を `sources:` に記載

来歴明示としては合理的だが、命名規則上 `sources:` は wiki source の slug 配列と定義されているため、
将来の機械走査(依存グラフ生成など)でノイズになる。受け入れるか、`evidence_paths:` 等の別キーへ
逃すかはスキーマ判断なので武田さん判断。

## 提案(すべて未実施・次の承認待ち)

| 案 | 内容 | 失うもの |
|---|---|---|
| A | `clipstudio-backup-external-symlink` の実ページ作成 or リンク削除 | 数分作業。リンク削除だと来歴が減る |
| B | 未収載 1 件 + 完全孤立 2 件の index 追記・相互リンク整備 | 数分作業。特になし |
| C | legacy 370 件は従来どおり「触る際に追補」で放置 | 即時の対応コストゼロ。legacy 比率は当面 3割超で推移 |

hide ch04 の PNG 修復は映像 ingest 再開タスクに吸収させる(本レポートの範囲外)。

## 制限事項

- Obsidian の frontmatter `aliases` は考慮していない(alias で張られたリンクは「切れ」側に誤分類される可能性)。
- raw/ ・ backups/ ・ tools/ は走査対象外(ただしリンク参照元としては集計に含む)。
- 矛盾検出は warning マークと既知分岐の確認ベース。全ページ本文の意味対照は実施していない。
- 別セッションが同時稼働中のため、ページ数・件数は 2026-08-23 走査時点のスナップショット。

## 使わなかったもの・落とした情報

なし(走査した全項目を収載)。第 1 パスで検出したが誤検出と確定させて除外した候補
(raw クリップ画像の添付解決漏れ、大文字小文字の厳密比較、テーブル内エスケープ記法)は
節 3・制限事項に記録済み。

## 関連リンク

- [[coloso-visual-ingest-resume-inventory]] — 埋め込み切れ(hide ch04)の正本記録
- [[synthesis-backlog-2026-06]] — 講座間矛盾の既知分岐の整理
- [[llm-wiki-ai-precision-schema]] — 本レポートが従う frontmatter スキーマ
