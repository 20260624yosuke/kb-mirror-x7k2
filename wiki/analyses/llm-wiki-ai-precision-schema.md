---
type: analysis
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-05-26
---

# LLM Wiki の AI 精度優先スキーマ調整

## 現在の結論

この保管庫は、人間が読みやすい Obsidian ノートとして整理することよりも、AI が質問回答や
再取り込み時に **根拠・現行説・旧説・不確実性・AI 仮説** を判別しやすい構造を優先する。

そのため、単純な「上書き禁止」ではなく、次の運用にする。

- 誤字・誤変換・リンク切れ・書式整理・明らかな source 要約の改善は通常更新してよい。
- 意味のある主張・解釈・自己理解・方針・評価を変更する場合は、旧主張を無言で削除しない。
- 古い主張を残す場合は、`active` / `superseded` / `contested` / `uncertain` などの状態を付け、AI が採用可否を判別できる形にする。
- source ページには元資料にない AI の推測を混ぜない。推測・比較・質問回答は analysis ページへ分離する。

## 調整理由

ユーザーは、この保管庫に対して「自分が見やすい整理」よりも「AI が情報の精度を高い次元で担保できる構成」を求めている。

そのため、ページを読み物として短く整えるだけでは不十分。AI が誤答しやすいのは、以下が混ざるときである。

- 元資料に書いてあること
- 複数ソースからの統合見解
- AI の仮説
- ユーザー本人の判断
- 古い説と現行説
- 矛盾している情報と未確認情報

これらを分離するため、`status` / `confidence` / `evidence_level` と、source / entity / concept / meme / build / analysis の役割分担を明示した。

## 採用する frontmatter

可能なページでは、最低限の `type` / `sources` に加えて以下を使う。

```yaml
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-05-26
```

`status` の意味:

- `active`: 現行説として扱ってよい。
- `superseded`: 旧説。履歴として残すが、現行回答の根拠にはしない。
- `contested`: 矛盾・対立あり。断言しない。
- `uncertain`: 不確実。追加確認が必要。

`evidence_level` の意味:

- `source-backed`: source ページまたは raw に根拠がある。
- `user-stated`: ユーザー本人の発言・判断に基づく。
- `inferred`: 複数情報からの推論。
- `ai-hypothesis`: AI の仮説。断言不可。

## 運用上の注意

既存ページを一括変換しない。大量の既存ページを一度に書き換えると、リンク・索引・古い文脈を壊すリスクが高い。

今後の新規 ingest / query、および重要ページを触るタイミングで段階的に新スキーマへ寄せる。

旧スキーマの既存ページは legacy として扱う。legacy は「使えないページ」ではなく、
`status` / `confidence` / `evidence_level` が未付与のため、強い断言の根拠に使う前に
source ページまたは raw へ戻って確認する対象という意味である。

通常使用では、ユーザーは `/llm-wiki ingest` で raw から wiki へコンパイルし、
`/llm-wiki query` で既存 wiki を横断した要約・仮説・分析を得る。この使用感は維持する。
legacy ページは query / ingest で触ったタイミングで、必要に応じて新スキーマへ昇格する。

## 粒度判断

2026-06-01 のユーザー指示により、ページ粒度は **人間が見やすいか / 分かりやすいか** ではなく、将来の `/llm-wiki query` で体系的にフィードバックできる精度を最重要基準にする。

実務上は、source ページに作例固有の細部を残し、concept ページには別質問でも再利用される判断軸・技法・講師固有語を切り出す。講師固有の発言は一般法則に昇格せず、`sources:` と本文で帰属を明記する。

## 関連

- [[pureref-session-restore]] — 「最新状態は維持しつつ、上書き前バックアップも残す」折衷の実例。
- [[feedback-granularity-ai-precision]] — 粒度判断の明示原則。
