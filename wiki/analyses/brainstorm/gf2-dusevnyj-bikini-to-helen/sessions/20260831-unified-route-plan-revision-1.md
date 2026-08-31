---
type: build
title: Helen原作再現 成果物までの単一状態・KB活用計画 2026-08-31
status: active
confidence: medium
evidence_level: user-stated+source-backed+inferred
created: 2026-08-31
last_reviewed: 2026-08-31
plan_status: draft-unapproved
approval_scope: unified-route-plan-revision-1
related:
  - "[[gf2-helen-repro-execution-audit-plan-20260830]]"
  - "[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]"
  - "[[gf2-helen-repro-v51-run]]"
  - "[[brainstorm-gf2-dusevnyj-bikini-to-helen]]"
tags: [gf2, helen, deliverable, state-machine, knowledge-base, model-routing]
revision: 1
---

# Helen原作再現 成果物までの単一状態・KB活用計画 — 2026-08-31

## 承認状態

**draft-unapproved（計画案・未承認）。** 2026-08-31のbrainstormカードで承認されたのは
「既存の監査計画と成果物計画を、小さい単一状態ファイルで接続する方針を計画化すること」。
本revision 1の実行はまだ承認されていない。

## 目的・完成条件・今回やらないこと

### 目的

最終目的は、**Helenの原作再現をBlender成果物として成立させること**。監査、KB、コード抽出、
モデルレビューを成果物の代わりに数えない。会話の圧縮や担当モデルの交代が起きても、現在工程と目的を
再解釈できないよう、既存の監査計画と成果物計画を1つの機械状態へ接続する。

### 最低限の完成条件

1. S6・S8・G10の対象範囲、原作正解、探索分母、候補コード、反証、効果試験、候補Blend、原作比較が
   SHA-256で1本の来歴として逆引きできる。
2. KBから使った主張はページパス、状態、根拠レベル、`last_reviewed`、ファイルSHAとともに固定される。
3. 探索範囲はハイエンドモデルが根拠と反証込みで設計し、抽出は決定論的な走査器を主にする。
4. Luna / Sonnet級モデルによる大量候補整理と、別のハイエンドモデルによる因果審査を分離する。
5. 候補コードが成果物へ効くことを変異試験で確認するまで、正規Blendへ反映しない。
6. 現在工程、今回の目的、次に許される作業を機械状態から生成できなければ作業を開始しない。
7. 原作比較とユーザーの対象群限定の明示判断を経るまで、`complete`または原作一致と報告しない。

### 今回やらないこと

- 会話本文、長いhandoff、制作側モデルの成功報告を現在状態の正本にしない。
- 7,424件の未一致MSLをすべてHelen関連とみなさない。
- DLL 33本、Lua 1,031本、MonoScript 14,534件を、関連性の契約なしに全件深掘りしない。
- 原作入力が無い部分を他キャラ、既定値、一般論で埋めて忠実版と呼ばない。
- 本計画の承認前に監査コード、f166、Blend、quality-gate、writerを変更しない。

## 高リスク成果物の6点ゲート

| 項目 | 固定内容 |
|---|---|
| 正解の所在 | 原作H0157動画・比較フレーム、Unity prefab / renderer / material / RampSetting、原作コードと実行条件 |
| 欠けうる入力 | H0157 scene runtime join、Helen本人prefab root、InternalLut、有効Volume、実行時髪処理、最終版f166結果 |
| 性質の違う対象群 | S6＝顔の白飛び、S8＝髪の形・被覆、G10＝材質・階調表の対応。相互承認を流用しない |
| 代表例 | G10。原作の `renderer → submesh → material → RampSetting` 参照鎖を代表にする |
| 原作比較方法 | 同一場面・時刻・ポーズ・カメラ・衣装・ROIで原作フレームと候補Blendを比較し、変更前後の数値と画像SHAを固定 |
| 停止条件 | 探索分母不明、KB根拠の版不明、候補経路の辺不足、効果未検出、原作条件不一致、未登録writer、入力欠損 |

## 現在地の訂正

2026-08-31の実ファイル照合では次が事実である。

- 現行Blend SHA-256は
  `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5`。
- 旧品質ゲートの`plan`はPASS。ただし成果物完成の証拠ではない。
- 旧`gate-results.json`は15項目中G10のみFAILだが、Blend SHAを持たず`unbound`。
- f166初回結果は、最終正規化修正前なので`stale`。
- 監査P0は完了。証拠48件、open finding 5件、writer候補54本を一時領域へ記録済み。
- 正規の`06_repro-v51/audit/`、外部登録簿、契約、guard、fixtureは存在しない。
- よって、既存監査計画の実装記録「P0B step 2で停止」は現物と一致しない。
  正しい状態は**P0完了・P0B本体実装前**。

本計画を実行する場合、最初の書き込みはこの状態差の修正とする。修正前にP0B step 2へ進んだ扱いにしない。

## 正本の優先順位

1. ユーザー要件: `/Users/takedayousuke/.claude/plans/mellow-questing-elephant-v5.1.md` のREQ。
2. 現在状態: 本計画で拡張する `06_repro-v51/audit/state.json`。
3. 執行規則: `[[gf2-helen-repro-execution-audit-plan-20260830]]` revision 4。
4. コード回収技術: `[[gf2-helen-repro-plan-repair-model-routing-handoff-20260827]]` section 9.6-2〜4。
5. 実測記録: `[[gf2-helen-repro-v51-run]]` と `[[gf2-helen-repro-v51-handoff]]`。

下位正本が上位と矛盾した場合は、自動で解釈して統合せず`contested`へ入れる。

## 単一状態ファイル

新しい第2の状態ファイルは作らない。既存監査計画で予定している
`06_repro-v51/audit/state.json`を、監査状態と成果物ルートの**唯一の機械状態**にする。

最低限のトップレベル項目:

```json
{
  "schema_version": 1,
  "project_id": "gf2-helen-starlit-waltz",
  "goal_id": "helen-faithful-reproduction",
  "goal_text": "Helenの原作再現をBlender成果物として成立させる",
  "current_phase": "baseline_frozen",
  "phase_purpose": "現在の入力・証拠・Blendを変更せず固定する",
  "allowed_actions": [],
  "forbidden_actions": [],
  "active_blocker_ids": [],
  "plan_refs": [],
  "knowledge_snapshot_sha256": null,
  "defects": {},
  "transition_history": []
}
```

- `goal_text`、`current_phase`、`phase_purpose`、`allowed_actions`を状態報告の必須4項目にする。
- 状態更新は`audit_guard.py`の遷移APIだけが行い、必要証拠の現物SHAを再計算する。
- `transition_history`はappend-only。過去行の上書き・削除を拒否する。
- `run-state.json`はlegacy互換用に残し、矛盾すれば`EA_LEGACY_STATE_CONFLICT`で停止する。
- 会話、Markdown、モデルの最終回答から状態を直接更新しない。

## KBを探索契約へ変換する

### `knowledge-snapshot.json`

最低限、次のページを現物から読み、ファイルSHAと採用した主張ID・節を固定する。

- 原計画v5.1のREQ / OBS / GATE
- `gf2-helen-repro-v51-run.md`
- `gf2-helen-repro-v51-handoff.md`
- `gf2-helen-repro-execution-audit-plan-20260830.md`
- `gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md` section 9.6〜9.9
- `gf2-character-repro-pipeline.md`

各参照は`path`、`sha256`、`status`、`evidence_level`、`last_reviewed`、`claim_ids`、
`used_for`を持つ。legacyまたは鮮度不明の主張は、sourceか実ファイルへ戻らない限り探索条件へ採用しない。

### `search-contract.json`

ハイエンドモデルはKB snapshotだけを根拠に、S6・S8・G10別に次を出す。

- 探索対象の形式、ルート、ファイル集合、分母
- キーワードだけでなく、シンボル、定数、データ構造、呼出し・参照の候補辺
- 陽性対照と陰性対照
- 対象外条件
- 見つからなかったときに否定できる範囲
- 追加形式へ拡張してよい条件

探索契約の作成者は、同じ契約の独立レビューを兼任しない。

## モデル配分

| 役割 | 能力水準 | 入力 | 出力 | 禁止 |
|---|---|---|---|---|
| 探索設計 | ハイエンドモデル | KB snapshot、defect契約、原作差 | `search-contract.json` | 候補コードを未読で確定扱い |
| 全量抽出 | 決定論的走査器 | 承認済み探索契約、入力SHA | `code-corpus.json` | モデルの記憶だけで「全件」と報告 |
| 候補整理 | GPT-5.6 Luna / Sonnet 5級 | 抽出原文、位置、依存辺 | `code-candidates.json` | 成果物へ効くと最終判定 |
| 因果審査 | 別のハイエンドモデル | 候補、反証、原作差、実行条件 | `effect-hypotheses.json` | 制作側説明を根拠に採用 |
| 効果検証 | 機械試験 | 単一候補、固定入力、対照 | `effect-tests.json` | 複数変更をまとめて因果主張 |
| 原作比較 | 独立検証役 | 原作、候補Blend、条件SHA | 差の一覧、比較証拠 | 内部PASSを原作一致と言い換え |

モデル名は固定しない。担当時点で同等以上のモデルを選べるが、役割の分離と直接証拠の要求は変えない。
低・中コストモデルの出力は、ファイル位置とSHAが無い行を自動で不採用にする。

## 状態遷移と成果物までの順序

| phase | 目的 | 進入に必要な肯定証拠 | 次に許す作業 |
|---|---|---|---|
| `baseline_frozen` | 現在地の固定 | P0のBlend・証拠・writer SHA、現物との状態一致 | bootstrap構築のみ |
| `enforcement_ready` | 誤進行を止める | rev.4 P0B、全fixture、writer独立確認、新plan PASS | f166最小修理 |
| `coverage_verified` | 探索分母を確定 | 最終版f166、入力・script SHA、1回の全量再走査、再集計 | KB snapshotと探索契約 |
| `search_scoped` | 探す範囲を固定 | KB snapshot、ハイエンド探索契約、別レビュー | 決定論的抽出 |
| `candidate_traced` | 候補と経路を記録 | 原文・位置・SHA・依存辺、Luna/Sonnet級整理、欠けた辺 | ハイエンド因果審査 |
| `causal_reviewed` | 効果仮説を反証込みで限定 | 別モデルの審査、陽性・陰性対照、単一変更の試験設計 | 変異試験 |
| `causal_tested` | 成果物への効果を実測 | 変更前後SHA、予測、測定差、再現試験 | 候補Blend書出し |
| `artifact_candidate` | 検証済み変更を候補へ反映 | writer begin/finish、入力・コード・出力SHA、回復経路 | 独立原作比較 |
| `artifact_measured` | 原作との差を列挙 | 同一条件の比較、差分、別検証役、対象群 | 人間レビュー |
| `human_review` | 許容差だけを判断 | 代表1〜4件、LLMが先に列挙した差 | accepted / rejected |
| `accepted` | 完成報告を許す | 対象SHA、比較条件、明示承認、open findingなし | complete報告 |

`blocked`、`contested`、`rejected`は既存監査計画revision 4の意味を使う。どのphaseから入ったかを
`entered_from`で保持し、証拠が揃ってもそれより先へ飛ばない。

## 実装順

### U0　記録差を直す

- 監査計画の実装記録を「P0完了・P0B本体実装前」へ訂正する。
- 一時領域の8ファイル、正規audit不存在、外部登録簿不存在を実測として記録する。
- Blend、f166、既存writerは変更しない。

### U1　既存監査計画revision 4のP0B〜P5を実装する

- `audit/state.json`に本計画のトップレベル状態を統合する。
- 一時領域で契約、guard、fixture、共有ゲート変更、非破壊a10、writer接続を構築する。
- 54 writerの分類は、走査を作った側と別のLuna / Sonnet級モデルが全行を直接照合する。
- 曖昧または複数採用経路を持つ行だけ、ハイエンドモデルへ上げる。
- 全試験後に一括導入し、新`plan` PASSで`enforcement_ready`へ進む。

### U2　f166を最小修理し1回だけ全量再走査する

- section 9.6-2〜3を実行する。旧結果を上書きしない。
- MSL末尾NUL、post不一致、vertex控除、LZ4失敗を再集計する。
- 18,568 bundleという旧分母も入力変更があれば再計算する。
- 非決定性が観測された場合だけ2回目を実行する。

### U3　KB snapshotとS6・S8・G10探索契約を作る

- ハイエンドモデルが作成し、別の検証役がKBの採用箇所と探索分母を直接確認する。
- G10を代表にするが、G10の承認をS6・S8へ流用しない。

### U4　機械抽出と低・中コストモデルの候補整理

- まずMSLを探索し、契約が要求したときだけDLL、Lua、MonoScript、variant表へ拡張する。
- 各候補に絶対パス、ファイルSHA、位置、周辺原文、入力・出力、依存辺、対象defectを持たせる。
- 抽出不能、暗号化、動的生成、欠損辺は`blocked`理由として残す。

### U5　ハイエンドモデルによる因果審査

- 候補が「存在する」ことと、Helenの原作差へ「効く」ことを分ける。
- 代替説明、実行条件、候補が呼ばれない可能性、別scene・別衣装の可能性を反証する。
- 1候補1変更の効果試験へ落とせない仮説は`candidate_traced`から進めない。

### U6　効果試験と候補Blend

- 正規Blendを直接上書きせず、guardが許可した一時出力へ書く。
- 変更前後で、対象欠陥に対応する測定値と画像だけが予測方向へ変わるか確認する。
- 効果が無い、逆方向、複数原因を区別できない場合は`rejected`または`contested`へ戻す。

### U7　原作比較とユーザー判断

- 独立検証役が原作、入力、コード、候補Blendを直接読む。
- LLMが差を先に列挙し、ユーザーへは代表1〜4件の許容判断だけを求める。
- 対象群・入力条件・比較項目が変われば承認は失効する。

## 承認範囲

本revision 1が後続カードで実行承認された場合、U0〜U7を1本の通常作業ルートとして扱う。
U1終了後に「次はf166を実行してよいか」を再質問しない。次の場合だけ停止して新しい判断を求める。

- 正規Blendの不可逆上書き、前面GUI、外部サービス、未承認の強制DL・attachが必要。
- 忠実版をblockedのままにするか、近似版を分離して作るかを選ぶ必要がある。
- 原作比較後の見た目の差を許容するか判断する。
- 目的、対象衣装、対象場面、完成条件を変更する必要がある。

## 機械ゲート

既存`project_quality_gate.py`へ次を追加する。

- `EA_PHASE_CONTEXT_MISSING`: 目的・現在工程・工程目的・許可作業のいずれか欠落。
- `EA_KB_SNAPSHOT_STALE`: 採用KBページのSHA、状態、根拠レベル、鮮度が不一致。
- `EA_SEARCH_SCOPE_INVALID`: 分母、対象外、陽性・陰性対照、反証条件が欠落。
- `EA_CANDIDATE_PROVENANCE_MISSING`: 候補の原文位置・SHA・依存辺が欠落。
- `EA_REVIEW_ROLE_COLLISION`: 探索契約作成者と独立確認者、候補整理者と因果審査者が同一。
- `EA_EFFECT_NOT_DEMONSTRATED`: 変異試験で予測方向の効果を確認できない。
- `EA_ARTIFACT_LINEAGE_BROKEN`: 入力、コード、writer、Blend、比較画像のSHA鎖が切れている。
- `EA_GOAL_DRIFT`: 状態報告の目的が固定`goal_id`と一致しない。

各エラーはfixtureで正常対照PASSと単一変異FAILを証明する。単に例外終了した結果は合格に数えない。

## 状態報告の固定形式

すべての作業開始・終了報告を状態ファイルから生成する。

```text
最終目的: Helenの原作再現をBlender成果物として成立させる
現在工程: <current_phase>
今回の意味: <phase_purpose>
次に許される作業: <allowed_actionsの先頭>
停止理由: <active_blocker_ids または なし>
成果物までの残り: <状態表上の未通過phase>
```

この6行を生成できない場合、通常の説明を続けず`EA_PHASE_CONTEXT_MISSING`で停止する。

## 完成・blocked・approximationの終点

- `accepted`: 原作比較と対象限定の人間判断まで完了。`complete`報告可能。
- `blocked`: 必要入力や実行時joinが回収できず忠実再現を進められない。完成とは言わない。
- `approximation`: 忠実版と別の成果物・別のstate枝にし、何を失うかを示して別承認を取る。
- 大量のコードを抽出したこと、ハイエンドモデルが有望と判定したこと、監査がPASSしたことを終点にしない。

## 捨てた案と、手元でどう変わるか

### 監査計画と成果物計画を会話で順番につなぐ

1. 捨てたもの: 会話の「次はこれ」で2計画を切り替える運用。
2. 手元でどう変わるか: 目的と現在工程の6行が毎回表示され、機械状態と違う作業は開始できなくなる。
   自由に工程を飛ばす速さは失う。
3. 戻せるか: state gateを無効化すれば戻せるが、コンテキスト崩壊の防止も同時に失うため通常運用では禁止。

### 7,424未一致MSLの無条件な全件深掘り

1. 捨てたもの: 7,424種をすべてモデルへ読ませる案。
2. 手元でどう変わるか: Helenと無関係なコードの解析待ちを減らし、S6・S8・G10契約に一致する候補から進む。
   契約が間違っている場合に候補を見落とす可能性があるため、陽性対照と探索契約の独立レビューを必須にする。
3. 戻せるか: 陽性対照欠落または候補0件なら、対象形式を段階的に拡張できる。

### 全工程を最上位モデルへ固定

1. 捨てたもの: 走査・抽出整理を含む全作業を最上位モデルだけで行う案。
2. 手元でどう変わるか: 機械抽出とLuna / Sonnet級整理で大量処理し、ハイエンドモデルを探索設計と因果審査へ集中できる。
   一人のモデルが全体を一度に把握する単純さは失う。
3. 戻せるか: 曖昧例だけハイエンドへ上げられる。役割分離と独立確認は戻さない。

## 実装前の最終条件

1. 本計画revision 1の明示承認。
2. 独立レビューでCritical・Major findingがないこと。
3. `quality-gate.json`の高リスク6点が本計画と一致すること。
4. U0の状態訂正後もBlend SHAと旧plan PASSが変わらないこと。

この4条件が揃うまで実装を開始しない。
