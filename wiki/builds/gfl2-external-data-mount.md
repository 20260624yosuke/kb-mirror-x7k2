---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-30
sources: []
---

# ドルフロ2 データ外付け固定(GFL2 external data mount)

## 現在の統合見解

イラスト資料目的で導入した iOS 版「ドールズフロントライン2:エクシリウム」(Mac App Store 版)の
ゲームデータ約 41GB を、内蔵ディスクではなく外付け SSD の専用区画 `GFL2Data` に置くための仕組み。

外付けの APFS 区画を、アプリのサンドボックス内 `LocalCache` の位置へ**マウント(はめ込み)**して
実現する。2026-07-29 の再発調査で、LaunchAgent 単独では `diskutil mount -mountPoint` が
失敗する環境差があると分かったため、現在は **同名の AppleScript 起動ラッパー `ドルフロ2.app` が
起動直前にマウントを確認・復旧してから本体を開く**構成にしている。LaunchAgent は補助と安全弁として残し、
外付け不在時や復旧失敗時は **書き込み禁止で内蔵への再ダウンロードを阻止**する。

- ユーザーが開く起動口: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2.app`
  (AppleScript 起動ラッパー)
- アプリ本体: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/ドルフロ2_本体.app`
  (外付け・3.5GB)
- データ区画: 外付けコンテナ disk7 内の APFS ボリューム `GFL2Data`
  (Volume UUID `E3201924-CBC6-45DB-88B3-4ED1E638D805`、使用 41GB)
- マウント先: `~/Library/Containers/com.haoplay.game.ios.exilium/Data/Documents/LocalCache`
- スクリプト正本: `tools/gfl2-data-mount/gfl2-data-mount.sh`
- 起動ラッパー正本: `tools/gfl2-data-mount/gfl2-launcher.applescript`
- 実行用コピー: `~/.local/bin/gfl2-data-mount.sh`
- 常駐設定: `~/Library/LaunchAgents/com.takedayousuke.gfl2-data-mount.plist`
  (`RunAtLoad` + `WatchPaths: /Volumes` + `ProcessType: Interactive`)
- ログ: `~/Library/Logs/GFL2DataMount/mount.log`、起動ラッパーは
  `~/Library/Logs/GFL2DataMount/launcher.log`

## 根拠(なぜこの方式なのか)

App Store 経由の iOS アプリは Mac 上でも iOS と同じサンドボックス(砂場)で動き、データの
読み書き先が `~/Library/Containers/<bundle id>/Data/` 配下に**住所で決め打ち**されている。
2026-07-15〜16 に以下を実測した。

| 試した方式 | 結果 | 理由 |
| --- | --- | --- |
| 外付けへ直接インストール | 不可 | App Store が置き場所を選ばせない |
| `Documents` 丸ごと symlink | **起動不可**(クラッシュ) | 起動時のコンテナ整合性検査で弾かれる。crash log: `failed integrity checks for container ...: Invalid argument` |
| `LocalCache` を symlink | **黒画面で停止** | 整合性検査は通るが、リンク先 `/Volumes/...` への読み書きをサンドボックスが拒否。`deny(1) file-read-data /Volumes/SSD_...` を確認 |
| **外付け区画を当該パスへマウント** | **成功** | 住所は砂場内のまま実体だけ外付け。ゲームからは空き 574GB に見える |

アプリ本体(3.8GB)が外付けでも動くのは、起動処理を行うのがサンドボックス下のゲーム自身では
なく macOS 側だから。データの読み書きだけが砂場の規則の対象になる。

クリスタのバックアップ移設([[clipstudio-backup-external-symlink]] 系)で symlink が通用したのは、
クリスタが通常の macOS アプリでこの砂場に入っていないため。「Mac で symlink が使えない」のでは
なく「iOS アプリだけが砂場から出られない」が正しい理解。

## 仕組みの動作

1. ユーザーが `ドルフロ2.app` を開く。
2. 起動ラッパーが `~/.local/bin/gfl2-data-mount.sh` を実行する。
3. すでに目的地にマウント済みなら何もしない。
4. 区画が見つかれば、既定の場所(`/Volumes/GFL2Data`)から一旦外し、`LocalCache` へマウントし直す。
   マウントが外れている間に内蔵へ作られた再DL断片は削除する。
5. 起動ラッパーは、`LocalCache` が外付けマウントであることと `AssetBundles_IOS` の Bundle 数が
   9,000 件以上であることを確認してから、本体 `ドルフロ2_本体.app` を開く。
6. ゲーム起動中はマウント変更しない(データ破損回避)。
7. LaunchAgent はログイン時・`/Volumes` 変化時に同じスクリプトを呼ぶ補助線として残す。
8. **区画が無い/マウント失敗時は `LocalCache` を書き込み禁止(500)にする** — 安全弁。
   これが無いと外付け不在時にゲームが 39.4GB を内蔵へ再ダウンロードし、本体(空き約16GB)を
   満杯にする。マウント成功時は上に区画がかぶさるので権限は問題にならない。

## 検証済み(2026-07-24)

- 試験1: 再起動後の状態(既定の場所にマウント)を再現 → スクリプトが `LocalCache` へ付け替え、
  41GB が正しく見えることを確認。
- 試験2: すでに正しい状態で再実行 → 何もせず正常終了。
- 試験3: 区画が見つからない状況を再現 → 書き込み禁止になり、実際に書き込みが
  `Permission denied` で拒否されることを確認。
- **未検証**: 実際の Mac 再起動での自動復帰(LaunchAgent の `RunAtLoad` は登録済み・
  手動実行では合格。実再起動での確認は次回起動時)。
- 試験4(2026-07-25): ヘレン入手後に休憩室・キャラ詳細を開いてもキャッシュ増減なし(9,031 Bundle のまま)。
  マウント構成は安定動作しており、ゲーム内操作でデータが壊れる兆候なし。
- 試験5(2026-07-29): アップデート表示後に 40GB 級再ダウンロードが始まった件を調査。
  原因は `GFL2Data` が `/Volumes/GFL2Data` に通常マウントされ、`LocalCache` が内蔵上の
  1.6GB・524 Bundle の再DL断片を見ていたこと。外付け側の 41GB・9,031 Bundle は残存。
- 試験6(2026-07-29): `~/.local/bin/gfl2-data-mount.sh` を手動実行し、内蔵再DL断片を削除したうえで
  `GFL2Data` を `LocalCache` へ復帰。41GB・9,031 Bundle を確認。
- 試験7(2026-07-29): LaunchAgent の実行先を外付け上のスクリプトから内蔵 `~/.local/bin` へ変更。
  外付けパス実行時の `Operation not permitted` は解消。ただし LaunchAgent 単独の
  `diskutil mount -mountPoint` は 20 回リトライしても失敗することを確認。
- 試験8(2026-07-29): `ドルフロ2.app` を一度シェル実行ラッパー化したが、Finder から開けないと
  判明したため、AppleScript アプリとして作り直した。同名の起動口からマウント復旧後に
  本体 `ドルフロ2_本体.app` を開く構成。外した状態から dry run で復旧し、41GB・9,031 Bundle を確認。
- 試験9(2026-07-30): ユーザーが Finder から `ドルフロ2.app` を開き、本体起動まで成功したと確認。
  起動口は `ドルフロ2.app`、`ドルフロ2_本体.app` はラッパーから呼ばれる本体であることを運用上確定。
- 掃除(2026-07-29): 2026-07-24 の退避データ `ドルフロ2_内蔵再DL退避_20260724/`(876MB)を削除。
  2026-07-29 の内蔵再DL断片(1.6GB)も復旧処理で削除済み。

## 矛盾・未確定

- LaunchAgent 単独では `diskutil mount -mountPoint` が失敗する。現在の主復旧線は起動ラッパー。
- 今後 App Store が本体アプリを更新する場合、`ドルフロ2_本体.app` 側に更新が入るか、
  新しい `ドルフロ2.app` が作られるかは未確認。更新後に再び 40GB 級ダウンロードが出た場合は、
  まずラッパーと本体の位置を確認する。

## 関連リンク

- [[gfl2-helen-starlit-waltz-reference-route]] — 導入の目的(ヘレン衣装の資料化)
- [[clipstudio-backup-external-symlink]] — 通常アプリでの外付け退避(symlink が通用する例)
