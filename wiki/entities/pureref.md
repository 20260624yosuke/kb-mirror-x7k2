---
type: entity
name: PureRef
aliases: [purr ref, ピュアレフ]
tags: [tool, reference-management, illustration-workflow, macOS]
sources: [coloso-ye-jji-ch12-color-rough]
---

# PureRef

## 概要

参照画像をフローティングウィンドウ上に貼り付けて一覧管理する macOS / Windows 向け軽量ビュアー。イラストレーター・コンセプトアーティスト・3D アーティストに広く使われている。

## 武田さんの使い方

X 主戦場のキャラ絵制作で資料管理に使用([[takeda-yohsuke]])。複数のラフを **同時並行** で進めるスタイルのため、1 つのラフにつき 1 つの PureRef ウィンドウ(= 1 つの `.pur` ファイル)を立ち上げる。月ごとのフォルダ(例: `2026:05/`)に日付付きの `.pur` ファイル(`2026:05:15_01.pur` 等)を蓄積する運用。`.pur` ファイル群は外付け SSD(`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/02_イラスト/`)上で管理。

## 技術的な内部挙動(2026-05-15 調査)

`.pur` は Qt ベースのバイナリ形式。実行時の挙動として:

- 起動するとファイルを **内部 autosave** にコピーする(保存先: `~/Library/Application Support/PureRef/<hash>_AutoSave.pur`)
- autosave は PureRef が当該ドキュメントを開いている間だけ存在し、**`Cmd+Q` で完全終了すると autosave も削除される**(クラッシュリカバリ用途のみ)
- autosave バイナリ内部には **元 `.pur` のフルパスが UTF-8 文字列として埋め込まれている**(`strings` で確認可能、ただし日本語 UTF-8 部分で `strings` のデフォルト出力は切れるためバイナリ regex 抽出が必要)
- `open -a PureRef <file>`(`-n` なし)で 2 つ目を開くと **既存ウィンドウに上書き** される(マルチドキュメント非対応に近い挙動)
- `open -na PureRef <file>`(`-n` 付き)で初めて新規ウィンドウが立ち上がるが、これは **別インスタンスとして起動** されるため Dock アイコンが増える
- 2026-05-21 追記: PureRef 2.1.2 には `Open files in = Window with scene open` 設定があり、設定ファイル上は `OpenFilesIn=NewOrExisting`。同じ scene は既存ウィンドウを前面へ、違う scene は別ウィンドウへ開く挙動を PureRef 本体に任せられる。

## 既知の制約

- AppleScript dictionary を持たない(`tell application "PureRef"` で直接ウィンドウ情報を取得不可)
- 「前回開いていたファイル群を起動時に復元」する公式機能なし
- macOS 標準の「Reopen windows when logging back in」も効かない
- 1 インスタンスで複数ウィンドウは UI 操作(`Cmd+Opt+Shift+N`)でしか作れない

## 関連

- [[pureref-session-restore]] — 上記制約を回避して再起動後に開いていた `.pur` 群を一括復元する自作の仕組み
- [[pureref-notion-link-workflow]] — Notion から PureRef を開く xcord ランチャーワークフロー

## 出典追加 (2026-05-17): [[ye-jji]] 本人の運用([[coloso-ye-jji-ch12-color-rough]])

ch12 カラーラフの描き方で、講師 [[ye-jji]] が **「QRF」(YouTube 文字起こしの誤変換、正しくは PureRef)** に資料を集めている、と明言。講師の運用は以下:

- 1 枚の絵を描く時に必要な資料群を 1 ボード(1 ファイル)に集める
- ボード内で **用途別に位置をまとめる**(「ここは形の参考」「ここは色合いの参考」)
- 主な収集元: Pinterest / X(Twitter)/ pixiv → 不足は Google 画像検索 + 自分で撮影
- 「ただ綺麗だから集める」NG。**目的を紐づけて保管 → 必要時に開く** = 応用力を決める

これは PureRef を **設計通り**(タグ付き多目的ボード)に使う良い実例で、武田さんの「ラフごとに 1 ボード」運用と方向性が一致する。

## 出典

2026-05-15 の武田さん自身の運用 + autosave バイナリ解析。
2026-05-17 [[coloso-ye-jji-ch12-color-rough]] — ye_jji 本人の運用解説。
