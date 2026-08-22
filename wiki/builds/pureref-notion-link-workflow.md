---
type: build
name: PureRef と Notion をつなぐ xcord ランチャーワークフロー
aliases: [pureref-notion-link-workflow, xcord-pureref-tools]
tags: [tool, macOS, automation, notion, pureref, xcord]
sources: []
---

# PureRef と Notion をつなぐ xcord ランチャーワークフロー

## 問題

武田さん([[takeda-yohsuke]])はエスキース進捗を Notion で管理しつつ、各ページから該当の `.pur` を直接開きたい。Notion 側にはスクリーンショット・ページタイトル・リンクを持たせ、視認性は Notion が担う。

ただし [[pureref|PureRef]] は通常の `open -a PureRef <file>` だと既存ウィンドウへ上書き展開されることがあり、複数ラフ同時並行運用と衝突する。また Notion は `pureref://...` のようなカスタム URL スキームをリンクとして扱わないケースがある。

## 採用した方針

Notion には通常の HTTP リンクを置く。リンク先は外部サーバーではなく、Mac 内だけで動くローカル中継サーバー。

```
Notion
  -> http://127.0.0.1:17777/open?path=...
  -> pureref-launcher-server.py
  -> pureref-open.sh
  -> PureRef
```

`127.0.0.1` はこの Mac 自身を指すため、クラウド料金は発生しない。外部公開サーバーでもない。

## xcord アプリ置き場

武田さんはアプリを見失いやすいため、自作アプリは次のフォルダへ集約する。

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord`

ローカルの `~/Applications/xcord` は重複混乱を避けるため削除済み。以後、xcord 系アプリは外付け SSD 側を正とする。

## 構成アプリ

| アプリ                         | 役割                                                                                |
| --------------------------- | --------------------------------------------------------------------------------- |
| `PureRefLauncher.app`       | `pureref://` を受ける URL ハンドラ。初期案の名残だが、外付け `xcord` に配置済み。                            |
| `PureRefLinkMaker.app`      | Finder 上の `.pur` をドラッグすると Notion 用 HTTP リンクをクリップボードへコピーする。                        |
| `PureRefCopyNotionLink.app` | 実行時に前面が PureRef なら、その `.pur` の Notion 用リンクをコピーする。判定できない場合だけ現在開いている `.pur` 一覧から選ぶ。 |
| `PureRefSessionRestore.app` | [[pureref-session-restore]] の最新スナップショット/世代バックアップから候補を選んで一括復元する。                   |

同フォルダに `README_xcord.txt` を置き、各アプリの用途を短く書いてある。機能を忘れた場合はまずこの README を見る。

## 開く側の安定化

2026-05-21 の検証で、`新規ウィンドウ作成ショートカット -> open` 方式は不安定と判明した。PureRef 側の処理順に負けると、既存ウィンドウへ上書き展開された後に新規ウィンドウが作られる。

そのため GUI 自動操作はやめ、PureRef の設定 `Open files in = Window with scene open` を使う方針に変更した。設定ファイル上では `OpenFilesIn=NewOrExisting`。同じ scene が既に開いている場合はそのウィンドウを前面へ、違う scene なら別ウィンドウへ、という挙動を PureRef 本体に任せる。

## 使い方

### Notion に貼るリンクを作る

1. PureRef で対象 `.pur` を開き、そのウィンドウをアクティブにして内容を確認する。
2. すぐ macOS サービス `PureRef Notionリンクをコピー` を実行する。2026-05-21 時点の割り当ては `Control + Command + Option + Shift + C`。ショートカット割り当て前は `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/PureRefCopyNotionLink.app` でもよい。
3. 実行時に前面 PureRef が特定できれば自動コピー。判定できない場合だけ候補一覧から対象を選ぶ。
4. 「Notion用リンクをコピーしました」と出たら Notion の URL 欄へ貼る。

Finder で `.pur` が見えている場合は、`PureRefLinkMaker.app` にドラッグしてもよい。

### Notion から開く

Notion のリンクは `http://127.0.0.1:17777/open?path=...` 形式。クリックするとブラウザに「PureRefに送信しました」と出ることがあるが正常。

## 注意

- ローカル中継サーバーは `~/Library/LaunchAgents/com.local.pureref-launcher.plist` で自動起動する。
- ログは `~/Library/Logs/PureRefLauncherServer.log` と `~/Library/Logs/PureRefLauncher.log`。
- macOS サービスがショートカット設定画面に出ない場合は、`~/Library/Services/<service>.workflow/Contents/Info.plist` の `NSServices` 登録が必要。2026-05-21 に `PureRef Notionリンクをコピー.workflow` と `PureRefCopyNotionLink.workflow` の `Info.plist` を追加済み。
- PureRef を大量に開いたまま検証すると、[[pureref-session-restore]] のスナップショットと干渉する可能性がある。2026-05-21 にその懸念が出たため、セッション復元側は世代バックアップ方式へ拡張した。
- 2026-05-21 修正: `PureRefCopyNotionLink.app` は当初 `RecentFiles0` を読んでいたため、「最後に開いたファイル」をコピーする危険があった。現在は PureRef の autosave / 実行プロセスから現在開いている `.pur` を取る方式に変更済み。
- 2026-05-21 追加修正: ファイル名だけで 30 個前後の PureRef を区別するのは現実的でないため、「PureRef をクリックしてアクティブ化 → すぐサービス/ショートカット実行」で、その瞬間の前面 PureRef を読む方式に寄せる。常駐 watcher 案はクラッシュ懸念と方向性未確定のため撤回。

## 関連

- [[pureref]] — PureRef 本体の挙動と制約。
- [[pureref-session-restore]] — 再起動後に PureRef セッションを復元する仕組み。
- [[takeda-yohsuke]] — このワークフローのユーザー。

## 出典

2026-05-21 の武田さんとの設計・実装セッション。
