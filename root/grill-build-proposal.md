# grill-build スキル 設計提案(Codex レビュー反映版)

- 作成: Claude Code, 2026-05-29
- Codex レビュー反映: 2026-05-30
- 対象 KB: LLM Knowledge Base _01(このフォルダ)
- ステータス: 実装済み(2026-05-30, `.claude/skills/grill-build/SKILL.md`)。Claude の発見場所に合わせ、提案の `skills/` ではなく `.claude/skills/` に配置。CLAUDE.md・AGENTS.md にも発見経路を追記済み。
- 元ネタ: Matt Pocock `grill-me`(github.com/mattpocock/skills)

## 背景・目的

創作支援ツール/自動化/ワークフロー(例: `wiki/builds/pureref-notion-link-workflow`)を作る前に、要件・仕様・設計判断を詰めるスキル。本家 `grill-me`(着手前に1問ずつ・推奨回答付きで計画の各枝を共通理解まで詰める)を本 KB 用にチューニングし、設計・実装セッションの前半を担う。

## 本家から踏襲する中核(Codex 承認済み)

- 質問は1問ずつ / 各質問に推奨回答(見立て)を添える / 決定の各枝を依存順に解決 / 共通理解まで継続。

## SKILL.md 最終素案(レビュー対象の本体)

```markdown
---
name: grill-build
description: 創作環境開発でツール・自動化・ワークフローを作る前に、要件・仕様・設計を1問ずつ詰める。/grill-build または grill-build と明示的に依頼されたときのみ起動する。llm-wiki の ingest/query、通常の相談、coloso 文字起こしでは起動しない。
---

# grill-build

本 KB ローカルの設計詰めスキル。実装に入る前に、作るものの要件・仕様・設計判断を
ユーザーと1問ずつ固める。llm-wiki ingest/query でも coloso 文字起こしでもない。

## Trigger
`/grill-build` または「grill-build」と明示的に依頼されたときのみ起動する。
「壁打ち」「相談」「この開発を詰めたい」などの広い自然文では起動しない
(llm-wiki query・通常相談・coloso 文字起こしと誤発動させない)。自発的に提案・起動もしない。

## Do
- まず `index.md` と `rg`(ripgrep)で関連候補を絞り、**関連する** `wiki/builds/`・`tools/` の
  ページ「だけ」を読む(全読みしない)。既に分かっていることは質問しない。
- 質問は1問ずつ。各質問に自分の推奨回答(見立て)を添える。
- 決定の各枝を依存順に1つずつ解決し、設計上の分岐・トレードオフを明るみに出す。
- 共通理解(何を・なぜ・どう作るか)に至るまで続ける。
- 終了時に、固まった要件・仕様・未決事項を要約する。
- ユーザーが記録を希望する場合は、**書き込む前に記録案を提示して明示承認を取る**
  (構造判断の承認停止 = 「方針決定の自律 NG」に従う)。提示する項目:
  - 保存先: `wiki/builds/`(仕様確定)か `wiki/analyses/`(検討段階)か
  - `type`(`build` か `analysis`)
  - slug / ファイル名
  - frontmatter(下記方針)
  - 更新対象ファイル(`index.md`・`log.md` を含むか)
  承認後、記録は llm-wiki の通常フローに委ねて書く。
- frontmatter 方針: `evidence_level: user-stated` を基本に、可能なら `status`・`confidence`・
  `last_reviewed` も付ける。仕様が固まったものは `status: active`、検討段階は `status: uncertain`
  とし、AI が採用可否を判別できる形にする。

## Do Not
- ユーザーに代わって方針を決めない(grill は引き出す行為。決定はユーザー)。
- `wiki/`・`index.md`・`log.md` を**承認前に**書き換えない。
- 一度に複数の質問を浴びせない。
- 本家 grill-with-docs のような `CONTEXT.md`/ADR の自動生成はしない。
- 実装(コード/ツール作成)に勝手に進まない。grill は設計詰めまで。
```

## 発見経路(実装時の追記案)

`skills/grill-build/SKILL.md` を置くだけでは Claude Code / Codex が自動発見する保証が弱い。`coloso-transcribe` が CLAUDE.md・AGENTS.md の両方に記載されているのと同様、**両ファイルの「オペレーション」節**へ次の項を追記する(既存内容は消さず1項追加):

```markdown
### grill-build(`/grill-build`)

`/grill-build` または「grill-build」と依頼されたら `skills/grill-build/SKILL.md` を読む。
これは ingest/query ではなく、実装前の要件・仕様詰め。自発起動しない。
```

## 実装手順(承認後)

1. `skills/grill-build/SKILL.md` を上記素案で新規作成。
2. CLAUDE.md・AGENTS.md の「オペレーション」節に上記「発見経路」項を追記。
3. これらはオペレーション定義の追記であり、`wiki/`・`index.md`・`log.md` は更新しない。
   skill 実装作業を `log.md` に記録したい場合は、先にログ規則へ新しい op を追加するかを
   ユーザーに確認し、明示承認後に別作業として扱う。

## Codex 指摘への対応表

| # | 指摘 | 対応 |
|---|---|---|
| 1 | トリガー語を狭める | description/Trigger を `/grill-build`・`grill-build` 明示のみに。広い自然文を削除 |
| 2 | 記録前の承認停止 | 保存先/type/slug/frontmatter/更新対象を提示→明示承認後に書く、と明記 |
| 3 | frontmatter 補強 | `status`/`confidence`/`last_reviewed` を追加。確定=active/検討=uncertain |
| 4 | 先読みを絞る | `index.md`+`rg` で絞り、関連 `builds`/`tools` のみ読む(全読みしない) |
| 5 | 発見経路 | CLAUDE.md・AGENTS.md 両方に発見経路を追記する案を明記 |
