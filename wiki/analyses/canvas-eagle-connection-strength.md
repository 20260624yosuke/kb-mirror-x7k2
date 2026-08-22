---
type: analysis
name: Canvas↔Eagle 連携の強度と「使用意図＝一次情報」方針
aliases: [canvas-eagle-connection-strength, canvas-intent-primary-data]
tags: [canvas, eagle, pureref-migration, provenance, image-intent, analysis]
sources: [art-canvas-9a22d71d38cd]
related:
  - "[[pureref-personal-fork]]"
  - "[[art-canvas-ingest-design]]"
  - "[[canvas-reference-tools]]"
  - "[[anyak]]"
status: active
confidence: medium
evidence_level: inferred
last_reviewed: 2026-06-07
---

# Canvas↔Eagle 連携の強度と「使用意図＝一次情報」方針

> 2026-06-07 の会話で武田さんが提起した2つの論点（Eagle 連携の強度／Canvas 抽出情報を一次情報として扱い、それで Eagle を整理したい）への評価。**事実（spec 由来）・武田さんの意図（user-stated）・Claude の推奨（ai-hypothesis）を分けて記す。**

## 問い（武田さん・user-stated）

- Eagle との親和性・つながりの強度が、PureRef 移行の目的に対して十分か。
- Canvas ページから抽出した「オレがこの画像を◯◯として使った」を **一次情報** として扱いたい。
- 元々の狙い: wiki に自分のパーソナルデータを集め、それを基に LLM が Eagle の画像を整理する。

## 連携の現状（source-backed: [[art-canvas-ingest-design]]）

- **読む向き（Eagle→Canvas/wiki）= 強い**: sha256（中身一致）で Eagle の1レコードに固定連結。フォルダ/タグ/注釈/url を sidecar にキャッシュ。実証 = [[anyak]] の誤記訂正（Canvas メモ「nayak」を Eagle url で ANYAK に補正）。カバー率 約86%（127枚中 confirmed 109 / candidate 13 / unmatched 3 / not_attempted 2）。
- **書き戻す向き（Canvas/wiki→Eagle）= 未実装**: Phase 3 ＝ 保留中の「項目2」。Eagle へは何も書いていない。
- 既知の課題（2026-06-07 訂正）: **AI タグは `ai_` 接頭辞で識別可能**（武田さんの運用。人間タグと区別は付く）。当初「区別できない」と記したのは誤り。残るのは、ingest ツールがこの規約を使って Eagle メタの証拠採否を行う処理が未実装な点（spec F のフォルダ識別を含む）。

## 評価: 移行目的に対して「半分達成」（ai-hypothesis）

[[pureref-personal-fork]] の構想では Eagle 連携は **双方向同期** が目的。現状は読む向きのみ。武田さんが「別タスク」とした項目2（Eagle 書き戻し）は、オマケではなく **移行を動機づけた双方向連携の片割れ**。

## 「使用意図＝一次情報」方針

**武田さんの立場（user-stated）**: Canvas の「この画像を◯◯として使った」は自分の一次情報。これを核に Eagle を整理したい。

**Claude の評価（ai-hypothesis）**: 妥当。ただし「一次情報」は **ドメイン（領域）限定** で扱うべき。
- Canvas は「**武田さんが画像をどう使った/どう捉えたか**（user-intent）」については一次情報。AI のタグ付けでは到達できない最高価値データで、まさに「画像意図データ層」構想の中身。
- 一方「**誰が本当に描いたか/何が写っているか**（external-fact）」については一次情報ではない。同じメモでもドメインが違う。
  - 例:「mx2j の水着セリカを肌の参考に使った」→「肌の参考に使った」＝一次情報（意図）／「mx2j 作」＝要裏取りの事実主張（[[anyak]] の教訓: ファイル名は嘘をつく）。

## 推奨する扱い: 3層を混ぜない（ai-hypothesis）

1. **一次（user-intent）**: 武田さんの明示メモ＋配置行為（どのグループに置いたか）。高信頼。Eagle 整理の燃料。
2. **要検証（external-fact）**: 作者・被写体などの事実主張。Eagle / 出典 url で裏取りしてから採用。
3. **AI 推測**: 画素類似・名前なし枠の意味づけ等。証拠が出るまで断定しない。

- 現 spec は user-assertion（メモ）と derived-interpretation（AI 推論）を既に分離済み。**未決点 ＝ 配置行為（group→used-for）の格付け**: 現 spec は AI 推論扱いだが、「配置＝武田さんのラベリング」という移行構想に沿えば **一次情報へ格上げ** する余地がある（名前付き枠のみ・曖昧は review、というガードは既存）。

## Eagle 整理（将来の項目2 / Phase 3）への含意

- 当初の狙い（personal data → LLM が Eagle 整理）を実現する燃料は、この Canvas 使用意図。
- 武田さんは既に **Eagle の AI タグへ `ai_` 接頭辞を付ける運用**で人間タグと分離済み。これは **名前空間方式が実際に機能する実証**。書き戻す際（項目2）は同じ規律を踏襲し、**層（provenance）を専用接頭辞で運ぶ**: 一次意図は高信頼の専用名前空間（spec の `llmwiki__`、例 `llmwiki__used-for__<テーマ>`）、AI 推測は裏取り後または別扱い。これで「自分が決めたのか AI が推測したのか」が後から必ず分かる。

## 結論

- 連携強度: 読む向きは強く PureRef より明確に前進。書き戻しは未達（＝項目2）。
- 「使用意図＝一次情報」は正しい。ただし **ドメイン限定＋3層を混ぜない** 運用が前提。武田さんの `ai_` 接頭辞運用が示すとおり、**専用接頭辞で層を分ければ Eagle 汚染は防げる**（"区別できない"のではなく、書き戻し側が同じ規約を守ればよい）。

## 関連リンク

- [[pureref-personal-fork]] / [[art-canvas-ingest-design]] / [[canvas-reference-tools]] / [[anyak]] / [[mx2j]] / [[art-canvas-9a22d71d38cd]]
