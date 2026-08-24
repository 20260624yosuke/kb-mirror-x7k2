---
type: analysis
title: MacBook内蔵SSD空き容量6%化の原因調査と解放(2026-08-24)
created: 2026-08-24
prompted_by: 内蔵SSDの空きが6%しかない。「20%は確保していたはず」という相談から、使用内訳の全量調査と一部解放を実施
sources: []
related: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-24
---

## 要約

MacBook 内蔵 SSD(APFS コンテナ 245GB)の空きが 11.4GB(≈5%)まで圧迫していた原因を実機計測で特定し、
Claude デスクトップの VM バンドル 10GB と Time Machine ローカルスナップショット 2 件の削除で
**空き 25.1GB(≈10%)へ回復**した。外付け SSD_M.2(931GB)は 62% 使用・340GB 空きで問題なし。

## 調査で確定した事実(すべて当日実測)

### コンテナ全体の使用マップ(調査時点: 使用 193.5GB / 空き 11.6GB)

| 領域 | 容量 | 内容 |
|---|---|---|
| /Users/takedayousuke | 97GB | 下記ユーザ領域参照 |
| /Applications | 20GB | Xcode 4G / DaVinci Resolve 3.4G / Chrome 2.1G 等 |
| /private | 15GB | var/folders 一時キャッシュ 5.5G / var/db 2.9G |
| スワップ (/System/Volumes/VM) | **12GB** | swapfile0〜10 + kernelcore。実使用 10GB(アプリ大量起動) |
| /opt | 9.5GB | anaconda3 5.5G / homebrew 3.7G |
| システム Library | 5.9GB | |

### ユーザ領域 97GB の内訳

| 項目 | 容量 | 備考 |
|---|---|---|
| Application Support/Claude/vm_bundles/claudevm.bundle | **10GB** | rootfs.img(Linux ルート FS)10GiB 固定。Cowork 隔離モード用 VM |
| Eagle アプリデータ | 8.6GB | ライブラリのため削除注意 |
| ~/Library/Caches | 9.3GB | Google 3.1G / Brave 1.6G / 更新キャッシュ等 |
| ~/Library/CELSYS (Clip Studio 素材) | 9.1GB | CLIPStudioCommon/Material 9.0G |
| Google (Chrome プロファイル) | 6.8GB | + キャッシュ 3.1G は上と別 |
| Notion | 5.7GB | |
| ~/.local/share | 4.7GB | opencode 3.5G / coloso-transcribe venv 1.0G |
| Spotlight インデックス (CoreSpotlight) | 4.7GB | 過大。rebuild 候補 |
| ~/Library/Containers(内蔵実分) | 4.4GB | exilium ゲーム 41GB は外付け SSD 側にマウント済みで内蔵非圧迫 |
| ~/.codex | 3.9GB | sessions 2.8G / logs sqlite 261M |

### 「20%確保」が崩れた主犯

単一の巨大ファイルではなく積み重ね:
1. Claude VM バンドル 10GB(8-18 にアプリ自動更新)
2. スワップ肥大 12GB(通常 1GB 程度。再起動で回収可)
3. Time Machine ローカルスナップショット(当日 2 件残存)
4. ブラウザ・更新系キャッシュ 約 9GB

## 実施した対応と結果

| 対応 | 結果 |
|---|---|
| claudevm.bundle 削除(rm -rf、アプリ起動中でもファイル未掴みを lsof で確認) | バンドル消滅。ただし直後は空き不変 |
| ローカルスナップショット削除(`tmutil deletelocalsnapshots <日付>` 形式) | 使用 193.7→180.0GB、**空き 11.4→25.1GB(+13.7GB)** |

### 教訓: ファイル削除後に空きが増えない場合

当日のローカルスナップショットが削除ファイルのブロックを参照し続けるため、
スナップショットを消すまで空きは返ってこない。`tmutil deletelocalsnapshots` へは
スナップショット名そのものではなく **日付部分のみ**(例: `2026-08-24-092425`)を渡す。

### Claude VM バンドルの正体(誤解しやすい点)

- 普段デスクトップアプリで使う Claude Code は **macOS ネイティブバイナリ**
  (`~/Library/Application Support/Claude/claude-code/<版>/claude.app/...`)として動き、VM を経由しない(稼働中プロセスのパスで確認)
- 10GB の `claudevm.bundle` は Cowork 隔離モード用の Linux VM。使わなければただ置いてあるだけで、
  アプリ更新時に自動で中身だけ更新される
- Codex とディスク消費が違うのは使用規模ではなく安全装置の設計差:
  Codex = macOS Seatbelt による直接制御(VM なし・ほぼゼロ)、Claude デスクトップ = OS ごと仮想化(最低 10GB 固定)

## 未実施の追加解放候補(武田さん判断待ち)

| 候補 | 期待効果 | リスク |
|---|---|---|
| Mac 再起動 | スワップ約 10GB 回収 | なし |
| ブラウザ・更新系キャッシュ削除 | 数 GB | なし(再生成される) |
| anaconda3(/opt)削除 | 5.5GB | Python 環境を使うなら不可 |
| Spotlight インデックス再構築 | 最大数 GB | 再構築中は検索が弱る |

20% 確保(約 49GB 空)を目指すなら、上記全部+α(Eagle/Notion 側の見直し)が必要。

## 使わなかったもの・落とした情報

なし(調査で捨てた情報はない。root 権限がないため .DocumentRevisions-V100 とスナップショット実サイズは直接測定できず、
スナップショット分は削除前後の差分(+13.7GB)から裏取りした点のみ注記)。

## 変遷

- 2026-08-24: 初版。調査〜解放完了までを記録。
