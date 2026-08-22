---
type: build
name: Canvas Ingest 設計 v2.3（画像資料の事実を Eagle＋wiki に紐づける取り込み）
aliases: [art-canvas-ingest-design, canvas-ingest-design, canvas-ingest-v2, canvas-metadata-ingest]
tags: [tooling, canvas, ingest, eagle, wiki, personalize, spec, planning]
sources: []
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-06-07
related:
  - "[[pureref-personal-fork]]"
  - "[[canvas-reference-tools]]"
  - "[[takeda-yohsuke]]"
canvas_target: raw/_MY_ART/2026_06/2026_06_02_アイドルxアスナx立ち絵_01/2026_05_30_アスナxアイドル衣装.canvas
---

# Canvas Ingest 設計 v2.3（画像資料の事実を Eagle＋wiki に紐づける取り込み）

> grill-build の設計・実装記録。本ページはリポジトリ内の**正本 spec**。B1・B2 は実装・自動試験・実 Canvas パイロット再実行まで完了。人間による全文目視レビューは非現実的と判明したため、機械検証と誤推測抑制を完成条件とする。
> 経緯: v1（二輪・即書き戻し）→ Codex① 条件付き承認 → v2.1 全面採用 → Codex②（Phase1 lossless のみ承認）
> → v2.2（②③④⑥ を詰める）→ **Codex③（実装ブリーフ査読）**: ブリーフが spec の未決より先走り determinism を要求、
> 受入試験に raw read-only 矛盾等 → 本 v2.3 で正規化・relation lineage・claim-span・registry 規則・テスト方式を確定。
> → **Codex④（安全スライス・ブリーフ査読）**: fail-closed `--safe-slice` CLI／Eagle 4-state match／複数 match 保持／lossless と派生の分離／missing-file 続行／hash 定義を追加確定（→「安全スライス実装仕様」節）。

## ステータス（Codex③ 判定）
- **✅ 実装済み・検証済み（2026-06-06）**: 安全スライス（lossless parse / `asset_type` / Eagle read-only 4-state observation）。Codex 実装 → Claude が実 canvas で受け入れテスト通過を独立確認（127 assets: confirmed 109 / candidate 13 / unmatched 3 / not_attempted 2、固定 node ID 一致、lossless deep-equality True、出力 sidecar のみ）。`tools/canvas_ingest.py` ＋ `tools/tests/test_canvas_ingest_safe_slice.py`。
- **✅ 実装済み・検証済み（2026-06-07）**: Phase 1 Task A（土台）。registry / 安定 `canvas_id` / previous sidecar 検証・復旧 / `ingest_runs[]` / direct text↔file edge の `related-to` relation / retract・再有効化 / Eagle observation のみ保存。隔離自動試験 38件＋全 tools test 48件通過。実 canvas pilot は `canvas_id: 9a22d71d38cd`、127 assets、147 relations、review candidates 0。出力: `wiki/sources/art-canvas-9a22d71d38cd.usage.json` と `wiki/canvas-registry.json`。
- **✅ B1 実装済み・自動試験済み・実 Canvas パイロット生成済み（2026-06-07）**: Phase 1 Task B を **B1**（group 全所属観測・filename 明示作者・source MD 事実のみ・移行/再実行）→ **B2**（高精度に限定した自然文解析）に分割。Codex が B1 を実装し、全 tools test 56件、Task A→B1 移行、実 Canvas の B1→B1 再実行で relation_id 158件の安定、`raw/`・Eagle metadata・許可外 wiki の不変を確認。パイロット source Markdown は [[art-canvas-9a22d71d38cd]]。
- **✅ B2 実装済み・自動試験済み・実 Canvas パイロット再実行済み・Claude独立検証済み（2026-06-07）**: 人間が長大な source Markdown を全文確認する方式を停止し、機械検証で完成判定する方針をユーザーが承認。各 file node の Markdown 掲載を1回に限定し、書き込み前の機械整合性検査を必須化した。B2 は直接 text↔file edge、明示的な節・否定・質問・仮説・作者だけを関係化する。口語の暗黙質問・断片を追加で要レビューへ寄せた最終実 Canvas は active relations 373件（`related-to` 147 / `used-for` 9 / `source-artist` 6 / `note` 211）、review candidates 76件。全 tools test 61件成功。B2→B2 再実行で relation ID と Markdown は変化なし。`raw/` 対象project 47件・参照file 127件、Eagle metadata 31,927件、許可外 wiki 613件は不変。`depicts`、名前なし枠の意味推測、メモ間伝播は生成していない。Claude の独立 spec 適合検証で重大な誤抽出・整合性問題なしと確認済み。
- **追加方針（2026-06-06）**: vision（画像内容の AI 解析）は canvas ingest 側では行わない。Eagle 側で一度だけ実施し、canvas ingest はその結果を読むのみ。マッチング用の画素類似度比較は引き続き OK。
- **✅ Phase 2 高精度 Q&A 軽量版＝実装・実証済み（2026-06-07）**: query を canvas sidecar（`relations`）＋Eagle 観測横断に拡張（手順は `CLAUDE.md` query 節「canvas 横断 Q&A」）。実 canvas で出典・確信度付き Q&A を実証（コード追加なし・sidecar 直読）。**保留**: wiki projection の残り（used-for テーマ／note 反映・自動同期）。
- **✅ Phase 3 Eagle 書き戻しパイロット＝実施・検証済み（2026-06-14）**: 手動 MCP（`item_add_tags`）で `llmwiki__` 名前空間タグを Eagle confirmed アイテムへ付与。対象: `used-for` 9件（`llmwiki__used-for__アスナ新衣装` 5件 + `llmwiki__used-for__アイドル衣装の起点` 4件）＋ `source-artist` 5件（`llmwiki__source-artist__mx2j` 2件 + `llmwiki__source-artist__ANYAK` 3件）。全 14件成功。既存タグ（`ai_*` 等）不変を確認。コード追加なし。該当 relation の `review_status` を `accepted` に更新。**保留**: ツール化（`canvas_ingest.py --eagle-write`）／ 循環汚染ガード実装（次回 ingest で `llmwiki__` タグを自己証拠から除外）／ note・depicts の書き戻し／ drift 検出・journal。
- **✅ wiki projection（source-artist・軽量版）＝実装済み（2026-06-07）**: canvas sidecar の `source-artist` を、紐づく画像の Eagle 観測 url で裏取りしてから entity ページへ手動 projection（コード追加なし・sidecar 既存キャッシュ直読・Eagle 再アクセスなし）。Canvas メモの誤記「nayak」を Eagle 出典で [[anyak|ANYAK（@ANYAK05）]] に訂正、[[mx2j]] は danbooru 作者タグで確認。raw/Canvas の誤記は read-only のため未修正で残し、正しい表記は entity 側で管理。新規: [[anyak]]／[[mx2j]]。**保留（projection 残り）**: used-for テーマ／note の反映・entity/concept への管理ブロック自動更新・自動同期。
- 実装は **Codex**、設計記録・ブリーフ・実装後の独立 spec 適合検証は Claude。B1・B2 の Claude 独立検証は完了。

## なぜ作るか
武田さんは Obsidian Canvas を創作の画像資料ボードとして使用（1作品=1canvas）。目的＝canvas の事実を LLM で抽出し Eagle（画像DB）＋ wiki（知識）に紐づけ、両参照の高精度 Q&A を実現。既存 [[art-canvas-pilot-2026-05-29-asuna-01]]（`tools/canvas_ingest.py` v0.1）は照合＋memo列挙止まり。[[pureref-personal-fork]] の「画像意図データ層」構想の Canvas 実体化。

## 検証済み事実（Codex 指摘の裏取り）
- Eagle はタグ付き **5,291 件**（総 31,089 / 未タグ 25,798・タグ種 410、自動付与）→「タグ自由領域」前提撤回。既存タグ・人間注釈・人間フォルダは保護必須。
- コード欠落: `slugify` 非ASCII除去（`tools/canvas_ingest.py:186`）/ `flipX`等ロス（`:206`）/ edge は text→group のみ（`:475`）。
- ASCII ファイル名規約（`AGENTS.md:145`）/ source ページに推論禁止（`AGENTS.md:158`）→ ともに遵守。
- 否定文の実在: 「胸の質感にはなるが、**長乳じゃない**から、量感の形は変わるか」→ **1メモに3主張**（positive/asserted・negative/asserted・question）。polarity/modality は claim 単位必須。

## アーキテクチャ
```
raw Canvas（編集で前状態消失＝唯一の入力・read-only）
  ↓ lossless parse（未知キー passthrough・root キーも保存）
.usage.json  ← 派生データの「正本」（relations を lineage/status 付きで保持）
  ↓ validate / diff / review queue
  ├─ compact source Markdown（観測・明示 user-assertion・抽出不確実性のみ。推論と lossless graph は出さない）
  ├─ entity/concept/analysis 更新（管理ブロックのみ・source-artist は 2026-06-07 に軽量 projection 済／残りは保留）
  └─ Eagle managed projection（Phase 3 保留）
```
**lossless の定義（Codex③）**: byte 一致でなく **JSON semantic deep-equality** ―― Canvas root の未知キー＋全 node/edge キー＋配列順序＋値型が一致。

## 確定仕様

### A. 同一性・ライフサイクル・正規化
- **canvas registry**（保存先 `wiki/canvas-registry.json` 固定・**atomic write**）。項目: `canvas_id` / `path_history[]` / `known_source_hashes[]` / `title` / `node_id_fingerprint` / `first_seen` / `last_seen` / `status`。**raw に ID を書かない**（read-only）。
- **再同定順序**（決定的）: ① `--canvas-id` 明示 → ①b previous sidecar の `canvas_id` 復旧（Registry 消失時、**sha256 または正規化済み source path の一致必須**。fingerprint 単独一致は copy ambiguity として停止）→ ② current path 一致 → ③ sha256 一致かつ旧パス消失 = **move**（path_history 更新）→ ④ sha256 一致かつ旧パス存在 = **copy ambiguity**（フラグ）→ ⑤ `node_id_fingerprint` **完全一致 hash**（類似ではない）= 候補提示（`--accept-new` 必須）→ ⑥ 新規発番。
- `source_canvas` と Registry の path は **wiki-root からの相対 POSIX path**（wiki-root 外は絶対 POSIX path）に正規化する。`path_history` は現在 path が末尾と異なるたびに追記し、過去 path へ戻った場合も current path を末尾で判別できるようにする。
- previous sidecar は使用前に schema / `canvas_id` / identity fields / `relations[]` / `ingest_runs[]` / run 参照整合性を検証し、不正なら fail-closed。Registry も schema / entries 型 / key と entry 内 ID の一致を検証する。
- **relation lineage**（Codex③・retract 追跡の核）:
  - `relation_lineage_id = hash(canvas_id + extractor_rule + predicate_slot + evidence_node_ids)`（claim_slot は含めない — Codex⑤⑥⑦ で offset/hash いずれも auto-supersede を壊すと判明し除外）
  - `relation_id = hash(relation_lineage_id + normalized_claim_value)`（`normalized_claim_value` = `{subject, predicate, object, qualifiers, polarity, modality}` の canonical JSON。Task A では `qualifiers={}, polarity=null, modality=null` で計算）
  - **Phase 1 simplified model**（Codex⑦ 合意）: 同一 `relation_id` → maintain / previous にあり new に無い → `retracted` / new にあり previous に無い → new `active`。**auto-supersede は Phase 1 では行わない**（テキスト編集は retraction + 新規 relation として記録。接続は Phase 2 以降）。
- **ingest_runs[]**（Phase 1 用・Phase 3 の `sync_runs[]` とは別物）。`first_seen_run` / `last_seen_run` はこれを参照。
- `lifecycle_status`（active/retracted/superseded）と `review_status`（pending/accepted/rejected）を分離。
- **正規化仕様**（determinism の前提・version 付き）: Unicode **NFC** / 文字列 trim・改行正規化 / subject・object の**型付き表現** / qualifiers ソート規則 / evidence_ids ソート規則 / confidence の型と値域 / `relation_schema_version`・`extractor_version`・`normalization_version`。

### B. 観測・主張・解釈
- `observation`（provenance subtype: `source_system: canvas|eagle|filesystem|filename`, `observed_at`, `source_state_hash`）/ `user-assertion` / `derived-interpretation`。
- `eagle-observation` は observation の一種（第4の層にしない）。
- 信頼は claim＋evidence path＋`truth_domain` に付与。confidence は2系統（`extraction_confidence` / `epistemic_confidence`）。

### C. relation スキーマ
```yaml
relations:
  - relation_id:            # hash(relation_lineage_id + normalized_claim_value)
    relation_lineage_id:    # hash(canvas_id + extractor_rule + predicate_slot + evidence_node_ids)
    extractor_rule:
    subject: / predicate: / object: / qualifiers:
    polarity: positive | negative
    modality: asserted | tentative | question
    evidence_ids: []
    evidence_spans:         # claim 単位の根拠スパン
      - node_id: / start: / end: / quoted_text:
    provenance_kind:        # user-assertion | derived-interpretation | observation
    truth_domain:           # user-intent | external-fact | ...
    extraction_confidence: / epistemic_confidence:
    lifecycle_status: active | retracted | superseded
    review_status: pending | accepted | rejected
    first_seen_run: / last_seen_run: / retracted_in_run: / supersedes_relation_id:
```

### D. edge/relation の意味論（ユーザー決定: 線はゆるい関連・向きは適当）
- 線＝**無向の「関連」**。向きは事実として記録のみ、意味判定に使わない。
- メモ↔画像 = 注釈関係（最優先）。group containment = テーマ/used-for ヒント（最内優先・全包含記録）。メモ→メモ = 1ホップ弱伝播のみ。
- **polarity/modality は claim（節）単位**（メモ単位でない）。`evidence_spans` で節を特定。**節分割に自信が無ければ自動 relation 化せず `review_candidates` へ**。

### E. depicts/used-for と evidence policy
- `depicts` / `used-for` / `usage-role` / `source-artist` を分離（「キャラ常時タグ」撤回）。
- evidence は **claim 種別ごとのポリシー**（「filename 最強」撤回。`source-artist` は元URL>filename もあり・X 投稿者≠作者 / `depicts` は `画像 11.jpg` 等で filename 無効）。

### F. Eagle projection（保留・Phase 3。Phase 1 は read-only）
- Phase 1: 全 folder/tags/annotation/url を **`eagle-observation` 保存**（信頼前提なし）。**AI 付与フォルダ識別規則は未実装のため、証拠採否は保留**（推測で実装しない）。
- Phase 3: 名前空間 `llmwiki__` のみ機械管理 / 既存タグ・人間注釈・人間フォルダ不可侵 / 全 sidecar の active relation を item_id 単位で**集約**→desired state→diff/apply / `sync_runs[]` journal / desired==actual 冪等・人間外部編集は drift/conflict / confirmed のみ書く / 循環汚染ガード（`llmwiki__`・AI フォルダ・未レビュー推論は次回証拠から除外）。
- 同一 sha256 が複数 item に一致 → `eagle_matches[]` に全 exact match を決定的ソートで保持（Codex④・「安全スライス実装仕様」参照）。

### G. 命名・出力
- ファイル名 `art-canvas-<short-canvas-id>.md`（ASCII）。日本語は title/alias。
- **source Markdown の内容制限**（`AGENTS.md:158` 準拠）: 観測 / Canvas に明示された user-assertion / 抽出上の不確実性 **のみ**。`derived-interpretation` は **sidecar のみ**（Phase 2 まで MD に出さない）。
- Task A ingest = **sidecar 1件のみ**。Task B 完了後の ingest = **source MD 1件 ＋ sidecar 1件**。entity/concept/analysis ページは生成しない（新規・曖昧語は sidecar `review_candidates` へ）。

## フェーズ
- **Phase 1**: 2段構成。**Task A（土台）**: ①安全スライス（実装済み）→ ②registry → ③構造 relation（text↔file edge を `related-to` として抽出。predicate 分類なし。polarity/modality は null 許可）→ Eagle observation 保存（relation 生成なし）→ sidecar 出力（スキーマ C の全フィールドを持たせる。未使用フィールドは null）。journal・folder 整理・rollback エンジンは作らない。**Task B（知能層）**: claim parser（polarity/modality）→ predicate 分類（辞書は Canvas 実データから定義）→ group/filename/propagation 抽出 → compact source Markdown。
- **Phase 2（高精度 Q&A・軽量版実装済み 2026-06-07）**: query を canvas sidecar＋Eagle 観測横断に拡張（`CLAUDE.md` query 節）。wiki projection は **source-artist のみ軽量・手動で反映済み（2026-06-07・Eagle url 裏取り）**。残り（used-for／note 反映・管理ブロック自動更新）＋ review バッチ＋ lint は別オプションとして保留。
- **Phase 3（保留）**: Eagle projection（集約・journal・drift・dry-run→適用→rollback）。

## 停止条件の状態
① 3層分離=クリア / ② depicts 分離＋**claim-span polarity**=クリア / ④ edge=クリア / ⑥ 安定 ID＋**lineage＋registry 規則**=クリア / ③ Eagle 同期=スキーマ定義済（実装 Phase 3）/ ⑤⑥ 検証=下記テスト方式で実施。

## テスト方式（Codex③・raw read-only 厳守）
- 全テストは**一時ディレクトリへ canvas＋参照 asset を複製**。**専用 registry・専用 output**・Eagle は読み取りのみ。round-trip は一時ディレクトリへ再構築。**raw を rename/move しない**。
- lossless = **JSON semantic deep-equality**（root 未知キー含む）。
- relations テストは**具体 node ID＋期待値固定**（例: `NIKKE Rapi` の file node→`depicts`; アスナ制作への使用→`used-for:アスナ`・`depicts` には付けない; 「長乳じゃない」メモ→`polarity: negative`）。
- 出力＝**Task A は sidecar 1件のみ**、Task B 完了時は **source MD 1 ＋ sidecar 1**。entity/concept/analysis は生成しない。Eagle 書き込みゼロ。
- 冪等（sidecar）: 無変更 canvas の再 ingest で relation churn ゼロ（relation_id 安定）。1メモ編集は当該 relation のみ変化（Phase 1 では retraction + 新規 active。auto-supersede は Phase 2 以降）。

## 安全スライス実装仕様（Codex④ 反映）

- **fail-closed CLI `--safe-slice`**: `--output-json` 必須／Markdown 生成しない／`--update-index`・`--update-log` を拒否／`wiki/sources/` への出力を拒否／Eagle library 自動検出を禁止し `--eagle-library` 明示必須。registry 未実装で canonical sidecar 名を決められない＝明示 `--output-json`。
- **sidecar 構造（lossless と派生を分離。混ぜると deep-equality が壊れる）**:
  ```yaml
  lossless_canvas:   # 元JSONを無改変保持（root/nodes/edges 全キー・配列順・値型）
  assets:            # node_id キーの派生情報（asset_type / fingerprint / file_status / eagle_matches）
  relations: []      # 安全スライスでは空
  ingest_runs: []
  registry_ref: null
  ```
- **Eagle match 4状態**（混同しない）: `confirmed`（sha256一致・image/other）。**安全スライスおよび Phase 1 では video は常に `not_attempted`**（Codex⑤ #7 で明確化）／`candidate`（画素類似・**image のみ**・`pixel_diff` 併記）／`unmatched`（照合したが不一致）／`not_attempted`（対象外＝video の画素照合・読取不能・missing）。**video を unmatched にしない**。
- **複数 match 保持**: 同一 sha256 が複数 Eagle item に一致する実例（node `ac0f9edf82b15b0c` → `MOI4VOAXSI5PG`／`MM5P1WVWL7V9B`）。先頭1件 primary は走査順依存で非決定的＝NG。`eagle_matches[]` に全 exact match を**決定的ソート**で保持（各 `{status, library_ref, item_id, observation}`）。
- **missing file でも続行**: 参照欠落で全停止しない（現状 `tools/canvas_ingest.py:358` は停止）。lossless parse は成功させ、当該 asset を `file_status: missing`／`fingerprint: null`／`eagle_match.status: not_attempted` で残す。
- **`source_state_hash` 定義**: `observed_at` を含めず、観測 Eagle fields の canonical JSON（keys sort・tags/folders は hash 用に sort・元の配列順は observation 本文に保持・`observed_at` は tz 付き ISO-8601・`library_ref` 保持）から生成。
- **受入テスト修正**: 「Pasted/clipboard=unmatched」は実データで不成立（Pasted 13=candidate／2=unmatched／`Clipboard …`=**confirmed**）。具体 node 固定 → unmatched: `49af6bc0bde18fb1`,`634239118ec13180`／candidate: `5277a01bff31cd14`／multiple confirmed: `ac0f9edf82b15b0c`。temp harness は canvas 参照が root-relative なので temp 配下に `raw/...` 構造を再現し `--wiki-root <temp-root>` を渡す（対象 asset 計 ~122MB・コピー可）。安全性検証は「MCP 未呼出」では弱い → raw・Eagle metadata・wiki の before/after **hash/mtime 不変**を確認。

## Task B 実装仕様（2026-06-07 確定 / B1→パイロット→B2 / Codex 4巡査読反映）
> 経緯: たたき台 → レビュー → Codex 査読①〜④ → 本版。実装は Codex、本節が実装ブリーフ。実データ 82 text node（画像直接 67／メモ経由 9／到達なし 6）・group 5（名前付き2「アスナ新衣装」「アイドル衣装の起点」／名前なし3）・名前付き外＋名前なし内に入る画像 3件・複数画像候補 50件超・無意味 filename 27。Task A の安定性（churn ゼロ）と raw/Eagle read-only を壊さない。

### 全体決定
- **predicate=案1**: `depicts`／`used-for`／`source-artist`＋`note`。
- **subject の保存（ID 衝突回避・査読①指摘2）**: `relation.subject` は**節の文章**。メモ全文は `lossless_canvas` の text node に保持済みで `evidence_ids` が当該 node を指す。lineage に claim_slot を含めない（spec A）ため subject=全文だと同一メモの同極性節が同一 ID となり [:1743](tools/canvas_ingest.py:1743) で停止。subject=節で回避し**意見は全文で残る**。
- **source MD は観測事実のみ（査読①指摘5・`AGENTS.md:180`）**: グループ所属・メモ全文（user-assertion）・Eagle observation のみ。**used-for／要約／depicts 等の解釈（derived-interpretation）は sidecar 限定**。
- **既存 related-to 147件は維持**: reconcile は relation_id 基準（[:1774](tools/canvas_ingest.py:1774)）で自動継続。新 predicate relation を別 extractor_rule で追加。
- **modality=3値維持**。
- **グループの意味（ユーザー確定 2026-06-07）**: 名前付き枠＝制作テーマ、**名前なし枠＝ユーザーが手で分けたサブグループ**（アングル/シチュエーション等。基準は枠に明示されない）。→ **全グループ所属を観測事実で記録**（名前なし枠の所属も保持）。**`used-for` は最内の名前付き枠からのみ生成**（例「アスナ新衣装」）。名前なし枠は used-for にせず、分類基準も推測しない（観測のみ。意味付けは B2/Phase2）。
- **B1 の対象（ユーザー確定 2026-06-07）**: group 所属観測・`used-for`・source Markdown の資料一覧は、画像だけでなく動画・その他ファイルを含む **Canvas 上の全 `file` node** を対象にする。text/group node 自体には `used-for` を生成しない。

### 段階分割（機械検証ゲート）
**B1（構造・filename から確実に取れるもの・determinism 高）**:
- group containment 観測（**全体包含**。`Rect.contains` と同じく file node の矩形全体が group 内に入る場合のみ。名前付き／名前なしの**全所属**を元 node 順で記録）。
- `used-for`（最内の名前付き枠から。**sidecar のみ**）。
- `source-artist`（filename の明示構文のみ。下表）。
- source Markdown 生成（事実のみ）＋ `index.md`／`log.md` 更新。
- Task A→B1 移行＋ B1→B1 再実行（下記）。
- → **B1 パイロットを機械検証し、矛盾・欠落・重複・不変性違反がなければ B2 へ進む**。人間による全文目視レビューは停止点にしない（2026-06-07 ユーザー承認）。

### B1 group 所属観測の保存形
`assets[file_node_id].group_memberships` に次の固定形で保存する。空文字・空白のみの label は `null`。`lossless_canvas` から毎回再生成し、個別日時・hash は持たせない。
```json
"group_memberships": [
  {"group_id": "...", "group_label": null, "containment": "full"}
]
```

### B1 最内の名前付き枠の決定規則
- 名前付き所属が1件なら採用。
- 複数なら、他の名前付き所属枠すべてに完全包含される枠を採用。
- 一意に決まらなければ `review_candidates` へ送り、`used-for` は生成しない。

**B2（2026-06-07 ユーザー承認・AI精度優先）**:
- 対象は **直接 text↔file edge で接続されたメモと file node の組だけ**。メモ間伝播は行わない。
- 節分割は改行と句読点 `。` / `、`。助詞「が」・疑問符の位置では分割しない。各節は元 text node 上の `start` / `end` / `quoted_text` を `evidence_spans` に保存する。
- `note`: 各高確度節を `subject={text, 節}`、`object={file-ref, file path}` として保存。`polarity` は明示的な否定形のみ `negative`、それ以外は `positive`。`modality` は明示 `?` / `？` がある節を `question`、明示的な「仮説」「かも」「かもしれ」「可能性」「なりうる」を `tentative`、それ以外を `asserted` とする。
- `source-artist`（メモ本文由来）: 直接接続メモ内の明示構文だけを対象にする。英数字・`_@.-` からなる作者名の `作者名が描いた`／`作者名さんの描く`／`drawn by 作者名` のみ。subject=file-ref、object=artist、provenance=`user-assertion`、truth_domain=`external-fact`。
- 複数疑問符を含む複合疑問、`じゃない？` 等の反語・確認疑問、疑問符なしの疑問表現、作者候補が複数ある節は relation 化せず `review_candidates` へ送る。
- **今回生成しない**: `depicts`、名前なし枠の分類基準の意味付け、メモ間伝播。誤推測を避けるため、実害が確認されるまで後回しにする。
- **実行時に外部 LLM を呼ばず固定ルール＋review_candidates**。人間による全文目視レビューは完成条件に含めない。

### B1 relation 出力形（決定的 ID とテストの前提・全フィールド固定・査読②指摘2,3）
共通固定値: `qualifiers={}`／`polarity=null`／`modality=null`／`review_status="pending"`／`evidence_spans=[]`／`lifecycle_status="active"`。relation_id に効くのは subject/predicate/object/qualifiers/polarity/modality（[:1734](tools/canvas_ingest.py:1734)）。

| predicate | subject | object | extractor_rule | evidence_ids | provenance_kind | truth_domain | extraction_conf | epistemic_conf |
|---|---|---|---|---|---|---|---|---|
| `used-for` | {`file-ref`, 画像path} | {`group`, グループ名} | `group-containment` | [file node, group node] | `derived-interpretation` | `user-intent` | 0.9 | null |
| `source-artist` | {`file-ref`, 画像path} | {`artist`, 作者名} | `filename-artist` | [file node] | **`derived-interpretation`** | `external-fact` | 0.9（構文を読めた確率） | **0.5（作者が本当に正しい確率＝中以下）** |

- `source-artist` は **filename 文字列自体を根拠に保存**。「filename にそう書いてある」は観測だが「作者が本当にそうか」は別、を confidence 2系統で表現（査読②指摘2）。
- **filename 作者の認識構文を固定**: 拡張子を除いたファイル名本体を Unicode NFC 正規化し、連続空白を1個へ統一する。英語は単一の `drawn by` 以降から末尾まで、日本語は単一の `が描いた` より前を作者名とする。作者名が空、または構文が複数一致なら抽出しない。review_candidates へ送る兆しは `drawn by`・`描いた`・`illust` の3種のみで、裸の `by`・`@` は使わない。実 canvas の期待値は抽出2件・`kronillust` 候補4件。
- B2 の `note`／`source-artist`（メモ本文由来）の出力形は上記で確定。`depicts` は今回生成しない。

### source Markdown 構造（2026-06-07 精度優先改訂・事実のみ）
- frontmatter は `type: source`・`sources: []`・`canvas_id`・`evidence_level: source-backed`・`last_reviewed`・`raw_source`・`sidecar_json` を必須とする。
- `## 要約`（観測事実の概要のみ。制作意図の解釈は書かない）。
- `## グループ索引`: 各 group node と所属 file node ID の参照だけを列挙する。資料の詳細は重複掲載しない。
- `## 資料一覧`: **全 file node を元 node 順で1回だけ**掲載し、file path、asset_type、全 group 所属、Eagle confirmed を記録する。
- `## Canvas メモ`（全 text node の node ID＋全文を元 node 順。孤立メモも含む）。
- `## 不確実・要レビュー`（review_candidates＋ Eagle `candidate`）。
- **Eagle: `confirmed` は `eagle_matches[].item_id`・`eagle_matches[].status`・`eagle_matches[].observation.url` のみ掲載し、tag/folder/annotation 全文は sidecar に留める。複数一致は全件掲載する。`candidate`（画素が似ているだけの未確定）は画像に紐づく事実として書かず「不確実・要レビュー」に未確定として載せる**。
- **`used-for`／derived-interpretation は MD に出さない**（sidecar のみ）。出力＝source MD 1 ＋ sidecar 1。entity/concept/analysis は生成しない。
- 書き込み前に、Canvas・sidecar・生成 Markdown の整合性を機械検査する。全 file/text node の欠落・重複、asset 不一致、存在しない group/edge/node 参照、relation ID 不整合、evidence span と元本文の不一致が1件でもあれば書き込みを停止する。

### 移行・再実行（査読②指摘1）
- **B1 モードは入力 sidecar が Task A 形式（`2.3-phase1-taskA`）と B1 形式（`2.3-phase1-taskB`）の両方を受理**。現状 [:1433](tools/canvas_ingest.py:1433) は taskA のみ受理なので拡張。
- **B1 出力は常に B1 形式**（`schema_version: 2.3-phase1-taskB`）。
- **Task A モードは B1 形式を拒否**し、B1 を Task A 形式へ戻さない（後退防止）。
- `extractor_version` と `ingest_runs[].mode` に B1 用値（例 `taskB1-1.0` / `phase1-taskB1`）。
- CLI: `--taskb1` 専用モード。MD/sidecar は `wiki/sources/art-canvas-<canvas_id>.md`・`.usage.json` 固定。`--output-json`・`--slug`・`--output-dir` を拒否し、`--eagle-library` 明示と、実実行では `--update-index --update-log` を必須とする。出力MDまたはsidecarが既に存在する全実行は `--overwrite` 必須。`--dry-run` は一切書かない。safe-slice・Task A の制限は維持する。
- 入力: 前回 sidecar（Task A の related-to 147 or 前回 B1）を読み、related-to 維持＋ B1 relation 追加。出力: source MD 1 ＋ sidecar 1。
- previous sidecar は relation の全必須項目・型・許容値・run参照・relation/lineage ID 再計算一致・B1 group_memberships 固定形を検証し、不正なら拒否する。
- index は B1 専用の「観測事実＋Eagle照合」概要文を使い、log は MD・sidecar・registry・index・log の全変更先を列挙する。既存モードの `update_index` / `update_log` の挙動は変更しない。

### テスト（spec テスト方式準拠・具体 node 固定・raw read-only）
- 名前付き「アスナ新衣装」枠内画像 → `used-for: アスナ新衣装`（sidecar のみ）。名前なし枠のみの画像 → used-for なし・所属は観測記録。
- 名前付き外＋名前なし内の 3件（`a6120419bf38fe42` 等）→ 全所属を観測記録＋ used-for は「アスナ新衣装」。
- filename「…drawn by mx2j…」「mx2j が描いた…」→ `source-artist: mx2j`（`derived-interpretation`・epistemic 0.5）。`@…kronillust` → review_candidates。
- **Task A→B1 移行**: Task A sidecar 入力 → related-to 147 維持＋ B1 relation 追加＋ MD 生成。
- **B1→B1 再実行**: B1 出力を再入力 → churn ゼロ（relation_id 安定）。
- 合成 canvas で名前付き枠の入れ子→最内採用、単なる重なり→review・used-forなし。
- Task A が B1 sidecar を拒否する。relation/lineage ID 不一致・relation 必須項目欠落・group_memberships 不正を拒否する。
- source MD に解釈（used-for／要約）が出ない・`candidate` が事実欄に出ない。
- source MD は item_id/URL/状態のみ、annotation を含む Eagle 全観測は sidecar に保持、複数 exact match は全件扱う。
- source MD の全 file/text node は見出しとして各1回だけ掲載される。Canvas・sidecar・Markdown の機械整合性検査が不一致を拒否する。
- B2: 直接 text↔file edge の節だけを `note` 化し、evidence span が元本文と一致する。「長乳じゃない」は `negative`、「仮説」は `tentative`、明示 `?` / `？` は `question`。反語・暗黙疑問・複合疑問は review へ送る。
- B2: `mx2jが描いた` 等の明示構文だけをメモ由来 `source-artist` 化する。`depicts`・名前なし枠意味付け・メモ間伝播 relation は生成しない。
- B1 の `--dry-run` は無書き込み。既存モードの台帳処理に回帰がない。
- **変更許可ファイルのみ変化**: `wiki/sources/art-canvas-<id>.md`／`.usage.json`／`wiki/canvas-registry.json`／`index.md`／`log.md`。**それ以外の wiki・`raw/`・Eagle は before/after の hash・mtime 不変**（査読②指摘6）。
- B2→B2 再実行で relation_id churn ゼロ。B2 dry-run は無書き込み。

### 今回やらないこと
- Phase 2（wiki projection）／ Phase 3（Eagle 書き戻し）／ vision／ predicate 網羅辞書。
- `depicts`／名前なし枠の意味付け／メモ間伝播は、実害または必要性が確認されるまで対応しない。

## 未決事項（実装/次段階）
predicate 語彙＝**確定（2026-06-07・案1。「Task B 実装仕様」参照）**／ Eagle 許可 facet ホワイトリスト（Phase 3 直前）／ 高精度 Q&A 取り出し（`/llm-wiki query` を sidecar＋wiki＋Eagle 横断にどう拡張するか）。

## Phase 3（Eagle 書き戻し）相談ブリーフ（Codex 向け・2026-06-07）

> 武田さんが Eagle 書き戻しの着手前に Codex 相談を希望（2026-06-07）。本節は相談の論点整理で、**すべて未決（decided ではない）**。Codex は本 spec の現行版＋[[canvas-eagle-connection-strength]] を読んで助言すること。`~/.claude/plans/` の旧 brief（`codex-precious-matsumoto.md` 等）は参照しない（版取り違え防止）。本日更新したのはこの2ファイル。

**前提（現状）**: ingest（safe-slice / Task A / B1 / B2）＋ Q&A 軽量版 ＋ source-artist の wiki projection は実装済み。Eagle は read-only。連携評価＝読む向きは強い／書き戻しは未達（[[canvas-eagle-connection-strength]]）。

**設計の核（武田さん確定の考え方・2026-06-07）**:
- 「Canvas の使用意図＝一次情報」。ただし user-intent ドメイン限定（誰が描いた等の external-fact は要裏取り）。
- 3層（一次 user-intent / 要検証 external-fact / AI 推測）を混ぜない。
- 武田さんの `ai_` タグ運用が「専用接頭辞で層を分ける」方式の実証。書き戻しは `llmwiki__` 名前空間で同じ規律を踏襲。

**提案する最小パイロット（Claude 推奨）**: `used-for`（配置由来の使用テーマ）を Eagle confirmed 9件へ `llmwiki__used-for__<テーマ>` タグで付与。ドライラン→武田さん承認→付与。可逆（タグ削除で戻せる）。人間タグ・`ai_` タグ・フォルダ不可侵。full Phase 3 エンジン（集約・journal・drift・rollback）はパイロットで価値実証後に判断。

**Codex への相談事項（open）**:
1. パイロットを「手動 MCP 書き込み」でやるか、`canvas_ingest.py` に最小 `--eagle-project --dry-run` モードを足すか（後者は再現性・安全検証が高いが工数増）。
2. タグ名前空間／形式（`llmwiki__used-for__<theme>` の是非、`ai_` との整合、predicate を含めるか、日本語テーマ名の扱い）。
3. group-placement（used-for）を `provenance_kind: user-assertion` へ格上げするか（現状 derived-interpretation）。spec D/E と整合させる。
4. 冪等性・drift: 再 ingest／canvas 編集／Eagle 手動編集時の desired vs actual 差分。full Phase 3 の集約・`sync_runs[]` journal・drift 検出をどこまで前倒しするか。
5. 撤回: canvas から関係が消えた時の Eagle タグ除去ルール。
6. スコープ: used-for のみか、検証済み source-artist（ANYAK/mx2j）も含めるか。
7. 安全検証: dry-run 前後で raw・Eagle metadata・許可外 wiki が不変であることの確認方法（Task B 方式の踏襲）。

## 関連リンク
- [[pureref-personal-fork]] / [[canvas-reference-tools]] / [[art-canvas-pilot-2026-05-29-asuna-01]] / [[takeda-yohsuke]]
- 既存ツール: `tools/canvas_ingest.py`（`slugify`:186 / `parse_canvas_nodes`:206 / `file_node_context`:475）/ `skills/canvas-ingest/SKILL.md`（旧 Task A 依頼書は `~/.claude/plans/codex-precious-matsumoto.md`・参照非推奨）
- Eagle ライブラリ: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library`
