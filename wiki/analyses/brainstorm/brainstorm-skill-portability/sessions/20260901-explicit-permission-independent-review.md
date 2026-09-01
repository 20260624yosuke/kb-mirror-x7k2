# 明示許可修理計画 第1版 独立レビュー

日時: 2026-09-01
モデル: gpt-5.6-sol
reasoning effort: medium
方式: 読取専用。編集・状態更新・フック実行なし。

判定: 実装前の計画として渡せない。Critical 1 / Major 6 / Minor 2。

## 必須修正

- Critical: 承認した計画文書と、実際に書いてよいパス・操作の正解集合を分離し、機械的に結ぶ。相対パス、symlink、rename/delete、新規ファイル、抽出不能なshell書込みはfail closedにする。
- Major: pending実行判断の生成規則とゼロ・複数時の拒否、terminal／カード選択／自由記述の遷移表を置く。
- Major: 許可を対象・版ごとの複数レコードにし、別許可を消さない。別会話へのhandoffを定義する。
- Major: 許可後のphase、カード、毎ターン記録、圧縮・SessionStart・Stop・完了・中断・版変更の遷移を定義する。
- Major: 検証→耐久保存→再読込一致後だけ有効にし、generationとlockで競合を拒否する。失敗時は旧許可を保持する。
- Major: 新許可イベントにinput SHA、対象SHA、decision bindingを残す。legacy importの正本ログ・一意性・重複防止を定義する。
- Major: 許可検査の発火条件をsession・親・計画・phaseで固定し、無関係な通常作業や未移行ready案件を一括停止しない。
- Minor: 親パスSHAの正規化対象、失効理由、曖昧発話原文の正本と個人情報境界を明記する。

第1版のR0–R6と実フック試験の範囲は過大ではないとの判断。上記を第2版へ反映して再レビューする。

## 第2版の再レビュー

- 1回目: Critical 1 / Major 1 / Minor 1。対象SHA不一致で検査が発火しない順序、別会話handoffのsource/destination束縛、パス正規化を修正。
- 2回目: Critical 0 / Major 0。実装前の計画として渡せる。対象SHA不一致の失効拒否、handoffの一回受領と再利用拒否、`resolve(strict=True)`＋NFC＋実ボリュームのcase sensitivity＋inode/dev固定を確認。
- これは計画PASS。実行承認・実装完了・実フック成立ではない。
