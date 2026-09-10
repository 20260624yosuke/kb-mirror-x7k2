---
type: build
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-09-10
sources: []
---

# brainstorm 封鎖ブリーフ（2026-09-10）

`/brainstorm` スキルとその監査の仕組みを「もう使わない」方針で畳むための、現状と段取りの
まとめ。**このブリーフは実装ではなく、次に何をするかを決めるための資料。**

> 出所の注記: 以下で名前を挙げる `brainstorm-skill.md`・各案件の `_index.md`（親メモ）・
> `.opencode/commands/brainstorm.md` などは **muse（opencode、2026-09-03〜09-08）が作成した
> ファイル**。このブリーフではそれらの結論を根拠に使っておらず、パス・`status`・登録状況を
> 私がこのセッションで直接開いて確認した在庫として挙げているだけ（muse の判断は未裏取りのまま）。

## 1. 現状（実ファイルを開いて確認した事実）

### 何が起きているか

- 2026-09-08 に武田さんが「brainstorm というスキル自体を使わない方針」と明言。
- 2026-09-09 の別作業（セッションログ写し取りの導入）で、brainstorm の「先回り注入 5 行」を
  設定ファイルから外した。この副作用で **Codex 側の brainstorm は丸ごと停止**している
  （承認カードの歯止め・親メモ必須化・カード記録も含めて素通り）。
- **Claude 側の brainstorm は半分だけ生きている。** スキル本体（`/brainstorm` で明示起動する分）は
  無傷。それとは別に、`~/.claude/settings.json` に登録された「常駐フック 3 本」が
  **毎セッション無条件で走っている**。

### 毎セッション走っている brainstorm 由来の常駐フック（Claude）

| 登録場所 | フック | いま何をしているか |
|---|---|---|
| `settings.json` PreToolUse（Write/Edit/NotebookEdit/Bash） | `brainstorm_guard.py guard-write --unread`（timeout 180 秒） | `brainstorm_status: ready` の生きたメモに紐づく実装以外はほぼ素通りする作り（`guard-stop-content` の検査2と同じ判定を使う）。それでも毎回起動する |
| `settings.json` Stop | `brainstorm_guard.py guard-stop-content` | 検査5（承認カードを出す回に本文が 120 字未満なら止める）は **brainstorm 以外でも発火**。検査2（`ready` メモ未実装で閉じる）は brainstorm メモがある時だけ |
| `settings.json` Stop | `brainstorm_guard.py guard-stop-handoff` | 引き継ぎ資料（`wiki/builds/*handoff*.md`／brainstorm 親メモ）を書き換えた回に、そのパスを本文へ出したかを見る（H1）。**brainstorm 以外でも発火** |

### 自己試験の FAIL（前回エージェントが報告したもの）

- コマンド: `python3 ~/.claude/skills/brainstorm/brainstorm_guard.py audit-handoff --selftest`
- 結果: 第1〜6層のうち **1 件だけ FAIL**。内容は
  「`settings.json` に `AskUserQuestion` のフックがある（brainstorm 以外でも発火する。武田さんの
  指示で禁止）」。
- **正体**: いま `settings.json` の `AskUserQuestion` に登録されているのは brainstorm ではなく
  `tools/deliverable_path_guard.py guard-card`（成果物パスをカードに出させる、グローバル
  CLAUDE.md 由来の別の仕組み）。自己試験のコードは「`AskUserQuestion` という文字列が
  `settings.json` にあれば FAIL」という粗い判定しかしておらず、**別仕組みのフックを
  brainstorm の違反として誤検知している**。
- この FAIL は前回変更より前から同じ内容で出ており、コードの変更では直っていない
  （バックアップ照合済み、と前回エージェントが報告）。

## 2. 目的（この作業で達成したいこと）

- brainstorm を使わない方針を、**設定と規約の実体に反映して確定させる**。
  「使わないのに毎セッション走っている」「自己試験が永久に FAIL する」という
  中途半端な状態を解消する。
- 同時に動いている別の仕組み（セッションログ写し取り、成果物パスガード、引き継ぎ H1）を
  **巻き添えで壊さない**。
- スキル本体のファイルは削除しない（方針は「削除せず使わない」）。戻せる形で止める。

## 3. 構成（brainstorm を成り立たせているものの一覧）

| 区分 | 実体 | 封鎖時の扱い（案） |
|---|---|---|
| スキル本体 | `~/.claude/skills/brainstorm/SKILL.md`（frontmatter に PreToolUse 封鎖・Stop 歯止め・AskUserQuestion カード） | 残置。`/brainstorm` を打たない限り発火しない |
| 監査スクリプト | `~/.claude/skills/brainstorm/brainstorm_guard.py`（約 3,600 行、9 サブコマンド） | 残置 |
| 共通判定本体 | `tools/brainstorm_core.py`（1,342 行）。2026-09-08 の解体資料は「呼び出し元 0 件」と記録（このブリーフでは未再確認） | 残置（未接続のまま） |
| 常駐フック（Claude） | `~/.claude/settings.json` の PreToolUse 1 本＋Stop 2 本（上の表） | **登録行を外すかどうかがこのブリーフの主論点** |
| 常駐フック（Codex） | `~/.codex/hooks.json` の `codex_adapter.py` 各行（既に素通り状態） | 登録行を外す（機能は既に死んでいる） |
| Codex スキル | `~/.codex/skills/brainstorm/`（SKILL.md・scripts・agents・tests） | 残置 |
| opencode | `.opencode/commands/brainstorm.md`・`.opencode/scripts/muse_brainstorm_check.py`・`.opencode/instructions/brainstorm-body.md` | 残置（明示コマンドのみ） |
| メモ本体 | `wiki/analyses/brainstorm/`（95 ファイル・2.5MB、親メモ 21 枚） | 残置。過去の思考の記録なので消さない |
| 規約 | `CLAUDE.md` / `AGENTS.md` の「brainstorm」節、「セッション座標の名乗り」節が `wiki/analyses/brainstorm/<案件>/_index.md`（muse 作成の親メモ）を指す | 「休止」表記へ書き換え |
| 正本ページ | `wiki/builds/brainstorm-skill.md`（muse 作成・`status: active`）ほか `wiki/builds/brainstorm-*.md` 多数 | `status: superseded` 化＋休止の追記 |
| 自己試験 | `brainstorm_guard.py` 内 `cmd_audit_selftest`（第3層の `AskUserQuestion` 検査） | 誤検知を直すか、自己試験ごと止めるか（4章 未解決2） |

## 4. 重要な依存関係（壊してはいけないもの）

1. **セッションログ写し取り**（`tools/session_log_mirror.py`）は brainstorm とは別登録。
   `settings.json` の Stop / SubagentStop、`hooks.json` の Stop / SessionEnd にある。
   写し取りは brainstorm とは別々の登録行（Stop・SubagentStop・SessionEnd）にある。
   外す対象は brainstorm の登録行に限定すること。**混同して消さないこと。**
2. **成果物パスガード**（`tools/deliverable_path_guard.py`）は `settings.json` の
   Stop（`guard-stop`）と PreToolUse `AskUserQuestion`（`guard-card`）に登録。
   これは brainstorm ではない。自己試験の FAIL の原因はこれ。**残す。**
3. **引き継ぎ到達性 H1**（`guard-stop-handoff`）は brainstorm_guard.py の中にあるが、
   グローバル CLAUDE.md「判断を求めるときは対象のパスを本文に書く」の H1 検査そのもの。
   brainstorm 専用ではない。**外すなら H1 を別スクリプトへ移すか、この 1 サブコマンドだけ
   残す判断が要る。**
4. **検査5（カード＋本文 120 字）** も `guard-stop-content` の中にあり、brainstorm 以外の
   AskUserQuestion 全般に効いている。外すと「カードだけ出して本文を書かずに閉じる」を
   止める仕組みが消える。
5. `tools/` の「手続きを見る検査」は brainstorm 導入後 11 日で 2 本 → 21 本に増えた。
   正本はこの増殖の原因を brainstorm と特定している。封鎖はこの増殖を止める意味もある。

## 5. 未解決事項（武田さんの判断が要る）

### 未解決1 — 常駐フック 3 本をどうするか

- **案A（推奨・最小）**: 3 本とも `settings.json` から登録行を外す。スクリプトは残す。
  - 失うもの: 検査5（カード＋本文 120 字）と H1（引き継ぎパスの本文出し）の**自動発火**。
    どちらも規約本文には残るので「規則としては生きているが機械では止まらない」状態になる。
  - 戻し方: `~/.claude/settings.json.bak-20260909-015518` から 3 行を戻す。
- **案B（分離）**: brainstorm 依存の `guard-write --unread` と `guard-stop-content` の検査2
  だけ止め、検査5・H1 は別の小さいスクリプトへ移して残す。
  - 失うもの: 作業 1〜2 セッション。移植先スクリプトの新規テストが要る。
  - 得るもの: brainstorm と無関係な 2 つの歯止めが機械で生き続ける。
- **案C（現状維持）**: 何もしない。
  - 失うもの: 毎セッション無処理フックが走り続ける（実害は小さいが `--unread` の 180 秒
    timeout 枠を毎回消費）。自己試験は FAIL のまま。

### 未解決2 — 自己試験の FAIL をどうするか

- **案i**: 自己試験の第3層の `AskUserQuestion` 検査を「`brainstorm_guard.py` を指す
  `AskUserQuestion` フックがあれば FAIL」へ絞る（`deliverable_path_guard.py` は許す）。
  1 行の条件変更。FAIL が消える。
- **案ii**: brainstorm を畳むので自己試験ごと呼ばない。`brainstorm_guard.py` の
  `audit-handoff --selftest` を今後実行しないと決める。
- **案iii**: 現状維持（FAIL を無視すると決める）。

### 未解決3 — 規約と正本の書き換え範囲

- `CLAUDE.md` / `AGENTS.md` の「brainstorm」節を「休止」に書き換えるか、丸ごと削除するか。
  - hold・plan-gate と同じ「休止（ファイルは残置）」表記に揃えるのが一貫する。
- 「セッション座標の名乗り」節が `wiki/analyses/brainstorm/<案件>/_index.md`（muse 作成）を座標の
  既定例にしている。brainstorm を畳むならこの例を `wiki/builds/<案件>-<日付>.md` へ
  差し替える必要がある（座標の仕組み自体はログ写し取り側で生きているので残す）。
- `wiki/builds/brainstorm-skill.md`（muse 作成）ほか `brainstorm-*` の正本を `superseded` 化。

### 未解決4 — Codex / opencode

- Codex は機能的に既に停止。`~/.codex/hooks.json` の `codex_adapter.py` 行を外すのは
  掃除だけの意味（戻しは `~/.codex/hooks.json.bak-20260909-021150`）。
- opencode の brainstorm は明示コマンド（`.opencode/commands/brainstorm.md`、muse 作成）経由で動く作り。
  今回は触らない選択でよい。

## 6. 次に実行すべき作業（順序）

**まず未解決1〜4 を決める。** 決まったら以下の順で 1 セッションで完了できる見込み。

1. `~/.claude/settings.json` をバックアップし、決めた案に沿って brainstorm 由来の
   登録行を編集（案A なら 3 行削除）。
2. `~/.codex/hooks.json` をバックアップし、`codex_adapter.py` の行を削除。
3. 自己試験を決めた案（i / ii / iii）に沿って処理。案i なら
   `brainstorm_guard.py` の第3層 1 条件を修正し、`audit-handoff --selftest` が
   PASS することを確認。
4. `CLAUDE.md` / `AGENTS.md` の「brainstorm」節を「休止」表記へ。座標の例を差し替え。
   （両ファイルは生成ブロック管理の可能性があるので、生成元があればそちらを直す）
5. `wiki/builds/brainstorm-skill.md`（muse 作成）ほか `brainstorm-*` 正本の frontmatter を
   `status: superseded` にし、冒頭へ「2026-09-10 休止」の 1 行。
6. `log.md` に `## [2026-09-10] build | brainstorm 封鎖` を 1 行、触ったファイルを列挙。
7. メモリ `project_brainstorm_skill_replaces_hold_2026-08-28` を「休止・封鎖済み」へ更新。
8. 検証: 新しい通常セッションを 1 本開き、`guard.log` に brainstorm 系の行が
   出ないこと、`session_log_mirror` の写し取りは続いていることを確認。

## 7. 現時点で判明していない点

- `CLAUDE.md` / `AGENTS.md` の「brainstorm」節に生成ブロック（自動生成の元ファイル）が
  あるかは未確認。`plan-gate` 節には `<!-- END GENERATED: plan-gate -->` があるので、
  brainstorm 節も生成物の可能性がある。編集前に確認が要る。
- `guard-stop-content` の検査5 を止めた場合に、他のグローバル検査
  （`deliverable_path_guard.py guard-stop` の D2）がどこまで肩代わりできるかは要確認。
