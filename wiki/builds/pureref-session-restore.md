---
type: build
name: PureRef セッション復元の仕組み
tags: [tool, macOS, automation, launchctl, illustration-workflow]
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-06-15
---

# PureRef セッション復元の仕組み

## 問題

武田さん([[takeda-yohsuke]])は [[pureref|PureRef]] を「複数ラフ同時並行」の資料管理に使うため、ウィンドウが常時 10〜30 個に乱立する。Mac 再起動の際、**前回開いていた全 `.pur` を手作業で 1 つずつ開き直すのが苦痛**。PureRef 自体にも macOS にもこれを自動化する純正機能はない。

## 対象範囲の明確化(2026-06-15)

- 実装済みなのは、再起動後に**前回開いていた PureRef の `.pur` 一覧を再び開く**こと。
- **ウィンドウの画面上の位置・大きさ・並び順は復元しない**。
- PureRef 以外も含む「Mac 全体のウィンドウ配置固定・再起動後の配置復元」は、
  ToDo のアイデア段階であり、設計・製品選定・実装はまだ行っていない。
- したがって「ウィンドウ固定ソフト / 再起動後のごちゃつき」は、本 build の未完了機能ではなく
  別候補。現時点ではプロジェクト化されていない。

## 解決アプローチの核心

PureRef の **autosave ファイル(`~/Library/Application Support/PureRef/<hash>_AutoSave.pur`)に元 `.pur` のフルパスがバイナリ文字列として埋め込まれている** ことを発見・利用。autosave は PureRef 起動中しか存在しないので、**定期的にスナップショットを外部ファイルに保存** しておき、再起動後はそれを読んで一括起動する。

## アーキテクチャ

```
[作業中] LaunchAgent (5 分おき)
   └─ pureref-snapshot.sh
        └─ pureref-list.py --paths-only
              (autosave をパース、元パスを抽出)
        └─ ~/.pureref-session.txt に保存
              (空なら上書きせず前回値を温存 = 終了直後でも壊れない)

[再起動後] 手動 1 コマンド
   └─ pureref-restore.sh
        └─ ~/.pureref-session.txt を読む
        └─ 各パスを open -na PureRef で順次起動
```

## 構成ファイル(すべて `/Users/takedayousuke/` 直下)

| ファイル | 役割 |
|---|---|
| `pureref-list.py` | autosave をパースして元パス一覧を出力。`--paths-only` で機械可読モード |
| `pureref-snapshot.sh` | スナップショット書き出し本体。`pgrep -x PureRef` で起動中のみ動作、空なら温存 |
| `pureref-restore.sh` | スナップショットから一括起動 |
| `pureref-install.sh` | LaunchAgent を `launchctl load` で登録(冪等) |
| `~/Library/LaunchAgents/com.takedayousuke.pureref-snapshot.plist` | 5 分(300 秒)おきの定期実行 + `RunAtLoad=true` |
| `~/.pureref-session.txt` | スナップショット本体(1 行 1 パス) |
| `~/.pureref-session.backups/` | 上書き前の世代バックアップ置き場(2026-05-21 追加) |
| `~/Library/Logs/PureRefSnapshot.log` | スナップショット更新ログ(2026-05-21 追加) |
| `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/PureRefSessionRestore.app` | 世代バックアップから選んで復元する GUI 補助アプリ(2026-05-21 追加) |

## 抽出ロジックの要点

autosave 内のバイト列から `/Volumes/...`, `/Users/...` で始まり `.pur` で終わるバイト列を抽出する正規表現:

```python
PATTERN = rb'(?:/Volumes|/Users|/private|/tmp)/[^\x00-\x1f]{4,500}?\.pur'
```

- `[^\x00-\x1f]` で制御文字以外(= 日本語 UTF-8 バイト 0x80+ を含む)を許容
- `?` 付きの最短マッチで複数パスを正しく分離
- ファイル存在チェック(`os.path.exists`)で偽陽性を除外

## 運用フロー

| タイミング               | 操作                                          |
| ------------------- | ------------------------------------------- |
| 日常作業中               | 何もしなくていい(LaunchAgent が裏で 5 分おきに snapshot)   |
| 再起動直前に新規ファイルを開いた時のみ | `bash ~/pureref-snapshot.sh` を 1 回(任意・無音終了) |
| 再起動後                | `bash ~/pureref-restore.sh` で前回セットを一括復元     |

確認コマンド:
- `cat ~/.pureref-session.txt` — 現在のスナップショット内容
- `launchctl list | grep pureref` — LaunchAgent 稼働確認
- `tail ~/Library/Logs/PureRefSnapshot.log` — スナップショット更新ログ

## 2026-05-21 拡張: 世代バックアップと復元アプリ

Notion 連携の検証中に PureRef ウィンドウが 30 個前後開いている状態があり、テスト中の少数起動が `~/.pureref-session.txt` を上書きすると、再起動復元用の既存セットが失われる懸念が出た。

ただし「件数が減ったら上書き拒否」は不採用。武田さんの通常運用では、プロジェクト終了時に `.pur` を閉じるため、36 件 → 20 件 → 10 件のように自然に減ることがある。減少そのものを異常扱いすると運用と衝突する。

採用方針:

- `~/.pureref-session.txt` はこれまで通り「最新の再起動復元用」として維持する。
- 空の抽出結果だけは上書きしない。
- 上書き前に必ず `~/.pureref-session.backups/YYYY-MM-DD_HH-MM-SS_<件数>files.txt` へ退避する。
- 更新ログは `~/Library/Logs/PureRefSnapshot.log` に残す。
- 過去セットへ戻したい場合は `PureRefSessionRestore.app` で候補を選ぶ。

復元アプリの置き場:

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/PureRefSessionRestore.app`

復元アプリは「最新 / 履歴」「件数」「日時」「先頭数件のファイル名プレビュー」を一覧表示し、選んだ候補を `open -na PureRef` で一括起動する。安全のため、既に開いている PureRef は勝手に閉じない。

2026-05-21 夜の修正:

- `PureRefSessionRestore.app` がシステム Python 3.9 で起動するため、`Candidate | None` などの新しめの型ヒントと f-string 内バックスラッシュを使うと `SyntaxError` / `TypeError` で落ちることが判明。`pureref-session-restore.py` を Python 3.9 互換に修正。
- テスト後の最新スナップショットが 4 件になっていたため、36 件の履歴が最新順リストの下に埋もれる問題があった。候補一覧は「最新」に加えて、件数の多い履歴候補を上位に出すよう変更。36 件候補は `履歴 | 36件 | ...` として上の方に表示される。
- UI は出るがファイルが開かないケースに備え、復元時の `open` 呼び出しを `open -n -a /Applications/PureRef.app -- <path>` に変更し、`~/Library/Logs/PureRefSessionRestore.log` に `OPEN_OK` / `OPEN_FAILED` を記録するようにした。
- 36 件復元後に空の PureRef ウィンドウが 4 件出た。照合結果では、復元ログは 36 件すべて `OPEN_OK`、autosave から確認できる元 `.pur` も 36 件。一方 PureRef プロセスは 40 件で、元 `.pur` を持たない空/未識別プロセスが 4 件あった。原因は、既に開いていたファイルを再復元したこと、および `ジム` / `ジム` のような Unicode 正規化違いの同一パス重複と推定。以後は復元前に既に開いている `.pur` と、正規化後に同一となる重複パスをスキップするよう修正。

## 制約・トレードオフ

- **30 並列起動 = 30 別インスタンス**: `open -na` を 30 回叩くと PureRef.app プロセスが 30 個立つ。Dock アイコンも 30 個。メモリ消費に注意(M1 Mac mini なら現実的範囲)。
- **Dock 視認性問題**: アイコンは全部同じで区別不能。`open -a`(`-n` なし)で 2 つ目を新規ウィンドウに展開してくれれば 1 インスタンス化できたが、PureRef は「上書き」挙動のため不可。実用解として **Mission Control 中心運用(F3 でサムネ一覧、内容で区別)+ Dock 自動非表示** を採用方針。完全な 1 インスタンス化は AppleScript UI 自動化で理論上可能だが、アクセシビリティ権限・タイミング依存で脆くなる。
- **未保存の新規ファイル**: パスがないので復元対象に含まれない(運用上、まず保存してから作業を進める)。
- **ファイル移動/リネーム**: スナップショットは絶対パスで記録するため、復元時に該当パスがなければスキップされる(`pureref-restore.sh` が `[スキップ: 存在しない]` と表示)。
- **Mac クラッシュ耐性**: 最大 5 分前の状態までは保証。直前 5 分以内に開いたファイルは復元されない。
- **復元アプリは閉じる操作をしない**: 未保存変更を失う危険があるため。大量に開いた状態で復元すると追加起動になる。

## 調査経緯メモ(却下した経路)

| 経路 | 却下理由 |
|---|---|
| `lsof -c PureRef` | autosave ファイルのパスしか出ず、元 `.pur` パスは取れない |
| `osascript ... tell application "PureRef"` | PureRef は AppleScript dictionary を持たないためエラー |
| `tell application "System Events" to tell process "PureRef"` | macOS の補助アクセス権限が必要 + ウィンドウタイトル止まりでフルパス復元には不十分 |
| `ps -axo command` で起動引数を見る | `open -na` 経由起動だと AppleEvent でファイルパスが渡されるため `ps` には出ない |
| macOS 標準の「再起動後にウィンドウを復元」 | PureRef は対応していない |

最終的に autosave バイナリの文字列パース方式が最も堅実だった。

## 関連

- [[pureref]] — ツール本体の挙動と制約
- [[pureref-notion-link-workflow]] — Notion から PureRef を開く xcord ランチャーワークフロー
- [[takeda-yohsuke]] — この仕組みのユーザー

## 出典

2026-05-15 のtakeda-yohsukeさんとの設計セッション(問題提起 → 5 経路の検証 → autosave 解析 → LaunchAgent 実装まで)。
2026-05-21 のtakeda-yohsukeさんとの設計セッション(Notion 連携検証 → スナップショット干渉懸念 → 世代バックアップ + 復元アプリへ拡張)。
