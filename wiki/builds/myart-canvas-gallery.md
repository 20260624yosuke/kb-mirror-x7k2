---
type: build
name: MY-ART Canvas ギャラリー(最後に開いた見た目のサムネで一覧・軽量ランチャー)
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-14
---

# MY-ART Canvas ギャラリー

## これは何 / 目的

`canvas-reference-tools` プラグイン(v0.5.8〜0.5.15 で追加)内の機能。MY-ART の全 Canvas を
「最後に開いていた窓の見た目」のサムネでグリッド一覧し、クリックでその Canvas を開ける Obsidian
内ビュー。**狙いは、これまでミッションコントロールを「Canvas を一覧して2クリックで飛ぶランチャー」
として使っていたのを、窓とは別の軽い一覧へ移し、Canvas のポップアウト窓を閉じられるようにすること**
(=デスクトップ4のメモリ圧迫の解消)。背景の"なぜ65窓を常時開くか"は [[canvas-idea-cultivation-workflow]]。

## 達成された成果(実機・メモリ実測 2026-07-02)

窓を一部閉じた前後の実測:

- スワップ使用量: **9.75GB → 7.54GB(-2.2GB)**
- Obsidian プロセス: 15個 / 合計RSS 1.24GB → 11個 / 0.34GB(**-0.9GB**)
- 武田さんの体感: ミッションコントロール・Firefox+Xタイムラインの資料保存とも「軽い」。

→ 当初目的(65窓を閉じても軽くなる=本丸)は **達成**。閉じても
[[canvas-idea-cultivation-workflow]] の「閉じると忘れる」が起きないのは、ギャラリーのサムネが
"忘れない受け皿"になり、クリックで即呼び戻せるため。

## 設計の要点(なぜこの作り)

- **サムネ=実描画スナップショット**(Electron `webContents.capturePage(rect)` で描画済み Canvas
  リーフの領域を PNG 化)。JSON から再描画する案は、画像のクロップ/回転/エッジ/テキストを自前で
  再現できず「窓の見た目」と別物になるため不採用。武田さんの要件は「窓の見た目に忠実」。A1 スパイクで
  `electron.remote` 経由で撮れること・メイン窓もポップアウトも撮れること・見た目が一致することを実機確認。
- **既存 `canvas-reference-tools` を拡張**(新規プラグインを立てない)。枠取得・Canvas/リーフアクセス・
  Electron 利用(clipboard 実績)が既存で揃っていたため最小工数。
- **サムネ保存先はプラグイン配下** `.obsidian/plugins/canvas-reference-tools/thumb-cache/`
  (`raw/` は read-only のため絶対に書かない)。`index.json` で `canvasPath → thumbファイル` を管理。
- **対象は MY-ART 全 Canvas**(`raw/_MY_ART/**/*.canvas`。派生の `*__light.canvas` と
  `*.before-light-sync-*.canvas` は除外)。

## 実装(ファイルと役割)

- `src/thumbnail-capture.ts` — 撮影核 `capturePngFromEl`、全撮影 `captureAllOpenMyArtCanvases`、
  単体撮り直し `recaptureSingleMyArtCanvas`、index 読み書き、`isMyArtCanvasPath` / `getCanvasRegionEl`。
- `src/gallery-view.ts` — `ItemView` のグリッド(`MyArtGalleryView`)。更新日の新しい順、blob URL で
  サムネ表示、クリックで Canvas を開く、`refreshThumbnail` で1枚だけ差し替え。
- `main.ts` — view 登録・リボン(画像アイコン)・コマンド(ギャラリーを開く / 全部撮り直す)、
  `active-leaf-change`(離脱時に直前 Canvas を撮り直し)と `window-close`(閉窓時の安全網)への配線、
  `notifyGalleryThumbnailUpdated`(裏の撮影 → 開いている一覧へ即反映)。
- `src/blur-recapture.ts`(v0.5.25 追加) — 窓の `blur`(フォーカス喪失)で可視 MY-ART Canvas を
  撮り直すリスナー。`collectVisibleMyArtCanvasesInDocument`(window-close と共通の可視 Canvas 走査)。
- `styles.css` — グリッドの CSS。

## 現在の使い勝手(2026-07-14)

- 並び替えを **更新順 / 名前順 / 追加日順** で切り替えられる。
- 既定は更新順(新しい順)。アイデアを最近触った順に追う従来運用を崩さず、探す時だけ名前順/追加日順へ切り替える設計。
- 武田さんの実機確認: **「動作良好」**。今回の追加で一覧運用の支障は出ていない。

## 段階と検証状況(すべて武田さん実機確認済み)

- **A1**(実現性スパイク): capturePage で1枚撮って開く。→ 撮影可・忠実・main/popout 両方OK を確認。
- **A2**(本体): 全 Canvas を一括撮影 + グリッド + クリックで開く。→ 71/73 撮影・見た目一致・クリック開き を確認。
- **Phase B**(鮮度自動化): Canvas から離れた瞬間 / 閉じる瞬間に、その1枚を自動撮り直し + 開いている一覧へ即反映。→ 確認。

現行版 **v0.5.15**。`npm run build`(tsc + esbuild)通過・既存ユニットテスト全通過。

## 検証中に見つけて直した不具合(実機デバッグ)

1. **クリックで無関係な Canvas 窓を乗っ取り**(「最初に見つかった canvas リーフ」を無条件採用)→
   既に開いていればその窓を前面に出すだけ、無ければ新規タブ、に修正(v0.5.10)。
2. **一括撮影が非表示リーフを撮って失敗計上**(同じパターン)→ 同一 path の候補から「表示中(サイズが
   出ている)」を優先、に修正(v0.5.10)。
3. **自動撮影は成功していたが一覧が更新されない**(ギャラリーは開いた時しかサムネを読まない設計)→
   撮影成功時に開いている一覧へ通知し、該当サムネ1枚だけ差し替える経路を追加(v0.5.13)。
   ※原因は憶測でなく、全分岐に診断ログを入れて1回の実機ログで特定 → 修正後にログ撤去(v0.5.14)。
4. **サムネが縦長で潰れて見える**(CSS `object-fit: cover` が縦長 Canvas の上下を切り取り)→
   `contain`(全体を縮小して収める・切れない)+ セル高さ引き上げに修正(v0.5.15)。

## 変更履歴

### 2026-07-14 — ギャラリー並び替え(更新順 / 名前順 / 追加日順)を追加、実機確認済み

エスキース一覧の運用改善として、ギャラリーのヘッダーに並び替えセレクトを追加。

- 追加: `更新順` / `名前順` / `追加日順` の3モード。既定は従来どおり更新順(新しい順)。
- 実装: `src/gallery-view.ts` に sort state と比較関数を追加。`name` は昇順、`updated` と `added` は新しい順。
- 追加日の根拠: Obsidian `TFile.stat.ctime` を使用。更新日は既存どおり `stat.mtime`。
- UI: `styles.css` にヘッダー右側のコントロール用スタイルを追加。既存の「全部撮り直す」ボタンと横並び。
- 実機確認: 武田さんが検証し、**「動作良好」** と確認。状態は **実装済み・自動試験済み・ビルド済み・実機確認済み**。

### 2026-07-12 — Eagle 前面化の真因判明: yabai の `focus-eagle-after-obsidian-close` signal(実機確認済み・解決)

v0.5.25(直下のエントリ)は「サムネ自動撮影(`vault.on("modify")`)がタイピング中に走る」ことを
Eagle 前面化の原因と推測して対策したが、**実機検証(前面アプリの時系列ログ採取)で、この推測は
誤りと判明**した。以下、切り分けの経過と真因。

**切り分け手順(すべて実機ログで確認・武田さんに手動タイピングを依頼して検証)**:
1. v0.5.25 適用後もタイピング中の Eagle 前面化は再現 → サムネ撮影は無罪。
2. **打っていない状態(90秒放置)でも Eagle が周期的に前面化** → Canvas 編集/タイピングとは無関係と判明。
3. Eagle の `ai-search` プラグイン(定期同期ログ)を一時無効化しても前面化は再現 → ai-search も無罪。
4. Eagle を完全終了した状態でタイピング → 今度は **Finder** が周期的に前面化(Eagle の代わりに
   直前使用アプリが繰り上がっただけと判明)。Eagle 自体は「とばっちり」で無実と確定。
5. `~/.yabairc` を確認し、真因を特定:

```
yabai -m signal --add \
  label=focus-eagle-after-obsidian-close \
  event=window_destroyed \
  app="^Obsidian$" \
  action="~/bin/focus-eagle-after-obsidian-close"
```

「Obsidian のウィンドウが1つ壊れる(`window_destroyed`)たびに Eagle を前面へ出す」という
yabai 自動処理(コメントによれば、別 Space に Obsidian を閉じた時に Eagle が取り残される問題への
対策として過去に追加されたもの)。Obsidian は Canvas 編集中に内部ウィンドウの生成・破棄を頻繁に
繰り返すため、そのたびにこの signal が発火し、**タイピングに合わせて Eagle が繰り返し前面化**して
いた。放置時にも起きたのは、保存やUI更新等でも同種の破棄イベントが単発で起きるため。

**対処**: 稼働中の yabai から signal を除去(`yabai -m signal --remove
focus-eagle-after-obsidian-close`)し、`~/.yabairc` の該当4行をコメントアウト(削除ではなく、
復活させたい場合は元に戻せる形)。修正後、武田さんの実機確認(タイピング中の観測)で
「症状は出ていない」と報告あり。**実機確認済み・解決**。

**教訓**: canvas-reference-tools 側の変更(v0.5.25)は「他ウィンドウをアクティブにした時にも
サムネ更新」という別要望には有効なため維持するが、Eagle 前面化そのものへの効果は無かった
(原因が全くの別レイヤーだったため)。「症状のタイミングが一致する」だけでは因果を確定できず、
実機ログでの反証(撮影を止めても再現/放置でも再現/Eagle を閉じても再現)まで踏み込んで初めて
真因(yabai signal)に到達した。

### 2026-07-12 — v0.5.25 サムネ撮影のトリガを「保存のたび」から「窓のフォーカス喪失」へ変更(実機確認待ち)

症状(武田さん報告): Canvas ページでテキストを打っていると、タイピングに合わせて Eagle が一瞬
アクティブになる挙動が起きて不快。武田さんの予想は「エスキース一覧のサムネ更新のタイミングで
変な挙動」。

原因(コード確認): `main.ts` に `vault.on("modify")` → 5〜60秒デバウンス後に `capturePage`
(サムネ撮影)を走らせる経路があった。Canvas でテキストを打つと Obsidian が自動保存 → modify
発火 → **タイピング最中に撮影が走る**。武田さんの予想とタイミングが一致。
※「撮影がなぜ Eagle を前面化させるか」の最終的な因果は未確定。まず撮影タイミングを変えて症状が
消えるかで犯人を切り分ける方針。

- 変更1: `vault.on("modify")` 由来の自動撮影経路を削除(`scheduleModifiedMyArtCanvasRecapture` /
  `recaptureModifiedMyArtCanvas` / `clearScheduledThumbnailRecapture` / タイマー用フィールド一式)。
- 変更2: 新規 `src/blur-recapture.ts`。MY-ART Canvas を表示している窓に `blur` リスナーを1個張り、
  **窓がフォーカスを失った瞬間**にその窓の可視 MY-ART Canvas を撮り直す(既存
  `recaptureSingleMyArtCanvas` を流用)。path ごと 3 秒デバウンス。`blur` は bubble しないので
  「窓自身がフォーカスを失った時」だけ発火し、窓内の入力欄の blur では発火しない。
- これで要望②「他のウィンドウをアクティブにした時にもサムネ更新」が同じ実装で同時に満たされる。
- 既存の active-leaf-change(離脱時)/ window-close(閉窓時)撮影は維持。window-close の走査は
  新設の共通関数 `collectVisibleMyArtCanvasesInDocument` に集約。
- 状態: **実装済み・自動試験済み・ビルド済み・実機確認済み(変更自体は動作)**。ただし
  ①Eagle 前面化への効果は無かった(上のエントリの通り、真因は yabai signal 側だったため)。
  ②「他ウィンドウアクティブ時のサムネ更新」自体は実装として維持するが、単体の実機確認は未実施。
- 逃げ道: 変更前ファイル一式を退避済み。

### 2026-07-12 — v0.5.24 診断ログ実機確認 → 開いた瞬間は直った/その後崩れる例が残る(調査保留)

診断ログ(`bounds-debug.log`)を武田さんの実機で採取。結果:

```
apply-immediate: win=popout electronWindow=true setBounds=true
apply-150ms:     win=popout electronWindow=true setBounds=true
check-600ms:     actual x=1280 y=30 w=1280 h=1410 (target 同一)
```

→ **v0.5.23 の setBounds 方式そのものは機能している**(開いた0.6秒後、実測サイズが保存値と完全一致)。
複数回のクリックで再現・一致を確認。

一方、武田さんの実機確認では「まだ崩れることがある/崩れないこともある」との報告。ログの範囲(窓を
開いた瞬間まで)では崩れは記録されず、**「開いた後、別の操作(BTT 配置・ミッションコントロール)を
挟んでから崩れる」**という、そもそもの②の症状と同じタイミングで起きている可能性が高い。つまり①(開く
時のサイズ)と②(開いた後に外部要因で変わる)が、根っこでは同一犯(BTT か Mission Control 側が
Obsidian の別窓に再配置イベントを起こしている)で重なっている疑いが浮上した。

- 再現が安定しない(「なったりならなかったり」)ため、武田さんの判断でこれ以上の実機再現待ちは保留。
- 診断ログ機構(`appendBoundsDebugLog`)は read-then-write を async 排他無しで行っており、短時間に
  連打すると書き込みが競合し合う既知の粗さがある(今回は間隔が空いたため実害なし)。特定用途限りの
  コードなので現状のまま残置。
- 状態: **開いた瞬間のサイズ適用は実機確認済みで機能**。開いた後の外部要因による再変形は**未解決・
  未特定**。次に崩れた瞬間を武田さんが気づいたタイミングで、②用の窓監視(`scratchpad/win-watch.sh`
  方式)を再設置すれば特定できる見込み。無理に頻度を上げて再現を追わない。

### 2026-07-12 — v0.5.23 適用方式を electronWindow.setBounds 直接指定へ変更(実機確認待ち)

v0.5.22 の実機確認で「開く窓のサイズが復元されない」と判明(実機確認1回目失敗)。切り分け:
記録側は成功(window-bounds.json に正しい値)、適用側の `openPopoutLeaf(data)` が効いていなかった。
Obsidian 本体(obsidian.asar)を読むと、初期指定は `window.open` の features 文字列経由で、
この経路は環境により位置・サイズ指定が無視されうる。

- 対策: 窓を開いた直後に、ポップアウト窓が公開する `electronWindow.setBounds()` へ直接
  位置・大きさを指定する方式に変更(`applyBoundsToPopoutWindow`)。生成タイミング対策で
  即時+150ms 後の2回適用。`openPopoutLeaf(data)` の指定も初期値として残す。
- 状態: 実装済み・自動試験済み・ビルド済み・**実機確認待ち**。撤退ラインまで残り1回。

### 2026-07-12 — v0.5.22 ポップアウト窓の位置・大きさを記憶(実機確認1回目失敗→v0.5.23で対策)

症状(武田さん報告): サムネクリックで開く窓が毎回「既定サイズ・画面中央」になり使いにくい。
原因: `openPopoutLeaf()` をサイズ指定なしで呼んでおり、Obsidian 本体は Canvas 単位の窓
ジオメトリを覚えないため。

- 新規 `src/window-bounds.ts`: 窓の位置・大きさを Canvas パス別に
  `.obsidian/plugins/canvas-reference-tools/window-bounds.json` へ保存(raw/ には書かない)。
- 記録タイミング: ポップアウト窓が閉じる時(window-close)と、ポップアウト内の Canvas から
  離れた時(active-leaf-change)。メイン窓は記録しない。幅・高さ 100px 未満は無効値として捨てる。
- 適用: ギャラリーのクリックで `openPopoutLeaf` / `moveLeafToPopout` に保存値
  (`{x, y, size}`)を渡す。記録が無い Canvas は従来どおり既定サイズ。
- 状態: **実装済み・自動試験済み・ビルド済み(tsc+esbuild 通過、既存テスト全通過)・実機確認待ち**。
  位置まで復元されるか(複数ディスプレイ・別 Space)は実機依存で未検証。
- 逃げ道: 変更前ファイル一式を退避済み。2回失敗で v0.5.21 へ戻す(撤退ライン)。

### 2026-07-07 — v0.5.18 サムネクリックをポップアウト窓で開く(実機確認済み)

UX改善要望(武田さん)。クリック時の挙動を「常に別窓」へ統一:

- 閉じている Canvas → `openPopoutLeaf()` で**新しいポップアウト窓**で開く(従来はメイン窓の新規タブ)。
- 既にメイン窓のタブで開いている → `moveLeafToPopout()` で**表示状態ごと別窓へ引っ越す**
  (タブを閉じて開き直す案は、ズーム位置等が失われうるためレビューで差し替え)。武田さん選択の方針。
- 既にポップアウト窓で開いている → 従来通り `revealLeaf` で前面に出すだけ(二重に開かない)。
- 判定はリーフの `containerEl.ownerDocument.defaultView` がメイン `window` と別かどうか。
- 状態: **実装済み・自動試験済み・ビルド済み・実機確認済み**。2026-07-07、武田さんが Obsidian 完全
  再起動後に動作確認し「動作してます」と報告。

## 既知の制限・未実装

- **Phase C 未実装**: Eagle 風のサムネ大小スライダー・並べ替え・フォルダサイドバー。当初から
  「あると便利」の後回し枠。武田さんの Eagle スクショ提供が前提。
- 別 Space(デスクトップ)に置いたままの Canvas 窓は、macOS が裏で描画を止めるため一括撮影で
  「失敗」計上されうる。撮る時は対象が見えている Space に居る前提。**閉じている Canvas は撮れない**
  (=一覧は最後に撮れた状態を保持。編集後は開いて離れれば Phase B で更新)。
- 同一ウィンドウ内のタブ切り替え直後の撮影は、理論上ごく一瞬の入れ替わりを撮る可能性(別々の
  ポップアウトを切り替える通常運用では起きにくい)。

## 関連リンク

- [[canvas-idea-cultivation-workflow]] ― なぜ65窓を開くか / 閉じると忘れる、の背景(この機能が解決する問題)。
- [[canvas-reference-tools]] ― 本機能を載せた自作プラグイン本体。
- [[obsidian-canvas-ui-lightweight-plan-2026-06-26]] ― もう一つの軽量化レバー(`__light`)。役割が別。
- [[window-layout-state-restore]] ― 創作デスクトップに約65窓が常駐する観測と配置復元。
- [[obsidian]] ― 本体(Canvas / Chromium 描画)。
