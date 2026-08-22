---
title: Claude handoff - X→Eagle 保存UIの統一 (Stage 2)
date: 2026-06-20
purpose: 別セッションで「拡張ボタンUIを右クリック保存の大窓へ統一する」作業を冷えた状態から始めるための引き継ぎ
---

# Claude 引き継ぎ: X→Eagle 保存UIの統一（Stage 2）

> [!warning] 2026-06-20 更新 — この引き継ぎは保留／一部陳腐化
> - **「拡張ボタンUIの統一（Stage 2）」は見送り**（武田さん「統一はしなくていいのかも」）。
> - 本文中の「現行版 0.5.8」は古い。**実コードの現行版は 拡張 0.5.11 / 補助処理 0.5.9**。
> - 方針転換: TL（一覧）からの**動画保存**を優先。X は動画の右クリックを独自メニュー（「動画のアドレスを
>   コピー」「動画をポスト」）に置き換えるため、右クリックに保存項目は出せない。代わりに X 純正の
>   「動画のアドレスをコピー」を入口にする**クリップボード方式**を v0.5.11 として実装済み。
> - 正本は `wiki/builds/x-eagle-free-save-pilot.md`、経緯は `log.md`。着手時は会話履歴やこの文書ではなく、
>   必ず実ファイルと正本ページをその場で読み直すこと。

## まず最初に

- このフォルダの `AGENTS.md` / `CLAUDE.md` に従ってください。一人称は「私」です。
- **正本優先**: この引き継ぎ文書は 2026-06-20 時点の要約です。着手前に必ず下記の実ファイルを
  その場で読み直し、ファイルの現在の中身だけを根拠にしてください（この文書や会話履歴を正本に
  しない）。
  - 正本ページ: `wiki/builds/x-eagle-free-save-pilot.md`（目的・完成条件・今回やらないこと・現状）
  - 実コード: `tools/x-eagle-save-extension/`（後述の各ファイル）と `tools/x-eagle-video-helper/server.js`
  - 試験: `tools/tests/test_x_eagle_save_extractor.js` / `tools/tests/test_x_eagle_video_helper.js`

## プロジェクト概要

X（Twitter）の投稿画像・動画を、X API を使わず無料で Eagle へ保存するブラウザ拡張機能。
画面に表示されている反応数（いいね・リポスト）を保存時点のスナップショットとして Eagle の注釈へ
残す。現行版は `manifest.json` の `version` が **0.5.8**。0.5.8 までは武田さんの実機確認済み
（最後の自動クローズも 2026-06-20 に良好確認）。

## 今回のゴール（Stage 2 = UI統一）

保存画面が現在 **2種類** あり、使用体験が割れている。これを **1つの保存ウィンドウへ寄せる**。

- 最終ゴール（武田さんと合意済みの「選択肢4」）: **右クリック保存でも拡張ボタンでも、同じ大きい
  保存ウィンドウ（`save.html`）が開く**。その中で、いま開いている投稿単体ページの **画像保存と
  動画保存の両方**を選べる。
- 右クリック保存の既存の使用感は壊さない。
- ねらい: 保存先選択・投稿プレビュー・反応数表示が見やすい右クリック保存UI（`save.html`）へ統一し、
  小さいポップアップUIをなくす。

Stage 1（`save.html` の公式風レイアウト・サイズ・中央表示）は 0.5.5〜0.5.6 で完了済み。今回は Stage 2。

## 確定した方針（2026-06-20 武田さん回答）

- **A. 統一先＝右クリックで開く別ウィンドウ `save.html`（本命）。** 拡張ボタンを押すと出る貼り付き型の
  小パネル `popup.html` は廃止し、その機能（特に動画保存）を `save.html` へ移す。**右クリックで
  できることを最大化**する（投稿単体ページなら、画像を右クリック→大窓で画像も動画も選べる形）。
  拡張ボタンは、右クリックで届かない場合（例: 画像が無い"動画だけ"の投稿）の入口としてだけ残すか
  消すかを実装時に詰める。＝統一の向きは「小パネル→大窓」。逆ではない。
- **B. フォルダ指定はどの入口でも必須**に統一する（ツールバー由来でも必須。未選択では保存不可）。
- **C. 今回は投稿単体ページで画像＋動画を `save.html` に統合するところまで。** TL（投稿の一覧画面）
  からの直接の動画保存は、**“実現性の小さな確認”だけ**行う（UIは作らない＝実現性ゲート方式）。
  本実装をやるかは確認結果を見て別途判断する。

## 現状: 2つの保存画面と機能の所在（ここが統一の核心）

### A. 拡張ボタンの小窓 — `popup.html` / `popup.js`（430px）

- `manifest.json` の `action.default_popup = "popup.html"` で開く。
- **動画保存ロジックを持つのはこちらだけ。** `popup.js`:
  - `VIDEO_HELPER_BASE_URL = "http://127.0.0.1:41795"`、ヘッダー認証（`x-eagle-video-helper`）
  - `checkVideoHelper()` = `GET /health` で補助処理の起動確認、`scheduleVideoHelperChecks()` で定期確認
  - `saveVideoSnapshot()` = `POST /save-x-video`
  - `getActiveTab()` / `extractSnapshot()` / `sendSnapshotRequest()` = **いま開いているタブの投稿**を
    content script 経由で抽出（右クリックの srcUrl 起点ではない）
  - `saveSnapshot()` = 画像保存、`renderImageSelection()` = 保存候補チェック
- 画面構成: status / 保存候補（チェック式）/ 保存理由＋chips / フォルダ検索・一覧 /
  `helperStatus`（動画補助の状態）/ 「画像を保存」「動画を保存」の2ボタン。

### B. 右クリックの大窓 — `save.html` / `save.js`（560×620、左プレビュー180px）

- `background.js` の `openSaveWindow()` が `save.html?contextId=...` を **popup ウィンドウ**として
  ブラウザ中央に開く。
- スナップショットは storage に `contextId` で保存され、`save.js` の `contextIdFromUrl()` /
  `loadContext()` が読む。
- フォルダUIが充実: `selectTopCandidate()` / `moveActive()`（↑↓）/ `Enter`保存 /
  `recentFolders()` / `createNewFolderOption()` / `openCreateFolderDialog()`。
- `closeAfterSuccessfulSave()` あり、保存候補チェックあり。
- **動画ボタンは無い（画像保存のみ）。**

### C. 振り分け役 — `background.js`

- 右クリックメニュー「X画像をEagleへ保存...」→ `handleContextMenuClick()` →
  `requestContextSnapshot()`（右クリック画像起点）→ `storeContext()` → `openSaveWindow(contextId)`。
- **ツールバーボタン用の `action.onClicked` リスナーは未実装**（今はボタン＝`default_popup`）。
- `extensionApi = browser || chrome` で Firefox/Chrome 両対応。

### D. 共通層 — `eagle-save.js`

- Eagle ローカルAPI（フォルダ一覧 / `addFromURL` / `folder/create`）と注釈生成。
  `popup.js` と `save.js` の両方が利用。基本そのまま使える想定。

### E. 動画補助処理 — `tools/x-eagle-video-helper/server.js`

- 手動起動（`start.command`）。`127.0.0.1:41795` 限定、拡張機能由来ヘッダーで許可、
  `GET /health` と `POST /save-x-video` のみ。`yt-dlp --cookies-from-browser firefox` で取得。

## 統一の実装方針（着手時に実ファイルを読んで最終判断）

1. **manifest.json**: `action.default_popup` を外す（または最小化）→ `action.onClicked` が発火する
   ようにする。`host_permissions` には既に `127.0.0.1:41795` が入っている。`version` を **0.6.0** へ。
2. **background.js**: `extensionApi.action.onClicked` リスナーを追加。クリック時に、いま開いている
   タブの投稿を抽出（`popup.js` の active-tab 抽出経路を流用）し、`source: "toolbar"`（=投稿単体
   ページ・動画可）の印を付けた context を `storeContext()` → `openSaveWindow()`。既存の
   `executeContentScripts()` / `storeContext()` / `openSaveWindow()` を再利用する。
3. **save.html**: 「動画を保存」ボタンと `helperStatus` を縦一直線レイアウトへ追加（`popup.html`
   から移植。明るいテーマ・既存構造は維持）。
4. **save.js**: `popup.js` の動画ロジック（helper URL・ヘッダー・`checkVideoHelper` /
   `scheduleVideoHelperChecks` / `saveVideoSnapshot` / `videoErrorDetails`、ボタン有効化）を移植。
   **動画ボタンは context が toolbar 由来かつ動画ありのときだけ表示・有効化**。右クリック由来の
   ときは出さない（動画のTL右クリックは非対象のため）。画像保存とフォルダUIは既存のまま。
5. **popup.html / popup.js**: 統一後の扱いを決める（下記「判断が要る点」A）。
6. **tests**: `test_x_eagle_save_extractor.js` に「ツールバー→`save.html`」経路と `save.html` の
   動画ボタン出し分けのアサーションを追加。`test_x_eagle_video_helper.js` は回帰として維持。

### 実装時に詰める細部（方針は上の「確定した方針」で確定済み）

- 動画ロジック（`popup.js` の helper 接続・`saveVideoSnapshot` 等）は `save.js`（または共通モジュール）
  へ寄せる。`popup.html`/`popup.js` の貼り付きUIは廃止。
- 投稿単体ページで画像が無い"動画だけ"の投稿をどう拾うか（右クリック対象の画像が無い場合の入口）。
  拡張ボタンを最小限の入口として残す案が単純。
- 動画ボタンの表示条件: 投稿単体ページ かつ 動画あり かつ 補助処理が起動中。TL・メディア欄・
  一覧から開いた大窓では出さない。
- フォルダ必須化（B）: 現状ポップアップは任意・右クリックは必須。統一後はどちらの経路でも必須にする。

## TL動画保存の実現性確認（今回の小タスク・UIは作らない）

C の方針に基づき、TL から動画を保存できるかを**小さく確認するだけ**。拡張機能やUIには手を入れず、
yes/no を根拠付きで出す。本実装は結果を見て別プロジェクトで判断する。

- 確認したい点: TL上の動画（インライン再生のポスト）を起点にして、(1) その投稿の status URL を特定
  できるか、(2) 既存の動画補助処理と同じ `yt-dlp --cookies-from-browser firefox <status URL>` で MP4 を
  取得できるか、(3) 反応数・本文を TL の DOM から読めるか。
- 参考（過去の実現性ゲート v0.4.0）: `https://x.com/<id>/status/<id>` から yt-dlp で MP4 取得 →
  Eagle `/api/item/addFromPath` で保存できることを 1 本で確認済み。今回はその手前の「TL起点で status URL
  を取れるか・動画要素をどう特定するか」に絞って確認する。
- 出力: 取れた/取れない・壊れやすさ・必要な追加調査、を正本 `wiki/builds/x-eagle-free-save-pilot.md`
  へ短く記録（実装はしない）。

## 完成条件

1. 拡張ボタンを押すと、貼り付き型パネルではなく `save.html` の大窓が開く（`popup.html` の貼り付きUIは廃止）。
2. その大窓で、いま開いている投稿単体ページの **画像保存と動画保存の両方**ができる
   （動画は補助処理が起動しているとき）。
3. **どの入口から保存してもフォルダ指定が必須**（フォルダ未選択では保存ボタンが押せない）。
4. 右クリック保存（TL・メディア欄・拡大表示）の画像保存は従来どおり。TL等から開いた大窓では
   動画ボタンを出さない。
5. **TL動画保存の実現性確認**（上記の小タスク）の結論が、正本へ根拠付きで記録されている（本実装は不要）。
6. 自動試験（下記）と、武田さんの実機確認（下記の方式で）が通る。
7. 正本 `wiki/builds/x-eagle-free-save-pilot.md`・`index.md`・`log.md` を更新。

## 今回やらないこと（範囲を広げない）

**TL動画保存の本実装・UI追加**（今回は実現性の確認だけ。作り込みは結果を見て別プロジェクト）、
Chrome動画保存、複数動画対応、補助処理の常駐化、自動フォルダ分類、作者・キャラの確定タグ付け、
既存Eagle公式拡張の改造、X API利用、複数フォルダ同時所属、画像サムネイル付き最近保存項目。
**UI統一＋保存導線の整理＋TL動画の実現性確認まで**に絞る。

## 注意・落とし穴

- **動画保存＝投稿単体ページのみ。** TL・メディア欄・一覧・Chrome動画は対象外。右クリックで
  `save.html` を開いた場合は動画ボタンを出さないこと。
- 補助処理は手動起動。`GET /health` の起動確認表示（「動画補助: 起動中」）を `save.html` へ持って
  いき、未起動なら動画ボタンは無効。
- Firefox/Chrome 両対応の `browser || chrome` パターンを保つ。Firefox は一時アドオン再読み込みで反映。
- `contextId` はワンショット（`storage.session` 優先）。ツールバー経路でも同じ contextId 方式で渡す。
- スナップショット形（`imageUrls` / metrics / postText / 動画有無）を、右クリック・ツールバーの
  両経路で `save.js` が同じに扱えるようにする。
- 保存成功後の自動クローズ（`closeAfterSuccessfulSave`）は統一後も維持。

## 検証のしかた（AGENTS.md の前面GUI規則に従う）

実機確認が必要。自動操作の同意だけを求めず、先に次の3択を提示し、精度・負担・画面占有・危険性の
違いと推奨を短く説明すること（反復・客観・可逆でなければ共同確認を推奨）。

1. 武田さんが手動確認して結果を共有
2. 私が前面GUIを自動操作
3. 私が短い手順を案内 → 武田さんが操作 → 私が結果を解析（**推奨**）

実機チェック項目（Firefox 一時アドオンを 0.6.0 へ再読み込み後、実投稿で）:
- ツールバーボタンで大窓が開く / 画像保存できる / （補助処理起動時）動画保存できる /
  右クリック保存が従来どおり / 動画ボタンが右クリック時に出ない。

自動試験（報告前に私が実施）:
- `node tools/tests/test_x_eagle_save_extractor.js`
- `node tools/tests/test_x_eagle_video_helper.js`
- `node --check` で `background.js` / `save.js` /（残すなら）`popup.js` / `eagle-save.js`
- `manifest.json` の JSON 検証（version 0.6.0）

## 着手前にその場で読むファイル（正本優先・冷えた状態の最初の一手）

```
wiki/builds/x-eagle-free-save-pilot.md
tools/x-eagle-save-extension/manifest.json
tools/x-eagle-save-extension/background.js
tools/x-eagle-save-extension/popup.html
tools/x-eagle-save-extension/popup.js
tools/x-eagle-save-extension/save.html
tools/x-eagle-save-extension/save.js
tools/x-eagle-save-extension/eagle-save.js
tools/x-eagle-save-extension/extractor.js
tools/x-eagle-save-extension/content-script.js
tools/x-eagle-video-helper/server.js
tools/tests/test_x_eagle_save_extractor.js
```

## 短縮版の依頼文（別セッションへ貼る用）

このフォルダの AGENTS.md に従ってください。一人称は「私」です。

X→Eagle 保存拡張機能（`tools/x-eagle-save-extension/`、現行 0.5.8）の **UI統一（Stage 2）** を
進めます。いまは保存画面が2種類あります。①拡張ボタンの小さいポップアップ（`popup.html`/`popup.js`、
**動画保存はここだけ**）②右クリックの大窓（`save.html`/`save.js`、画像のみ・フォルダUIが充実）。
ゴールは、**拡張ボタンでも右クリックでも同じ大窓 `save.html` が開き、その中で画像保存と動画保存の
両方を選べる**ようにすること。右クリック保存の使用感は壊さない。動画は投稿単体ページのときだけ。
正本は `wiki/builds/x-eagle-free-save-pilot.md`、詳しい引き継ぎは
`claude-handoff-x-eagle-ui-unification.md` です。まず両方と上記の実コードをその場で読み、実装方針を
確認してから着手してください。方針は確定済みです（A: 統一先は右クリックの大窓 `save.html`、貼り付き型
`popup.html` は廃止／B: フォルダ指定は必須／C: 今回は投稿ページで画像＋動画統合まで、TL動画は実現性の
小さな確認だけでUIは作らない）。実装中の細部（"動画だけ"の投稿の入口など）だけ必要時に確認してください。
