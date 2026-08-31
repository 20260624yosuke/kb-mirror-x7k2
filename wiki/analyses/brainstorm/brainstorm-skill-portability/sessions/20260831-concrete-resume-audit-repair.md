---
type: analysis
status: active
confidence: high
evidence_level: source-backed+user-stated
last_reviewed: 2026-08-31
---

# Codex brainstorm: 曖昧な再開点を拒否する監査の修理記録

## 現在の結果

修理途中・限定解除待ち。全面解決／運用開始とは扱わない。Helen固有の監査コードは変更していない。

## 原因と変更

1. 旧Stopはカード状態・メモマーカー・引継ぎ到達性だけを確認。次の担当、操作、対象、終了条件を検査していなかった。
2. stop_hook_activeがtrueなら無条件通過していた。
3. 起動正規表現が行頭の裸の/brainstorm・$brainstormだけを認識し、実際のUIスキルリンクを認識していなかった。

Codex版のscripts/resume_contract.pyを追加し、親メモの単一brainstorm-checkpointを読む検査を実装。
次の担当・操作・実在対象・終了条件、現在地と先の停止地点・復帰先、根拠ファイルSHA、回答本文への掲載を検査する。
今実行できるassistant作業が残る状態での終了は拒否。記録の意味の真実性は保証しない。
codex_adapter.pyのStopに組込み、再入時の無条件通過を削除。UIリンク起動と最新の実ユーザー入力からの起動復旧を追加。
復旧はactivation_recoveredとして記録し、UserPromptSubmit発火があったことにはしない。

変更版SHA:
- resume_contract.py: 385bfbab3c70e134f9aa40130d74fbed0ea0c70af7a445d50cc4c20ed75a7f75
- codex_adapter.py: ba4aeaed7b4318d97c61980ff7a1cf91ee1aea8fced254fb2809d8621465d74d

## 検査結果

- 既存test_codex_adapter.py: unittest discoverで11/11 PASS。
- 追加の読取中心インライン試験: 23/23 PASS。
  正常契約、owner/action/target/done_when欠落、曖昧なaction、現在と停止の混同、復帰辺欠落、
  停止点無断削除、根拠SHA古さ、停止根拠欠落、実行可能作業の放置、表示欠落、HTMLコメントだけの表示、
  承認状態の矛盾、通常HTML表示、3種の明示起動、3種の引用非起動、Stop再入で拒否を試験した。
- 実イベント: 今回のタスクでPostToolUseからの起動復旧、同ターンメモパッチ検証、カード呼出し、
  同じ呼出しIDへの空回答、awaiting_card維持を観測。
- 追加23ケースの恒久テストファイルへの保存は未実施。実Stopの拒否・再入・正常通過の一連の試験も未実施。

## 作動後に顕在化した停止

本体修理中に実装禁止の文脈が実際に注入されたため、残りの実装修理には限定解除を求めた。
実カードは空回答だった。承認・中断へ変更していない。bypassは実行していない。
さらに新しいresume_contract.pyの読取専用CLI呼出しを旧guard-writeが成果物書込として拒否した。
別の呼出し方で迂回していない。この誤拒否もCodex brainstorm本体の残修理とする。
Wikiルートへの説明HTML保存も書込境界で拒否。規則上文書保存を許された既存wiki添付フォルダへ保存した。

## 解除後の担当と終了条件

担当は私。対象はCodex版brainstormのみ。
1. resume_contractの読取専用CLIを正確に識別して許可し、任意Python書込を許可しない否定試験を加える。
2. 新23ケースと必要な回帰試験をtestsに保存。SKILL.mdのcheckpoint書式・CLI手順を同期する。
3. 合成テストの正常通過／欠落拒否／復元後通過、実Stopでの拒否と再入検査を直接確認する。
4. 機械記録に未確認が残るならそれを残し、全面運用開始とは報告しない。

解除はHelen計画の実行承認ではない。Helenの戻り先は一本化計画revision 3のモデル配分・設定差分具体化。
P0済み・P0B本体前と、さらに先の原作比較の停止を消さない。

## 図

保存先（回答ではリンクだけで渡さずHTML全文を出力）:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index/20260831-brainstorm-checkpoint-and-helen-timeline.html

HTML構文、外部資源なし、内部リンク、SVG XML、4地点の存在を検査。ブラウザ描画・ChatGPT Preview実表示は未観測。

## 使わなかったもの・落とした情報

旧承認、旧記録、Helen成果物の削除なし。スキル本体を環境間で共有する方式は採用せず、Codex版だけ変更した。
未回答カードを解除と扱う方式は採用しない。続行が一旦止まるが、未承認の書込へ広げない。明示回答後に戻れる。
