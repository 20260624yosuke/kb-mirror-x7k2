---
type: build
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-30
---

# Claude版を正本とするCodex brainstorm再実装計画

## 現在の承認状態

- **現行計画**: 本ページの再実装計画。
- **現行判断**: 2026-08-30、二段階の独立レビューを反映した本計画について、武田さんが
  `再改訂計画を承認` と確認質問 `はい、この選択でよい` を選択し、その後に実装を依頼した。
- 実装は、この計画を確定した brainstorm 会話では行わず、`$brainstorm` を付けない新しい通常モード
  会話へ渡す。

### 失効した旧承認とレビュー履歴

- 本ページ旧版にあった「2026-08-30 に承認済み」は、その後の武田さんの明示発言
  `承認しない` により撤回された。**旧版の承認を現行判断へ使わない。**
- 第1版は独立レビュー前の案。`gpt-5.6-terra`・reasoning effort `medium` のレビュー判定は No。
  実機成立、本文代替、フックイベント、状態束縛、trust の不足を指摘した。
- Claude版を正本とした改訂案は、1回目の `gpt-5.6-sol`・reasoning effort `medium` のレビュー判定が
  No。未承認状態、フック不発、並行メモ、二問カード遷移、probe、Claude差分の不足を指摘した。
- 全件反映後の再改訂案は、2回目の `gpt-5.6-sol`・reasoning effort `medium` のレビュー判定が No。
  親メモの旧ready、過剰な4 ID必須、任意選択肢、カード固定文、ターン証拠、fail-open、本番発火の
  不足を指摘した。本ページはその必須修正をすべて反映した版。

## 目的と正解

Codex版を、現在のClaude版brainstormと同じ使用感へ合わせる。通常モードで毎ターンwikiへ記録し、
同じ応答内で本物の二問承認カードを表示する。

- 使用感の正本: `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`
- Codexの実装は独立させ、ClaudeのPythonをそのまま共有しない。
- 正解はCodex Desktopの新規通常モード会話における実表示、回答、継続、フックログ、対象メモ更新。
- 本文による疑似カードは禁止する。通常モードのカードまたは回答監査が成立しなければ、Plan modeへ
  代替せず `technical_stopped` とする。

## 実装手順

### 1. 変更前記録

- Codex版、機能フラグ、`config.toml`、`hooks.json`、trust、ログ位置と各SHAを保存する。
- バックアップ、期待差分、probe状態はDesktop再読込後も辿れるCodex brainstorm配下の固定ディレクトリへ
  保存する。
- 復元は追加したフラグ、probe、trustだけを対象にする。無関係な同時変更があれば上書きせず停止する。

### 2. 通常モード実現性probe

- `default_mode_request_user_input` を有効化する。
- 一時probeはPreToolUse/PostToolUseを通過させるだけとし、専用トークンを最初のUserPromptSubmitで
  検出した実セッションだけを観測対象にする。通常のbrainstormは起動しない。
- 専用テストメモと既知のテスト文字列を使い、実際のbrainstorm親メモを汚さない。
- probeログには本文・回答・生IDを保存せず、フィールド名、型、IDハッシュ、値ハッシュだけを残す。
- probeを信頼済みにし、Desktop再読込後の新規通常モードタスクで次を順に確認する。
  1. `request_user_input` がツールとして公開される。
  2. 本ページで定義する二問カードが表示される。
  3. 回答を機械的に観測できる。
  4. 回答後に同じターンの会話が継続する。
  5. セッションと現在のカード呼出しを古いカードから一意に区別できる実在IDが1つ以上ある。
  6. 利用可能なライフサイクルイベント、回答返却、継続、Stopの順序を対応付けられる。
- request・item・turn・questionの4種類すべては要求しない。実測で得た最小のカード呼出しIDと、入力側の
  固定 `question.id`、質問ハッシュ、選択肢ハッシュを使う。
- カード呼出しIDまたは回答を機械観測できなければ、後続実装へ進まず技術的停止とする。
- 成否にかかわらずprobe、追加trust、フラグ差分を撤去・復元し、SHAで照合する。

### 3. ゲート成功時だけ行う本番改修

- `/Users/takedayousuke/.codex/skills/brainstorm/SKILL.md` から本文カード代替を削除する。
- `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py` から、本文に
  `【承認待ち】` があるだけでStopを通す分岐を削除する。
- 本番フックは実測したイベントだけに接続し、brainstorm作動中だけカードを監査する。
- brainstorm以外の `request_user_input` へ介入しない。
- 親メモへ `## 機械化した指摘` を追加し、新しく `ready` へ上げる時だけ存在を検査する。
- 方針承認と実行承認を見出しで区別し、`### 終わったら次に取る承認` を申し送りへ必須化する。

## カードの外部仕様

- 主質問は必ず `【承認待ち】` で開始する。
- 各選択肢の説明へ「それを選ぶと失うもの」を具体的に書く。
- 同じカードに固定質問 `（確認）上の選択でよろしいですか` を置く。
- 確認質問の選択肢は `はい、この選択でよい` と `いいえ、押し間違えた` の2つに固定する。
- 引数なしの `$brainstorm` は、Claude版と同じ待機文1行だけを返し、カードを出さない。
- カード閉鎖・無回答は承認または中断として扱わない。

## 状態遷移

状態は `awaiting_card` / `answer_received` / `approved` / `stopped` / `technical_stopped` とする。

| 観測 | 次の状態と動作 |
|---|---|
| 実カード表示 | `awaiting_card` |
| 現在のカード呼出しIDと全question IDが一致する完全回答 | 一時的に `answer_received` |
| 確認「はい」＋明示承認肢 | `approved` |
| 確認「はい」＋明示中断肢 | `stopped` |
| 確認「はい」＋通常の案選択または主回答の自由記述 | 親メモへ記録し、次カード発行後 `awaiting_card` |
| 確認「いいえ」、確認欄の自由記述、部分回答 | 主回答を破棄し、同じ質問の再発行後 `awaiting_card` |
| カード閉鎖・無回答 | 状態不変。次のユーザー入力まで `awaiting_card` を維持して再提示 |
| 古いカードID・別question ID | 状態不変 |
| 内部故障を保存できた | `technical_stopped` |

- カード外の承認・中断は、許可語だけからなる独立した最新発話に限定する。引用、説明、否定形、
  `承認しない` は除外する。
- `technical_stopped` は承認・中断と区別する。明示的な再開要求とpreflight再合格まで正常カード運用へ
  戻さない。

## 毎ターン記録

- brainstorm開始時に対象親メモの絶対パスを1つ固定する。
- 可視の小さな追記と `bs:v1` マーカーを同じパッチで挿入する。
- マーカーはハッシュ化したセッションキー、セッション内単調カウンタ、入力SHAを持つ。実turn IDが
  取得できた場合だけ、そのハッシュも加える。
- カード回答には実在するカード呼出しIDのハッシュを使い、疑似カードIDを作らない。
- 同じマーカーがあれば二重追記しない。競合時は再読込して小さなパッチを再試行し、全文上書きしない。
- Stopが機械保証するのは「現在ターンに対応する可視追記とマーカーが対象親メモへ同時挿入されたこと」
  まで。本人の言葉の忠実性は機械判定できないと明記する。
- マーカーは `done` まで保持し、`done` 昇格時にセッション子メモへ移して親から絶対パスで辿れるように
  する。

## fail-openと技術的停止

1. フックプロセスは内部例外でも終了コード0とし、通常作業を壊さない。
2. 状態を書ける場合は `technical_stopped` と理由・再開点を保存する。
3. 次に生存したUserPromptSubmitまたはSessionStartが停止状態を文脈へ再注入する。
4. 状態保存も全フックも不発なら検知不能である。この残存限界を正本へ記録し、完全保証とは報告しない。

UserPromptSubmit heartbeatはそのイベント1本の生存証拠に限定する。合成試験を本番発火の証拠にしない。

## テストと完成条件

- 選択＋確認「はい」、確認「いいえ」、通常案選択、主回答自由記述、確認欄自由記述、部分回答、閉鎖、
  古いカード、別質問を個別に試す。
- 並行2セッションで同じ親メモを更新し、別セッションの追記ではStopを通さないことを確認する。
- フック内部例外、未信頼、無効、圧縮後再注入、brainstorm作動外の通常カードを試す。
- probe撤去後の信頼済み本番フックについて、新規通常モードタスクで各必須イベントを最低1回実発火させる。
- 同じカードについて、実際に利用可能だったライフサイクルイベント、回答返却、継続、Stopの順序を
  本番ログで確認する。
- 合成試験、信頼済み本番フック、通常モードの実ログ、対象ターンの可視追記とマーカーが揃った場合だけ
  運用可能とする。
- 成否にかかわらず `wiki/builds/brainstorm-skill.md` と
  `wiki/builds/brainstorm-port-request-20260829.md` へ、Codex版、到達段階、停止理由、復元結果を記録する。
- 成功時だけ、通常モードのカード、永続メモ、状態遷移、技術的停止を説明するHTMLを作成し、表示確認する。

## 機械化した指摘

| 指摘 | 再発しうるか | 機械判定できるか | 変換先 |
|---|---|---|---|
| 未承認計画を実装へ渡さない | する | できる | 親メモの `brainstorm_status` と現行承認状態の検査 |
| 実装前レビューは指定された `gpt-5.6-sol / medium` で中立に行う | する | 一部できる | レビュー履歴・モデル・effort・判定の申し送り検査 |

## 絶対にしてはいけないこと

- 本文の `【承認待ち】` や番号付き選択肢を、本物のカードの代替にしない。
- カード呼出しIDを推測・生成しない。
- フックイベントの形を実測前に固定しない。
- 古いカード、別質問、別メモ更新を現在の承認へ流用しない。
- trust未完了、probeだけの成功、合成試験だけの成功を運用可能と言い換えない。
- この計画を確定した brainstorm 会話でコード・設定・フックを実装しない。

## 終わったら次に取る承認

### 再実装結果の実機受入承認

新しい通常モード会話で実現性ゲートと実装を終えた後、通常モードでの二問カード、毎ターン保存、
確認拒否、閉鎖、並行セッション、圧縮後再注入の実機結果を提示し、運用開始の承認を取る。

## 再開の入口（実パス）

1. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-codex-default-mode-card-plan-20260830.md`
2. `/Users/takedayousuke/.claude/skills/brainstorm/SKILL.md`
3. `/Users/takedayousuke/.codex/skills/brainstorm/SKILL.md`
4. `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py`
5. `/Users/takedayousuke/.claude/skills/brainstorm/brainstorm_guard.py`
6. `/Users/takedayousuke/.codex/hooks.json`
7. `/Users/takedayousuke/.codex/config.toml`

## 使わなかったもの・落とした情報

- 捨てたもの: 本文の疑似承認カード、Plan modeへの代替、4種類すべての内部ID必須、実測前のフック仕様。
- 手元でどう変わるか: ゲート失敗時は文章の選択肢もPlan modeカードも出ず、未解決のまま
  `technical_stopped` になる。成功時だけClaude版と同じ二問カードを通常モードで使える。
  実装前なので見え方は未確認。
- 戻せるか: 追加した機能フラグ、probe、trust、本番フックを変更前記録と期待差分に基づいて戻す。

## 関連リンク

- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-port-request-20260829]] — `wiki/builds/brainstorm-port-request-20260829.md`
- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
