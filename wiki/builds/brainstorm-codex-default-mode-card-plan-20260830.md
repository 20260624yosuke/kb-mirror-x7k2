---
type: build
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-08-30
---

# Codex brainstorm 通常モード承認カード修正計画

## 位置づけ

Codex の `$brainstorm` を通常モードで使いながら、本文の疑似選択肢ではなく本物の
`request_user_input` 承認カードを出すための実装計画。2026-08-30 に武田さんが承認した。

実装は新しい通常モード会話で行う。この計画を作った brainstorm 会話では実装しない。

## 正解の所在と停止条件

- 正解は Codex Desktop の新規通常モード会話における、カード表示・回答・会話継続の実挙動とフックログ。
- `default_mode_request_user_input` は存在するが、通常モードの実機成立は未確認。
- カード表示、回答、継続、イベント識別子のいずれかを確認できなければ設定を戻し、
  `technical_stopped` として停止する。本文選択肢や疑似 ID へ代替しない。

## 実装順序

### 1. 実現性ゲート

1. 現在の設定・フック・ログを、変更前後が比較できる形で記録する。
2. `default_mode_request_user_input` だけを有効化し、Codex Desktop を再読込する。
3. 新規の通常モード会話で次を個別に確認する。
   - カード表示
   - 選択回答
   - 自由記述
   - カードを閉じた場合
   - 回答後の会話継続
4. 同じ試験で `request_user_input` 前後のフックイベント、入力、結果、イベント識別子、Stop との順序を
   `guard.log` へ採取する。
5. 核心項目が一つでも成立しなければ設定を復元し、後続実装へ進まない。

実機操作は、LLM が短い手順を示し、武田さんが操作して LLM が結果を解析する共同確認を既定とする。
前面 GUI を LLM が自動操作する場合は、影響・範囲・停止条件を示して別途明示同意を取る。

### 2. ゲート成功時だけ行う改修

- `/Users/takedayousuke/.codex/skills/brainstorm/SKILL.md` と
  `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py` から、本文の
  `【承認待ち】` をカードとして通す分岐を削除する。
- 実測したイベント形式だけを根拠にカード呼出しと回答を監査する。ツール呼出し識別子が取得できない
  場合は、疑似 ID を設けず `technical_stopped` にする。
- セッション状態は `awaiting_card` / `answer_received` / `approved` / `stopped` /
  `technical_stopped` の5状態に限定する。
- 状態に対象親メモの絶対パス、カード識別子、質問ハッシュ、選択肢ハッシュ、回答、未記録ターンを持つ。
- 承認・中断は現在待機中のカード回答、または仕様上の明示文だけで成立させる。無回答、カード閉鎖、
  古いカード、別質問への回答は状態遷移の証拠にしない。
- brainstorm 開始時に対象親メモを1つ固定し、そのターンのユーザー入力またはカード回答がそのファイルへ
  記録されたことだけを有効な更新証拠とする。別メモの変更では通さない。
- フック例外時は通常利用を壊さないが、brainstorm は未検証として `technical_stopped` にする。
- `/hooks` で各フックの信頼状態を確認し、信頼済みの新規通常モード会話で実発火を確認する。

## 完成条件

- 通常モードで、選択回答、自由記述、カード閉鎖、明示承認、明示中断、技術的停止が区別される。
- 各ケースで対象親メモだけが更新され、カードと回答が対応し、誤った状態遷移が起きない。
- 並行セッション、古いカード回答、別メモ更新、フック無効・例外、圧縮後の再注入を試験済み。
- 合成セルフテスト、フックの信頼状態、通常モードの実ログ、親メモの実更新がすべて揃っている。
- 実測結果を `wiki/builds/brainstorm-skill.md` と
  `wiki/builds/brainstorm-port-request-20260829.md` へ反映している。
- 通常モードのカード、永続メモ、状態遷移、技術的停止を説明する HTML を作り、ブラウザで表示確認済み。

## 絶対にしてはいけないこと

- 通常モードの本物のカードが未確認なのに、利用可能・運用可能と報告しない。
- 本文の `【承認待ち】` や番号付き選択肢をカードの代替にしない。
- フックイベントの形や識別子を推測で実装しない。
- 古いカード、別の質問、別の親メモの更新を現在の承認へ流用しない。
- trust（フックを実行してよいと信頼済みにする状態）未完了を実装済みと扱わない。
- この brainstorm 会話の中で実装しない。

## 独立レビューで計画へ反映した点

計画作成者とは別の `gpt-5.6-terra`・reasoning effort `medium` が、仕様と現行実装を直接比較した。
その結果、実機成立の先行確認、本文代替の削除、実イベント採取後の設計、カードと対象親メモに束縛した
状態遷移、trust 済み実機試験を必須へ変更した。レビュー判定は、修正前計画をそのまま実装へ渡せない
というものだった。

## 再開の入口（実パス）

1. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-codex-default-mode-card-plan-20260830.md`
2. `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/brainstorm-skill.md`
3. `/Users/takedayousuke/.codex/skills/brainstorm/SKILL.md`
4. `/Users/takedayousuke/.codex/skills/brainstorm/scripts/codex_adapter.py`
5. `/Users/takedayousuke/.codex/hooks.json`
6. `/Users/takedayousuke/.codex/config.toml`

## 使わなかったもの・落とした情報

- 捨てたもの: 本文の疑似承認カードと、実イベントを確認する前のフック詳細設計。
- 手元でどう変わるか: 実機ゲートに失敗した場合、本文の選択肢は出ず、問題は未解決のまま
  `technical_stopped` と表示される。成功時だけ本物のカードが出る。実装前なので見え方は未確認。
- 戻せるか: 機能フラグを変更前の値へ戻し、変更前に記録した設定と比較して復元する。

## 関連リンク

- [[brainstorm-skill]] — `wiki/builds/brainstorm-skill.md`
- [[brainstorm-port-request-20260829]] — `wiki/builds/brainstorm-port-request-20260829.md`
- [[brainstorm-brainstorm-skill-portability]] — `wiki/analyses/brainstorm/brainstorm-skill-portability/brainstorm-brainstorm-skill-portability.md`
