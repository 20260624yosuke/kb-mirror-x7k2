---
type: build
status: active
confidence: medium
evidence_level: source-backed+user-stated
last_reviewed: 2026-09-08
---

# セッションログを一次情報にする — 実装計画（2026-09-08）

関連: [[kb-experience-reproducibility]] ／ 説明版 HTML
`wiki/_attachments/kb-experience-reproducibility/20260908-fact-check-wiring-plan.html`

## 1. 目的（武田さんの明言）

会話の開始時と圧縮のたびに案件一覧を約 8,000 字ぶん先回りで読み込ませている仕組みを、
**推論の事実確認を一次情報へ照合する導線**へ置き換える。保管庫の整合性は崩さない。

一文の抽象化（2026-09-08 に武田さんが「その通り」と承認）:

> 推測で進めないために、一次情報へ機械的に辿り着ける導線を用意する。
> 文脈を先回りで注ぎ込む方式をやめ、必要なときに照合する方式へ変える。

### 5つの要素

| 要素 | 中身 |
|---|---|
| 守りたいもの | 文脈が圧縮されても、情報の粒度とプロジェクトの優先順位が崩れないこと |
| やめること | 会話の開始時と圧縮のたびに、案件の一覧を先回りで読み込ませること |
| 代わりに置くこと | 推論が事実と食い違っていないかを、そのつど一次情報で確かめる導線 |
| 一次情報 | ログとファイルの中身。要約や記憶ではない |
| つなぎ方 | 機械監査。心がけや推測に依存しない |

### 5つの制約

1. この保管庫の整合性を崩さない
2. 特定のサービス専用にしない（他のモデルで使えない仕組みは修正ループを生む）
3. 監査を勝手に増やさない（問題を感じたときに指示が出てから作る）
4. 優先順位は本人が明言する。機械に推測させない。ただし、いつでも可視化できる状態にしておく
5. 一度で完璧を作らない。拡張して育てられる形にする

## 2. 確定済みの決定（この会話で武田さんが選択）

| 項目 | 決定 |
|---|---|
| 記録の担い手 | 手で書く親メモをやめ、スクリプトがログを写す（トークン消費ゼロ） |
| 写す範囲 | **丸写し**。ツール結果を含む全行。容量より粒度を優先 |
| 置き場所 | 保管庫の中。ただし Obsidian の索引に載せない |
| 遡り | いま本体に残っている分（Claude 約 517MB）も写す |
| 座標 | **案B**。会話の頭で LLM が座標を名乗る。無ければその場で 1 行の計画ファイルを作る |
| 優先順位 | 武田さんの明言でのみ更新。LLM は書き写すだけ |
| 監査 | この計画の 5 本以外は作らない |
| Inbox | 仕組みごと廃止（機械監査が上位互換） |

## 3. 実測にもとづく前提（2026-09-08 に実ファイルで確認）

| 項目 | 値 | 確認方法 |
|---|---|---|
| Claude のログ | `~/.claude/projects/<slug>/<session_id>.jsonl` 126 本・517MB | `ls` / 全行パース |
| Claude ログの最古 | 2026-08-03（36 日前）。それ以前は消えている | `ls -lt` |
| Codex のログ | `~/.codex/sessions/YYYY/MM/DD/rollout-<ts>-<id>.jsonl` 399 本・3.0GB。最古 2026-05-17 | `find` / `du` |
| opencode | `~/.local/share/opencode/opencode.db`（SQLite） | `ls` |
| 本体の空き | 19GB / 使用率 91% | `df -h` |
| 保管庫（外付け） | 931GB・空き 312GB | `df -h` |
| ログの内訳 | ツール結果 77.2%／道具呼び出し 18.6%／メタ 3.5%／応答本文 0.4%／武田さんの発言 0.2% | 全 126 本を分類集計 |
| Inbox 未処理 | 298 件（最古 14 日経過） | `tools/inbox.py list` |

形式の一致（移植性の根拠）:

- Claude・Codex ともに **1 行 1 件の JSONL**。
- Claude: 各行に `cwd` を持つ。圧縮境界は `type=system, subtype=compact_boundary` で、
  `logicalParentUuid` により圧縮前の発話へ辿れる。
- Codex: 先頭行 `type=session_meta` の `payload` に `cwd` `id` `originator` を持つ。
- opencode のみ SQLite。読み取り部品を別に作る必要がある。

フックのペイロード（実装上の入力）:

- Claude Code: Stop / SessionEnd で `transcript_path` `cwd` `session_id` が渡る（`brainstorm_guard.py` が現に使用）。
- Codex: `~/.codex/hooks.json` に同形式で登録でき、`session_id` `cwd` が渡る。**`transcript_path` は渡らない**ため、
  `session_id` から rollout ファイルを解決する。

## 4. 成果物（作るもの 5 本）

すべて `tools/` に置く。名前は仮。

### 4.1 `tools/session_log_mirror.py`（写し取り）

- **入力**: フックの JSON（stdin）。`transcript_path` があればそれを使い、無ければ `session_id` から解決。
- **動作**: 元ログの総行数と、前回写した行数を比較し、**増えた行だけ**を写しへ追記する。
- **出力先**: `_logs/<project>/<session_id>.jsonl`（下記 5 章）。
- **状態**: `_logs/.state/<harness>-<session_id>.json` に `{lines, sha256, mtime, src}` を保存。
- **トークン**: 標準出力に何も書かない。ブロックもしない。**会話に一切載らない**。
- **失敗時**: 例外は握りつぶして終了コード 0（fail-open）。会話を止めない。
- **外付け未接続時**: 何もせず終了。次回接続時に差分としてまとめて追いつく。

### 4.2 `tools/log_readers/`（読み取り部品）

サービス差を吸収する薄い層。共通インターフェースは 2 関数だけ。

```
resolve(payload) -> Path | None     # このセッションの元ログの場所
iter_lines(path, from_line) -> Iterator[str]
```

- `claude.py` … `transcript_path` をそのまま返す
- `codex.py` … `~/.codex/sessions/**/rollout-*-<session_id>.jsonl` を解決
- `opencode.py` … SQLite から会話行を取り出し、共通の JSONL 形へ整形（段階4以降）

**サービス依存はこのフォルダの中だけ。他の 4 本は harness を知らない。**

### 4.3 `tools/session_log_verify.py`（整合の監査）

- 元ログの行数と写しの行数を比較。
- 写した範囲（先頭 N 行）の sha256 を元ログの同範囲と比較。
- **元ログを常に正**とする。写しの手編集を禁止し、食い違えば写しを作り直す。
- 実行は手動 1 コマンド、および段階3以降で Stop フックに 1 行（不一致時のみ出力）。

### 4.4 `_logs/index.jsonl`（対応表）と `tools/session_index.py`

1 セッション 1 行の JSONL。

```json
{"harness":"claude","session_id":"...","project":"kb-experience-reproducibility",
 "coordinate":"wiki/analyses/brainstorm/kb-experience-reproducibility/_index.md",
 "log":"_logs/kb-experience-reproducibility/<id>.jsonl",
 "started":"2026-09-08T22:15:18Z","updated":"...","priority":null,"title":""}
```

- 会話終了時に写し取りが 1 行を追記／更新する。
- `project` と `coordinate` は、**会話の頭で LLM が名乗った座標**から決まる（4.6）。
- `priority` は武田さんの明言でのみ書き換える。LLM は写すだけ。

### 4.5 `tools/session_log_query.py`（照合の入口）

- `--project <名前>` でその案件のログを、`--grep <語>` で発言・応答を検索。
- 既定は **武田さんの発言と私の応答本文だけ**を対象にし、`--all` でツール結果まで広げる
  （丸写しは保持したうえで、検索の既定を軽くする）。
- 出力は日時・harness・該当行。**これが「事実確認」の入口**。

### 4.6 座標の名乗り（規約側の 1 行）

`CLAUDE.md` / `AGENTS.md` / `KIMI.md` に次を追加する。

> 会話の最初の応答で、その回の座標（案件の正本ファイルの相対パス）を 1 行で名乗る。
> 該当が無ければ `wiki/builds/<案件>-<日付>.md` を 1 行で新規作成し、それを座標にする。

座標が無い会話は `project: "_uncoordinated"` として写す（記録は必ず残す）。

## 5. 置き場所と Obsidian

- 写しの置き場所: `<KB>/_logs/`
- Obsidian の索引から外す方法は **2 案**。実機で確認して選ぶ。
  1. Obsidian 設定の「除外フォルダ」に `_logs` を追加（設定 UI・武田さんの操作が要る）
  2. フォルダ名を `.logs` にする（先頭ドットは Obsidian が読まない想定・**未確認**）
- **どちらも未検証。段階1の完成条件に「実機で索引に出ないこと」を含める。**

## 6. 撤去するもの

| 対象 | 実体 | やり方 |
|---|---|---|
| 先回りの注入 | `~/.claude/settings.json` の SessionStart `inject-full` と UserPromptSubmit `inject-light`、`~/.codex/hooks.json` の SessionStart `codex_adapter.py session-start` | フック登録の行を外す。スクリプトは残す |
| 成果物 Inbox | `tools/inbox.py`、`CLAUDE.md` / `AGENTS.md` / `KIMI.md` の「成果物 Inbox」節、`inbox-dashboard.md`、Raycast の 2 項目 | 規約の節を「廃止（2026-09-08）」に書き換える。スクリプトとボードは残置し、呼ばれなくする |

**どちらもファイルを削除しない。** 戻すときは登録行と節を戻すだけ。

## 7. 段階と完成条件

各段階は単体で役に立つ状態で終える。**注入を外すのは段階3。順序を逆にしない。**

### 段階1 — 写しと整合（土台）

- 作る: 4.1 / 4.2（claude・codex）/ 4.3
- 配線: Claude `settings.json` の Stop に 1 行、Codex `hooks.json` の Stop に 1 行
- 遡り: 既存 126 本を一括で写す
- **完成条件**（すべて実測で確認）
  1. 会話を 1 回終えると、写しの行数が増える
  2. `session_log_verify.py` が全セッションで一致を返す
  3. その間、私は 1 文字も記録を書いていない（応答に記録の記述が無い）
  4. Obsidian の検索に `_logs` の中身が出ない

### 段階2 — 対応表と座標

- 作る: 4.4、規約への 4.6 の 1 行
- **完成条件**: 案件名を渡すと、その案件のログのパスが機械で引ける。座標なしの会話も `_uncoordinated` で残る

### 段階3 — 照合と、注入の撤去

- 作る: 4.5
- 撤去: 6 章の「先回りの注入」
- **完成条件**: 注入が止まっており（会話の頭に案件一覧が出ない）、かつ案件の事実を `session_log_query.py` で引ける

### 段階4 — Inbox 撤去と優先順位の可視化

- 撤去: 6 章の「成果物 Inbox」
- 作る: 対応表の `priority` を一覧にする出力（新規スクリプトにせず 4.4 のサブコマンド）
- opencode の読み取り部品を追加
- **完成条件**: 規約から Inbox が消え、「いまの順位」を 1 コマンドで出せる

## 8. 今回やらないこと

- 新しい監査をこの 5 本以外に作らない
- 既存の常時監査 7 本には触らない（注入だけを外す）
- 過去の親メモを書き換えない（記録として残す）
- 本体ログの自動削除設定は段階1では変更しない（写しの動作を確認してから判断）
- ログの要約・分類・タグ付けはしない（丸写しのみ）

## 9. 危ないところ

| リスク | 対処 |
|---|---|
| 外付け未接続で写せない | fail-open。次回接続時に差分で追いつく（元ログは本体にあるため取りこぼしなし） |
| 本体ログが 30 日で消え、写す前に失われる | 段階1で遡り一括を先に実施する |
| Claude が本体を圧迫（19GB 空き） | 段階4以降で退避を検討。段階1では触らない |
| opencode だけ形式が違う | 段階1〜3は Claude / Codex で通し、opencode は読み取り部品 1 本の追加で入れる |
| 注入を外した直後の案件取り違え | 段階2（座標の名乗り）が効いていることを確認してから段階3へ進む |
| 写しと元の二重管理 | 元を常に正とし、写しの手編集を禁止。`session_log_verify.py` で機械的に検出 |
| Obsidian 索引除外が効かない | 段階1の完成条件に含め、2 案のどちらかで必ず満たす |

## 10. 未確認

- Claude のログ自動削除を止める設定の正確な名前と単位
- Obsidian の索引除外の効き方（上記 2 案とも実機未確認）
- opencode の SQLite が会話の中身をどこまで保持しているか
- Codex のフックで `session_id` から rollout ファイルを解決できること（形式は確認済み、解決処理は未実装）
