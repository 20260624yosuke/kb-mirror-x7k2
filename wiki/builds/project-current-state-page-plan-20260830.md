---
type: build
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-31
revision: 3
sources:
  - project-hub-index
  - project-current-state-page-plan-review-20260830
  - project-current-state-page-plan-review2-20260830
---

# プロジェクト現在位置ページの導入 — 実装計画 rev.3（2026-08-31）

> [!info] この計画の位置づけ
> `/brainstorm` セッション（2026-08-30〜31）で武田さんの承認を得た設計を、実装可能な粒度まで落としたもの。
> ブレストメモの正本は
> `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md`。
> **この計画はまだ実装承認を得ていない。**

> [!warning] rev.3 での変更（2回目の独立レビューの critical 2件・major 多数を受けて）
> 1. **判定⑤を2系統にした**（5.1）。rev.2 は共通欄ファイルの時刻しか見ず、承認済み完成条件
>    「`run-state.json` とのずれで止める」が不成立だった（A-1）。
> 2. **`verify` の仕様を明記**（8.2）。rev.2 は4か所から参照しながら仕様が存在しなかった（A-6）。
> 3. **節3・節4・節8 を機械区画へ移した**（3.3）。所有権の未定義・二重所有・正解表を作れない問題を
>    まとめて解消（A-2/A-3/A-4/A-6）。
> 4. **1.2 の表を実測し直した**（A-7）。#3 は 585行、合計 4,593行。
> 5. **壊れ#4 の実態を書き換えた**（1.1/1.3）。「印が無い」ではなく **4枚が互いに「自分が正本」と
>    主張している**。武田さんの承認により **該当行だけ書き換える（案1）**。禁止事項に例外を1つ作る。
> 6. **Bash 経由の誤検知試験を追加**（9.1 試験11・12）。実測された誤検知の型がこれ（A-8）。
> 7. **手順12 に承認D を追加**（A-9）。残り3案件への展開を無承認で行わない。
> レビュー全文: `wiki/analyses/project-current-state-page-plan-review2-20260830.md`

---

## 0. 用語

- **KB ルート**: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
- **現在位置ページ**: 1案件につき1枚。新しいセッションが最初に読む唯一の入口。
- **`run-state.json`**: 各作業フォルダに既存する記録ファイル。**この計画では読み取り専用。**
- **共通欄ファイル**: 新設する `run-state.common.json`。`run-state.json` の隣に置く。
- **機械区画 / LLM 区画**: 現在位置ページ内で、スクリプトが書く節と LLM が会話から書き起こす節。

---

## 1. 解こうとしている問題（実測・2026-08-30〜31）

セッションが切り替わるたびに、武田さんが「引き継ぎ資料を作って」と LLM へ指示し、
出てきた資料を次のセッションへ添付している。これが次の3つを生んでいる。

1. **毎回の手間**が発生する。
2. **その資料がどれだけ保持できているか測れない。**
3. **資料が増える。** 1.2 の7枚・合計4,593行。

### 1.1 構造的な壊れ（4件）

| # | 壊れ | 実測値 |
| --- | --- | --- |
| 1 | 現在位置を主張するものが2系統 | `run-state.json` 最終更新 2026-08-26 20:55 ／ wiki 側の引き継ぎは 08-27〜08-30 |
| 2 | 閉じた項目が「残り」に残る | `agent_executable_remaining` は3件を名乗るが、実態は完了1・対応済み1・実装不能1＝**実行可能な残りは0件** |
| 3 | 派生案件の所在がバラバラ | 水着化だけ 3D資料フォルダの外（KB 内 `output/gf2-helen-swimsuit/`） |
| 4 | **引き継ぎ資料4枚が互いに「自分が正本」と本文で宣言している** | 1.3 の表のとおり。冒頭に印を足しても本文が主張し続ける |

### 1.2 引き継ぎ資料7枚（2026-08-31 に `wc -l` で再計測）

| # | 実パス | 行数 |
| --- | --- | --- |
| 1 | `wiki/builds/gf2-helen-repro-v51-run.md` | 1737 |
| 2 | `wiki/builds/gf2-helen-repro-v51-handoff.md` | 1582 |
| 3 | `wiki/builds/gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md` | **585** |
| 4 | `wiki/builds/gf2-repro-and-swimsuit-conversation-handoff-20260827.md` | 235 |
| 5 | `06_repro-v51/reports/HANDOFF.md` | 313 |
| 6 | `06_repro-v51/reports/NEXT-SESSION-PROMPT.md` | 131 |
| 7 | `06_repro-v51/reports/HANDOFF-2026-08-20.md` | 10 |

合計 **4,593行**（rev.2 の 4,578 は #3 の行数が古かった）。
**実装の手順1で再計測し、値が違えばこの表を更新してから進む。**

### 1.3 壊れ#4 の実態と、武田さんの決定（案1）

4枚が本文で「自分が正本」と宣言している（2026-08-30 実読）。

| ファイル | 行 | 文面 |
| --- | --- | --- |
| `gf2-helen-repro-v51-run.md` | 14 | 「…に従う実行の**正本**。この会話の記憶ではなく、このページと下記ファイル群が現在位置を持つ」 |
| `gf2-helen-repro-v51-handoff.md` | 30 | 「前の版は `reports/HANDOFF.md`。**衝突したらこちらが新しい**」 |
| `gf2-helen-repro-plan-repair-model-routing-handoff-20260827.md` | 54 | 「現行の提案・再開地点はsection 9を**正本とする**」 |
| `gf2-repro-and-swimsuit-conversation-handoff-20260827.md` | 26 | 「巨大な既存**正本**を置き換えず…」 |

**武田さんの承認（2026-08-30・案1）**:

> 4枚の中の「正本」「こちらが新しい」と書いてある行**だけ**を
> 「このページは旧。現在位置は○○が正本」へ直す。**他の本文は1文字も触らない。**
> 直した行は元の文面ごと全件記録する。

- 対象は上の4〜6行のみ。実装の手順3で、対象行を全部拾い出して武田さんへ提示する（承認B-2）。
- 記録先: `wiki/builds/gf2-helen-repro-master-claim-rewrites-20260831.md`（新規）。
  「ファイル・行番号・変更前の全文・変更後の全文」を4〜6件すべて残す。
- **これは禁止事項「既存の本文を書き換えない」の唯一の例外**（10節-2）。

### 1.4 分量の問題（別立て）

`run-state.json` は **129,913文字**・履歴113件。正確だがそのままセッションへ渡せない。
現在位置ページの 400行上限（3.4）で対処する。

### 1.5 承認済みの要件「案1と案2を両方」

- **案1** = 現在位置ページを新設する（本計画の中心）。
- **案2** = 既存ページ側から現在位置ページへ**戻る道**を作る。置くのは**1行ポインタだけ**。
  一覧は複製しない（複製は二重管理になり必ず片方が古くなる）。

> 生成側に案件ごとの読み取り規則を持たせる案は **「対応表案」** と呼び、11節で却下している。
> **武田さんが承認した「案2」とは別物。** 名前の衝突による混同を避けるための呼び分け。

---

## 2. 完成条件

**合格の基準は1つ。「引き継ぎ資料を作って」と指示せずに、新しいセッションが HELEN-REPRO v5.1 を再開できること。**
判定方法は9.2（閉じた試験）。以下はその内訳。

1. `wiki/builds/` に **1案件1枚の現在位置ページ**が存在する（新ディレクトリ・新 `type` は作らない）。
2. ページが **8見出し構成**で、機械区画と LLM 区画が分かれている（3.3）。
3. **全対象案件に共通欄ファイル `run-state.common.json` がある**（4節）。
   **既存 `run-state.json` 3ファイルが1バイトも変更されていない**（9.1 試験10）。
4. **6つの機械判定**が動く（5節）。フック登録と実効性確認を、9.2 の本番試験**より前**に終える。
5. helen の壊れ4件（1.1）が解消されている。#4 は **1.3 の対象行が書き換わり、
   記録ファイルに変更前後が全件残っている**状態を指す。
6. **既存ページ側から現在位置ページへ戻れる**（1.5 の案2）。対象は 1.2 の7枚と `index.md` の該当行。

### 完成条件に**含まない**もの

- 成果物 blend の変更。
- helen の工程を先に進めること（G10 の解消、未回収コードの回収など）。
- 水着化・ふたなり化・キャラ抽出の**内容**の整理（現在位置ページを置くところまで）。

---

## 3. 現在位置ページの仕様

### 3.1 置き場と命名

- 置き場: `wiki/builds/`
- ファイル名: `<案件slug>-current-state.md`（helen → `gf2-helen-repro-current-state.md`）
- **新ディレクトリを作らない・新 `type` を作らない。**

> [!warning] frontmatter スキーマの追加は構造決定にあたる
> KB の `CLAUDE.md` 2節は「frontmatter スキーマや `type` の追加変更」を**要承認**と定めている。
> 3.2 の欄はすべて承認A-1 の対象。

### 3.2 frontmatter

```yaml
---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: YYYY-MM-DD
sources: []

# ここから下がこの計画で足す欄（構造決定・承認A-1 の対象）
current_state_page: true
project_slug: gf2-helen-repro
project_status: active            # active | blocked | paused | done
work_roots:
  - /絶対パス
common_files:
  - /絶対パス/run-state.common.json
synced_from:                      # ファイルパスごとの mtime（対応表。スカラー不可）
  "/絶対パス/run-state.common.json": "YYYY-MM-DDTHH:MM:SS+09:00"
  "/絶対パス/run-state.json": "YYYY-MM-DDTHH:MM:SS+09:00"
last_synced: "YYYY-MM-DDTHH:MM:SS+09:00"
---
```

- `synced_from` には**共通欄ファイルと `run-state.json` の両方**を入れる（判定⑤b のため）。
- `last_synced` / `synced_from` はスクリプトだけが書く。LLM は書き換えない。

### 3.3 本文の見出し（8つ・完全一致で固定）

| # | 見出し | 書く主体 | 生成元 |
| --- | --- | --- | --- |
| 1 | `## いま何をしているか` | **LLM** | 会話から書き起こす。3行以内 |
| 2 | `## 現在位置` | 機械 | `common.current_step` / `gates` / `artifacts` |
| 3 | `## 止まっている理由` | 機械 | `common.blocked[]` のうち `state != done` |
| 4 | `## 次の選択肢` | 機械 | `common.next_choices[]` |
| 5 | `## 武田さんの判断` | **LLM** | 会話から書き起こす。書式は 3.5 で固定 |
| 6 | `## 関連ファイル（実パス）` | 機械 | `common.related` |
| 7 | `## 履歴の所在` | 機械 | `common.history_ref` |
| 8 | `## 片付いた記録` | 機械 | `common.blocked[]` のうち `state == done` |

- **LLM 区画は節1と節5の2つだけ。** 機械は節1/5 を読むだけで書き換えない。
  LLM は節2/3/4/6/7/8 を手で書かない。
- rev.2 では節3を「機械と LLM」、節4を「LLM」としていたため、所有権が決まらず、
  節8を判定②と③が二重に所有し、正解表も作れなかった。**rev.3 で全部機械側に寄せた。**
- 節3・節8の列: `件名 | state | 何が起きているか | 根拠の実パス`
- `state` の語彙: `blocked` / `unresolved` / `waiting-user` / `ready` / `done`
- 判定③（片付いた行の移動）は、**`common.blocked[].state` を見て振り分けるだけ**になる。
  ページ内で行を移動する処理は無い。

### 3.4 分量の上限

- 現在位置ページ全体で **400行以内**。
- **超えても sync をエラーにしない。** 次の順に切り詰める。
  1. 節8を `<slug>-current-state-archive.md` へ移す（3.6）
  2. 節7の列挙を件数だけに縮める
  3. それでも超えたら、超過した旨を節7の末尾に1行書いて**そのまま生成する**（警告扱い）
- 400行に根拠は無い。helen で実測してから調整する（10節）。

### 3.5 節5「武田さんの判断」の書式（判定④のため固定）

```markdown
| 日付 | 状態 | 判断 | 出どころ |
| --- | --- | --- | --- |
| 2026-08-18 | 有効 | 胸下のイボイボは現状問題なし。この項目は閉じる | 会話（武田さん明示） |
| 2026-08-28 | 有効 | LLM の画像判読は封印する | 会話（武田さん明示） |
```

- `状態` は `有効` か `失効` のみ。`失効` の行は消さずに残す。
- 判定④は「全行に `YYYY-MM-DD` があり、状態が `有効`/`失効` のいずれか」を機械で見る。

### 3.6 archive ファイルの仕様（3.4-1 で作る場合）

- ファイル名: `<slug>-current-state-archive.md`、置き場は `wiki/builds/`。
- frontmatter は `type: build` / `status: superseded` / `project_slug: <同じ>`。
  **`current_state_page` は付けない**（判定①の重複判定に当たらないようにするため）。
- 冒頭に1行: 「これは `<slug>-current-state.md` の片付いた記録の置き場。現在位置ではない。」
- `index.md` へは登録する。案2のポインタは不要（現在位置ページから機械が参照する）。

---

## 4. 共通欄ファイル `run-state.common.json`

### 4.1 なぜ既存 `run-state.json` の中ではなく隣に置くのか（承認A-0 の対象）

武田さん承認済みの「案1」は「`run-state.json` に共通の最小欄を足す」だった。
1回目のレビューで次が判明したため、**同じ内容を隣の別ファイルに置く方式へ変更する**ことを提案する。

| `run-state.json` の中に足す場合の問題 | 別ファイル方式での解消 |
| --- | --- |
| `json.load` → `json.dump` で全体を書き直すため、キー順・インデント・非 ASCII エスケープ・改行が変わる。helen は日本語が大量にあり既存の grep 前提スクリプトが壊れうる | **既存ファイルを1バイトも触らない** |
| 複数セッション並行で読み込み→全体書き戻しすると後勝ちで消える | 書き込み対象が別ファイルになる |
| helen には既にトップレベル `blocked` があり、語彙が違う2本が同居する | 同居しない。既存 `blocked` は履歴として扱う（4.3） |
| バックアップ `.bak` の置き場が判定の対象に入る | バックアップ自体が不要 |

**承認済み内容からの差分（承認A-0 で明示する）**:

1. 共通欄の置き場が `run-state.json` の中 → **隣の別ファイル**。
2. 水着化について、承認済み文言は「`run-state.json` を**新規作成**」だったが、
   別ファイル方式では **`run-state.common.json` だけを作り、`run-state.json` は作らない**。
3. 現在位置ページの構成が、承認済み文言の「7節」→ **8見出し**（`## 片付いた記録` を明示したため）。

再承認が得られない場合は `run-state.json` へ直接足す方式に戻し、上の4行のリスクを10節へ明記して進む。

### 4.2 スキーマ（判定②が読む入力の全部）

```json
{
  "schema": "run-state.common/1",
  "_notice": "工程・止まっている理由・次の選択肢の正本はこのファイル。run-state.json の他のキーは履歴であり、現行判定に使わない。",
  "project_slug": "gf2-helen-repro",
  "updated_at": "2026-08-31T12:34:56+09:00",
  "source_run_state": "/絶対パス/run-state.json",
  "current_step": "E（検証）40回目",
  "gates": {
    "passed": ["G1", "G2a"],
    "failed": ["G10"],
    "source": "/絶対パス/logs/f152-visual-gates.json"
  },
  "artifacts": [
    {"path": "/絶対パス/blends/helen-h0157-repro.blend",
     "sha256": "04ef8b79…", "mtime": "2026-08-25T17:29:00+09:00"}
  ],
  "blocked": [
    {"item": "…", "state": "blocked", "why": "…", "evidence": "/絶対パス"}
  ],
  "next_choices": [
    {"label": "A", "what": "SH→Blender ライティング再構成の別計画を立てる", "cost": "…"}
  ],
  "related": {
    "projects": [{"name": "ヘレン水着化", "root": "/絶対パス"}],
    "handoffs": [{"path": "/絶対パス", "status": "current"}],
    "data":     [{"path": "/絶対パス", "note": "原作データ"}]
  },
  "history_ref": {"file": "/絶対パス/run-state.json", "key": "history", "count": 113}
}
```

- **生成側（判定②）はこのファイルだけを読む。** 案件ごとの読み取り規則を持たない
  ＝「対応表案」に戻らない。
- 欄が欠けていたら**生成を止める**（部分生成しない）。欠けている欄名を出す。
- `next_choices[]` は rev.3 で追加。**節4を機械区画にし、正解表を作れるようにするため**（A-6）。
- `related.handoffs[].status` は `current` / `superseded` のみ。
  **これが 1.2 の7枚リストと「現行/旧」表示の唯一の出所**（A-2/A-5）。
- `history_ref.key` は `null` を許す（ふたなり・キャラ抽出には `history` キーが存在しない）。

### 4.3 既存 `run-state.json` との関係

- **既存 `run-state.json` は読み取り専用。書き換えない。**
- 共通欄ファイル先頭の `_notice` で、正本がどちらかを明示する。
- ただし次のセッションが `run-state.json` を直接読めば古い記述を拾いうる。
  **判定⑥（現在位置ページ未読ブロック）**で手前に関所を置く。それでも残るリスクは10節。

### 4.4 対象4案件

| 案件 | 共通欄ファイルの置き場 | 既存 `run-state.json` |
| --- | --- | --- |
| helen 原作再現 | `06_repro-v51/run-state.common.json` | 226,044バイト・`last_updated` |
| ふたなり化 | `07_futa-helen/run-state.common.json` | 3,294バイト・`updated_at`（TZ無し） |
| キャラ抽出 | `gf2-char-extract/run-state.common.json` | 49,439バイト・`updated_at` |
| 水着化 | `output/gf2-helen-swimsuit/run-state.common.json` | **無い。作らない** |

- 内部時刻と mtime は既にずれている（ふたなり: 内部 `2026-08-27T03:10:00` / mtime Aug 26 22:45、
  抽出: 内部 `2026-08-26T22:45:00+09:00` / mtime Aug 26 22:11）。
  **判定⑤はファイルの mtime だけを見る。JSON 内部の時刻は使わない。**

---

## 5. 6つの機械判定

実装先: `<KBルート>/tools/project_state_sync.py`（新規）。

| # | 判定 | 発火点 | 落ちたときの動作 |
| --- | --- | --- | --- |
| ① | 同じ `project_slug` の現在位置ページが2枚以上ないか | `check` / ページ書き込み前 | エラーで止める。両方のパスを表示 |
| ② | 節2/3/4/6/7/8 を共通欄ファイルから生成 | `sync` | 欄が欠けていれば止める（部分生成しない） |
| ③ | `common.blocked[]` を `state` で節3と節8へ振り分け | `sync` | — |
| ④ | 節5の全行に日付があり状態が `有効`/`失効` か | `check` | 該当行を挙げて FAIL |
| ⑤ | **2系統のずれ検知**（5.1） | `work_roots` への書き込み前（フック） | 書き込みを拒否し `sync` を促す |
| ⑥ | 現在位置ページを読まずに `work_roots` へ書こうとしていないか | 同上 | 拒否し、現在位置ページの実パスを示す |

- 判定①の走査範囲: `<KBルート>/wiki/` 配下の `*.md`。`_attachments/` と `*.bak*` は除外。
- **判定⑥は判定⑤と同じ適用範囲・同じ除外規則を使う**（5.1-1、5.1-2）。

### 5.1 判定⑤の詳細（A-1 と B-6 の両方に対処）

**2系統のずれを見る。**

- **⑤a（現在位置ページが古い）**: 共通欄ファイルの mtime > `synced_from[共通欄ファイル]`
- **⑤b（共通欄ファイルが古い）**: `run-state.json` の mtime > `synced_from[run-state.json]`

どちらかが成立したら書き込みを拒否する。
rev.2 は⑤a しか見ておらず、**作業側が `run-state.json` を更新しても検知されなかった**（A-1）。
⑤b を足したことで、承認済み完成条件「`run-state.json` とのずれで止める」が成立する。

**自己ロックを起こさないための規定（B-6 の再発防止）**:

1. **除外**: 次への書き込みは判定⑤⑥の対象外。
   `run-state.json` / `run-state.common.json` / `*.bak*` / `__pycache__/` /
   `<KBルート>/logs/project-state-sync.log`
   → **共通欄ファイルや `run-state.json` を更新する行為自体がロックの原因にならない。**
2. **適用範囲の限定**: `wiki/builds/` に `current_state_page: true` のページが存在し、その
   `work_roots` に含まれるパスだけを対象にする。**該当しなければ即座に通す。**
   `~/.claude/settings.json` はグローバルなので、無関係のセッションを巻き込まない。
3. **脱出口**: `python3 tools/project_state_sync.py resync --force <slug>` で、生成に失敗しても
   `synced_from` だけを現在の mtime へ更新できる。使用は `logs/project-state-sync.log` に残す。
4. **エラーで詰まない**: 3.4 のとおり 400行超は警告に留め、sync は成功する。
5. **外付け未マウント時**: `stat` が失敗したら**止める**（安全側）。マウント案内を出す。

**通常の流れ**: 作業 LLM が `run-state.json` を更新 → 次の書き込みで⑤b が止める →
共通欄ファイルを更新して `sync` → 通る。
これにより **10節-1「共通欄ファイルを誰が維持するか」も機械が促す形**になる。

### 5.2 判定⑥の詳細

- `brainstorm_guard.py` の「未読ブロック」と同型。セッション内で現在位置ページを読んでいなければ、
  `work_roots` への書き込みを拒否する。
- 適用範囲と除外は 5.1-1・5.1-2 と同一。
- **完全ではない**（読んだうえで `run-state.json` も読めば古い記述は目に入る）。10節に残す。

### 5.3 コマンド

```
python3 tools/project_state_sync.py sync    <project_slug>   # 節2/3/4/6/7/8 を生成
python3 tools/project_state_sync.py check   <project_slug>   # 判定①④＋案2ポインタの検査
python3 tools/project_state_sync.py verify  <project_slug>   # 8.2 の照合
python3 tools/project_state_sync.py resync --force <slug>    # 脱出口（5.1-3）
python3 tools/project_state_sync.py list                     # 一覧と鮮度
```

---

## 6. 案2（既存ページ側からの戻り道）の仕様

- 対象: `common.related.handoffs[]` に載っている全ファイル（＝1.2 の7枚）と、`index.md` の helen 関連行。
- **ブロックは機械が書く。** 手で書かない（A-2 の二重管理を避けるため）。
  `status` の値は `common.related.handoffs[].status` が唯一の出所。

```markdown
<!-- current-state-pointer:start -->
> [!info] この案件の入口
> 現在位置は `wiki/builds/gf2-helen-repro-current-state.md` が正本。このページは経緯の記録。
> 現行/旧: **旧**
<!-- current-state-pointer:end -->
```

- マーカーで囲むことで、機械が何度でも置換できる（本文は触らない）。
- `check` は、対象全ファイルにブロックがあり、**その `現行/旧` が `common` の `status` と一致する**ことを
  確認する（在ることだけを見ない）。

---

## 7. 実装手順

| 手順 | 内容 | 承認関所 |
| --- | --- | --- |
| 0 | 4.1 の差分3点（別ファイル化・水着化の扱い・8見出し）を提示 | **承認A-0** |
| 1 | 1.2 の7枚を `wc -l` で再計測。既存 `run-state.json` 3ファイルの **SHA-256 を基準値として記録** | — |
| 2 | helen の現在位置ページを**手で1枚**作る（雛形の確定）＋ frontmatter 欄の提示 | **承認A-1** |
| 3 | 1.3 の書き換え対象行を全部拾い出し、変更前後の全文を提示 | **承認B-2** |
| 4 | 共通欄ファイルのスキーマを確定し提示 | **承認B-1** |
| 5 | 4案件に `run-state.common.json` を作成（既存 `run-state.json` は読むだけ） | — |
| 6 | 1.3 の該当行を書き換え、記録ファイルへ変更前後を全件保存 | — |
| 7 | `tools/project_state_sync.py` を実装（`sync`/`check`/`verify`/`resync`/`list`） | — |
| 8 | helen で `sync` → `verify` を実行し、生成物が実データと一致することを確認 | — |
| 9 | 案2のポインタを機械で挿入（対象7枚と `index.md`） | — |
| 10 | **判定⑤⑥のフックを登録し、9.1 の実効性試験を全部通す** | — |
| 11 | **本番の閉じた試験**（9.2） | **承認C** |
| 12 | `index.md` 更新・`log.md` 追記・成果物 Inbox へ申告 | — |
| 13 | 残り3案件へ展開 | **承認D**（対象群が違うため別承認） |

- 承認A-0・A-1・B-1・B-2・C・D では**停止する**。無回答・沈黙・カードを閉じただけは承認ではない。

---

## 8. `verify` の仕様（rev.3 で新設・A-6 への対処）

`verify <slug>` は、**生成物と実データを照合**する。すべて機械で判定でき、1つでも不一致があれば
非ゼロ終了する。

### 8.1 照合項目

| # | 照合するもの | 方法 |
| --- | --- | --- |
| 1 | `common.artifacts[].sha256` | 実ファイルの SHA-256 を再計算して一致するか |
| 2 | `common.artifacts[].mtime` | 実ファイルの mtime と一致するか |
| 3 | `common.blocked[].evidence` | そのパスが実在するか |
| 4 | `common.gates.source` | そのパスが実在するか |
| 5 | `common.related` の全パス | 実在するか |
| 6 | `common.history_ref` | `file` が実在し、`key` があればその要素数が `count` と一致するか |
| 7 | 生成された節2/3/4/6/7/8 の各値 | `common` の対応する値と文字列一致するか |
| 8 | `common.source_run_state` | そのパスが実在するか |

- **照合できないもの**: `current_step` と `gates.passed/failed` の**内容**が実態と合っているか。
  これは機械では判定できない。10節-1 に残す。

### 8.2 正解表の生成

`verify --answer-key <slug>` で、9.2 の閉じた試験に使う正解表を出力する。

- 内容: `current_step` ／ `blocked[]` のうち `state != done` の**全件** ／ `next_choices[]` の**全件**。
- 出力先: `06_repro-v51/reports/current-state-answer-key-YYYYMMDD.md`。
- **武田さんがこの正解表を承認してから**、9.2 の試験を行う（承認C の前段）。

---

## 9. 検証手順

### 9.1 機械判定の実効性（手順10で全部通す）

| # | 試験 | 合格条件 |
| --- | --- | --- |
| 1 | 共通欄ファイルを `touch` して `work_roots` へ書き込む | **拒否される**（⑤a） |
| 2 | `run-state.json` を `touch` して `work_roots` へ書き込む | **拒否される**（⑤b・A-1 の確認） |
| 3 | `sync` 後に同じ書き込みをする | **通る**（誤検知していない） |
| 4 | `work_roots` の外へ書き込む | **通る**（巻き添えが無い） |
| 5 | `run-state.common.json` 自身へ書き込む | **通る**（自己ロックしない） |
| 6 | `run-state.json` 自身へ書き込む | **通る**（同上） |
| 7 | 現在位置ページを読まずに `work_roots` へ書き込む | **拒否される**（⑥） |
| 8 | 同じ `project_slug` のページを2枚目作る | **止まる**（作った2枚目は削除する） |
| 9 | `common.blocked[]` の1件を `state: done` にして `sync` | 節3から消え、節8へ出る |
| 10 | 節5から日付を1つ消して `check` | **FAIL する**（④）。試験後に戻す |
| 11 | **`work_roots` 内のスクリプトを Bash で実行し `logs/` へ書く** | **通る**（A-8。実測された誤検知の型） |
| 12 | **KB 内の別スクリプト（`tools/inbox.py`）を Bash で実行する** | **通る**（同上） |
| 13 | 生成後のページ行数 | 400行以内、または警告が出て生成は成功 |
| 14 | 既存 `run-state.json` **3ファイル**の SHA-256 | **手順1で記録した基準値と完全一致** |
| 15 | 案2ポインタの `現行/旧` を手で書き換えて `check` | **FAIL する**（`common` と不一致を検出） |

- 試験3〜6・11・12 が**誤検知（通るべきものが止まる）の検出**にあたる。
  rev.2 は 11・12 の経路を試していなかった。

### 9.2 本番の閉じた試験（承認C）

1. 手順8の `verify --answer-key` で正解表を生成する。
2. **武田さんが正解表を承認する。**
3. 新しいセッションを開き、**現在位置ページ1枚の実パスだけ**を渡す。
   `work_roots` の実パス・`run-state.json`・1.2 の7枚の実パスは**渡さない**。
4. 「HELEN-REPRO v5.1 を再開して」と指示する。**「引き継ぎ資料を作って」とは言わない。**
5. 述べられた工程・止まっている理由・次の選択肢を、正解表と照合する。
6. **判定するのは武田さん。** LLM の自己申告を根拠にしない（KB CLAUDE.md 1節）。
7. 合格条件: 正解表の「止まっている理由」の全件と「次の選択肢」の全件が、**過不足なく**述べられること。
   工程は文言一致でなく内容一致でよい。
8. 試験セッションが `run-state.json` や引き継ぎ7枚を読もうとしたら**その時点で不合格**とし、
   何を読もうとしたかを記録する（現在位置ページに情報が足りない証拠になる）。

---

## 10. 絶対にやってはいけないこと

1. **武田さんが md を直接書く前提の設計にしない。** 手入力を要求する形は不可（2026-08-30 武田さん明示）。
2. **既存の本文を書き換えない。** 例外は **1.3 の4〜6行だけ**（2026-08-30 武田さん承認・案1）。
   例外分は変更前後の全文を記録ファイルへ残す。それ以外の行に触ったら違反。
3. **既存 `run-state.json` を1バイトも変更しない。** 9.1 試験14 で機械的に確認する。
4. **成果物 blend に触らない。**
5. **文言ルール（LLM の心がけ）で担保して「できた」と報告しない。**
   → ただし節1・節5（LLM 区画）の鮮度は現状どの判定でも測れない。**11節-2 に未解決として明記する。**
6. **新ディレクトリ・新 `type` を勝手に作らない。** frontmatter の欄追加も承認A-1 を経る。
7. **`brainstorm_guard.py` の既知の誤検知を持ち込まない。** 9.1 試験11・12 で確認する。
8. 承認A-0・A-1・B-1・B-2・C・D を飛ばさない。

---

## 11. 未確認・リスク

1. **`current_step` と `gates` の内容が実態と合っているかは機械で判定できない**（8.1 の但し書き）。
   共通欄ファイルの維持は判定⑤b が促すが、**中身の正しさは保証されない**。
2. **節1・節5（LLM 区画）の鮮度を測る判定が無い。** 10節-5 と正面から矛盾する。
   古い記述が残っていても機械は気づかない。次の改訂の課題。
3. **判定⑥は完全ではない。** 現在位置ページを読んだうえで `run-state.json` も読めば、
   古い記述（`state_2026_08_18: 未着手` など）は依然として目に入る。
4. **判定⑤⑥のフックが実際に発火するかは未確認。** `brainstorm_guard.py` は実績があるが、
   別スクリプトを別条件で登録した場合の挙動は試していない。
   実測: `~/.claude/settings.json` の PreToolUse は matcher `Write|Edit|NotebookEdit|Bash` の1本のみ。
5. **helen 以外の3案件は `run-state.json` の粒度が粗い。** 共通欄ファイルをどこまで埋められるかは未確認。
   ふたなり・キャラ抽出には `history` キーが存在しない。**承認D で対象群ごとに判断する。**
6. **400行の上限に根拠は無い。** helen で実測してから調整する。
7. **外付け未マウント時は判定⑤が止める設計**（5.1-5）。マウントを忘れると作業フォルダへ書けない。
   安全側に倒した結果であり、意図した挙動。
8. **現在位置ページが8枚目の引き継ぎ資料として既存7枚と併存する。**
   7枚をいつ役目終了にするかは、この計画では決めていない。案2のポインタで「旧」と示すのみ。
9. **絶対パスがボリューム名 `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/` に固定される。**
   マウント名が変わると全案件が一斉に壊れる。
10. **水着化の作業ルートは KB 内（`output/gf2-helen-swimsuit/`）にあり、現在も稼働中**
    （実測: 2026-08-30 16:30 / 16:31 更新）。`work_roots` に入れると判定⑤⑥が進行中作業を直撃する。
    **承認D の対象に含め、helen の結果を見てから判断する。**
11. **KB CLAUDE.md 1A（高リスク成果物の品質ゲート）に該当するかは未判断。**
    この計画は記録と仕組みだけを扱い、原作再現の成果物には触れないため**該当しない**と考えるが、
    `gf2-helen-starlit-waltz/quality-gate.json` と `tools/project_quality_gate.py` は実在する。
    承認A-1 の場で確認する。
12. **成果物 Inbox への申告が brainstorm 封鎖の誤検知で通らない**（2026-08-30 実測）。
    実装中も同じ症状が出る可能性がある。
13. **サブエージェントの reasoning effort を指定する手段がメインエージェント側に無い。**
    レビューの深さを武田さんの指定どおりに固定できていない。

---

## 12. 捨てた案と理由（蒸し返さないため）

| 捨てた案 | 理由 |
| --- | --- |
| 客観的な豆知識の entity 化 | 「効果ないならいらない」（武田さん 2026-08-30）。再現精度に効かない |
| `run-state.json` を正本にする | 武田さんの判断を JSON に書くことになり、人が読めなくなる（既に226KB） |
| 新しく小さい正本ファイルを作る（wiki 側とは別に） | 正本を名乗るものが一時的に3つになる |
| **対応表案**（生成側に案件ごとの読み取り規則を持たせる） | 案件が増えるたび追記が要り、忘れるとその案件だけ黙って反映されない。**武田さん承認済みの「案2」とは別物** |
| 自動生成を削り、中身を読まずに取れるものだけにする | 「引き継ぎ資料を作って」が不要になるという最大の効果が半分になる |
| 既存ページに一覧を複製する | 更新場所が2つになり、必ず片方が古くなる（1行ポインタのみ） |
| **本文を触らず冒頭に1行だけ足す**（壊れ#4） | 本文が「自分が正本」と言い続けるため、問題が実質残る（2026-08-30 武田さん判断で案1を採用） |
| **古い4枚を別フォルダへ退避**（壊れ#4） | 実パスでの参照が全部切れる。新ディレクトリ新設は構造変更で別途承認が要る |

---

## 13. 承認関所の一覧

| 関所 | 何を承認するか | 種類 |
| --- | --- | --- |
| A-0 | 4.1 の差分3点（別ファイル化・水着化の扱い・8見出し） | 方針 |
| A-1 | 現在位置ページの雛形の形と frontmatter 欄の新設（KB の構造決定）／1A 該当可否 | 方針 |
| B-1 | 共通欄ファイルのスキーマ確定。4案件へ置く直前 | 実行 |
| B-2 | 1.3 の書き換え対象行（変更前後の全文） | 実行 |
| C | 正解表の承認と、閉じた試験の合否判定 | 実行 |
| D | 残り3案件への展開（対象群が違うため別承認） | 実行 |

---

## 14. 関連ファイル（実パス）

- ブレストメモ（正本）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md`
- 1回目のレビュー:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/project-current-state-page-plan-review-20260830.md`
- 2回目のレビュー:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/project-current-state-page-plan-review2-20260830.md`
- 設計の説明:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-handoff-mechanism-design.html`
- 壊れ#4 の説明:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-four-files-claim-master.html`
- helen の全容:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-helen-repro-v51-overview.html`
- helen の生データ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json`
- 既存の判定スクリプト（応用元）:
  `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`
