---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-10
sources: []
---

# azooKey 半角記号入力カスタム

## 現在の統合見解

azooKey の日本語入力中に、LLM 操作用の記号を半角で打ちやすくするためのローカル設定。
BetterTouchTool のキーシーケンスはライブ変換中の未確定文字列と衝突するため、azooKey の
カスタム入力テーブルで処理する方針にした。

現在は、デフォルトのローマ字入力テーブルをベースにし、次を追加している。
2026-07-10 のユーザー実機検証で、今回追加した `ーー -> -`、`＋＋ -> +`、数字間の
`。 -> .` 系を含めて動作確認済み。

```tsv
＿	_
＊	*
・・	/
＃	#
ーー	-
＋＋	+
0。0	0.0
...
9。9	9.9
```

## 正本ファイル

- 外側設定ファイル: `/Users/takedayousuke/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv`
- コンテナ内設定ファイル: `/Users/takedayousuke/Library/Containers/dev.ensan.inputmethod.azooKeyMac/Data/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv`
- 行数: 両方400行
- 更新時刻: 2026-07-10 15:00 JST 前後
- SHA-256: `9ba7f84a8fa9f931ef44eb7b6a74df628deca76e3585a182334f01e1392301ef`
- 入力方式: azooKey 設定上は `カスタム`
- 成功状態バックアップ: `/Users/takedayousuke/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv.backup-20260710-azookey-symbols`
- 失敗状態バックアップ: `/Users/takedayousuke/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv.backup-20260710-implement-before-rollback`
- コンテナ側反映前バックアップ: `/Users/takedayousuke/Library/Containers/dev.ensan.inputmethod.azooKeyMac/Data/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv.backup-20260710-container-before-symbols`
- コンテナ側400行反映前バックアップ: `/Users/takedayousuke/Library/Containers/dev.ensan.inputmethod.azooKeyMac/Data/Library/Application Support/azooKeyMac/CustomInputTable/custom_input_table.tsv.backup-20260710-container-before-full-decimal`

## 経緯

- 最初は BTT のキーシーケンスで `・・ -> /` などを扱っていたが、ライブ変換中は未確定文字列へ後から削除・置換が割り込むため相性が悪かった。
- azooKey のコード調査で、`* -> ＊`、`_ -> ＿`、`/ -> ・` のような半角から全角への記号マップがあることを確認した。
- Foundation Models は「いい感じ変換」のバックエンドであり、キー入力を半角記号化する層ではないと判断した。
- 1回目は標準ローマ字ベースで作ったが、ユーザー環境のカスタム編集画面が `ms -> ます` など AZIK 既定表を表示しており、噛み合わなかった。
- 2回目は AZIK ベースにしたが、`ji -> じ` が崩れるなど、ユーザーの求める「デフォルト操作感」と違った。
- 3回目に、デフォルトローマ字ベースへ戻し、記号4件だけを追加した。ユーザー検証で基本動作は良好と確認された。
- 2026-07-10 に、同じ入力表へ `ーー -> -`、`＋＋ -> +`、`0。0` から `9。9` までの100通りの小数点変換を追加したが、実機検証で改善を確認できなかった。
- 同日中に、100通り追加をいったん失敗状態として退避し、成功確認済みの298行版へ戻したうえで、`ーー -> -`、`＋＋ -> +`、`5。4 -> 5.4` の3件だけを載せた301行のパイロット状態へ切り替えた。
- その後、azooKey 入力メソッド本体がコンテナ内の Application Support 側にも `custom_input_table.tsv` を持っており、外側の設定ファイルだけでは実入力へ反映されないことを確認した。
- 外側とコンテナ内の両方を400行版へ同期し、azooKey と TextInputMenuAgent を再起動した。
- 同期後の再検証で、ユーザー実機上で今回の拡張が動作すると確認された。

## ユーザー検証

- `###` が半角で打てる。
- `_`、`*`、`・・ -> /` も機能している。
- 使用感は良い。
- ただし `おー` と入力すると `oh` へ変換されるケースがあり、使い勝手が悪い。

## 2026-07-10 実装内容

- 外側設定ファイルとコンテナ内設定ファイルの差分を確認し、コンテナ側に今回の追加行が入っていないことを原因として特定した。
- 外側とコンテナ内の両方へ `ーー -> -`、`＋＋ -> +`、`0。0` から `9。9` まで100通りの小数点変換を反映した。
- azooKey と TextInputMenuAgent を再起動し、反映前提の状態を作り直した。

## 検証状態

- 実装済み: 外側設定ファイルとコンテナ内設定ファイルの両方へ 400 行版を反映済み。
- 自動試験済み: 行数 400、SHA-256 一致、主要追加行存在、azooKey と TextInputMenuAgent の再起動後プロセス起動を確認済み。
- 実機確認済み: 2026-07-10 のユーザー検証で、今回の拡張が実際の日本語入力中に動作すると確認済み。
- 運用開始可能: 以後の更新で「外側だけを直して終わる」手順を禁止し、下の更新手順を守る前提で運用可能。

## 今後の更新手順

1. まず外側設定ファイルを更新する。
2. 同じ内容をコンテナ内設定ファイルへ同期する。
3. 両方の行数と SHA-256 を比較し、一致を確認する。
4. azooKey と TextInputMenuAgent を再起動する。
5. 実機で少なくとも `ーー`、`＋＋`、`5。4`、通常の `。` を確認する。
6. wiki の build ページに、変更内容・一致した SHA・実機確認結果を追記する。

## 再発防止ルール

- 「`~/Library/Application Support/azooKeyMac/...` を直したら完了」と見なさない。必ずコンテナ内の同名ファイルも確認する。
- 実機確認前に「直った」と断定しない。ファイル更新とプロセス再起動は前提条件であって、改善確認そのものではない。
- 追加ルールを広く入れる前に、必要なら少数行のパイロットで切り分ける。ただし最終反映先は必ず外側とコンテナ内の両方に揃える。
- 次回以降の不具合調査では、最初に「どちらの `custom_input_table.tsv` が変わっているか」を確認する。

## レビュー候補

### 変換エンジン側のクセ

| 現象 | 判定 | 理由 | 次の対応候補 |
|---|---|---|---|
| `おー -> oh` | 要レビュー | カスタム入力テーブル内に `oh` は無く、azooKey/Zenzai の変換候補または学習由来の可能性が高い。リポジトリのテストにも `おーけー -> OK` のような英語候補がある。 | ユーザー辞書で `おー -> おー` を強める、履歴学習の影響を調べる、ライブ変換中だけ候補選択を観察する |

### 削ってもよい可能性がある候補

| 入力 | 出力 | 判定 | 理由 |
|---|---|---|---|
| `zh` | `←` | 確認待ち | z 系の矢印ショートカット。使わないなら通常文章の誤爆防止のため削除候補。 |
| `zj` | `↓` | 確認待ち | 同上。 |
| `zk` | `↑` | 確認待ち | 同上。 |
| `zl` | `→` | 確認待ち | 同上。 |

### 見た目は変だが残す推奨

| 入力群 | 出力例 | 判定 | 理由 |
|---|---|---|---|
| `bb`〜`zz` | `っb`、`っk` など | 残す推奨 | `kko -> っこ` のような普通の促音入力を作る途中規則。消すと通常のローマ字入力が壊れる可能性が高い。 |
| `n{any character}` | `ん{any character}` | 残す推奨 | `n` の後に別文字が来たとき `ん` へ確定するための通常規則。 |
| `ny -> ny` | `ny` | 残す推奨 | `nya -> にゃ` 系との衝突を避ける例外規則。 |

## 矛盾・未確定

- `おー -> oh` は実機症状としてユーザーが確認済みだが、入力テーブル上の直接原因は見つかっていない。変換エンジン候補、履歴学習、ライブ変換のいずれかは未確定。
- `zh/zj/zk/zl` を削除してよいかは未決定。ユーザー確認後に作業する。
- BTT 側に残る `・・ -> /` キーシーケンスが有効なままだと、azooKey 側の挙動検証に混ざる可能性がある。
- 2026-07-10 の失敗原因は、外側の Application Support 側だけを更新し、azooKey 入力メソッド本体が読むコンテナ内 Application Support 側へ同期していなかったこと。現在は両方を同期済みで、ユーザー実機確認も完了している。

## 関連リンク

- [[llm-chat-enter-guard]] — BTT が IME 変換中の入力と衝突しやすいという過去の判断。
- [[azookey-mode-reconversion]] — azooKey 自前ビルドによる打ち間違いモード復元機能の実装計画。本ページの再起動手順・設定二重構造を前提にする。
