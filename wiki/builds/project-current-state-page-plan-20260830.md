---
type: build
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-30
sources:
  - project-hub-index
---

# プロジェクト現在位置ページの導入 — 実装計画（2026-08-30）

> [!info] この計画の位置づけ
> `/brainstorm` セッション（2026-08-30）で武田さんの承認を得た設計を、実装可能な粒度まで落としたもの。
> ブレストメモの正本は
> `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md`。
> **この計画はまだ実装承認を得ていない。** レビュー後に武田さんの承認を取る。

---

## 0. 用語

- **KB ルート**: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
- **現在位置ページ**: この計画で新設する、1案件につき1枚の wiki ページ。新しいセッションが最初に読む唯一の入口。
- **`run-state.json`**: 各作業フォルダに既存する記録ファイル。工程・止まっている理由などを持つ。
- **機械区画 / LLM 区画**: 現在位置ページ内で、スクリプトが書く節と LLM が会話から書き起こす節。

---

## 1. 解こうとしている問題（実測・2026-08-30）

セッションが切り替わるたびに、武田さんが「引き継ぎ資料を作って」と LLM へ指示し、
出てきた資料を次のセッションへ添付している。この運用が次の3つを生んでいる。

1. **毎回の手間**が発生する。
2. **その資料がどれだけ保持できているか測れない。**
3. **資料が増える。** HELEN-REPRO v5.1 系統だけで引き継ぎ資料が **7枚・合計4,124行**。
   うち2枚は冒頭に「このファイルは古い」と自己申告している。

さらに実測で、次の構造的な壊れが確認された。

| # | 壊れ | 実測値 |
| --- | --- | --- |
| 1 | 現在位置を主張するものが2系統 | `run-state.json` 最終更新 2026-08-26 20:55 ／ wiki 側の引き継ぎ4枚は 08-27〜08-29 |
| 2 | 閉じた項目が「残り」に残る | `agent_executable_remaining` は3件を名乗るが、実態は完了1・対応済み1・実装不能1＝**実行可能な残りは0件** |
| 3 | 派生案件の所在がバラバラ | 水着化だけ 3D資料フォルダの外（KB 内 `output/gf2-helen-swimsuit/`） |
| 4 | 正確な記録ほど渡しにくい | `run-state.json` は **129,913文字**。履歴113件を持つが、そのままセッションへ渡せない |

#2 は「追記しかできない」構造が原因。イボイボの項目には
`state_2026_08_18_3: 完了（武田さんの明示）` が正しく書かれているのに、
同じ項目の古い `state_2026_08_18: 未着手` が残り、リストからも外れていないため、
2026-08-30 に実際に LLM（Claude）が古い方を読んで誤報告した。

---

## 2. 完成条件

**合格の基準は1つ。「引き継ぎ資料を作って」と指示せずに、新しいセッションが HELEN-REPRO v5.1 を再開できること。**
以下はその内訳。

1. `wiki/builds/` に **1案件1枚の現在位置ページ**が存在する（新ディレクトリ・新 `type` は作らない）。
2. ページが **7節構成**で、機械区画と LLM 区画が分かれている（3節の仕様）。
3. **全対象案件の `run-state.json` に共通の最小欄**がある（4節の仕様）。既存の欄は1つも消えていない。
4. **5つの機械判定**が動く（5節の仕様）。とくに②の自動生成と⑤のずれ検知。
5. helen の壊れ4件（1節の表）が解消されている。

### 完成条件に**含まない**もの

- 成果物 blend の変更。この計画は記録と仕組みだけを扱う。
- helen の工程を先に進めること（G10 の解消、未回収コードの回収など）。
- 水着化・ふたなり化・キャラ抽出の**内容**の整理（現在位置ページを置くところまで）。

---

## 3. 現在位置ページの仕様

### 3.1 置き場と命名

- 置き場: `wiki/builds/`
- ファイル名: `<案件slug>-current-state.md`
  - helen 原作再現 → `gf2-helen-repro-current-state.md`
- **新ディレクトリを作らない・新 `type` を作らない。** `type: build` のまま frontmatter に欄を足す。
  （理由: 新 type / 新ディレクトリは KB の構造決定にあたり別途承認が要る。得られるのは分類の見た目だけ。）

### 3.2 frontmatter

```yaml
---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: YYYY-MM-DD
sources: []

# ここから下がこの計画で足す欄
current_state_page: true          # このページが現在位置ページであることの目印
project_slug: gf2-helen-repro     # 案件の識別子。1案件につき1つ
project_status: active            # active | blocked | paused | done
work_roots:                       # 実作業フォルダの絶対パス（複数可）
  - /絶対パス
state_files:                      # 読み取り元の run-state.json（複数可）
  - /絶対パス/run-state.json
last_synced: "YYYY-MM-DDTHH:MM:SS+09:00"   # 機械が最後に節2/6/7を生成した時刻
synced_from_mtime: "YYYY-MM-DDTHH:MM:SS+09:00"  # そのとき読んだ run-state.json の更新時刻
---
```

- `current_state_page: true` かつ同じ `project_slug` を持つページが **2枚以上あってはならない**（判定①）。
- `last_synced` と `synced_from_mtime` はスクリプトだけが書く。LLM は書き換えない。

### 3.3 本文の7節（見出しは完全一致で固定）

| 節 | 見出し | 書く主体 | 中身 |
| --- | --- | --- | --- |
| 1 | `## いま何をしているか` | LLM | 3行以内。目的と、いま進めていること |
| 2 | `## 現在位置` | **機械** | 工程・合否・成果物のパス/日時/SHA-256 |
| 3 | `## 止まっている理由` | 機械と LLM | 表。1行1件。各行に `state` を持つ |
| 4 | `## 次の選択肢` | LLM | 武田さんが選ぶもの。選ぶと失うものを併記 |
| 5 | `## 武田さんの判断` | LLM | 保留・封印・目的。日付と `有効`/`失効` |
| 6 | `## 関連ファイル（実パス）` | **機械** | 派生案件・引き継ぎ資料・生データ。全部絶対パス |
| 7 | `## 履歴の所在` | **機械** | 詳細記録の場所と件数。**本文は転記しない** |
| — | `## 片付いた記録` | 機械 | 節3から `done` になって移動した行の置き場 |

- 機械は節1/4/5 を**読むだけで書き換えない**。LLM は節2/6/7 を**手で書かない**。
- 節3の表の列: `件名 | state | 何が起きているか | 根拠の実パス`
  - `state` の値: `blocked`（外部要因で進めない） / `unresolved`（原因未特定） /
    `waiting-user`（武田さんの判断待ち） / `ready`（着手可能） / `done`（片付いた）
  - `done` になった行は、次回の生成で節3から消え `## 片付いた記録` へ移る（判定③）。

### 3.4 分量の上限

- 現在位置ページ全体で **400行以内**。超えたら生成を止めてエラーにする。
  （理由: 渡せる大きさに落とすことがこの計画の目的そのもの。上限が無いと `run-state.json` の
  二の舞になる。）
- 節7は「どこに何件あるか」だけを書き、履歴本文を転記しない。

---

## 4. `run-state.json` に足す共通の最小欄（案1・承認済み）

### 4.1 足す欄

```json
{
  "common": {
    "updated_at": "2026-08-30T12:34:56+09:00",
    "current_step": "E（検証）40回目",
    "next_action": "縮小計画のレビュー待ち",
    "blocked": [
      {"item": "...", "state": "blocked", "why": "...", "evidence": "/絶対パス"}
    ]
  }
}
```

- **トップレベルに `common` オブジェクトを1つ足すだけ**にする。既存のキーと衝突しない。
- **既存の欄は1つも消さない・書き換えない。** とくに helen の226KB。
- `blocked[].state` は 3.3 の `state` と同じ語彙を使う。

### 4.2 対象4案件

| 案件 | `run-state.json` の場所 | いまの状態 |
| --- | --- | --- |
| helen 原作再現 | `01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json` | 226KB・`last_updated` |
| ふたなり化 | `01_イラスト/07_3D資料/gf2-helen-starlit-waltz/07_futa-helen/run-state.json` | 3.3KB・`updated_at` |
| キャラ抽出 | `01_イラスト/07_3D資料/gf2-char-extract/run-state.json` | 49KB・`updated_at` |
| 水着化 | `LLM Knowledge Base _01/output/gf2-helen-swimsuit/`（**無し**） | 新規作成する |

- 既存3ファイルは**バックアップを取ってから**触る（`<名前>.bak-YYYYMMDD-HHMMSS`）。
- 書き込み後、`json.load` が通ること・既存キーの数と値が変わっていないことを機械で確認する。

---

## 5. 5つの機械判定

実装先: `<KBルート>/tools/project_state_sync.py`（新規）。
判定①③⑤は `brainstorm_guard.py` の既存判定の応用。**ゼロから作るものではない。**

| # | 判定 | 発火点 | 落ちたときの動作 |
| --- | --- | --- | --- |
| ① | 同じ `project_slug` の現在位置ページが2枚以上ないか | `check` 実行時／ページ書き込み前 | エラーで止める。両方のパスを表示 |
| ② | 節2/6/7 を `run-state.json` から生成 | `sync` 実行時 | 生成できなければ止める（部分生成しない） |
| ③ | 節3で `state: done` の行を `## 片付いた記録` へ移す | `sync` 実行時 | — |
| ④ | 節5の各行に日付と `有効`/`失効` があるか | `check` 実行時 | 欠けている行を挙げて FAIL |
| ⑤ | `synced_from_mtime` と `run-state.json` の実 mtime のずれ | 作業フォルダへの書き込み前（フック） | ずれていたら書き込みを拒否し、`sync` を促す |

### 5.1 ⑤の詳細（いちばん重要）

- 対象: `work_roots` 配下への書き込み（Write / Edit / Bash の書き込み系）。
- 判定: `run-state.json` の mtime > `synced_from_mtime` なら **ずれている**。
- 動作: 書き込みを拒否し、`python3 tools/project_state_sync.py sync <project_slug>` を促すメッセージを返す。
- **既知のリスク**: `brainstorm_guard.py` のパス抽出は 2026-08-30 時点で誤検知が確認されている
  （`tail -1 index.md` をパスと誤認、`python3 tools/inbox.py` を書き込み対象と誤認）。
  同じ抽出方式を流用する場合、**この誤検知を持ち込まないこと**。

### 5.2 コマンド

```
python3 tools/project_state_sync.py sync  <project_slug>   # 節2/6/7 を生成し last_synced を更新
python3 tools/project_state_sync.py check <project_slug>   # 判定①③④を実行（書き込みなし）
python3 tools/project_state_sync.py list                   # 現在位置ページの一覧と鮮度
```

---

## 6. 実装手順

| 手順 | 内容 | 承認関所 |
| --- | --- | --- |
| 1 | helen の現在位置ページを**手で1枚**作る（雛形の確定） | **承認A**: 形を見せて続行の可否 |
| 2 | 足す共通欄の一覧を確定し、武田さんへ提示 | **承認B**: 既存ファイルを触る直前 |
| 3 | 4案件の `run-state.json` に `common` を足す（バックアップ必須） | — |
| 4 | `tools/project_state_sync.py` を実装（`sync` / `check` / `list`） | — |
| 5 | helen で `sync` を実行し、節2/6/7 が生成されることを確認 | — |
| 6 | **新しいセッションで、引き継ぎ資料を作らずに再開できるかを実際に試す** | **承認C**: 結果を見せて合否判定 |
| 7 | ⑤のフックを `settings.json` へ登録し、ずれを起こして拒否されることを確認 | — |
| 8 | 残り3案件の現在位置ページを作る | — |

- 承認A・B・C では**停止する**。無回答・沈黙・カードを閉じただけは承認ではない。

---

## 7. 検証手順（合格の測り方）

1. **判定⑤の実効性**: `run-state.json` を1バイト書き換えて mtime を進め、`work_roots` 配下への
   書き込みが**実際に拒否される**ことを確認する。拒否されなければ FAIL。
2. **判定①の実効性**: 同じ `project_slug` のページを2枚目を作り、**止まる**ことを確認して削除する。
3. **判定③の実効性**: 節3の1行を `state: done` にして `sync` し、**節3から消えて `## 片付いた記録` に
   移る**ことを確認する。
4. **分量**: 生成後のページが 400行以内であること。
5. **非破壊**: 4案件の `run-state.json` について、`common` 以外のキーと値が**変更前と完全一致**すること
   （バックアップとの差分で確認）。
6. **本番の合格判定**: 新しいセッションを開き、現在位置ページ1枚だけを渡して
   「HELEN-REPRO v5.1 を再開して」と言う。**引き継ぎ資料の作成を求めずに、工程・止まっている理由・
   次の選択肢を正しく述べられれば合格。**

---

## 8. 絶対にやってはいけないこと

1. **武田さんが md を直接書く前提の設計にしない。** 手入力を要求する形は不可（2026-08-30 武田さん明示）。
2. **`run-state.json` の既存の欄を消さない・書き換えない。** 足すだけ。
3. **成果物 blend に触らない。** この計画は記録と仕組みだけ。
4. **文言ルール（LLM の心がけ）で担保して「できた」と報告しない。** 機械が判定すること。
5. **新ディレクトリ・新 `type` を勝手に作らない。**
6. **helen の引き継ぎ資料7枚の本文を要約・分割しない。** 武田さん本人の言葉と実測値が入っている。
7. **`brainstorm_guard.py` の既知の誤検知を持ち込まない。**
8. 承認A・B・C を飛ばさない。

---

## 9. 捨てた案と理由（蒸し返さないため）

| 捨てた案 | 理由 |
| --- | --- |
| 客観的な豆知識の entity 化（Helen とは何者か等） | 「効果ないならいらない」（武田さん 2026-08-30）。再現精度に効かない |
| `run-state.json` を正本にする | 武田さんの判断を JSON に書くことになり、人が読めなくなる（既に226KB） |
| 新しく小さい正本ファイルを作る | 正本を名乗るものが一時的に3つになる |
| 生成側に案件ごとの読み取り対応表を持たせる（案2） | 案件が増えるたび追記が要り、忘れるとその案件だけ黙って反映されない＝今回と同じ失敗の再生産 |
| 自動生成を削り、中身を読まずに取れるものだけにする（案3） | 「引き継ぎ資料を作って」が不要になるという最大の効果が半分になる |

---

## 10. 未確認・リスク

1. **`run-state.json` の3ファイルは構造が大きく違う。** `common` を足すこと自体は安全だが、
   **`common` の中身を誰が維持するか**は未設計。作業する LLM が毎回更新しないと⑤が形骸化する。
2. **判定⑤のフックが実際に発火するかは未確認。** `brainstorm_guard.py` は動作実績があるが、
   別スクリプトを別条件で登録した場合の挙動は試していない。
3. **helen 以外の3案件では、`run-state.json` の粒度が helen ほど細かくない。**
   節2/6/7 の自動生成がどこまで意味のある内容になるかは未確認。
4. **400行の上限は根拠のある数字ではない。** helen で実際に生成してから調整が要る可能性がある。
5. **成果物 Inbox への申告が brainstorm 封鎖の誤検知で通らない**（2026-08-30 実測）。
   この計画の実装中も同じ症状が出る可能性がある。

---

## 11. 関連ファイル（実パス）

- ブレストメモ（正本）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/project-hub-index/_index.md`
- 設計の説明:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-handoff-mechanism-design.html`
- helen の全容:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260830-helen-repro-v51-overview.html`
- helen の生データ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json`
- 既存の判定スクリプト（応用元）:
  `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`
