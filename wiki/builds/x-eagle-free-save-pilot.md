---
type: build
sources:
  - eagle-save-script-use-cases-2026-06-17
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-03
---

# X→Eagle無料保存パイロット

## 目的

X の画像付き投稿を Eagle へ保存するとき、X API を使わず無料で、保存時点の画面表示情報を
Eagle 注釈へ残す。

## 完成条件

- Chrome または Firefox の拡張機能ボタンから、現在開いている X 投稿ページを読む。
- 投稿内画像 URL、投稿 URL、作者 ID、保存日時、画面表示上の反応数を取得する。
- Eagle のローカル API へ画像を送り、注釈にスナップショットを残す。
- 保存理由を一言だけ任意入力できる。
- 保存時に Eagle のフォルダを検索し、1つ指定できる。

## 今回やらないこと

- X API の有料利用。
- 自動フォルダ分類。
- 作者・キャラ・作品の確定タグ付け。
- 既存 Eagle 拡張機能への後付け改造。
- 数値の完全精度保証。
- Firefox アドオンの公開ストア掲載。
- Mozillaアカウント作成・署名鍵の発行代行。
- 保存時の複数フォルダ同時所属。

## 経緯（2026-06-17時点）

- 保存スクリプト案として、X 画像を Eagle へ保存する時点の投稿URL・作者ID・保存日時・
  いいね数・リポスト数・引用数・表示数を画像情報と一緒に残す用途を検討した。
- X API（X が正式に提供するデータ取得口）は従量課金のため、最初の方針は無料パイロットとして、
  Chrome拡張機能で画面上の数字を読む方式にした。正確な数値保証よりも、保存時点のメモ価値を優先。
- v0.1系で基本保存は動き、ユーザー実機で「いい感じ」と評価あり。ただし、複数画像投稿で
  4枚中すべてに同じ情報を残せない問題が出たため、複数画像保存と保存後確認を強化する必要が出た。
- 公式Eagle拡張機能に近いUIとして、保存時にEagleフォルダを検索して指定できる機能を追加した。
- FirefoxでXを見ている運用に合わせるため、同じ拡張機能をFirefox一時アドオンとして読み込む方針にした。
  ただしFirefoxはChromeと拡張機能API（ブラウザ拡張が使う機能の入口）の挙動が違い、複数回の修正が必要になった。
- Firefox v0.2.1 は `snapshot is undefined` で失敗し、v0.2.2 で content script
  （Xページ側に置く読み取り係）と message passing（拡張機能内の通信）方式へ変更した。
- Firefox v0.2.2 は `投稿読み取り処理を初期化できませんでした` で失敗し、v0.2.3 で抽出APIを
  `globalThis` と `window` の両方へ配置する修正を入れた。
- Chrome側は v0.2 系の途中で `Failed to fetch` により保存できない退行が出たため、v0.2.3 で
  主経路を Eagle 側画像URLダウンロードへ戻した。ユーザー実機で Chrome は「いい感じ」と再確認済み。
- Firefoxは v0.2.3 で投稿読み取り・フォルダ選択までは進んだが、保存時に
  `NetworkError when attempting to fetch resource` で失敗。v0.2.4 で EagleローカルAPIへの
  `POST` から `Content-Type: application/json` を外し、Firefox保存通信の改善を試みた。
- v0.2.5 で、Eagleメモ欄の視認性改善として、文面は変えずに `取得方法` と反応数
  （いいね・リポスト・引用・返信・表示）を注釈の一番上へ移動した。
- v0.3.0 で Chrome 先行の TL 右クリック保存を追加した。投稿ページを開かず、TL 上の画像を
  右クリックして `X画像をEagleへ保存...` から保存用小窓を開く。小窓では保存先フォルダ選択を
  必須にし、既存の投稿ページ用ポップアップ保存はフォルダ任意のまま維持した。
- 2026-06-17、ユーザー実機で画像1枚投稿の TL 右クリック保存が動き、Eagle保存結果の
  ファイルパスを確認した。保存小窓では、公式UIに近い補助として「最近使ったフォルダ」を
  出す方針に絞った。
- v0.3.1 で、右クリック保存小窓の右側上部に「最近使ったフォルダ」を5件表示するようにした。
  画像サムネイル付きの最近保存項目は用途が曖昧なため入れない。
- 2026-06-17、ユーザーが v0.3.1 を検証済み。別チャットへ引き継ぐため、現行状態と未確認点を
  このページへ固定する。
- v0.3.2 で、FirefoxでもChrome版と同じTL右クリック保存の使用感を目指し、Manifest V3
  （拡張機能設定形式の第3版）の `background` を Chrome/Firefox 両対応形へ変更した。
  さらに保存画面では、保存先表示・保存ボタン・状態表示を候補フォルダ一覧から独立させ、
  候補一覧だけがスクロールする構造へ変更した。
- 2026-06-17、ユーザーが v0.3.2 を実機検証し、使用感は「いい感じ」と報告した。
  Eagle保存完了までの明示確認はこの時点の記録には含めない。
- v0.3.3 では、ユーザー要望により、保存小窓のサムネイルをクロップ（端が切れて見える表示）から
  画像全体が見える `contain` 表示へ変更し、画像の縦横比をプレビュー枠にも反映するようにした。
  さらに検索語から Eagle の新規フォルダを作成し、作成後そのフォルダを保存先に選ぶ機能を追加した。
- 複数フォルダ同時所属は、同じ右クリック操作で同一投稿内の複数画像を一括保存する使用感と
  バッティングするため、2026-06-17時点では保留にする。
- v0.4.0 では、X動画をEagleへ保存するパイロットを追加した。初回はFirefoxの投稿単体ページから
  拡張機能ボタンを押し、手動起動の `tools/x-eagle-video-helper/` が `yt-dlp`
  （X投稿URLから動画を取得するコマンドラインツール）で動画1本を一時取得し、EagleローカルAPI
  `/api/item/addFromPath` へ保存する。TL右クリック動画保存、Chrome動画保存、複数動画、GIF風投稿の
  個別判定は初回対象外。
- 2026-06-17、ユーザーがFirefoxで v0.4.0 相当を検証し、サムネイル表示は良好と報告した。
  ただし新規フォルダ作成後、そのフォルダへ画像保存できないバグがあった。
- v0.4.1 では、v0.4.0 の動画保存パイロットを維持したまま、右クリック画像保存小窓だけを修正した。
  新規フォルダ作成後、Eagle側のフォルダ一覧へ反映されるまで短く待ち、作成APIの戻り値にIDがある場合は
  そのIDを保存先として保持してから画像保存へ進む。
- 2026-06-17、ユーザーがFirefoxで v0.4.1 を検証し、新規フォルダ作成後、そのフォルダへの画像保存まで
  成功したと報告した。
- 2026-06-17、ユーザーがFirefoxで動画保存を試し、拡張機能側に `Eagle API HTTP 500` が表示された。
  補助処理の起動とトークン入力は成功していたため、動画取得後にEagle `/api/item/addFromPath` へ渡す段階の失敗と判断。
- v0.4.2 では、動画補助処理の一時保存先をNode.js標準の `/var/folders/...` ではなく、検証済みの `/tmp`
  に固定した。さらに Eagle 失敗時に、一時動画パス・Eagleへ渡したファイルパス・保存先ID・Eagle応答本文を
  拡張機能側へ返すようにした。
- 2026-06-18、ユーザーが v0.4.2 相当で動画保存を検証し、動画本体とメタデータ抽出ができていると報告した。
- v0.5.0 では、ポップアップから動画補助トークン入力欄を外した。補助処理側は `GET /health` で起動確認を返し、
  拡張機能由来の専用ヘッダーと拡張機能オリジン（呼び出し元）を確認する。さらに
  `tools/x-eagle-video-helper/start.command` を追加し、起動時の手打ちコマンド負担を下げた。
- v0.5.1 では、裏起動時に `yt-dlp` を見つけられず `spawn yt-dlp ENOENT` で失敗する問題を修正した。
  Homebrew の標準場所 `/opt/homebrew/bin` と `/usr/local/bin` を補助処理側の実行PATHへ追加し、
  `yt-dlp` の実パスを `/health` と起動ログで確認できるようにした。
- 2026-06-18、ユーザーが v0.5.1 を検証し、動画保存自体は良好だが、Eagleに保存できているのに
  拡張機能側が失敗表示になるケースを報告した。
- v0.5.2 では、Eagleメモ欄の先頭を保存時UIに近い整理済み表示へ変更した。さらに動画保存で
  Eagle `/api/item/addFromPath` が失敗応答を返しても、同じ投稿URL・保存先・ファイル名の動画が
  Eagle内に作られていれば成功扱いへ戻す補正を追加した。
- v0.5.3 では、Xのメディア欄（プロフィールの「メディア」タブの一覧）や、画像を開いた拡大表示から
  画像を右クリックしても保存できないバグを直した。これらの画面では画像が投稿のかたまり（`<article>`）の
  外にあるため、従来は「右クリックした画像の投稿を特定できませんでした」で止まっていた。`<article>` が
  見つからないとき、ページURL（`/status/投稿ID/photo/N`）またはクリック画像を包むリンクの `href` から
  投稿を特定し、クリックした画像1枚を保存するフォールバックを追加した。拡大表示では横の投稿パネルから
  反応数も読む。一覧サムネを直接右クリックした場合は反応数が画面に出ないため空欄になる。
- v0.5.4 では、右クリック保存小窓のフォルダ選択をキーボード中心に変えた。小窓を開くと検索欄へ
  自動でカーソルが入り、すぐ入力できる。フォルダ名を打つと一致候補の先頭が自動で選択状態になり、
  `Enter` で即保存、`↑`/`↓` で選び直せる。検索欄が空のあいだは、開いた直後の誤 `Enter` 保存を
  避けるため自動選択しない（先頭候補が「選択された状態」になるのは入力後）。マウスでのクリック保存は
  従来どおり。`save.html` / `save.js` の変更で、extractor・動画補助処理・注釈形式は変えていない。
- v0.5.5 では、右クリック保存小窓のレイアウトを公式 Eagle 拡張の保存パネルに近づけた。中央にあった
  「保存先＋保存ボタン」の帯を廃止し、`save.html` の右カラムを「検索 → フォルダ一覧 → （最下部に
  保存先＋保存ボタン）」の縦一直線に並べ替えた（スクロールするのはフォルダ一覧だけ）。視線が上から
  下への一方向になり、フォルダ選択中に保存ボタンへ目を戻す動きをなくす狙い。明るいテーマ・中身・
  キーボード操作（v0.5.4）は維持し、`save.js` は変更していない（要素IDを保ち、DOM並べ替えとCSSのみ）。
- 2026-06-19、ユーザーが v0.5.5 を実機検証し、動作確認済みと報告した。
- v0.5.6 では、ウィンドウサイズを公式 Eagle 拡張の保存パネルに近い小ぶりな比率へ変更した。
  `background.js` の定数を 920×680 → 560×620 に縮小し、左プレビュー欄を 250px → 180px に
  細くした。プレビュー画像の最大高さ、投稿テキスト表示域、コメント欄、フォルダ候補行の余白も
  小さめに調整し、狭いウィンドウ内で情報が収まるようにした。さらにユーザーの指摘により、
  保存小窓の表示位置をOS既定（左上隅など）から、今見ているブラウザウィンドウの中央に重ねて
  表示するよう変更した。`windows.getCurrent` で現在のウィンドウ位置・サイズを取得し、
  `windows.create` に `left`/`top` を渡す。取得失敗時はOS既定へフォールバック。
  `save.js` は変更していない。
- v0.5.7 では、複数画像投稿の保存を「全画像を初期保存候補にし、不要な画像だけチェックを外す」
  方式へ変更した。通常ポップアップと右クリック保存小窓の両方に `保存候補` 一覧を追加し、
  初期状態では全画像をチェック済みにする。チェックを外した画像は `saveSnapshotToEagle()` へ渡す
  `imageUrls` から除外される。2枚目だけを保存した場合も、元投稿内の画像番号を保ち、ファイル名と
  注釈は `画像 2/2` のように残す。
- 2026-06-20、ユーザーが v0.5.7 の保存候補チェック除外を実機検証し、動作良好と報告した。
  検証証跡としてスクリーンショット
  `/Users/takedayousuke/Library/Mobile Documents/com~apple~CloudDocs/ダウンロード/02_スクショ保存/ 2026-06-20 0.02.10.jpg`
  が提示された。
- v0.5.8 では、保存成功後に保存画面を自動で閉じるようにした。右クリック保存小窓の画像保存、
  投稿ページポップアップの画像保存、投稿ページポップアップの動画保存の3経路が対象。保存失敗時は
  エラー確認と再試行のため閉じない。成功メッセージを表示してから短い待ち時間後に `window.close()` を
  呼ぶ。
- 2026-06-20、武田さんが動画保存で「動画補助: 未起動」と表示されて保存できない事象を報告。切り分けの結果、
  コード不具合ではなく、手動起動の補助処理プログラムが起動していなかったため(Mac再起動や端末ウィンドウを
  閉じると止まる)。補助処理を起動したら動画保存に成功することを武田さんが実機確認した。
- v0.5.9 では、上記の切り分けで判明した「動画保存まわりの2つの実害」を直し、再発防止として自動起動を追加した。
  - **Eagleには保存できているのにUIが「失敗」と表示される不具合**: 補助処理は Eagle `/api/item/addFromPath` で
    保存が成立した後に `/api/item/info` で確認していたが、動画はEagle側の取り込み・サムネイル生成が遅く、
    約6秒の確認待ちに間に合わないと丸ごと「失敗」を返していた。`waitForEagleItem()` を、確認できなくても
    例外にせず `null` を返す best-effort 方式へ変更(待ちは20回×0.5秒へ延長)。`buildVideoSaveResult()` を追加し、
    `addFromPath` が item ID を返した時点で保存成功として返す。Eagle確認が取れたときだけ一時ファイルを消す
    (`confirmed` フラグ)。2026-06-20、武田さんが「Eagleに保存済み・UIは失敗表示」を実機で再現報告し、本修正の対象とした。
  - **未起動表示の紛らわしさ**: ポップアップ上部の緑枠が、補助処理が未起動でも常に「動画: 補助処理で取得を
    試せます」と出て、下の赤い「動画補助: 未起動」と矛盾していた。緑枠から動画の行を外し、未起動表示を
    「動画補助: 未起動（動画保存には起動が必要）」へ変更した。
  - **動画補助処理のログイン時自動起動(再発防止)**: Mac ログイン時に補助処理を自動起動する LaunchAgent
    `com.takedayousuke.x-eagle-video-helper` を追加した。これで「未起動で動画保存できない」を原則なくす。
    手動起動の `start.command` は残す。常駐化はこれまで非対象だったが、2026-06-20 に武田さんが自動起動の追加を承認した。
- 2026-06-20、武田さんが v0.5.9 を実機検証。動画保存で Eagle に保存済みかつポップアップも「保存しました」と成功表示に
  なることを確認（以前の誤った失敗表示は解消）。ログイン時自動起動（LaunchAgent）は武田さんが Mac 未再起動のため未検証。
- 2026-06-20、武田さんが画像の複数保存で報告: **Eagle が重複（同名画像）を検知して「重複追加の警告」ダイアログを出すと、
  拡張機能が固まり、ダイアログを処理するとエラー表示になる**（成功 2/4、重複2枚が「Eagle内で … を確認できませんでした」で
  失敗表示）。公式拡張は重複でもワークフローが切り離されて完了する。要望は「重複処理と拡張機能のワークフローを切り離す」。
  画像の抽出・フォルダ振り分け自体は機能している。
- v0.5.10 では、画像保存（`popup.js` と右クリック保存 `save.html` の両経路が使う `eagle-save.js`）から**保存後の存在確認
  （item/list ポーリング）を廃止**した。`/api/item/addFromURL` を Eagle が受け付けた時点で成功とみなし、Eagle の重複
  ダイアログなど事後処理を拡張機能のワークフローから切り離す。これで重複時の「固まる・誤って失敗表示・二重追加」を解消。
  フォールバック（ブラウザ側取得）は主経路の add 自体が失敗したときだけにし、確認失敗では起こさない（二重追加防止）。
  トレードオフ: 画像URLが無効などで Eagle 側が静かに取り込み失敗するケースは成功表示になりうる（X 画像URLでは稀）。
- 2026-06-20、メディア欄からの複数画像一括取得（投稿ページを開かず全画像を取る案）について、シームレス方式
  （裏で投稿ページを読み、必要なら一瞬前面に出して全画像を読む）の実現性を検討した。投稿ページからの全画像取得は
  既存の保存候補機能で実証済みで、裏タブの描画不確実性は「必要なら一瞬だけ前面」で回避可能と整理（実現性は高いが
  実機ゲートは未通過）。ただし武田さんが ChatGPT 提案の外部拡張 **Control Panel for Twitter**
  （`Hide retweets in user profiles` を有効化）を導入し、プロフィールの「ポスト」欄からリポストを隠して本人の
  画像付き投稿を追いやすくしたため、当面はポスト欄＋既存の保存フロー（投稿ページで全画像が保存候補に出る）で
  運用回避できると判断。**メディア欄シームレス取得（②）は実装せず保留**。再度必要になれば実現性ゲートから着手する。
  Control Panel for Twitter は第三者製の外部拡張で本プロジェクトのツールではない（user-stated。相談記録は添付
  ChatGPT ログ `~/Downloads/x-profile-hide-reposts.md`）。
- 2026-06-23、Firefoxの一時アドオンは再起動で外れるため、常用インストール用に v0.5.13 を追加した。
  保存ロジックは変えず、`manifest.json` に Firefox 用の固定 add-on ID（拡張機能ID）、
  `strict_min_version`、データ送信申告を追加した。さらに署名なし `.xpi` を作る
  `scripts/build-firefox-xpi.sh` と、addons.mozilla.org の API credentials（署名用の鍵）を使って
  unlisted（公開一覧に載せない自己配布）署名を行う `scripts/sign-firefox-xpi.sh` を追加した。
  署名コマンドは、手元の Node.js 25 系で `web-ext` が `Bus error` になるため、Node.js 22/24 系を優先して使う。

## 現在の状態（2026-06-24時点）

- 実装済み: Chrome/Firefox共通の拡張機能、Eagleフォルダ選択、複数画像保存、保存後確認、
  画面表示からの反応数取得、注釈上部への反応数表示、Chrome向けTL右クリック保存、
  Firefox向けTL右クリック保存の土台、フォルダ必須のEagle風保存小窓、右クリック保存小窓の
  最近使ったフォルダ5件表示、保存操作パネルの独立表示、サムネイルの画像全体表示、
  検索語からの新規Eagleフォルダ作成、Firefox投稿単体ページからのX動画1本保存パイロット、
  手動起動の動画補助処理、動画補助処理の起動確認表示、Token入力不要の動画保存呼び出し、
  `start.command` による補助処理起動、Eagleメモ欄の先頭整理、動画保存後の誤った失敗表示補正、
  メディア欄・拡大表示からの右クリック保存（`<article>` 不在時のフォールバック）、
  右クリック保存小窓の検索欄自動フォーカス・入力後の先頭候補自動選択・`↑`/`↓`/`Enter` キーボード操作、
  右クリック保存小窓の公式風レイアウト（検索→一覧→最下部の保存先＋保存ボタンの縦一直線）、
  公式風の小ぶりなウィンドウサイズ（560×620、左プレビュー180px）、
  保存小窓のブラウザウィンドウ中央表示、複数画像投稿の保存候補チェック除外、保存成功後の自動クローズ、
  動画保存の確認未了でも成功扱い（Eagleに保存済みなのにUIが失敗と出る不具合の解消）、未起動表示の明確化、
  動画補助処理のログイン時自動起動（LaunchAgent）、画像保存をEagleの重複ダイアログから切り離し
  （保存後の存在確認を廃止し、重複でも固まらず・誤って失敗表示・二重追加にしない）、
  TL一覧からの動画保存（X純正「動画のアドレスをコピー」→拡張ボタンのクリップボード方式）、
  拡張ボタンのポップアップのフォルダ選びを右クリック画面の使い勝手にそろえ（最近フォルダ・キーボード操作・新規フォルダ作成・0.5.12）、
  Firefox常用インストール用の固定 add-on ID、データ送信申告、署名なし `.xpi` 作成スクリプト、
  AMO（addons.mozilla.org、Firefox拡張の署名を行うMozillaのサイト）unlisted（公開一覧に載せない自己配布）署名スクリプト（0.5.13）、
  Firefox自動更新用の `update_url` 設定スクリプト、更新情報JSON生成スクリプト、署名から配布フォルダ生成までのreleaseスクリプト、
  GitHub Pages向けGit公開補助スクリプト、
  今後保存する画像・動画の Eagle メモ欄を `【見る用】` と `【LLM用】` に分ける注釈形式（0.5.14）、
  クリップボードにコピーしたTikTokなどの外部動画URLを同じEagleフォルダ選択画面から保存する経路（0.5.15）。
- 自動試験済み: URL・画像URL・`srcset`・反応数テキストの基本抽出、`manifest.json` JSON検証、
  `popup.js` / `extractor.js` / `content-script.js` 構文確認、TL風HTMLでの右クリック画像起点抽出、
  フォルダ必須保存の拒否、Firefox想定の `browser` API（拡張機能がFirefoxで使う呼び出し口）で
  右クリック保存入口から保存小窓を開く処理、新規フォルダ作成APIの送信内容、
  保存画面のサムネイル全体表示・新規フォルダ作成UI構造、新規フォルダ作成後に
  保存先フォルダIDを確認してから画像保存へ進む処理、動画補助処理のURL検証・注釈生成・
  `yt-dlp` コマンド生成・Eagle `/api/item/addFromPath` payload（送信内容）生成、
  動画補助処理の `/health` 起動確認、拡張機能由来ヘッダーによる呼び出し許可、
  Eagle保存済み動画を `item/list` で再確認して成功扱いへ戻す補正、保存候補チェック後の
  対象画像URL絞り込み、2枚目だけ保存する場合に元番号 `02` と注釈 `画像 2/2` を保つ処理、
  保存成功時の `window.close()` 呼び出し、Firefox用 manifest の `browser_specific_settings.gecko`、
  署名なし `.xpi` の生成、v0.5.14 の `【見る用】` / `【LLM用】` 注釈形式、
  v0.5.15 の外部動画URL判定・注釈生成・`yt-dlp` コマンド生成・ローカル/家庭内ネットワークURL拒否、
  Firefox自動更新用 `updates.json` の生成、HTTPS URL強制、署名済み `.xpi` の配布名コピー、
  `web-ext lint`（Mozillaの拡張機能検査）で errors 0 / notices 0。
- 実機確認済み: Chrome保存はユーザー実機で改善確認済み。v0.3.0 のChrome TL右クリック保存は、
  画像1枚投稿で取得・Eagle保存結果の確認済み。v0.3.1 の最近使ったフォルダ5件表示も
  ユーザー検証済み。v0.3.2 のFirefox使用感と保存操作パネル独立表示はユーザーが良好と報告済み。
  v0.4.0相当のFirefox保存小窓では、サムネイル全体表示は良好とユーザーが報告済み。
  v0.4.1 でFirefoxの新規フォルダ作成後、そのフォルダへの画像保存までユーザー実機で確認済み。
  v0.4.2 相当でFirefox投稿単体ページからの動画保存、動画本体とメタデータ抽出がユーザー実機で確認済み。
  v0.5.1 で動画保存自体は良好とユーザーが報告済み。ただし保存済みなのに失敗表示になるケースが残った。
  v0.5.3 のメディア欄・拡大表示からの右クリック保存は、2026-06-18 にユーザーが実機検証し、動作問題なしと報告。
  v0.5.5 の公式風レイアウト（縦一直線）は 2026-06-19 にユーザーが実機検証し、動作確認済みと報告。
  v0.5.7 の保存候補チェック除外は、2026-06-20 にユーザーが実機検証し、動作良好と報告。
  v0.5.8 の保存成功後の自動クローズは、2026-06-20 にユーザーが実機検証し、良好と報告。
  v0.5.15 の外部動画URL保存は、2026-06-24 にユーザーがTikTok URLで実機検証し、動作OKと報告。
  Firefox `about:debugging` で旧一時ID版 `...@temporary-addon` と固定ID版
  `x-eagle-snapshot-saver@takedayousuke.local` が2件表示されたが、旧一時ID版を削除済み。
  v0.5.15 の署名済み `.xpi` インストールと Firefox 再起動後の保持は、2026-06-24 にユーザーが実機確認済み。
  v0.5.16 で導入した `update_url` により、2026-06-24 に Firefox の「今すぐ更新を確認」操作で
  v0.5.17 へ上がることをユーザーが実機確認済み。
- API確認済み: v0.3.0で保存したX画像2件は、EagleローカルAPI上で注釈・投稿URL・保存先フォルダを確認済み。
  v0.4.0の実現性ゲートとして、`https://x.com/fv5b9x/status/2061039996543570164` から
  `yt-dlp --cookies-from-browser firefox` でMP4を取得し、Eagle `/api/item/addFromPath` で保存できることを
  item ID `MQI17LE0PLEWK` で確認済み。返却情報には `ext: mp4`、動画寸法、再生時間、注釈、投稿URL、
  保存先フォルダが含まれた。
  v0.5.15 のTikTok保存結果として、Eagle item ID `MQRM9BZKF5YT5`、ファイル
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library/images/MQRM9BZKF5YT5.info/tiktok-miamia_nyann-7645682227623300360-video.mp4`
  を確認済み。Eagle API上で `ext: mp4`、duration 約14.4秒、URL、保存先フォルダ、`source: external-video` 注釈を確認。
- 未確認: v0.2.5以降として新規保存した
  Eagle注釈のEagle画面表示確認。v0.5.2へFirefox一時アドオンを再読み込みした後、Eagleメモ欄の
  先頭整理と動画保存後の失敗表示補正が実機で期待通り効くか。Mac再ログイン後に
  LaunchAgentが自動起動するか（v0.5.15の手動再起動と `/health` 確認は済み、再ログイン後は未確認）。外付けSSD未マウント時の起動挙動。
  v0.5.10へ拡張機能を再読み込み後、重複画像を含む複数保存で固まらず・誤って失敗表示にならず・重複以外が保存されるか。
  v0.5.11へ再読み込み後、TL動画を右クリック→「動画のアドレスをコピー」→拡張ボタンで、URL自動読み取り・反応数表示・
  補助処理経由の動画保存が通るか。Firefoxでクリップボード自動読み取りが許可されるか（不可なら手動貼り付け欄で代替）。
  v0.5.14 へ再読み込み後、新規保存した画像・動画の Eagle メモ欄で `【見る用】` と `【LLM用】` が期待どおり表示されること。
  v0.5.15 のTikTok以外の外部動画URL保存。
- 非対象: 画像サムネイル付きの最近保存項目表示。
- 保留: メディア欄からの複数画像一括取得（②シームレス方式）。武田さんが外部拡張 Control Panel for Twitter で
  ポスト欄のリポストを隠し、当面はポスト欄＋既存の保存フローで回避するため、今回は実装しない。再度必要になれば
  実現性ゲート（裏タブで全画像が取れるか）から着手する。
- 残存リスク: Firefox保存がまだ失敗する場合は、ブラウザから直接画像を渡す方式ではなく、
  一度ローカルに保存してから Eagle の別APIで取り込む方式への切り替えを検討する。Chrome TL
  右クリック保存はXのTL DOM構造に依存するため、X側の画面変更で再調整が必要になる可能性がある。
  `web-ext lint` の警告として、Chrome向け `background.service_worker` は Firefox では未対応で無視される。
  ただし Firefox向け `background.scripts` を併記しているため、検査上は warning 1 / errors 0 に留まる。
  外部動画URL保存は `yt-dlp` が取得できる公開URLだけが対象。ログイン必須・DRM（コピー防止の仕組み）・サイト側制限のある動画は失敗する。
  安全側の制限として、ローカル端末や家庭内ネットワークのURLは対象外にしている。
  Firefox自動更新は、公開HTTPSの置き場がないと成立しない。現行運用ではGitHub Pagesに `updates.json` と署名済み `.xpi` を置く。
  URL非公開運用だが、URLを知っている人は取得できる。

## 引き継ぎメモ（2026-06-20）

- 現行版: `tools/x-eagle-save-extension/manifest.json` の `version` は `0.5.38`（動画補助処理 `HELPER_VERSION` は 0.5.17）。
- 2026-07-09 再点検(配布物と実機反映を分離):
  - 確認できたもの:
    - `tools/x-eagle-save-extension/manifest.json` は `0.5.38`
    - 署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.38.xpi` が存在し、
      アーカイブ内 `manifest.json` も `0.5.38`
    - ローカル `dist/firefox-update-site-repo/updates.json` は `0.5.38` を指す
    - 公開 `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` も `0.5.38`
    - 公開 XPI URL `asset-12914371f98d4dc7-0.5.38.xpi` は HTTP 200
  - まだ確認できていないもの:
    - 起動中 helper `/health` は **`version: 0.5.17`** で、`duplicateIndex.status` も `stale`
    - Firefox 実機で拡張機能が本当に `0.5.38` へ上がっているかは未確認
    - Eagle 実機で「重複を増やさない」終端確認は未確認で、ユーザー報告は失敗
  - ルール:
    - `署名済みXPIがある` / `公開updates.jsonが0.5.38` は **配布済み** の証拠
    - `Firefox実機で新版が動いた` / `重複が増えなかった` までは証明しない
- 2026-07-09 失敗記録（user-stated + 実行ログ由来）:
  `v0.5.38` では、重複候補を `existing / new / blocked` に分類し、`ambiguous`、helper未起動、
  timeout、壊れたJSON、`index-not-ready` では `addFromURL` に進まず保存停止する計画だった。
  既存が見つかった場合は `addFromURL` せず、既存itemへメモ追記し、可能ならフォルダ追加プラグイン経由で
  フォルダ追加する想定だった。自動テストでは `node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、`python3 -m py_compile tools/eagle_dedup_merge.py` が通過。
  ただし、ユーザー実機で「重複処理がされません。失敗です」と報告されたため、**v0.5.38の重複処理は
  実機成功として扱わない**。
  直前の実機反映作業では、helperを `launchctl kickstart -k gui/$(id -u)/com.takedayousuke.x-eagle-video-helper`
  で再起動し、`/health` に `fresh` / `refreshing` / `status` が出る新形式を確認した。Firefox配布は、AMO上に
  既に存在した署名済み `0.5.38` (`5f5a5887db534a7e979f-0.5.38.xpi`)を取得し、
  GitHub Pages更新フィードを `asset-12914371f98d4dc7-0.5.38.xpi` へ差し替えた。Pages側は一度 stale
  / build失敗になったため、公開リポジトリに `.nojekyll` と静的 `index.html` を追加し、最終的に
  commit `0dbb6bd` で Pages build `built`、公開 `updates.json` が version `0.5.38`、XPI URLが HTTP 200
  であることを確認した。
  しかし、この確認は「配布物が 0.5.38 になった」証拠であり、「重複処理が実機で成功した」証拠ではなかった。
  次回調査では、Firefox側に本当に `0.5.38` が入っているか、保存入口が新版 `eagle-save.js` を通っているか、
  helper lookup / content lookup が実保存時に何を返しているか、Eagleフォルダ追加プラグインが起動しているか、
  既存item特定に失敗して `new` 扱いで `addFromURL` へ進んでいないかを、実機ログまたは最小再現で確認する。
- v0.5.36で応急処置＋方針確定: 2026-07-01、武田さんが v0.5.35 を実機検証(Eグル重複通知オフ + 再保存)した結果、
  **画像がまとまらず新規で追加されただけ**と報告(対象 `x-modememorium-2071894125142589825-01〜04`。元 `MR1OF...` と別に
  新 `MR1SK...` が4枚できた)。実データ確認で、元アイテムにはメモ17:05分が追記されていた(v0.5.35のメモ追記は動作)が、
  元のフォルダは変わらず、新アイテムが別フォルダで新規作成されていた。→ **Eグルは重複通知オフでも既定で「両方保持」
  (新規追加)し、既存アイテムへフォルダを足さない**ことが実機で確定。v0.5.35の「addFromURLでEグルに委ねる」方式は
  重複ファイルを増やすため撤回。
  応急処置として `saveOneImage` を変更: 既存アイテムが見つかった場合(helper/content照合)は **addFromURLせず**、
  メモの非破壊追記のみ行い `return`(重複アイテムを増やさない)。Eグルに無い新規画像は従来どおり addFromURL で保存。
  既存アイテムへのフォルダ追加は v0.5.36 時点では行わない(HTTP APIで不可能なため)。
  `tools/tests/test_x_eagle_save_extractor.js` の helper found / content found テストを、addFromURL を呼ばない
  (メモ追記のみ)期待へ更新。README の該当節も v0.5.36 実態へ更新。
  署名済み `.xpi` `5f5a5887db534a7e979f-0.5.36.xpi`(SHA-256 `7915ab8912bae7568a90fbb702d91d19e4715d2778ab418969c834f84804f392`)を
  GitHub Pages(commit `985d73b`)へ公開・反映済み。
  **次段階**: 既存アイテムへのフォルダ追加を実現するため、Eグルのバックグラウンドサービスプラグイン
  (`tools/eagle-folder-plugin/`、未着手)を自作する。武田さん承認済み(2026-07-01)。設計は下記「Eグルフォルダ追加プラグイン設計」。
  拡張機能は将来 v0.5.37 でこのプラグイン経由のフォルダ追加を呼ぶ。
  検証(武田さん): v0.5.36 で同じ画像を再保存しても**新規アイテムが増えないこと**、既存アイテムのメモに新情報が
  積まれることを実機確認する。
- 2026-07-07、次段階として予定していたフォルダ追加プラグイン経由の後処理バッチを実装し、
  **ライブラリ内に残っていた旧重複66組を非破壊統合済み**。役割分担は、
  - 拡張機能(v0.5.36系): 今後の新規重複を増やさない
  - 別バッチ `tools/eagle_dedup_merge.py`: 既に溜まっていた旧重複を一回きりで統合する
  に確定。詳細は [[eagle-dedup-merge-2026-07-07]] を参照。
- v0.5.35で刷新（**保存方式は v0.5.36 で撤回。原因調査部分は有効**）: 2026-07-01、武田さんの検証で
  「重複を検知したときフォルダの追加ができない(メモ更新はできている)」と報告。
  原因を徹底調査した結果、**Eグルの公開HTTP APIには既存アイテムのフォルダを変更する機能が存在しない**ことが判明した。
  - `/api/item/update` に `folders` を送ると `status: success` を返すが、実際には folders を変更しない(公式ドキュメントの
    対応パラメータは `id`/`tags`/`annotation`/`url`/`star` のみ。実データ `MR1KJ60OZKL0B` へ実APIで送信して確認)。
    メモ(annotation)が効いてフォルダ(folders)が効かないのは、この非対称のため。
  - フォルダ変更はEグルの**プラグインAPI**(`eagle.item.getById()` → `item.folders = [...]` → `item.save()`)でのみ可能。
    Eグル MCP(`Eagle/Plugins/mcp-server/modules/mcp/tools/item.js`)がこの方法で folders を変更していることをソースで確認。
    ブラウザ拡張はEグルプラグインでないため、この方法は使えない。
  - Eグルは 4.0.0(build 20260401)だが、HTTP API 自体は 3.0 世代のままでフォルダ操作エンドポイントの追加はない。
  対処として、武田さんの承認のもと保存経路を刷新した。拡張機能は既存画像を見つけても保存を避けず、
  **常に `addFromURL` + `folderId` でEグルへ送り、フォルダ追加はEグル本体の重複処理に委ねる**。
  Eグルの「重複インポート通知」(`preferences.notification.notification.when.repeatImage`)をオフにしておけば、
  重複時もダイアログを出さずにEグルが既存アイテムへフォルダを追加する。メモ(annotation)は update で変更できるので、
  既存アイテムを特定できた場合(helper=メタデータ照合 → content=中身照合、`findExistingItemForMemoAppend`)に限り、
  新しい取得情報を非破壊で既存メモへ追記する(`appendMemoToExistingItem`。folders は送らない=効かないため)。
  helper が ambiguous(候補複数)のときは誤爆防止でメモ追記しない。補助処理停止時は既存を探せずメモ追記なし・addFromURL のみ。
  `saveOneImage` から旧「保存前に既存を見つけて addFromURL を避ける」経路(`updateExistingDuplicate`、
  `appendDuplicateAnnotationSafely`、`waitForDuplicateMetadataTarget`、`recentFolderDuplicateTargetResult`、
  `pickBestDuplicateTarget` など v0.5.23〜0.5.34 の複雑な事後メモ追記ロジック)は呼ばれなくなった(死にコード化・要 lint 整理)。
  `tools/tests/test_x_eagle_save_extractor.js` の旧挙動統合テスト13本を、新挙動4本(helper found→メモ追記+addFromURL、
  content found→同、ambiguous→メモ追記なし+addFromURL、補助処理停止→addFromURLのみ)へ置き換え。
  署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.35.xpi` を作成しGitHub Pagesへ公開。
  詳細は [[x-eagle-duplicate-existing-item-discard-fix-2026-07-01]] の変遷節を参照。
  **運用前提**: Eグルの重複インポート通知をオフにする必要がある(README に手順記載)。通知オフ時のEグル既定動作
  (既存アイテムへフォルダ追加=「既存使用」か、新規追加=「両方保持」か)は未検証で、武田さんの実機検証対象。
  どちらの既定でも情報は失われない設計(既存にメモ追記済み + Eグルがフォルダ処理)。
- v0.5.34で追加: 2026-07-01、武田さんから「そもそもEagleの重複ダイアログが出ないようにしてほしい(情報は壊さず)」
  という方向性の指摘を受け、保存前の重複検知に**画像の中身そのものによる照合**を追加した。
  これまでの保存前チェック(v0.5.31)は名前・投稿URL・メモ欄など文字情報の一致しか見ておらず、
  `@The_Antin.jpg`のように文字情報が一つも一致しない古い項目には原理的に気づけなかった。
  Eagle自身は画像の中身(見た目)で重複を判定しているため、同じ土俵に立つ必要があった。
  `tools/x-eagle-video-helper/server.js`(0.5.17)に以下を追加。
  - 重複索引に、各既存画像の**ファイルサイズ**をキーにした `bySize` マップを追加(`metadata.json`の
    `size`から取得、追加コストはほぼゼロ)。
  - 新しい `POST /duplicate-index/lookup-by-content` を追加。保存しようとしている画像のURLを
    補助処理自身がダウンロードし、その場でファイルサイズ→sha256指紋を計算。ファイルサイズが
    一致する既存候補(通常0〜数件に絞られる)だけ実ファイルを読んで指紋を照合し、完全一致する
    項目が1件だけならその項目を返す。ライブラリ全項目(3万件超)を毎回ハッシュ化する必要はない。
  `tools/x-eagle-save-extension/eagle-save.js` を変更し、`saveOneImage()` の最終手段として
  この中身照合を追加。名前・投稿URL・メモ欄による既存の手がかりベース照合(helper lookup /
  keyword検索)が両方とも既存項目を見つけられなかった場合だけ発動し、見つかればEagleへの
  新規送信(`addFromURL`)自体を行わず、その場で `updateExistingDuplicate()` へ書き足す
  (v0.5.33で直したフォルダ・注釈の両方を保持する経路をそのまま再利用)。これにより
  Eagle自身の重複確認ダイアログが出る前に処理が終わり、ダイアログ自体が出なくなる。
  制約として、この中身照合は動画補助処理が起動している場合だけ有効(未起動時は従来どおり
  ダイアログが出ることがあるが、v0.5.32/33の事後保険は引き続き効く)。また画質を落として
  再アップロードされた画像などファイルサイズ自体が変わるケースは検出できない。
  `tools/tests/test_x_eagle_video_helper.js` に、ファイルサイズでの絞り込み・sha256一致/不一致・
  ローカルホスト拒否・imageUrl必須の検証を追加。`tools/tests/test_x_eagle_save_extractor.js` に、
  中身照合で既存項目が見つかった場合に `addFromURL` を一切呼ばず既存項目へ直接書き足す
  回帰テストを追加。両テストとも通過(実行時間に影響なし)。
  実ライブラリ(33915件)に対して手動で動作確認: 今日保存した`@Antin_Illust`の画像URLで照合したところ、
  ファイルサイズ絞り込みで正しく候補を2件(`@The_Antin.jpg`と本日保存した`x-Antin_Illust-...-01`、
  どちらも本当にバイト単位で同一)に絞り、sha256でどちらも一致することを確認できた
  (今回は本日の検証で偶然2つ重複コピーが存在するため`ambiguous`扱いになったが、通常の
  1対1重複であれば`found`として単独特定される設計)。
  署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.34.xpi` を作成し、GitHub Pages
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` へ公開済み(公開 `.xpi` の
  SHA-256 `d0ba17b2765c830abba5a0b8fb6f909ef2e346c571dad007013b64e0111cf73f` が `update_hash` と一致することを確認)。
  動画補助処理のLaunchAgentを再起動し、`/health` で version 0.5.17 を確認済み。
  詳細は [[x-eagle-duplicate-existing-item-discard-fix-2026-07-01]] の変遷節を参照。
  Firefox実機での自動更新確認、および実際のX投稿保存でダイアログが出なくなることの実地確認は未実施。
- v0.5.33で追加: 2026-07-01、武田さんがv0.5.32検証中に「重複処理のときにフォルダの追加が行えていない。
  デフォルトでは追加できていた」と報告。原因は v0.5.32 で追加した `appendDuplicateAnnotation()` の
  事後メモ書き足しにあった。この関数は既存項目へ `/api/item/update` を呼ぶとき、`{id, annotation}` だけを
  送り `folders` を含めていなかった。Eagle公式ドキュメント(`api.eagle.cool/item/update`)でも `folders` は
  そもそも明記されていない未文書化フィールドで、省略時にEagle側が意図せず既存のフォルダ所属を巻き戻す
  余地があると判断した。実際、同じファイル内の別関数 `updateExistingDuplicate()`（保存前に重複が確定した
  場合の更新経路）は元から `folders` を明示送信しており、`appendDuplicateAnnotation()` 側だけこの対応が
  漏れていた——v0.5.32で待ち時間を延ばし新しい書き足しフォールバックを追加したことで、以前は
  ほぼ発動しなかった `appendDuplicateAnnotation()` の更新呼び出しが実際に発動する頻度が上がり、
  この既存の抜け漏れが顕在化したと考えられる。
  対処として、既存項目の `folders` へ保存先フォルダIDを足し込む処理を `mergedFoldersWithTarget()` として
  共通化し、`updateExistingDuplicate()` と `appendDuplicateAnnotation()` の両方から呼ぶように統一。
  `appendDuplicateAnnotation()` の更新可否判定も、注釈だけでなくフォルダ所属も見るように拡張し、
  常に `folders` を明示送信するようにした。
  `tools/tests/test_x_eagle_save_extractor.js` の既存2テスト（cross-author-existing-item /
  unrelated-old-item）に、更新payloadの `folders` が正しい配列であることを確認する検証を追加。
  署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.33.xpi` を作成し、GitHub Pages
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` へ公開済み。詳細は
  [[x-eagle-duplicate-existing-item-discard-fix-2026-07-01]] の追記節を参照。
  Firefox実機での自動更新確認、および実際の重複ダイアログでのフォルダ追加確認は未実施。
- v0.5.32で追加: 2026-07-01、武田さんが「重複した時に警告が出て、インポートを押すと抽出したデータが破棄される」と
  スクリーンショット付きで報告（対象は `@Antin_Illust` の2枚組投稿、既存項目 `@The_Antin.jpg`）。
  調査で、Eagle公式サポート記事から重複ダイアログの実際の仕様を確認した。「両方を保持」は新規ファイルとして
  そのまま追加されるため注釈は残るが、初期状態で選ばれている「既存の項目を使用」は**取り込もうとした新しい画像
  そのものを取り込まなかったことにし、既存項目には保存先フォルダだけを追加する**（注釈・反応数・投稿文などの
  抽出データは一切引き継がれない)。実際にEagle内を確認したところ今回は「両方を保持」側の結果で2枚ともデータは
  残っていたが、デフォルト選択のまま「インポート」を押した場合にデータが消える構造的リスクは残っていた。
  拡張機能には元々「既存の項目を使用」に備えて保存直後に既存項目へメモを書き足す保険があるが、次の2点で
  今回のケースを救えなかった。
  1. 既存項目 `@The_Antin.jpg` は名前・URL・投稿IDのいずれも今回の投稿と一致点がなく、保存前・保存後とも
     Eagle keyword 検索で候補ゼロ（`candidateCount: 0`）になっていた。
  2. 候補ゼロのときの待ち時間 `DUPLICATE_WAIT_NO_CANDIDATES_MS` が1.5秒しかなく、人がEagleの確認ウィンドウに
     気づいてクリックするより先に諦めていた。
  対処として `tools/x-eagle-save-extension/eagle-save.js` を変更した。
  - 待ち時間の定数を統合し、候補の有無にかかわらず最大60秒待つ `DUPLICATE_WAIT_MS` に一本化。
  - `recentFolderDuplicateTargetResult()` に、投稿ID一致が1件も無いときの緩いフォールバックを追加。
    「保存先フォルダ内で、操作直後にちょうど1件だけ触られた画像」があれば、投稿IDが一致しなくても書き足し先
    候補にする（同じ投稿の別画像を誤って巻き込まないよう、対象の `post_id:` を注釈にすでに含む項目は除外）。
  - `warnDuplicateAnnotationResult()` の警告抑止条件を、候補ゼロでもフォルダ指定があれば警告が出るように調整
    （`new-item-created` は正常系のため引き続き警告対象外）。
  `tools/tests/test_x_eagle_save_extractor.js` に、名前・URL・投稿IDが無関係な既存項目でも、フォルダ内の
  唯一候補として書き足しに使われることを確認する回帰テストを追加。あわせて、`item/list` を常に空配列で
  返していた2箇所のテスト用モックが、待ち時間延長により実時間60秒×2で待ちきってしまう副作用が出たため、
  addFromURL成功後は自分自身の新規項目が検索に出る現実的な挙動に修正（`node tools/tests/test_x_eagle_save_extractor.js`
  は 0.2秒程度、`node tools/tests/test_x_eagle_video_helper.js` も通過）。README のバージョン説明も更新。
  署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.32.xpi` を作成し、GitHub Pages
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` へ公開済み（公開直後は
  GitHub Pages反映待ちで旧版を返したが、その後 version 0.5.32 / update_hash
  `sha256:19d5b041f22ca9394a78539bc9a50e6776d4e98069a102ef28d776369cbff0a8` に切り替わり、署名済み `.xpi` の
  SHA-256と一致することを確認済み）。詳細は [[x-eagle-duplicate-existing-item-discard-fix-2026-07-01]]。
  Firefox実機での自動更新確認、および実際に「既存の項目を使用」を選んでのメモ書き足し確認は未実施
  （今回のスクリーンショット時点では「両方を保持」側の結果だったため、この分岐は自動試験のみで実機未通過）。
- v0.5.31で追加: 2026-06-28の重複検知再発分析を受け、保存前の既存 item 探索を Eagle API の
  `keyword` 検索だけに依存しないよう、`tools/x-eagle-video-helper/server.js` に Eagle ライブラリ重複索引を追加した。
  補助処理は `2024_11_16_eagle_フォルダ管理.library/images` 配下の `*.info/metadata.json` を読み取り専用で走査し、
  `capture_key`、`image_url`、`post_id + media key`、`post_id + target`、生成予定ファイル名、単一画像投稿の
  `post_id` で既存項目を探す。`GET /health` は `duplicateIndex.libraryImagesDir`、存在確認、読取可否、
  索引件数、最終構築時刻を返す。
  拡張側は画像保存前に `POST /duplicate-index/lookup` を呼び、1件に安全に絞れた場合だけ `addFromURL` を呼ばず、
  `/api/item/info` で最新 item を取り直してから `/api/item/update` で注釈とフォルダを更新する。
  候補が曖昧な場合は、旧 keyword 経路でも最新更新 item を勝手に選ばないようにし、その保存では既存 item の
  自動更新を抑止する。既存 item の一括リネーム・一括マージは対象外。
  `tools/tests/test_x_eagle_video_helper.js` には仮ライブラリ索引、`name: 画像` item の lookup、曖昧候補、
  認証なしPOST拒否を追加。`tools/tests/test_x_eagle_save_extractor.js` には helper lookup 成功時の
  `item/info` 取り直し、helper曖昧時の自動更新抑止、helper停止時の keyword fallback を追加した。
  自動試験は実装中に通過済み。実ライブラリ索引は 33696 件を読み、`MO0THSO94QFX1` を `capture-key`
  で返すことを helper API 経由で確認済み。AMO署名済み `.xpi`、GitHub Pages公開、公開 `.xpi` の
  SHA-256 一致まで確認済み。Firefox実機での v0.5.31 自動更新と、`MO0THSO94QFX1` 同型の再保存終端確認は未完了。
- v0.5.29で追加: 2026-06-27のユーザー報告で、同じX画像をリンクから再保存したとき、重複処理で新しい取得情報が
  既存メモへ積まれないケースを確認した。対象は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library/images/MQUAHVAN1A2UQ.info/x-kaigan0211-2070068421837103141-01.jpg`。
  `metadata.json` には既に `capture_key: x:2070068421837103141:image-1-of-1:HLpcNvrasAA8P6r` が入っていた。
  Eagle API検索では投稿ID `2070068421837103141`、`kaigan0211`、生成ファイル名で対象を取得できており、
  候補探索の失敗ではなかった。原因は、既存メモに同じ `capture_key` があると、拡張機能が
  「同じ画像の情報は既にある」と判断して追記を止める設計だったこと。
  これは重複防止としては安全だが、ユーザーの意図である「再インポート時点の新しい反応数・保存時刻を、
  古い情報の上に非破壊で積む」挙動と衝突する。
  対処として、`mergeAnnotation` は `capture_key` などの識別子一致だけでは追記を止めず、完全に同じ注釈本文が
  既に含まれる場合だけ重複防止するよう変更した。さらに、同じ `capture_key` を持つ既存項目でも、
  Eagle重複ダイアログ後に更新時刻増加または選択フォルダ内の最近更新で「今回の操作で既存項目が触られた」
  ことを確認できれば、新しい注釈を上へ追記する。古い注釈は `---` の下に残す。
  `tools/tests/test_x_eagle_save_extractor.js` に、同じ `capture_key` の古い注釈がある状態で、
  新しい `captured_at` / `metrics.likes` を上に積み、古い `captured_at` / `metrics.likes` を下に残す
  回帰テストを追加済み。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.29-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.29.xpi`、
  公開用 `asset-d0be6ac7699f0e0e-0.5.29.xpi` を生成済み。
  2026-06-27、GitHub Pages公開URLの `updates.json` が v0.5.29 へ反映され、
  公開 `.xpi` の SHA-256 `80e2a78be6f9d21d89a779c881a03fe7a42ac25ee3ee27f86369ee71db9b5e13` が
  `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での v0.5.29 自動更新と、`MQUAHVAN1A2UQ` 同型の再保存履歴追記は未実施。
- v0.5.24で追加: 2026-06-26のユーザー要望により、Eagleメモ欄の `【見る用】` で横並びだった反応数表示を
  箇条書きへ変更した。従来は `反応: いいね ... / リポスト ... / 表示 ...` と
  `内訳: 返信 ... / 引用 ...` で1行にまとまっていたが、v0.5.24以降の新規保存・重複追記では
  `反応:` の下に `- いいね:`、`- リポスト:`、`- 表示:`、`内訳:` の下に `- 返信:`、`- 引用:` を
  1項目1行で残す。`【LLM用】` の `metrics.likes` / `metrics.reposts` などの機械読み取り用行は変更しない。
  `tools/tests/test_x_eagle_save_extractor.js` で新しい注釈先頭形式を検証済み。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.24-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.24.xpi`、
  公開用 `asset-12a9b3ee54eee878-0.5.24.xpi` を生成済み。
  2026-06-26、GitHub Pages公開URLの `updates.json` が v0.5.24 へ反映され、
  公開 `.xpi` の SHA-256 `a4de5f0a6bdd000866c551ad3842d4f2186fde0d280f6bb4b386a0f219818efc` が
  `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での v0.5.24 自動更新と、メモ欄箇条書き表示の実機確認は未実施。
- v0.5.23で追加: 2026-06-26のユーザー実機検証で、v0.5.22でも重複メモ追記が不発になる条件を確認した。
  確認対象は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library/images/MQ0RI9CC9RQF9.info/@ayamachi3284 DAY30.カヨコ.jpg`。
  `metadata.json` は `name: @ayamachi3284 DAY30.カヨコ`、`url: https://x.com/jeonghee1414/status/2062601913770836159`、
  `annotation` 空、`folders: M3JLEZ3ZRD4IX` だった。Eagle APIでは投稿ID `2062601913770836159` 検索は0件、
  `jeonghee1414` 検索でも対象は出ず、`@ayamachi3284` / `DAY30` / `カヨコ` 検索でだけ対象が出た。
  つまり、X投稿URL側の作者IDと既存ファイル名側の作者IDが違う保存済み項目では、
  v0.5.22の「投稿作者ID・生成予定ファイル名・media key・投稿ID」中心の候補探索が対象を掴めない。
  ただし対象項目の `modificationTime` / `lastModified` は 2026-06-26 10:28頃に更新されており、
  Eagle側では重複ダイアログ後に既存項目を実際に触っていた可能性が高い。
  対処として、保存直前時刻を基準にし、保存後に選択フォルダ内の最近更新項目を `folders=<folderId>` 付き
  `item/list` で確認する経路を追加した。同じ投稿IDで、今回の操作時刻以後に更新され、候補が1件だけなら
  `recent-folder-status` として既存メモ追記対象にする。新規コピーらしい項目や複数候補の場合は更新しない。
  さらに、候補がある重複待機は60秒まで延長し、ユーザーがEagleの重複ダイアログを押すまでに8秒を超えても
  先に諦めにくくした。
  `tools/tests/test_x_eagle_save_extractor.js` に、投稿作者IDと既存ファイル名作者IDが違い、通常検索では対象が
  出ないが、選択フォルダ内の最近更新項目から追記できるケースを追加済み。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.23-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.23.xpi`、
  公開用 `asset-605a1fc592ea7e13-0.5.23.xpi` を生成済み。
  2026-06-26、GitHub Pages公開URLの `updates.json` が v0.5.23 へ反映され、
  公開 `.xpi` の SHA-256 `52a893796c4b8e40c8edd2308fc86675e20758d5044ce7abd148e1b4dc14bef6` が
  `updates.json` の `update_hash` と一致することを確認。
  2026-06-26のユーザー実機検証で今回の修正対象は機能したと報告。添付メモの対象は
  `MQISKLV3VBLP8`、`MPGRO6D8PEH95`、`MQI533RQXJ08R`、`MQ0RI9CC9RQF9` の4件。
  各 `metadata.json` で `【LLM用】` と `capture_key` が入っていることを確認し、とくに
  `MQ0RI9CC9RQF9` は `capture_key: x:2062601913770836159:image-1-of-1:HJ_Vfota8AIT5qM` が入ったため、
  投稿URL側作者IDと既存ファイル名側作者IDが違うケースでの重複メモ追記は実機確認済み。
- v0.5.22で追加: 2026-06-26の「重複メモ追記が不安定」という報告への根本修正。
  原因は、v0.5.19〜0.5.21の重複メモ追記が、Eagle側の重複ダイアログ処理後に観測できる副作用
  （保存先フォルダ追加、または更新時刻の増加）を確認してから既存メモを更新する設計だったこと。
  既に同じフォルダに入っている項目ではフォルダ追加が起きず、Eagleが更新時刻を進めない場合もあり、
  APIからは「既存項目を使った」証拠が出ないため不安定になっていた。
  さらに、候補探索が作者ID検索の先頭ページに依存していたため、同じ作者の保存数が多い場合に対象候補が漏れる可能性もあった。
  対処として、Eagleの `item/list` 検索を `offset` 付きで最大1000件までページ送りし、
  完全一致に十分近い既存候補が保存前後で1件だけ安定している場合は `stable-exact-target` として更新対象にする。
  ただし、保存後に新規コピー（新しい項目）が検出できる場合は既存項目を更新しない。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.22-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.22.xpi`、
  公開用 `asset-6f7d3103257ae235-0.5.22.xpi` を生成済み。
  2026-06-26、GitHub Pages公開URLの `updates.json` が v0.5.22 へ反映され、
  公開 `.xpi` の SHA-256 が `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での v0.5.22 自動更新と、同一フォルダ重複・作者保存数多数ケースの実機確認は未実施。
- v0.5.21で追加: 2026-06-26時点のコードでは、X画面から投稿日時をできる範囲で読み取り、
  Eagle注釈へ `投稿日時`、`posted_at`、投稿から保存までの `経過` を残す。AMO署名済み `.xpi` と
  GitHub Pages公開 `updates.json` は v0.5.21 まで作成・push済み。
- v0.5.20で追加: 2026-06-25のユーザー実機検証で、v0.5.19の重複メモ追記が動作していることを確認した。
  確認対象は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library/images/MQS3XS6EJVJMF.info/@neil_limu NIKKE NIKKEfanartラピ.jpg`。
  同ディレクトリの `metadata.json` には `capture_key: x:2069624053221007713:image-1-of-1:HLgD0rWasAAWoXV` を含む新しいXメタデータと、
  既存メモ `これは既存メモ。消えたらNG。` が両方残っている。
  フォルダ候補については、Eagleの `/api/folder/listRecent` が現時点で16件だけ返しており、
  「最近使った一致フォルダ」はEagle側の最近履歴だけに依存すると上限・抜けが出る可能性がある。
  そのため、保存成功時に選んだフォルダIDを拡張機能側でも30件まで保存し、Eagleの最近履歴と統合するよう補強した。
  検索中の上位「最近使った一致フォルダ」は最大5件に変更した。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.20-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.20.xpi`、
  公開用 `asset-0113ecd96cdf71d9-0.5.20.xpi` を生成済み。
  2026-06-25、GitHub Pages公開URLの `updates.json` が v0.5.20 へ反映され、
  公開 `.xpi` の SHA-256 が `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での v0.5.20 自動更新と、拡張機能側の最近フォルダ履歴補強の実機確認は未実施。
- v0.5.19で追加: 2026-06-25の実機検証で判明した、v0.5.18の重複メモ追記不発と検索候補の見分けづらさを修正した。
  重複メモ追記は、Eagle既存項目の `url` に含まれる `/status/<投稿ID>` と作者ID検索を使い、Eagleが既存項目を使った証拠
  （保存先フォルダが追加された、または更新時刻が進んだ）を確認できた1件だけ更新する。通常の新規保存では、保存前候補が0件なら待たない。
  フォルダ検索では「最近使った一致フォルダ」と「その他の一致フォルダ」を見出しで分ける。
  公開補助スクリプトは一時フォルダで配布ファイルを作り、GitHub Pages公開リポジトリでは古い `.xpi` を残し、`.nojekyll` を再追加しない。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.19-unsigned.xpi`、
  AMO署名済み `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.19.xpi`、
  公開用 `asset-d19b06545c8d131f-0.5.19.xpi` を生成済み。
  2026-06-25 19時ごろ、GitHub Pages公開URLの `updates.json` が v0.5.19 へ反映され、
  公開 `.xpi` の SHA-256 が `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での自動更新、検索候補区切り表示、Eagle重複ダイアログ後の既存メモ追記の終端確認は未実施。
- v0.5.18で追加: 2026-06-24の3要望と重複挙動検証を反映し、保存ロジックと保存画面を更新した。
  フォルダ検索では、検索語に一致する最近使ったフォルダを最大3件まで候補上位に出す。
  右クリック保存小窓の大きいプレビューは、画像の縦横比に合わせて幅・高さが変わる表示へ戻した。
  Eagleの重複追加でユーザーが「既存の項目を使用」を選んだ場合、Eagle側はフォルダ追加を自動で行うため、
  拡張機能側はフォルダ配列を更新しない。代わりに、既存項目を1件だけ特定できる場合に限り、
  新しいXメタデータ注釈を既存メモの上へ `---` 区切りで追記する。対象が0件または複数候補で曖昧な場合は、
  誤更新を避けるため何もしない。
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.18-unsigned.xpi` 生成済み。
  2026-06-24 23時台に、AMO署名済み `.xpi` とローカル配布ファイル生成まで完了。
  2026-06-24 23:59ごろ、GitHub Pages公開URLの `updates.json` が v0.5.18 へ反映され、
  公開 `.xpi` の SHA-256 が `updates.json` の `update_hash` と一致することを確認。
  Firefox実機での自動更新・Eagle重複メモ追記の終端確認は未実施。
- v0.5.17で追加: Firefox自動更新の終端確認用に、保存ロジックを変えず `manifest.json` の `version` のみを上げた。
  `update_url` は v0.5.16 と同じ `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json`。
  署名なし `.xpi` 生成と検査は完了。2026-06-24 15:40、AMO署名済み `.xpi` とローカル配布ファイル生成も完了。
  2026-06-24 15:50、GitHub Pages配置と公開URL上の `updates.json` / `.xpi` HTTP 200、
  SHA-256一致を確認。2026-06-24、ユーザーがFirefoxの「今すぐ更新を確認」で v0.5.16 から
  v0.5.17 へ更新されることを確認済み。
- v0.5.16で追加: Firefox自動更新の初回導入用に `browser_specific_settings.gecko.update_url` を
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` に設定した。
  既存の保存ロジックは変更していない。通常版Firefoxへ入れるには、AMO署名済み `.xpi` の生成と
  GitHub Pagesへの `updates.json` / `asset-8aa480928040bd54-0.5.16.xpi` 配置が必要。
  2026-06-24 15:15、AMO署名済み `.xpi` とローカル配布フォルダ生成は完了。
  2026-06-24 15:28、GitHub Pages上で `updates.json` / `.xpi` ともに HTTP 200 を確認し、
  公開 `.xpi` の SHA-256 が `updates.json` の `update_hash` と一致することを確認。
- 2026-06-24追加: Firefox自動更新の準備として `scripts/firefox-auto-update.js`、
  `scripts/release-firefox-auto-update.sh`、`scripts/publish-firefox-update-git.sh` を追加。
  `firefox-auto-update.js set-update-url` は `manifest.json` の `browser_specific_settings.gecko.update_url`
  （Firefoxが更新情報JSONを見に行くURL）を設定し、`prepare-site` は署名済み `.xpi` と `updates.json`
  を配布用フォルダへ出す。公開用 `.xpi` は `asset-<digest>-<version>.xpi` 形式にし、
  公開フォルダのREADMEにもプロジェクト名を出さない。GitHub Pages用に `.nojekyll` も出す。
  2026-06-24時点でGitHub Pages配置とFirefox実機自動更新まで確認済み。
- v0.5.15で追加: クリップボードにコピーしたTikTokなどの外部動画URLを、既存のポップアップとEagleフォルダ選択から保存できるようにした。
  X投稿URLは従来どおり反応数・本文・作者IDを扱い、X以外は元サイト・元URL・保存日時・保存先・保存理由だけを注釈に残す。
  X以外のURLではFirefox Cookieを読まない。`yt-dlp` が取得できる公開URLだけ対象で、ローカル端末や家庭内ネットワークのURLは拒否する。
- v0.5.14で追加: 今後保存される画像・動画の Eagle メモ欄を、先頭の `【見る用】` と後続の `【LLM用】` に分けた。
  `【見る用】` は作者、主要反応数、本文、保存先の末尾フォルダ名、対象、保存日時、保存理由を短く読むための欄。
  `【LLM用】` は `source`、`item_kind`、`author_id`、`post_url`、`captured_at`、`target`、`folder_path`、
  `metrics.*`、`post_text`、`save_reason` をキー付きで残す欄。既存 Eagle 項目の注釈は書き換えていない。
- v0.5.13で追加: Firefox再起動後も拡張機能を残すための常用インストール準備。`manifest.json` に
  `browser_specific_settings.gecko.id: x-eagle-snapshot-saver@takedayousuke.local`、`strict_min_version: 109.0`、
  `data_collection_permissions.required`（`browsingActivity` / `websiteContent` / `websiteActivity`）を追加。
  `tools/x-eagle-save-extension/scripts/build-firefox-xpi.sh` は署名なし `.xpi` を作る。通常版 Firefox 用には
  `scripts/sign-firefox-xpi.sh` が `WEB_EXT_API_KEY` / `WEB_EXT_API_SECRET` を使い、`web-ext@8 sign --channel unlisted`
  で署名済み `.xpi` を作る。保存ロジックは v0.5.12 と同じ。自動試験済み、署名と実機インストールは未検証。
- v0.5.12で追加: 拡張ボタンのポップアップ（`popup.html`/`popup.js`）のフォルダ選びを、右クリック大窓
  （`save.html`）の使い勝手にそろえた。最近使ったフォルダ5件・検索の `↑`/`↓`/`Enter`・その場で新規フォルダ作成を
  `save.js` から移植し、見た目も明るいテーマに合わせた。画像保存もフォルダ必須に統一（従来は任意）。動画保存・
  クリップボード読み取り・補助処理表示・右クリック側 `save.html` は変更していない。自動試験済み・武田さん実機確認済み（2026-06-21、UI良好）。
- v0.5.11で追加: TL（一覧）からの動画保存をクリップボード方式で追加。X が動画の右クリックを独自メニュー
  （「動画のアドレスをコピー」「動画をポスト」）に置き換えるため、拡張機能の右クリック項目を動画には出せない。
  X 純正「動画のアドレスをコピー」を入口にし、コピー済みURLを拡張ボタンのポップアップが自動で読み取り（手動
  貼り付け欄も用意）、既存の動画保存（補助処理 `yt-dlp`）へ渡す。投稿単体ページの既存動作は不変。自動試験済み・
  武田さん実機確認済み（2026-06-20、シームレスに動画保存できることを確認）。
- v0.5.10で追加: 画像保存の保存後存在確認（item/list ポーリング）を廃止し、Eagle の重複ダイアログから切り離した。
  重複時に固まる・誤って失敗表示・二重追加になっていたのを解消。`eagle-save.js` の `saveOneImage` から
  `waitForSavedItem` / `itemExistsByName` を削除。自動試験済み・武田さん実機確認待ち。
- v0.5.9 実機検証結果（2026-06-20）: 「Eagleに保存済みなのにUI失敗表示」の解消は武田さんが実機確認済み（成功表示になる）。
  ログイン時自動起動は未検証（Mac 未再起動）。
- v0.5.9で追加: (1) 動画がEagleに保存済みでもUIが「失敗」と出る不具合を解消（Eagle確認待ちを best-effort 化し、
  `addFromPath` 成功＝item IDあり の時点で成功扱い。確認できたときだけ一時ファイル削除）。(2) 緑枠から
  紛らわしい「動画: 補助処理で取得を試せます」を外し、未起動表示を「動画補助: 未起動（動画保存には起動が必要）」に。
  (3) Mac ログイン時に動画補助処理を自動起動する LaunchAgent `com.takedayousuke.x-eagle-video-helper`
  （`install_x_eagle_video_helper_agent.sh` / `uninstall_x_eagle_video_helper_agent.sh` 付き）。
  自動試験済み・ローカルで `/health` が `version 0.5.9` / `state=running`（pidあり）を確認。実機での
  「失敗表示の解消」と「再ログイン後の自動起動」は武田さん確認待ち。手動起動の `start.command` は併存。
- v0.5.8で追加: 保存成功後に保存画面を自動で閉じる。対象は右クリック保存小窓の画像保存、
  投稿ページポップアップの画像保存、投稿ページポップアップの動画保存。保存失敗時は閉じない。
  自動試験済み・ユーザー実機確認済み（2026-06-20、動作良好）。
- v0.5.7で追加: 通常ポップアップと右クリック保存小窓に `保存候補` を追加。初期状態は全画像を
  保存対象にし、不要な画像だけチェックを外す。保存処理にはチェック済みのURLだけを渡すが、
  ファイル名と注釈は元投稿内の画像番号を維持する。自動試験済み・ユーザー実機確認済み
  （2026-06-20、動作良好）。検証証跡:
  `/Users/takedayousuke/Library/Mobile Documents/com~apple~CloudDocs/ダウンロード/02_スクショ保存/ 2026-06-20 0.02.10.jpg`
- v0.5.6で追加: ウィンドウサイズを公式風の小ぶりな比率へ（920×680 → 560×620）。左プレビュー欄を
  250px → 180px に。プレビュー画像・投稿テキスト・コメント欄・フォルダ行の余白も縮小。さらに
  保存小窓の表示位置を、OS既定からブラウザウィンドウの中央に重ねて表示するよう変更。`save.js` は変更なし。
  自動試験済み。
- v0.5.5で追加: 右クリック保存小窓のレイアウトを公式風の縦一直線に（中央の保存ボタン帯を廃止し、
  保存先＋保存ボタンをフォルダ一覧の直下へ移動）。`save.html` のDOM並べ替え＋CSSのみ、`save.js` は変更なし。
  明るいテーマ・既存機能は維持。自動試験済み・ユーザー実機確認済み（2026-06-19、動作OK）。
- v0.5.4で追加: 右クリック保存小窓のフォルダ選択をキーボード中心化（検索欄の自動フォーカス、入力後に
  一致候補の先頭を自動選択、`Enter`で保存、`↑`/`↓`で選び直し、検索語が空のあいだは自動選択しない）。
  `save.html` / `save.js` の変更。自動試験済み・ユーザー実機確認済み（2026-06-18、動作OK）。extractor・動画補助処理は変更なし。
- v0.5.3で追加: メディア欄（プロフィールの「メディア」タブの一覧）・拡大表示からの右クリック保存。
  `<article>` が見つからないときのフォールバック。自動試験済み・ユーザー実機確認済み（2026-06-18、動作問題なし）。動画補助処理（0.5.2）は変更なし。
- 現行の主経路: ChromeまたはFirefoxでXのTL上の画像を右クリックし、`X画像をEagleへ保存...` から保存小窓を開く。
  小窓では保存先フォルダ選択が必須で、最近使ったフォルダ5件、検索、または検索語からの新規作成で選ぶ。
- できていること: 画像1枚投稿のTL右クリック取得、Eagle保存、保存結果ファイルパス確認、
  最近使ったフォルダ5件表示、保存操作パネルの独立表示、サムネイルの画像全体表示、新規フォルダ作成UI、
  X動画1本を `yt-dlp` 補助処理で取得してEagleへ `addFromPath` 保存する実現性確認、
  コピー済み外部動画URLを既存ポップアップから保存するv0.5.15経路の実装と自動試験。
- 次に確認するなら: Eagle画面上で注釈の見え方が問題ないか、v0.5.2再読み込み後にEagleメモ欄の
  先頭整理と動画保存後の失敗表示補正が期待通りか。
- 次チャットで広げないこと: 画像サムネイル付き最近保存項目、自動フォルダ分類、作者・キャラの
  確定タグ付け、既存Eagle公式拡張機能の改造、X API利用、複数フォルダ同時所属、動画のTL右クリック保存、
  複数動画対応。
- 重要な運用前提: ユーザーは保存時に1つはフォルダ分けしたい。あとで整理する方式は負担になるため、
  右クリック保存ではフォルダ必須を維持する。
- 操作リマインド（2026-06-25 user-stated）: Firefox の「ファイルからアドオンをインストール」や
  Finder風のファイル選択画面で、長いファイルパスを指定する場面では、`Cmd+Shift+G`
  （macOSの「フォルダへ移動」、パスを直接貼り付けて移動するショートカット）を案内する。
  ユーザーはこのショートカットを忘れやすいと自己申告しているため、今後 `.xpi`、`manifest.json`、
  添付ファイル、生成物などの場所を開かせる場合は、検索欄やクリック探索だけでなく
  「`Cmd+Shift+G` でこのパスを貼る」と明示する。
- GitHub運用リマインド（2026-06-25 user-stated）: Firefox自動更新用のGitHub Pages
  （GitHub上のファイルをWeb公開する機能）作成・更新作業は、Safariから `owakoidhi` アカウントで行った。
  次回 `browser-update-feed-7m4q2d` リポジトリの更新、Pages設定確認、ファイルアップロードを案内する場合は、
  Safariで該当アカウントに入っている前提を先に確認する。公開リポジトリURLは
  `https://github.com/20260624yosuke/browser-update-feed-7m4q2d`。
- 次のユーザー作業: Firefox一時アドオンまたは署名済み `.xpi` を v0.5.15 へ更新する。
  補助処理は LaunchAgent を再起動済みで `/health` は v0.5.15 を返す。ポップアップに
  `動画補助: 起動中` と表示されれば、Token入力なしで動画保存を試せる。
- UI統一（Stage 2）方針確定（2026-06-20 武田さん）。別セッションで進める。詳細な実装引き継ぎは
  ルート直下 `claude-handoff-x-eagle-ui-unification.md`（本ページが正本、そちらは実装手順の補助文書）。
  - A: 統一先は右クリックで開く別ウィンドウ `save.html`（本命）。拡張ボタンの貼り付き型パネル
    `popup.html` は廃止し、動画保存機能を `save.html` へ移す。右クリックでできることを最大化する。
  - B: どの入口から保存してもフォルダ指定を必須に統一（現状ポップアップは任意・右クリックは必須）。
  - C: 今回は投稿単体ページで画像＋動画を `save.html` に統合するところまで。TL（一覧画面）からの
    直接の動画保存は“実現性の小さな確認だけ”（UIは作らない＝実現性ゲート方式）。本実装は結果を見て別途。
- 上記以外で今回広げないこと: 複数動画対応、自動分類、
  複数フォルダ同時所属。

## Eグルフォルダ追加プラグイン設計（次段階・未着手・2026-07-01）

既存Eグルアイテムへのフォルダ追加は、EグルのHTTP APIでは不可能で、Eグルプラグインの `eagle.item.save()` でのみ可能
（メモリ `reference-eagle-http-api-no-folder-change` 参照。wiki 外のメモリファイル）。武田さん承認済み(2026-07-01)。次のセッションで実装する。

- **成果物**: `tools/eagle-folder-plugin/`(新規)。Eグルのバックグラウンドサービスプラグイン。
- **構成**:
  - `manifest.json`: `"main": { "serviceMode": true, "url": "index.html", ... }`。`serviceMode: true` で
    Eグル起動時に自動常駐(mcp-serverプラグインと同じ方式)。
  - `index.html`: `plugin.js` を読むだけの最小HTML(サービスは非表示ウィンドウで動く)。
  - `plugin.js`: `eagle.onPluginCreate()` 内で Node.js `require('http')` のHTTPサーバーを
    ローカル(`127.0.0.1`、専用ポート案 41796)で起動。`POST /add-to-folders {itemId, folders}` を受けたら
    `const item = await eagle.item.getById(itemId); item.folders = [...new Set([...item.folders, ...folders])]; await item.save();`
    を実行。認証は既存 helper と同じ拡張機能ヘッダー方式(`x-eagle-...`)＋拡張機能オリジン確認。CORSも同様に付ける。
- **実現性の裏付け**: `serviceMode: true` は Eグル公式(developer.eagle.cool のBackground Service)で確認。
  Node.js `http` はEグルMCPプラグイン(`eagle-api-cli.js` が `require('http')`)で実証。`eagle.item.getById().folders`
  変更＋`item.save()` はEグルMCPの `item.js`(`item_add_to_folders`)で実証。
- **拡張機能側(将来 v0.5.37)**: `saveOneImage` で既存アイテムを見つけたとき、メモ追記(当時の v0.5.36)に加え、
  このプラグインへ `POST /add-to-folders` でフォルダ追加を依頼する。addFromURL はしない(重複を増やさない)。
  プラグイン未起動時はメモ追記のみ(当時の v0.5.36 の挙動にフォールバック)。
- **実機検証が必須な点**: プラグインのEグルへのインストール(`tools/eagle-folder-plugin/` を Eグルの
  Plugins へ配置 or 開発者モードで読み込み)と、`item.save()` が実際にフォルダを永続化するかは、武田さんの実機確認が必要
  (私=LLMはEグルにプラグインを入れられない)。

## 実装

- 拡張機能: `tools/x-eagle-save-extension/`
- 動画補助処理: `tools/x-eagle-video-helper/`
- フォルダ追加プラグイン（予定）: `tools/eagle-folder-plugin/`（上記設計・未着手）
- 方式: ブラウザ拡張機能で X ページの DOM（画面を構成するHTML要素）を読み、
  Eagle ローカル API `http://localhost:41595/api/item/addFromURL` へ送る。
- 動画方式: Firefoxの投稿単体ページで拡張機能ボタンを押し、投稿URL・作者ID・投稿ID・反応数・投稿本文・
  保存理由・保存先フォルダを `http://127.0.0.1:41795/save-x-video` へ送る。補助処理は
  `yt-dlp --cookies-from-browser firefox` で動画を一時取得し、EagleローカルAPI `/api/item/addFromPath` へ渡す。
  Eagle item ID で保存確認できた場合のみ一時ファイルを削除する。
- Eagle 読み取り確認: 2026-06-17、`/api/application/info` と `/api/library/info` が成功。
- Eagle 版本: 4.0.0 build 20260401。
- 2026-06-17: EagleローカルAPI `/api/folder/listRecent` はローカル確認で成功。保存小窓上部の
  「最近使ったフォルダ」候補に使える。
- 2026-06-17: EagleローカルAPI `/api/item/list` のキーワード検索で、v0.3.0保存のX画像2件の
  注釈・投稿URL・保存先フォルダを確認。ただしEagle画面での注釈表示は未確認。
- v0.3.1: `save.html` / `save.js` で、EagleローカルAPI `/api/folder/listRecent` から
  最近使ったフォルダを読み、保存小窓右側のフォルダツリー上部に最大5件表示する。検索入力中は
  最近フォルダを隠し、検索結果を優先する。
- v0.3.2: `manifest.json` の `background` に `scripts` と `service_worker` を併記し、
  Chromeはservice worker（拡張機能の裏側で動く処理）、Firefoxはbackground script
  （Firefox側で動く裏側の処理）を使える形にした。`background.js` は `browser` / `chrome`
  の両方のAPI呼び出しに対応した。
- v0.3.2: `save.html` で保存先表示・保存ボタン・状態表示を `save-panel` として検索欄直下へ独立させ、
  候補フォルダ一覧だけがスクロールするようにした。これにより候補が多くても保存操作部分は常に見える。
- v0.3.3: `save.html` / `save.js` で、左側のサムネイルを `object-fit: contain`
  （画像全体を枠内に収める表示）へ変更し、画像読み込み後の `naturalWidth` / `naturalHeight`
  からプレビュー枠の縦横比をCSS変数へ反映する。
- v0.3.3: `eagle-save.js` に EagleローカルAPI `/api/folder/create` 呼び出しを追加した。
  保存小窓では検索語があるとフォルダ一覧の先頭に作成行を出し、現在選択中のフォルダの下、
  未選択ならルートに作成する。作成後はフォルダ一覧を読み直し、新規フォルダを保存先に選ぶ。
- v0.4.0: `tools/x-eagle-video-helper/server.js` を追加した。手動起動時に起動時トークンを表示し、
  `127.0.0.1` 限定で `POST /save-x-video` だけを受け付ける。非X URLと不一致トークンは拒否する。
- v0.4.0: `popup.html` / `popup.js` で、投稿単体ページ用ポップアップに「動画補助トークン」と
  「動画を保存」ボタンを追加した。画像保存は従来どおり画像URLがある場合だけ有効、動画保存は
  投稿情報・保存先フォルダ・補助トークンがある場合だけ有効にする。
- v0.4.1: `save.js` で、新規フォルダ作成後に `folder/list` へ反映されるまで待つ処理を追加した。
  `eagle-save.js` 側でも画像保存前に保存先フォルダIDがEagle側で確認できることを待つ。
  これは右クリック画像保存小窓の修正であり、v0.4.0 の動画補助処理は変更しない。
- v0.4.2: `tools/x-eagle-video-helper/server.js` で、動画の一時保存先を `/tmp/x-eagle-video-*` に固定した。
  Eagle API失敗時は、応答本文、一時動画パス、Eagleへ渡したファイルパス、ファイルサイズ、保存先IDを
  JSONの `details` として返す。`popup.js` はその詳細をエラー表示へ含める。
- v0.5.0: `tools/x-eagle-video-helper/server.js` に `GET /health` を追加した。`popup.js` はポップアップ表示時に
  起動確認し、未起動時は3秒ごとに再確認して、`動画補助: 起動中` / `動画補助: 未起動` を表示する。
- v0.5.0: `popup.html` / `popup.js` から動画補助トークン入力欄を外し、`POST /save-x-video` は専用ヘッダーで
  呼び出す。補助処理側は拡張機能由来の呼び出しを許可し、Webページ由来の呼び出しは拒否する。起動用に
  `tools/x-eagle-video-helper/start.command` を追加した。
- v0.5.1: `tools/x-eagle-video-helper/server.js` で、裏起動でも `/opt/homebrew/bin/yt-dlp` を使えるようにした。
  実行時PATHへ `/opt/homebrew/bin` と `/usr/local/bin` を追加し、`/health` の戻り値に `ytDlpBin` を含める。
- v0.5.2: `eagle-save.js` と `tools/x-eagle-video-helper/server.js` の注釈生成を変更し、Eagleメモ欄の先頭を
  `@作者ID`、反応数2行、投稿本文の短い抜粋にした。保存日時・投稿URL・取得方法などの機械的情報は
  `## 保存情報` と `## 技術メモ` の下へ移動した。
- v0.5.2: `tools/x-eagle-video-helper/server.js` で、`/api/item/addFromPath` が失敗応答を返した後でも、
  `item/list` で同じファイル名・投稿URL・保存先フォルダの動画を確認できた場合は成功扱いにして、
  一時動画ファイルを削除する。
- v0.5.3: `tools/x-eagle-save-extension/extractor.js` の `extractFromContextImage()` に、親 `<article>` が
  見つからないときのフォールバックを追加した。`statusInfoForContextImage()` がページURL→クリック画像の祖先
  `a[href*="/status/"]`→文書内リンクの順で投稿を特定し、`findArticleByStatusId()` が一致記事を返せば既存
  `extractMetrics()` / `extractPostText()` で反応数・本文を読む。保存対象はクリックした画像1枚。`save.html` /
  `save.js` / `eagle-save.js` は変更なし（注釈生成は反応数が空でも `未取得` 表示で動く）。`manifest.json` の
  `version` を 0.5.3 に上げた。動画補助処理は変更しない。
- v0.5.4: `tools/x-eagle-save-extension/save.js` / `save.html` で、右クリック保存小窓のフォルダ選択を
  キーボード操作に対応させた。`save.html` の検索 `input` に `autofocus` を付け、`save.js` は表示時と
  ウィンドウ復帰時に検索欄へフォーカスする。`renderFolderList()` が現在の候補（検索時は一致フォルダ、
  検索語が空のときは最近使ったフォルダ）を `navCandidates` に保持し、入力ごとに `selectTopCandidate()`
  が先頭を選ぶ。`moveActive()` が `↑`/`↓` で選択を移し、検索欄の `Enter` で `saveSnapshot()` を呼ぶ。
  各フォルダ候補に `data-folder-id` を付け、`applyActiveHighlight()` が再描画なしで選択表示を更新する。
  検索語が空のあいだは自動選択しない（誤保存防止）。`manifest.json` の `version` を 0.5.4 に上げた。
  extractor.js・動画補助処理・注釈形式は変更しない。
- v0.5.5: `tools/x-eagle-save-extension/save.html` で、右カラム `.main-pane` のDOM順を
  `.toolbar` → `.folder-browser` → `.save-panel` に並べ替え、`grid-template-rows` を
  `auto minmax(0,1fr) auto` に変更。中央にあった保存操作の帯（`.save-panel`）を最下部へ移し、
  検索→フォルダ一覧→保存先＋保存ボタンの縦一直線にした。`.save-panel` の区切り線を `border-bottom`
  → `border-top` に。`.save-panel` のクラス名・要素ID・`save.js` は変更せず、DOM並べ替えとCSSのみ。
  `manifest.json` の `version` を 0.5.5 に上げた。
- v0.5.6: `tools/x-eagle-save-extension/background.js` のウィンドウサイズ定数を 920×680 → 560×620 に変更。
  `save.html` の左プレビュー欄を 250px → 180px に、プレビュー画像の最大高さを 340px → 200px に、
  投稿テキスト表示域・コメント欄・フォルダ候補行の余白を縮小。さらに `openSaveWindow()` で
  `windows.getCurrent` を呼び、現在のブラウザウィンドウの中央に保存小窓を重ねて表示するよう変更
  （取得失敗時はOS既定位置へフォールバック）。`save.js` は変更なし。
  `manifest.json` の `version` を 0.5.6 に上げた。
- v0.5.7: `tools/x-eagle-save-extension/save.html` / `popup.html` に `保存候補` 一覧を追加した。
  `save.js` / `popup.js` は `selectedImageUrls` を持ち、初期状態では `snapshot.imageUrls` 全件を
  保存対象にする。チェックを外すと `selectedImageUrlList()` から外れ、`eagle-save.js` の
  `saveSnapshotToEagle()` へ渡す `imageUrls` がチェック済みだけになる。`eagle-save.js` には
  `saveTargets()` を追加し、保存対象の絞り込み後も元投稿内の `originalIndex` / `originalTotal` を
  注釈とファイル名へ使う。これにより2枚目だけ保存しても `x-...-02` と `画像 2/2` が残る。
  `manifest.json` の `version` を 0.5.7 に上げた。
- v0.5.8: `tools/x-eagle-save-extension/save.js` / `popup.js` に `closeAfterSuccessfulSave()` を追加した。
  保存成功時に成功表示を出した後、短い待ち時間を置いて `window.close()` を呼ぶ。右クリック保存小窓の
  画像保存、投稿ページポップアップの画像保存、投稿ページポップアップの動画保存で呼び出す。保存失敗時の
  `catch` 経路では呼ばないため、エラー確認と再試行ができる。`manifest.json` の `version` を
  0.5.8 に上げた。
- v0.5.9: `tools/x-eagle-video-helper/server.js` の `waitForEagleItem()` を、Eagle確認が取れなくても
  例外にせず `null` を返す best-effort 方式へ変更（20回×0.5秒）。`buildVideoSaveResult()` を追加し、
  `addFromPath` が item ID を返した時点で `ok: true` を返す。`confirmed`（Eagle側で確認できた）が真の
  ときだけ一時ファイルを削除する。`HELPER_VERSION` を 0.5.9 に上げた。これで「Eagleに保存済みなのに
  UIが失敗表示」を解消する。
- v0.5.9: `tools/x-eagle-save-extension/popup.js` で、緑枠の状態文（`snapshotStatusMessage`）から
  「動画: 補助処理で取得を試せます」の行を削除し、未起動表示を「動画補助: 未起動（動画保存には起動が必要）」へ
  変更した。`manifest.json` の `version` を 0.5.9 に上げた。
- v0.5.9: 動画補助処理のログイン時自動起動として、`tools/x-eagle-video-helper/com.takedayousuke.x-eagle-video-helper.plist`
  と `install_x_eagle_video_helper_agent.sh` / `uninstall_x_eagle_video_helper_agent.sh` を追加した。
  `RunAtLoad` + `KeepAlive`、`ThrottleInterval` 30、`PATH` に `/opt/homebrew/bin` 等、ログは
  `~/Library/Logs/XEagleVideoHelper/`。Label は `com.takedayousuke.x-eagle-video-helper`。日記キャプチャの
  LaunchAgent 作法（`launchctl bootstrap` / `kickstart`）に合わせた。
- v0.5.10: `tools/x-eagle-save-extension/eagle-save.js` の `saveOneImage` から保存後の存在確認
  （`waitForSavedItem` / `itemExistsByName`）を削除した。`/api/item/addFromURL` を Eagle が受け付けた時点で
  成功とみなし、重複時の「重複追加の警告」ダイアログなど Eagle 側の事後処理を拡張機能のワークフローから
  切り離す。フォールバック（ブラウザ側取得）は主経路の add 自体が失敗したときだけにし、確認失敗では起こさない
  （二重追加防止）。`manifest.json` の `version` を 0.5.10 に上げた。helper（`server.js`）は変更なし。
- v0.5.11: TL（一覧）からの動画保存をクリップボード方式で追加した。`extractor.js` に
  `extractFromStatusUrl(documentRef, statusUrl)` を追加（`/status/ID` URL から `findArticleByStatusId()` で
  TL DOM の該当 article を探し、`extractFromArticle()` で反応数・本文を読む。該当 article が現在のDOMに
  無くても、投稿URLだけで動画保存できるよう `ok:true`・`postInView:false`・`metrics` 空で返す）。
  `content-script.js` に `x-eagle-snapshot/extract-by-status` メッセージのハンドラを追加。`popup.js` は開いた時、
  投稿単体ページなら従来どおりページ抽出（`extractFromDocument`）、そうでなければクリップボードのX動画URL
  （`navigator.clipboard.readText`、手動貼り付け欄 `#videoUrl` もあり）から `extractFromStatusUrl` を呼び、
  `postUrl` を既存の `saveVideoSnapshot()` へ渡す。`popup.html` に「TL動画のURL」欄を追加。`manifest.json` に
  `clipboardRead` を追加し `version` を 0.5.11 に上げた。投稿単体ページの既存動作と helper（`server.js` 0.5.9）は変更なし。
- v0.5.12: 拡張ボタンのポップアップ（`popup.html` / `popup.js`）のフォルダ選びを、右クリック大窓（`save.html`）の
  使い勝手にそろえた。`save.js` から最近フォルダ（`/api/folder/listRecent` → `recentFolders` / `renderRecentFolders`）、
  キーボード操作（`navCandidates` / `selectTopCandidate` / `moveActive` / `applyActiveHighlight` / 検索欄の
  `↑`/`↓`/`Enter`）、新規フォルダ作成（`createNewFolderOption` / `openCreateFolderDialog` / `createFolderFromDialog` /
  `waitForCreatedFolder`、`eagleSave.createFolder` 再利用）を `popup.js` へ移植。`popup.html` に最近フォルダ欄と
  新規フォルダ作成ダイアログを追加し、フォルダ一覧の見た目を `save.html` のテーマに合わせた。画像保存もフォルダ必須に
  統一（`updateSaveButtons` と `saveSnapshot` に未選択ガード。従来の localStorage 直近フォルダと「フォルダ指定なし」は廃止）。
  動画保存・クリップボード読み取り・補助処理表示・`initialize` の入口判定、右クリック側 `save.html` / `save.js`、
  `background.js`、`manifest`(version除く) は変更なし。`manifest.json` の `version` を 0.5.12 に上げた。フォルダ選びの
  ロジックは `save.js` と重複（動いている `save.js` を触らない方針を優先。将来の共通化は別途）。
- v0.5.13: Firefox常用インストール用に `manifest.json` の `version` を 0.5.13 へ上げ、
  `browser_specific_settings.gecko` を追加した。固定 add-on ID は `x-eagle-snapshot-saver@takedayousuke.local`。
  Manifest V3（拡張機能設定形式の第3版）のため `strict_min_version` は `109.0`。
  Firefoxのインストール時表示に合わせ、X投稿URL・本文・画像URL・反応数・保存操作をローカルのEagle APIと
  動画補助処理へ渡す用途として `data_collection_permissions.required` に `browsingActivity` /
  `websiteContent` / `websiteActivity` を記載した。`scripts/build-firefox-xpi.sh` は署名なし `.xpi` を作り、
  `scripts/sign-firefox-xpi.sh` は AMO API credentials（addons.mozilla.orgの署名用の鍵）で
  unlisted（公開一覧に載せない自己配布）署名を行う。`web-ext` は Node.js 25系で
  `Bus error` になったため、署名スクリプトでは Node.js 22/24系を優先する。保存ロジックは変更していない。
- v0.5.14: Eagle メモ欄の注釈生成を、画像保存側 `eagle-save.js` と動画補助処理 `server.js` の両方で変更した。
  先頭に `【見る用】` を置き、作者、主要反応数、本文、保存先の末尾フォルダ名、保存対象、JST 表示の保存日時、
  保存理由を短く表示する。続けて `【LLM用】` を置き、`source` / `item_kind` / `author_id` / `post_id` /
  `post_url` / `captured_at` / `target` / `folder_path` / `image_url` / `metrics.*` / `post_text` /
  `save_reason` をキー付きで残す。既存 Eagle 項目は書き換えていない。`manifest.json` の `version` を 0.5.14 に上げた。
- v0.5.15: 動画補助処理をX投稿URL専用から、クリップボード由来の外部動画URLにも対応する形へ広げた。
  `popup.js` はクリップボードまたは手動入力欄から、まずX投稿URLを従来どおり `extractFromStatusUrl()` に渡し、
  Xでなければ `external-video` のスナップショット（元URL・元サイト・保存日時のみ）を作る。
  `server.js` は `/save-video` を追加し、従来の `/save-x-video` は互換のため残した。X動画では従来どおりFirefox Cookieを
  `yt-dlp` に渡すが、TikTokなどの外部動画URLではCookieを渡さない。外部動画の注釈は `source: external-video`、
  `source_host`、`source_url`、`save_reason` を中心に残す。安全側の制限として、`localhost`、プライベートIP、
  `.local` などローカル端末・家庭内ネットワークのURLは拒否する。`manifest.json` と `HELPER_VERSION` を 0.5.15 に上げた。
- v0.2.0: Eagle ローカル API `/api/folder/list` からフォルダ一覧を読み、保存時に `folderId`
  を渡す。フォルダは検索して選ぶ。
- v0.2.0: 画像URLだけを Eagle へ渡す方式から、ブラウザ側で画像を取得して `base64`
  形式で Eagle へ渡す方式へ変更。X画像の一部だけ Eagle 側で取り込みに失敗するリスクを下げる。
- v0.2.0: 保存後に `/api/item/list` で Eagle 側に該当名の項目が見えるか確認する。
- v0.2.0: Firefox は同じ `manifest.json` を一時アドオンとして読み込む方針。コード上は
  `browser` / `chrome` API の両方に寄せたが、Firefox実機保存は未検証。
- v0.2.1: Firefox で `snapshot is undefined` になったため、`extractor.js` 注入後に別実行で
  `window.XEagleSnapshotExtractor` を読む方式をやめ、`extractor.js` と `snapshot-runner.js` を
  同時注入して、その1回の実行結果から投稿情報を受け取る方式へ変更。ただしこの方式も
  Firefox実機で有効とは確認できず、現行方式ではない。
- v0.2.2: Firefox対応を `content_scripts`（Xページ側に置く読み取り係）と `tabs.sendMessage`
  （拡張ポップアップと読み取り係の通信）へ変更。ポップアップから注入結果を直接読む依存を外した。
  既に開いているXタブ向けに、読み取り係が未接続の場合だけ `extractor.js` と `content-script.js`
  を後から注入して再通信する保険を残す。
- v0.2.2: ポップアップ下部に拡張機能バージョンを表示し、Firefox側で古い一時アドオンを見ている
  状態を判別できるようにした。
- v0.2.3: Firefoxで `投稿読み取り処理を初期化できませんでした` が出たため、抽出APIを
  `globalThis` と `window` の両方へ明示的に配置し、`content-script.js` 側も両方を見るように変更。
  Firefoxのcontent script（Xページ側に置く読み取り係）で、`globalThis` と `window` が同一扱いに
  ならない可能性を原因候補として扱う。
- v0.2.3: Chromeで `Failed to fetch` が出たため、v0.2.0で主経路にしたブラウザ側画像取得を
  fallback（失敗時の予備手段）へ下げ、通常はv0.1同様にEagle側で画像URLをダウンロードする方式へ戻す。
  保存後確認とフォルダ指定は維持する。
- v0.2.4: Firefoxで投稿読み取りとフォルダ選択は成功したが、保存時に
  `Eagle側ダウンロード: NetworkError when attempting to fetch resource` が出たため、
  EagleローカルAPIへの `POST` から `Content-Type: application/json` を外した。Eagle公式サンプルも
  JSON文字列を送る際にこのヘッダーを付けていないため、Firefoxの事前確認通信で失敗する可能性を下げる。
- v0.2.5: Eagle注釈の視認性改善として、文面は変えずに `取得方法` と反応数
  （いいね・リポスト・引用・返信・表示）を注釈の一番上へ移動。
- v0.3.0: Chrome 先行で `contextMenus`（右クリックメニュー）を追加し、X/Twitter上の画像に
  `X画像をEagleへ保存...` を表示する。`background.js` が右クリックメニュー選択を受け、
  `content-script.js` が最後に右クリックされた画像要素と親 `article` を使って、投稿URL・作者ID・
  投稿本文・反応数・同一投稿内画像URLを抽出する。
- v0.3.0: `save.html` / `save.js` を追加し、右クリック保存専用のEagle風2カラム保存小窓を開く。
  左側に画像プレビュー・投稿情報・任意コメント、右側にEagleフォルダ検索と階層表示を置く。
  この小窓ではフォルダ未選択の保存を拒否する。
- v0.3.0: `eagle-save.js` を追加し、注釈生成・画像名生成・Eagle保存・保存後確認を共通化した。
  既存 `popup.js` は `requireFolder: false`、新規 `save.js` は `requireFolder: true` を指定することで、
  既存ポップアップの挙動を維持しつつ右クリック保存だけフォルダ必須にした。

## 注釈形式

```text
@作者ID
いいね: ... / リポスト: ...
返信: ... / 引用: ... / 表示: ...
投稿本文の短い抜粋

## 保存情報
保存対象: 画像 1/4
保存先: 資料_画像フォルダ_まとめ / 00_創作_in_box / 2026_06
保存日時: 2026-06-17T...
投稿URL: https://x.com/...
作者ID: @...
投稿ID: ...
画像URL: https://pbs.twimg.com/media/...
保存理由: ...

## 技術メモ
取得方法: X画面表示から取得（無料方式・丸め表示の可能性あり）
数値は保存時点の画面表示由来。X側の丸め表示や表示差がそのまま残る。
```

動画版では、保存対象と取得方法が次の形になる。

```text
@作者ID
いいね: ... / リポスト: ...
返信: ... / 引用: ... / 表示: ...
投稿本文の短い抜粋

## 保存情報
保存対象: 動画
保存先: ...
保存日時: 2026-06-17T...
投稿URL: https://x.com/...
作者ID: @...
投稿ID: ...
保存理由: ...

## 技術メモ
取得方法: yt-dlp補助 + X投稿メタデータ
数値は保存時点の画面表示由来。X側の丸め表示や表示差がそのまま残る。
```

## 検証状態

- v0.1 ユーザー実機確認: 「いい感じ」と評価あり。
- 2026-06-17 実データ確認: 対象投稿 `https://x.com/22_0724/status/2066845693533454574` は
  `01`、`02`、`04` の3件に注釈あり。`03` は Eagle ライブラリ内に確認できず、注釈欠落ではなく
  画像追加自体の欠落として扱う。
- v0.2 実装済み: フォルダ選択UI、`folderId` 保存、画像のブラウザ側取得、保存後のEagle項目確認、
  Firefox向けAPI分岐、`srcset` 画像抽出補強。
- v0.2 自動試験済み: URL・画像URL・`srcset`・反応数テキストの基本抽出、拡張設定 JSON、
  JS 構文、Eagle フォルダ一覧629件の読み取り。
- v0.2 実機未検証: Chrome/Firefox への再読み込み、X 実ページでの4枚保存再テスト、
  フォルダ指定保存のEagle反映、Firefox実機保存。
- v0.2.1 自動試験済み: 既存抽出テスト、`manifest.json` JSON検証、`popup.js` /
  `extractor.js` / `snapshot-runner.js` 構文確認。
- v0.2.1 実機未検証: Firefox一時アドオン再読み込み後に `snapshot is undefined` が解消するか。
  ユーザー指摘により、解消できていない可能性が高い暫定修正として扱う。
- v0.2.2 自動試験済み: 既存抽出テスト、`manifest.json` JSON検証、`popup.js` /
  `extractor.js` / `content-script.js` 構文確認。
- v0.2.2 実機未検証: Firefox一時アドオン再読み込み後に、ポップアップで `バージョン: 0.2.2`
  が表示され、X実ページで読み取りが成功するか。
- v0.2.3 自動試験済み: 既存抽出テスト、ブラウザ環境想定で `globalThis` / `window` の両方に
  抽出APIが配置されること、`manifest.json` JSON検証、`popup.js` / `extractor.js` /
  `content-script.js` 構文確認。
- v0.2.3 実機未検証: Firefoxで読み取りが成功するか、Chromeで画像保存が復旧するか。
- v0.2.4 状況: ユーザー実機で Chrome 保存は「いい感じ」と確認済み。Firefoxは投稿読み取り・
  フォルダ選択まで到達したが、保存通信で失敗。v0.2.4はその保存通信の修正で、実機再確認は未実施。
- v0.2.4 自動試験済み: 既存抽出テスト、`manifest.json` JSON検証、`popup.js` /
  `extractor.js` / `content-script.js` 構文確認。
- v0.2.5 自動試験済み: `manifest.json` JSON検証、`popup.js` / `extractor.js` /
  `content-script.js` 構文確認、既存抽出テスト。
- v0.3.0 自動試験済み: 既存抽出テスト、TL風HTMLでの右クリック画像起点抽出、同じ画像URLが
  複数投稿にある場合でも右クリックした投稿を選ぶこと、フォルダ未選択時の保存拒否、
  `manifest.json` JSON検証、`background.js` / `content-script.js` / `eagle-save.js` /
  `extractor.js` / `popup.js` / `save.js` 構文確認。
- v0.3.0 実機確認済み（部分）: Chromeで画像1枚投稿のTL上右クリックから保存小窓を開き、
  Eagle保存結果のファイルパスを確認。
- v0.3.0 API確認済み: 保存したX画像2件の注釈・投稿URL・保存先フォルダをEagleローカルAPIで確認。
- v0.3.0 実機未確認: TL右クリック保存の複数画像投稿、Eagle画面での注釈表示、Firefox保存成功。
- v0.3.1 自動試験済み: `manifest.json` JSON検証、`background.js` / `content-script.js` /
  `eagle-save.js` / `extractor.js` / `popup.js` / `save.js` 構文確認、既存抽出テスト。
- v0.3.1 ローカルAPI確認済み: `/api/folder/listRecent` で最近使ったフォルダ5件を取得。
- v0.3.1 実機確認済み: Chrome拡張機能再読み込み後、小窓に最近使ったフォルダ5件が表示されることを
  ユーザーが検証済み。
- v0.3.2 自動試験済み: `manifest.json` JSON検証、`background.js` / `content-script.js` /
  `eagle-save.js` / `extractor.js` / `popup.js` / `save.js` 構文確認、既存抽出テスト、
  Firefox想定の `browser` APIで右クリック保存入口から保存小窓を開く処理、保存操作パネル構造。
- v0.3.2 実機確認済み（部分）: ユーザーがFirefox使用感と保存操作パネル独立表示を「いい感じ」と報告。
- v0.3.2 実機未確認: FirefoxでEagle保存完了まで通ることの明示確認。
- v0.3.3 自動試験済み: `manifest.json` JSON検証、`background.js` / `content-script.js` /
  `eagle-save.js` / `extractor.js` / `popup.js` / `save.js` 構文確認、既存抽出テスト、
  サムネイル全体表示用のHTML/CSS構造、新規フォルダ作成APIのpayload（送信内容）、
  フォルダ階層の `parentId` 保持を確認。
- v0.3.3 公式API確認済み: Eagle公式ドキュメント上で `/api/folder/create` は `POST`、
  `folderName` 必須、`parent` 任意として確認。
- v0.3.3 実機未確認: Chrome/Firefox拡張機能を再読み込みした実画面で、サムネイルがクロップされず
  全体表示されるか、新規フォルダ作成後にそのフォルダへ保存できるか。
- v0.4.0 実現性ゲート済み: `yt-dlp --cookies-from-browser firefox` でX投稿
  `https://x.com/fv5b9x/status/2061039996543570164` からMP4を取得し、Eagle `/api/item/addFromPath` で保存成功。
  Eagle API `/api/item/info?id=MQI17LE0PLEWK` で `ext: mp4`、動画寸法、再生時間、注釈、投稿URL、
  保存先フォルダを確認。一時ファイルは削除済み。
- v0.4.0 自動試験済み: `manifest.json` JSON検証、`background.js` / `content-script.js` /
  `eagle-save.js` / `extractor.js` / `popup.js` / `tools/x-eagle-video-helper/server.js` 構文確認、
  既存抽出テスト、動画補助処理の単体テスト、補助サーバーの非X URL拒否とトークン不一致拒否。
- v0.4.0 コマンド確認済み: 補助処理と同じ `yt-dlp` 引数で対象X投稿のMP4取得が正常終了。
- v0.4.0 実機未確認: Firefox拡張機能を再読み込みし、ポップアップから動画補助処理へ接続して
  実際のX動画投稿をEagleへ保存する操作。Eagle画面上での動画再生・注釈表示の目視確認。
- v0.4.1 自動試験済み: 新規フォルダIDが `folder/list` へ反映されるのを確認してから
  `/api/item/addFromURL` へ進むこと、画像保存payloadに新規フォルダIDを渡すことを確認。
- v0.4.1 実機確認済み: Firefoxでサムネイル全体表示はユーザーが良好と報告。新規フォルダ作成後、
  そのフォルダへ画像保存まで通ることもユーザー実機で確認済み。
- v0.4.2 自動試験済み: `server.js` / `popup.js` 構文確認、`tools/tests/test_x_eagle_video_helper.js`、
  `tools/tests/test_x_eagle_save_extractor.js` が通過。一時保存先が `/tmp` であることを単体テストで確認。
- v0.4.2 実機確認済み（ユーザー報告）: Firefox投稿単体ページからの動画保存、動画本体とメタデータ抽出が成功。
- v0.5.0 自動試験済み: `server.js` / `popup.js` 構文確認、`tools/tests/test_x_eagle_video_helper.js`、
  `tools/tests/test_x_eagle_save_extractor.js` が通過。`/health` 起動確認と拡張機能由来ヘッダーの許可を単体テストで確認。
- v0.5.0 実機未確認: Firefox一時アドオンを再読み込みした後、Token入力なしで動画保存できるか。
- v0.5.1 自動試験済み: `server.js` 構文確認、`tools/tests/test_x_eagle_video_helper.js`、
  `tools/tests/test_x_eagle_save_extractor.js`、`/opt/homebrew/bin/yt-dlp --version` が通過。
- v0.5.1 ローカル確認済み: 補助処理を再起動し、`GET /health` が `version: 0.5.1`、
  `tempRoot: /tmp`、`cookieBrowser: firefox`、`ytDlpBin: /opt/homebrew/bin/yt-dlp` を返すことを確認。
- v0.5.1 実機未確認: `spawn yt-dlp ENOENT` 修正後、同じX動画投稿で保存が通るか。
- v0.5.2 自動試験済み: `server.js` / `eagle-save.js` / `popup.js` / `save.js` 構文確認、
  `tools/tests/test_x_eagle_video_helper.js`、`tools/tests/test_x_eagle_save_extractor.js` が通過。
  注釈の先頭整理と、Eagle保存済み動画を検出して成功扱いへ戻す補正を単体テストで確認。
- v0.5.2 ローカル確認済み: 補助処理を再起動し、`GET /health` が `version: 0.5.2`、
  `tempRoot: /tmp`、`cookieBrowser: firefox`、`ytDlpBin: /opt/homebrew/bin/yt-dlp` を返すことを確認。
- v0.5.2 実機未確認: Firefox一時アドオン再読み込み後、Eagleメモ欄の先頭整理と
  動画保存後の失敗表示補正が実画面で期待通りか。
- v0.5.3 自動試験済み: `node --check tools/x-eagle-save-extension/extractor.js`、
  `node tools/tests/test_x_eagle_save_extractor.js`（拡大表示=ページURL由来、一覧サムネ=リンク由来の
  フォールバック2ケースと manifest version 0.5.3 を追加）、回帰として
  `node tools/tests/test_x_eagle_video_helper.js` が通過。
- v0.5.3 実機確認済み（ユーザー報告）: 2026-06-18、武田さんがメディア欄・拡大表示からの右クリック保存を
  実機検証し、動作問題なしと報告。詳細条件（拡大表示の反応数、一覧サムネ直接右クリック等の内訳）は未細分。
- v0.5.4 自動試験済み: `node --check tools/x-eagle-save-extension/save.js`、`manifest.json` の JSON検証
  （version 0.5.4）、`node tools/tests/test_x_eagle_save_extractor.js`（`save.html` の `autofocus` と
  Enter保存ヒント、`save.js` の `selectTopCandidate` / `moveActive` / `keydown` / `data-folder-id` を
  確認するアサーション追加、固定versionを0.5.4へ）、回帰として `node tools/tests/test_x_eagle_video_helper.js`
  が通過。
- v0.5.4 実機確認済み（ユーザー報告）: 2026-06-18、武田さんが拡張機能を再読み込みして実機検証し、動作OKと
  報告。保存小窓を開くと検索欄へ自動でカーソルが入り、フォルダ名入力後に先頭候補が選択状態になり、
  `Enter`保存・`↑`/`↓`移動が動作。`Enter`即保存・検索欄が空のときの未選択という既定の挙動はそのまま
  受け入れ（変更要望なし）。細かな内訳（個々のキー操作・誤保存有無の検証範囲）は未細分。
- v0.5.5 自動試験済み: `node --check tools/x-eagle-save-extension/save.js`、`manifest.json` の JSON検証
  （version 0.5.5）、`node tools/tests/test_x_eagle_save_extractor.js`（`.save-panel` クラス名維持で構造
  アサーションは不変、固定versionを0.5.5へ）、回帰として `node tools/tests/test_x_eagle_video_helper.js` が通過。
- v0.5.5 実機確認済み（ユーザー報告）: 2026-06-19、武田さんが保存小窓の公式風レイアウト
  （検索→一覧→最下部の保存先＋保存ボタンの縦一直線）を実機検証し、動作確認済みと報告。
- v0.5.6 自動試験済み: `node --check tools/x-eagle-save-extension/save.js`、
  `node --check tools/x-eagle-save-extension/background.js`、`node tools/tests/test_x_eagle_save_extractor.js`、
  回帰として `node tools/tests/test_x_eagle_video_helper.js` が通過。
- v0.5.7 自動試験済み: `node tools/tests/test_x_eagle_save_extractor.js`（保存候補チェック除外、
  2枚目だけ保存時のファイル名 `02` と注釈 `画像 2/2` を確認）、回帰として
  `node tools/tests/test_x_eagle_video_helper.js` が通過。`save.js` / `popup.js` / `eagle-save.js` は
  `node --check` で構文確認済み。
- v0.5.7 実機確認済み（ユーザー報告）: 2026-06-20、武田さんが保存候補チェック除外を実機検証し、
  動作良好と報告。検証証跡として
  `/Users/takedayousuke/Library/Mobile Documents/com~apple~CloudDocs/ダウンロード/02_スクショ保存/ 2026-06-20 0.02.10.jpg`
  が提示された。細かな内訳（通常ポップアップと右クリック保存小窓のどちらを使ったか、除外枚数など）は未細分。
- v0.5.8 自動試験済み: `node tools/tests/test_x_eagle_save_extractor.js`（`save.js` / `popup.js` の
  成功時クローズ関数と `window.close()` 呼び出し、manifest version 0.5.8 を確認）、回帰として
  `node tools/tests/test_x_eagle_video_helper.js` が通過。`save.js` / `popup.js` / `eagle-save.js` は
  `node --check` で構文確認済み。
- v0.5.8 実機確認済み（ユーザー報告）: 2026-06-20、武田さんが保存成功後の自動クローズを実機検証し、
  良好と報告。保存失敗時に閉じない挙動も設計どおり（失敗時の細かな内訳は未細分）。
- v0.5.9 自動試験済み: `node --check`（`server.js` / `popup.js`）、
  `node tools/tests/test_x_eagle_video_helper.js`（`buildVideoSaveResult` が Eagle確認未了で `ok:true` /
  `confirmed:false` / `tempDeleted:false`、確認済みで `tempDeleted:true` を返す回帰テスト追加、`HELPER_VERSION` 0.5.9）、
  `node tools/tests/test_x_eagle_save_extractor.js`（manifest 0.5.9、`popup.js` から「補助処理で取得を試せます」が
  消え「動画保存には起動が必要」が入ることを確認）、`plutil -lint` で plist 検証。
- v0.5.9 ローカル確認済み: LaunchAgent を `launchctl bootstrap` + `kickstart` で登録し、`/health` が
  `version: 0.5.9` を返し、`launchctl print` で `state = running`（pid あり）を確認。ポップアップ相当の
  リクエスト（専用ヘッダー + moz-extension オリジン）に HTTP 200。
- v0.5.9 実機確認済み（ユーザー報告）: 2026-06-20、武田さんが補助処理を起動した状態での動画保存成功を実機確認。
  同日、拡張機能 0.5.9 で「Eagleに保存済みなのにUIが失敗表示」が解消し「保存しました」と成功表示になることも実機確認。
- v0.5.9 実機未確認: Mac 再ログイン後に LaunchAgent が自動起動して「動画補助: 起動中 v0.5.9」になるか（武田さん未再起動）。
  外付けSSD未マウント時の挙動。
- v0.5.10 自動試験済み: `node --check tools/x-eagle-save-extension/eagle-save.js`、
  `node tools/tests/test_x_eagle_save_extractor.js`（manifest 0.5.10、保存経路が `/api/item/list` を呼ばないこと
  ＝重複ダイアログからの切り離しを確認）、回帰として `node tools/tests/test_x_eagle_video_helper.js` が通過。
  `waitForSavedItem` / `itemExistsByName` 参照が残っていないことも grep で確認。
- v0.5.10 実機未確認: 拡張機能を 0.5.10 へ再読み込み後、重複画像を含む複数保存で固まらず、Eagle の重複ダイアログを
  処理してもエラーにならず、重複以外が保存されるか。
- v0.5.11 自動試験済み: `node --check`（background.js / popup.js / save.js / eagle-save.js / content-script.js /
  extractor.js / server.js）、`node tools/tests/test_x_eagle_save_extractor.js`（`extractFromStatusUrl` の
  TL一致・該当なし（`postInView:false`）・非X URL拒否、manifest 0.5.11 と `clipboardRead`、`popup.html` の
  `videoUrl` 欄、`popup.js` の `extract-by-status` 経路を確認）、回帰として
  `node tools/tests/test_x_eagle_video_helper.js` が通過。
- v0.5.11 実機確認済み（ユーザー報告）: 2026-06-20、武田さんが Firefox で実機検証。TL動画を右クリック→X純正
  「動画のアドレスをコピー」→拡張ボタンで、コピー済みURLが読み取られ、シームレスに動画を Eagle 保存できることを
  確認（クリップボード自動読み取りも機能）。
- v0.5.12 自動試験済み: `node --check`（popup.js / save.js / background.js / eagle-save.js / content-script.js /
  extractor.js / server.js）、`node tools/tests/test_x_eagle_save_extractor.js`（popup.html の `recentFolders` /
  `createFolderDialog` / `autofocus` / Enter保存ヒント、popup.js の `selectTopCandidate` / `moveActive` /
  `renderRecentFolders` / `openCreateFolderDialog` / `createFolderFromDialog` / `keydown` / `data-folder-id`、
  manifest 0.5.12 を確認）、回帰として `node tools/tests/test_x_eagle_video_helper.js` が通過。
- v0.5.12 実機確認済み（ユーザー報告）: 2026-06-21、武田さんが Firefox で実機検証し、ポップアップのフォルダ選び
  （最近フォルダ・検索の `↑``↓``Enter`・新規フォルダ作成）と見た目が良好と報告（「UIいい感じ」）。
- v0.5.13 自動試験済み: `node --check`（background.js / content-script.js / eagle-save.js / extractor.js /
  popup.js / save.js / server.js）、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js` が通過。`scripts/build-firefox-xpi.sh` で
  `dist/x-eagle-snapshot-saver-0.5.13-unsigned.xpi` を生成し、中の manifest が version 0.5.13、
  Firefox add-on ID、データ送信申告を含むことを確認。Node.js 22系で
  `npx --yes web-ext@8 lint --source-dir tools/x-eagle-save-extension --ignore-files README.md scripts dist`
  を実行し、errors 0 / notices 0 / warnings 1（Firefoxでは `background.service_worker` が未対応で無視される警告のみ）。
- v0.5.13 実機未確認: AMO unlisted 署名、署名済み `.xpi` の通常版 Firefox へのインストール、
  Firefox 再起動後も拡張機能が残ること。
- v0.5.14 自動試験済み: `node --check`（eagle-save.js / server.js）、
  `node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js` が通過。`scripts/build-firefox-xpi.sh` で
  `dist/x-eagle-snapshot-saver-0.5.14-unsigned.xpi` を生成し、中の manifest が version 0.5.14 であることを確認。
  Node.js 22系に PATH を切り替えたうえで
  `npx --yes web-ext@8 lint --source-dir tools/x-eagle-save-extension --ignore-files README.md scripts dist`
  を実行し、errors 0 / notices 0 / warnings 1（Firefoxでは `background.service_worker` が未対応で無視される警告のみ）。
- v0.5.14 実機未確認: 拡張機能を再読み込み後、新規保存した画像・動画の Eagle メモ欄で
  `【見る用】` と `【LLM用】` が期待どおり表示されること。
- v0.5.15 自動試験済み: `node --check`（server.js / popup.js / eagle-save.js / background.js / save.js / extractor.js /
  content-script.js）、`node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js` が通過。
  TikTok URL `https://www.tiktok.com/@nepali.baddies0/video/7632558905234689301...` は `external-video` として
  `sourceLabel: tiktok`、`authorId: nepali.baddies0`、`postId: 7632558905234689301` に解析されることを確認。
  `yt-dlp --simulate` で同URLの取得候補を確認。`scripts/build-firefox-xpi.sh` で
  `dist/x-eagle-snapshot-saver-0.5.15-unsigned.xpi` を生成し、中の manifest が version 0.5.15 であることを確認。
  Node.js 25系では `web-ext` が `Bus error` で落ちるため、Node.js 22.22.2 をPATH優先にして
  `npx --yes web-ext@8 lint --source-dir tools/x-eagle-save-extension --ignore-files README.md scripts dist --self-hosted`
  を実行し、errors 0 / notices 0 / warnings 1（Firefoxでは `background.service_worker` が未対応で無視される警告のみ）。
  LaunchAgentを再起動し、`GET /health` が `version: 0.5.15` を返すことを確認。
- v0.5.15 実機確認済み（TikTokのみ）: 2026-06-24、武田さんがTikTok URLをコピーして拡張機能から保存する流れを検証し、
  動作OKと報告。保存ファイルは Eagle item `MQRM9BZKF5YT5` の
  `tiktok-miamia_nyann-7645682227623300360-video.mp4`。Eagle APIで `source: external-video` 注釈、
  元TikTok URL、保存先フォルダ、duration 約14.4秒を確認済み。TikTok以外の外部動画サイトは未検証。
- v0.5.15 実機確認済み（Firefox常用インストール）: 2026-06-24、AMO unlisted 署名済み `.xpi`
  `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.15.xpi` を通常版Firefoxへインストール。
  Firefox再起動後も `about:addons` の拡張機能一覧に `X to Eagle Snapshot Saver` が残っていることをユーザーが確認済み。
- Firefox自動更新準備 自動試験済み: 2026-06-24、Mozilla公式仕様に合わせて
  `browser_specific_settings.gecko.update_url` 設定と `updates.json` 生成の補助スクリプトを追加。
  `node --check tools/x-eagle-save-extension/scripts/firefox-auto-update.js`、`bash -n`（追加シェルスクリプト）、
  署名済み `.xpi` からの `updates.json` 生成テスト、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js` が通過。公開用ファイル名が `asset-de25e996eecf7bd8-0.5.15.xpi`
  のような非説明的な名前になり、GitHub Pages用の `.nojekyll` が生成されることを仮URLで確認。
  後続の v0.5.16 / v0.5.17 で、公開HTTPS配置とFirefox自動更新終端確認まで実施済み。
- v0.5.16 自動試験済み: `manifest.json` を v0.5.16 に上げ、`update_url` を
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` に設定。
  `node --check`（主要JSと `firefox-auto-update.js`）、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、署名なし `.xpi` 生成、`web-ext lint --self-hosted`
  errors 0 / notices 0 / warnings 1 を確認。AMO署名で
  `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.16.xpi` を生成済み。
  `tools/x-eagle-save-extension/dist/firefox-update-site/updates.json` と
  `asset-8aa480928040bd54-0.5.16.xpi` を生成済み。GitHub Pages配置済み。
  Firefox実機インストール済み。その後の上位版への自動更新終端確認は v0.5.17 で確認済み。
- v0.5.17 自動試験済み・実機確認済み: `manifest.json` を v0.5.17 に上げ、
  `node --check`（主要JSと `firefox-auto-update.js`）、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、署名なし `.xpi` 生成、生成物内 manifest の v0.5.17 / `update_url` 確認、
  `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 1 を確認。
  公開用ファイル名は `asset-410948d49eaac256-0.5.17.xpi`。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.17.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.17、`update_hash`
  `sha256:2254825d12953d1db7cb7df2f2f304bc356ba9d7f64a3e03b64ba3c750264fa7`。
  2026-06-24 15:50時点でGitHub Pages公開側は v0.5.17 に更新済み。
  `asset-410948d49eaac256-0.5.17.xpi` は HTTP 200、SHA-256 は `updates.json` の `update_hash` と一致。
  2026-06-24、ユーザーがFirefox上部の歯車メニューから「今すぐ更新を確認」を押し、
  ポップアップ下部の表示が v0.5.16 から v0.5.17 へ上がったことを確認。
- v0.5.18 自動試験済み・実機未確認: `manifest.json` を v0.5.18 に上げ、
  `eagle-save.js` に最近フォルダ優先順位付け、重複時の既存注釈追記、重複追記の重複防止キーを追加。
  `save.js` / `popup.js` は検索時の候補順を最近使った一致フォルダ優先へ変更し、`save.html` は大きいプレビュー枠を
  画像縦横比に合わせて伸縮する設定へ変更。`README.md` とテストも更新。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、署名なし `.xpi` 生成、生成物内 manifest の v0.5.18 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 1 を確認。
  `~/.x-eagle-signing-env` を作成し、署名スクリプト・自動更新ファイル生成スクリプト・公開補助スクリプトが
  そのファイルを自動で読むようにした。秘密の値はこのWikiに記録しない。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.18.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.18、公開用ファイル名は
  `asset-38f00c8807af8b6b-0.5.18.xpi`、`update_hash`
  `sha256:7c4573bdc6f1291c8e6253a747e4c783a56065c2a917c83878d015d72430f6e2`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.18` commitを作成済み。
  最初のGitHub tokenは `Contents: Read and write` 権限不足で失敗したが、作り直したtokenで
  `Release Firefox extension 0.5.18` commitと、Pages再ビルド用commitをpush済み。
  GitHub Pagesの公開 `updates.json` は version 0.5.18、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-38f00c8807af8b6b-0.5.18.xpi`、
  `update_hash` `sha256:7c4573bdc6f1291c8e6253a747e4c783a56065c2a917c83878d015d72430f6e2`。
  公開 `.xpi` のSHA-256も同値で一致。
  Firefox実機での自動更新、フォルダ検索候補順、大プレビュー表示、Eagle重複ダイアログ後の既存メモ追記は未確認。
- v0.5.19 自動試験済み・公開済み・実機未確認: `manifest.json` を v0.5.19 に上げ、
  `eagle-save.js` に、作者ID/`@作者ID` を含む重複候補探索、`item.url` の投稿ID一致判定、保存前候補と保存後候補の比較、
  フォルダ追加または更新時刻による「Eagleが既存項目を使った」証拠確認、候補0件時の待機スキップ、失敗時の console warning を追加。
  `save.js` / `popup.js` は検索候補を「最近使った一致フォルダ」と「その他の一致フォルダ」に分け、`save.html` / `popup.html` に見出し表示を追加。
  `publish-firefox-update-git.sh` は一時生成フォルダ経由にし、GitHub Pages公開リポジトリでは古い `.xpi` を残し `.nojekyll` を再追加しない。
  `README.md` と `tools/tests/test_x_eagle_save_extractor.js` も更新。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.19 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 1 を確認。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.19.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.19、公開用ファイル名は
  `asset-d19b06545c8d131f-0.5.19.xpi`、`update_hash`
  `sha256:650fa82b7baed4c9ec18a0bc0331bdd6c7f75dbd70609ec9c6d4c405a2dad9a6`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.19` commitを作成し、push済み。
  GitHub Pages Actions の build job は success。公開 `updates.json` は version 0.5.19、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-d19b06545c8d131f-0.5.19.xpi`、
  `update_hash` `sha256:650fa82b7baed4c9ec18a0bc0331bdd6c7f75dbd70609ec9c6d4c405a2dad9a6`。
  公開 `.xpi` のSHA-256も同値で一致。
  Firefox実機での自動更新、検索候補区切り表示、Eagle重複ダイアログ後の既存メモ追記、他サイトの外部動画URL保存は未確認。
- v0.5.20 自動試験済み・公開済み・実機一部確認済み: `manifest.json` を v0.5.20 に上げ、
  `eagle-save.js` に `mergeRecentFolderIds` を追加。`save.js` / `popup.js` は、
  Eagleの `/api/folder/listRecent` に加えて、拡張機能側の `storage.local` に保存成功時のフォルダ履歴を30件まで保持し、
  両方を統合して最近フォルダ候補に使うよう変更した。検索中の「最近使った一致フォルダ」は最大5件。
  `README.md` と `tools/tests/test_x_eagle_save_extractor.js` も更新。
  ユーザー実機検証で、v0.5.19の重複メモ追記が指定Eagle項目の `metadata.json` に反映済みであることを確認。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.20 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 1 を確認。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.20.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.20、公開用ファイル名は
  `asset-0113ecd96cdf71d9-0.5.20.xpi`、`update_hash`
  `sha256:63f142662119705400faa5187b5eb99c5903c30e23fcd4b296f8717066516dfd`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.20` commitを作成し、push済み。
  GitHub Pages Actions の build job は success。公開 `updates.json` は version 0.5.20、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-0113ecd96cdf71d9-0.5.20.xpi`、
  `update_hash` `sha256:63f142662119705400faa5187b5eb99c5903c30e23fcd4b296f8717066516dfd`。
  公開 `.xpi` のSHA-256も同値で一致。
  Firefox実機での v0.5.20 自動更新、拡張機能側の最近フォルダ履歴補強、他サイトの外部動画URL保存は未確認。
- v0.5.21 自動試験済み・公開済み・実機確認済み: 投稿日時と投稿から保存までの経過時間を注釈へ残す版。2026-06-26、ユーザーが保存画像 `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/06_イラスト_画像資料/2024_11_16_eagle_フォルダ管理.library/images/MQU8L2RKT5WBE.info/x-ningen_mame-2069796952724902022-01.jpg` で投稿日時・経過が注釈に入ることを実機確認し、「経過時間があることで(いいね数が投稿何時間/何日時点の数字かの)視認性が増した」と評価した。
  `eagle-save.js` は `postedAt` から `投稿日時` / `posted_at` / `経過` を出す。`extractor.js` は
  X画面の `time` 要素から投稿日時を抽出する。`README.md` も更新済み。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.21.xpi` を生成済み。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.21` commitを作成し、push済み。
- v0.5.22 自動試験済み・公開済み・実機未確認: `manifest.json` を v0.5.22 に上げ、
  重複メモ追記の不安定要因を、Eagle副作用待ち依存から安定候補判定へ寄せて修正。
  `eagle-save.js` は `item/list` を `offset` 付きでページ送りし、作者ID検索の先頭ページに出ない既存項目も候補化する。
  保存前後で同じ完全一致候補が1件だけ安定している場合は、同一フォルダでフォルダ追加・更新時刻増加が出なくても
  `stable-exact-target` として既存メモ追記対象にする。保存後に新規コピーが検出できる場合は既存項目を更新しない。
  `tools/tests/test_x_eagle_save_extractor.js` に、同一フォルダ重複でも更新するケースと、
  作者ID検索の2ページ目に対象があるケースを追加。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.22 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 1 を確認。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.22.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.22、公開用ファイル名は
  `asset-6f7d3103257ae235-0.5.22.xpi`、`update_hash`
  `sha256:7fe3396999052fccfc0c14421986890960d4422a3bbe86a56e713e0cf609609d`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.22` commitを作成し、push済み。
  GitHub Pages Actions の build job は success。公開 `updates.json` は version 0.5.22、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-6f7d3103257ae235-0.5.22.xpi`、
  `update_hash` `sha256:7fe3396999052fccfc0c14421986890960d4422a3bbe86a56e713e0cf609609d`。
  公開 `.xpi` のSHA-256も同値で一致。
  その後のユーザー実機検証で、投稿URL側の作者IDと既存ファイル名側の作者IDが違う項目では不発になる条件が見つかり、
  v0.5.23で追加修正した。Firefox実機での v0.5.22 自動更新、同一フォルダ重複、作者保存数多数ケース、
  他サイトの外部動画URL保存は未確認。
- v0.5.23 自動試験済み・公開済み・対象条件は実機確認済み: `manifest.json` を v0.5.23 に上げ、
  v0.5.22で残っていた「投稿URL側の作者IDと既存ファイル名側の作者IDが違う既存項目を検索で掴めない」問題を修正。
  `eagle-save.js` は保存後に選択フォルダ内の最近更新項目を `folders=<folderId>` 付き `item/list` で確認し、
  同じ投稿IDかつ今回の操作時刻以後に更新された項目が1件だけなら `recent-folder-status` として既存メモ追記対象にする。
  新規コピーらしい項目や複数候補の場合は更新しない。候補がある重複待機は60秒まで延長した。
  `tools/tests/test_x_eagle_save_extractor.js` に、`@ayamachi3284` 名の既存ファイルが
  `https://x.com/jeonghee1414/status/2062601913770836159` を持つ今回型の回帰テストを追加。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.23 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 5 を確認。
  warning 5は Firefox未対応の `background.service_worker` と、配布用シェルスクリプト同梱への警告。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.23.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.23、公開用ファイル名は
  `asset-605a1fc592ea7e13-0.5.23.xpi`、`update_hash`
  `sha256:52a893796c4b8e40c8edd2308fc86675e20758d5044ce7abd148e1b4dc14bef6`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.23` commitを作成し、push済み。
  公開 `updates.json` は version 0.5.23、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-605a1fc592ea7e13-0.5.23.xpi`、
  `update_hash` `sha256:52a893796c4b8e40c8edd2308fc86675e20758d5044ce7abd148e1b4dc14bef6`。
  公開 `.xpi` のSHA-256も同値で一致。
  2026-06-26のユーザー実機検証で、今回の `MQ0RI9CC9RQF9` と同型の重複メモ追記が機能したと報告。
  添付メモにあった `MQISKLV3VBLP8`、`MPGRO6D8PEH95`、`MQI533RQXJ08R`、`MQ0RI9CC9RQF9` の
  `metadata.json` を確認し、いずれも `【LLM用】` と `capture_key` を含む。Firefox実機での v0.5.23
  自動更新状態の細部、他サイトの外部動画URL保存は未確認。
- v0.5.24 自動試験済み・公開済み・実機未確認: `manifest.json` を v0.5.24 に上げ、
  Eagleメモ欄の `【見る用】` で反応数を箇条書き表示に変更。`反応:` の下に `- いいね:`、`- リポスト:`、
  `- 表示:`、`内訳:` の下に `- 返信:`、`- 引用:` を出す。`【LLM用】` の `metrics.*` 行は据え置き。
  `tools/tests/test_x_eagle_save_extractor.js` は、新しい注釈先頭形式と manifest version 0.5.24 を検証する。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.24 / `update_url` 確認、
  Node.js 22系での `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 5 を確認。
  warning 5は Firefox未対応の `background.service_worker` と、配布用シェルスクリプト同梱への警告。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.24.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.24、公開用ファイル名は
  `asset-12a9b3ee54eee878-0.5.24.xpi`、`update_hash`
  `sha256:a4de5f0a6bdd000866c551ad3842d4f2186fde0d280f6bb4b386a0f219818efc`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.24` commitを作成し、push済み。
  公開 `updates.json` は version 0.5.24、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-12a9b3ee54eee878-0.5.24.xpi`、
  `update_hash` `sha256:a4de5f0a6bdd000866c551ad3842d4f2186fde0d280f6bb4b386a0f219818efc`。
  公開 `.xpi` のSHA-256も同値で一致。
  Firefox実機での v0.5.24 自動更新、メモ欄反応数の箇条書き表示、他サイトの外部動画URL保存は未確認。
- v0.5.29 自動試験済み・公開済み・実機未確認: `manifest.json` を v0.5.29 に上げ、
  同じ画像を再保存したとき、同じ `capture_key` が既存メモにあるだけでは追記を止めないよう修正。
  完全に同じ注釈本文が既にある場合だけ重複防止し、`captured_at` や `metrics.*` が違う新しい取得情報は
  既存メモの上へ `---` 区切りで積む。Eagle重複ダイアログ後に既存項目の更新時刻増加または選択フォルダ内の
  最近更新で今回の操作対象だと確認できる場合に更新する。
  `tools/tests/test_x_eagle_save_extractor.js` に、同じ `capture_key` の古い注釈がある状態で、新しい
  `captured_at` / `metrics.likes` を上に積み、古い `captured_at` / `metrics.likes` を下に残す回帰テストを追加。
  `node --check`（`eagle-save.js` / `save.js` / `popup.js`）、`bash -n`（公開系シェルスクリプト）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js`、
  署名なし `.xpi` 生成、生成物内 manifest の v0.5.29 / `update_url` 確認、
  `npm exec --yes --package web-ext@8 -- web-ext lint --self-hosted` errors 0 / notices 0 / warnings 5 を確認。
  warning 5は Firefox未対応の `background.service_worker` と、配布用シェルスクリプト同梱への警告。
  AMO署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.29.xpi` を生成済み。
  ローカル配布フォルダの `updates.json` は version 0.5.29、公開用ファイル名は
  `asset-d0be6ac7699f0e0e-0.5.29.xpi`、`update_hash`
  `sha256:80e2a78be6f9d21d89a779c881a03fe7a42ac25ee3ee27f86369ee71db9b5e13`。
  ローカル公開リポジトリ `tools/x-eagle-save-extension/dist/firefox-update-site-repo` に
  `Release Firefox extension 0.5.29` commitを作成し、push済み。
  公開 `updates.json` は version 0.5.29、`update_link`
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-d0be6ac7699f0e0e-0.5.29.xpi`、
	  `update_hash` `sha256:80e2a78be6f9d21d89a779c881a03fe7a42ac25ee3ee27f86369ee71db9b5e13`。
	  公開 `.xpi` のSHA-256も同値で一致。
	  Firefox実機での v0.5.29 自動更新、`MQUAHVAN1A2UQ` 同型の再保存履歴追記、
	  他サイトの外部動画URL保存は未確認。
- v0.5.31 自動試験済み・公開済み・実機未確認: 補助処理を 0.5.16 に上げ、Eagle ライブラリの
  `images/*.info/metadata.json` から重複索引を作る。拡張機能は保存前に `POST /duplicate-index/lookup` を呼び、
  `name: 画像` のように Eagle keyword 検索から漏れる既存 item も、`capture_key` や `image_url` が一致すれば
  `addFromURL` 前に見つける。見つかった item は `/api/item/info` で取り直してから `/api/item/update` する。
  同ランク複数候補は、フォルダ条件で1件に絞れない限り `ambiguous` とし、既存 item を自動更新しない。
  `node --check tools/x-eagle-video-helper/server.js`、`node --check tools/x-eagle-save-extension/eagle-save.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、`node tools/tests/test_x_eagle_save_extractor.js` は通過。
  実ライブラリ 33696 件の索引で `MO0THSO94QFX1` を `capture-key` 検出できることを helper API 経由で確認。
  AMO署名済み `.xpi` `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.31.xpi`、
  署名なし `.xpi` `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.31-unsigned.xpi`、
  ローカル配布 `asset-15ff086da3fac2ca-0.5.31.xpi` を生成済み。公開 `updates.json` は version 0.5.31、
  `update_link` `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/asset-15ff086da3fac2ca-0.5.31.xpi`、
  `update_hash` `sha256:f8b77486bd6b2539a52de2962e33592a96fb5e03db020b641f9c2478c254a617`。
  公開 `.xpi` のSHA-256も同値で一致。公開リポジトリには `Release Firefox extension 0.5.31` commit
  `5799fbf` を push済み。Firefox実機での v0.5.31 自動更新と、`MO0THSO94QFX1` 型再保存確認は未完了。
- v0.5.40 実装済み・自動試験済み・署名済み・実機未確認・未公開: `manifest.json` を v0.5.40 に上げ、
  Firefoxのプロフィール通常「ポスト」欄で、本人が直接添付した静止画像付き投稿だけを残す
  `プロフィール資料探し: 本人の画像だけ` モードを追加した。初期値はオフで、状態は
  `browser.storage.local` の `xEagleProfileImageOnlyMode` に保存する。
  `profile-image-filter.js` を新設し、プロフィールURL判定、本人投稿判定、静止画像判定、リポスト・引用・返信・動画・
  リンクカード等の除外判定を分離した。`content-script.js` は storage 変更と popup message を受け、
  `MutationObserver` で新規追加 `article` だけを `requestAnimationFrame` 単位で分類する。
  オフ時または対象外URLでは、自分が付けた `x-eagle-profile-image-filter-hidden` と
  `data-x-eagle-profile-filter-reason` を解除して投稿を復元する。
  `popup.html` / `popup.js` にはトグルと即時反映処理を追加した。`background.js`、`popup.js` の手動注入経路、
  `manifest.json` の content script、`build-firefox-xpi.sh` に `profile-image-filter.js` を含めた。
  `README.md` と `tools/tests/test_x_eagle_save_extractor.js` も更新済み。
  自動試験: `node --check`（`profile-image-filter.js` / `content-script.js` / `popup.js` / `background.js` /
  `extractor.js` / `save.js` / `eagle-save.js`）、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、公開系シェルスクリプトの `bash -n` が通過。
  `bash tools/x-eagle-save-extension/scripts/build-firefox-xpi.sh` で
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.40-unsigned.xpi` を生成し、アーカイブ内
  `manifest.json` が v0.5.40 かつ `profile-image-filter.js` を含むことを確認した。
  Node.js 22系で `npm exec --yes --package web-ext@8 -- web-ext lint --source-dir tools/x-eagle-save-extension --self-hosted`
  を実行し、errors 0 / notices 0 / warnings 5（既知の Firefox未対応 `background.service_worker` と
  scripts 同梱警告）を確認した。
  AMO unlisted署名で `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.40.xpi` を生成済み。
  署名済みXPI内の `manifest.json` も v0.5.40 で、`profile-image-filter.js` を含むことを確認した。
  自動更新先 `tools/x-eagle-save-extension/dist/firefox-update-site/updates.json` は v0.5.38 のままで、
  v0.5.40 は公開更新フィードへ未反映。
  未確認: Firefox実機でのトグル表示、プロフィール通常ポスト欄での本人静止画像だけの表示、オフ時即時復元、
  ホーム・返信タブ・メディアタブ・投稿単体ページへの非干渉、既存Eagle画像保存・動画保存・フォルダ検索・
  ドラッグ&ドロップ保存への回帰なし、ユーザーの体感改善。
- v0.5.41 緊急修正・自動試験済み・署名済み・実機再確認待ち・未公開: 2026-07-16、武田さんが
  v0.5.40 を導入後、プロフィール通常ポスト欄で投稿がほぼ全消去される状態をスクリーンショット付きで報告。
  原因は、Xの描画途中 `article` を `not-profile-author` / `no-static-image` と早判定して隠し、その後の
  DOM更新を再判定していなかったことと判断した。
  `profile-image-filter.js` は、`/status/` リンクがまだ無い未完成記事を
  `pending-status-link` として隠さないよう変更。`content-script.js` は
  `data-x-eagle-profile-filter-processed` による一回限り処理を廃止し、記事ごとのシグネチャを `WeakMap` で保持して、
  リンク・画像・本文・`data-testid` が変化した記事だけ再分類する方式へ変更した。`MutationObserver` は
  `childList` に加え `attributes` / `characterData` も監視し、追加ノードだけでなく既存記事内の更新もキューへ入れる。
  `manifest.json` と README の表示版を v0.5.41 に更新し、`tools/tests/test_x_eagle_save_extractor.js` に
  描画途中記事は隠さない回帰テストを追加した。
  自動試験: `node --check`（`profile-image-filter.js` / `content-script.js` / `popup.js`）、
  `node tools/tests/test_x_eagle_save_extractor.js`、`node tools/tests/test_x_eagle_video_helper.js` が通過。
  `bash tools/x-eagle-save-extension/scripts/build-firefox-xpi.sh` で
  `tools/x-eagle-save-extension/dist/x-eagle-snapshot-saver-0.5.41-unsigned.xpi` を生成。
  Node.js 22系で `web-ext lint --self-hosted` errors 0 / notices 0 / warnings 5（既知警告）を確認。
  AMO unlisted署名済みXPI `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.41.xpi` を作成し、
  アーカイブ内 `manifest.json` が v0.5.41 かつ `profile-image-filter.js` を含むことを確認。
  自動更新先 `tools/x-eagle-save-extension/dist/firefox-update-site/updates.json` は v0.5.38 のままで、v0.5.41 は未公開。
  未確認: 武田さん実機で、投稿の全消去が解消し、本人静止画像投稿だけが残るか。
- v0.5.42 操作性・保存停止修正・自動試験済み・署名済み・公開済み・実機再確認待ち: 2026-07-16、武田さんから
  「読み込みが遅い」「重複していないはずなのに重複判定の赤文字で保存が止まる」と報告。
  精査時の実環境では、補助処理は起動中だったが約3.5万件の重複索引が `stale` で、既定TTLが30秒、
  画像本体照合の待機上限が10秒だった。さらにv0.5.39の保存経路は、候補複数・索引準備中・補助処理停止・
  timeout・壊れた応答をすべて保存失敗にしており、2026-07-12に確定した「重複防止より保存導線の安定を優先」
  という現行方針と食い違っていた。
  `eagle-save.js` は重複索引の短時間照合を250ミリ秒上限の補助扱いへ変更。一意な既存項目だけ従来どおり
  統合し、それ以外は `addFromURL` へ進む。画像本体のダウンロードとsha256照合は通常保存から外した。
  `server.js` は helper v0.5.24へ更新し、約3.5万件の同期索引再構築頻度を30秒から1時間へ緩和した。
  プロフィール絞り込みは、X全体の属性変更監視を `href` / `src` / `srcset` / `data-testid` に限定し、
  100ミリ秒単位でまとめて再判定する。`tweetPhoto` がある画像読込待ち投稿は隠さず、画像の遅延読込を妨げない。
  `manifest.json` は v0.5.42。自動試験では、既存1件統合を維持しつつ、候補複数・helper停止・索引準備中・
  壊れた応答・content照合省略の各ケースで通常保存へ進むことを確認。
  `node --check`、`node tools/tests/test_x_eagle_save_extractor.js`、
  `node tools/tests/test_x_eagle_video_helper.js`、`web-ext lint --self-hosted` が通過
  （lintはerrors 0 / notices 0 / 既知warnings 5）。AMO unlisted署名済みXPI
  `tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.42.xpi` を生成し、アーカイブ内manifest v0.5.42、
  対象content scripts、Mozilla署名ファイルを確認。SHA-256は
  `7408b26f970949019c693d42401ec421a879e690c8a122323dc39fcde30118df`。
  起動中helperはv0.5.24、索引34,954件、`ready`、TTL 3,600,000ミリ秒。準備後の照合は実測0.018秒。
  2026-07-26、Codexが公開更新フィードをv0.5.42へ反映した。公開URL
  `https://20260624yosuke.github.io/browser-update-feed-7m4q2d/updates.json` はv0.5.42を返し、公開XPI
  `asset-4c37bd3c303fae06-0.5.42.xpi` のSHA-256は
  `7408b26f970949019c693d42401ec421a879e690c8a122323dc39fcde30118df` で更新フィードと一致。
  起動中helperはv0.5.24、索引35,321件、`ready`、TTL 3,600,000ミリ秒、AI検索readyを確認。
  未確認: Firefox実機で自動更新後に拡張機能表示がv0.5.42になったこと、プロフィール読込体感、
  通常画像保存の待ち時間、赤文字停止が出ないこと。
- v0.5.15 実機観察: Firefox `about:debugging` 上で一時的な拡張機能が2件表示された。スクリーンショットでは
  旧版が `...@temporary-addon`（バックグラウンド停止中）、現行版が
  `x-eagle-snapshot-saver@takedayousuke.local`（バックグラウンド実行中）。v0.5.13で固定 add-on IDを追加したため、
  Firefoxが旧一時ID版と固定ID版を別物として扱った可能性が高い。対処は旧 `@temporary-addon` 側を削除し、
  今後は固定ID版の「再読み込み」を使う。2026-06-24、武田さんが上側の旧 `@temporary-addon` を削除済み。
- 2026-07-30、武田さんがYouTube動画ページ（`https://www.youtube.com/watch?v=gygAJWAolLc`、
  「Girls Frontline 2: Exilium - Mityl Outfit Animations & Dormitory」）で拡張機能から動画保存を実行し、
  「動画を取得してEagleへ保存中...」の表示のまま変化しない事象を報告した（helper起動中v0.5.24、
  拡張機能v0.5.42時点）。
  - 調査序盤、動画IDの末尾を「gygAJWAo**I**Lc」（大文字I）と誤読し、存在しない動画IDでyt-dlp・helperを
    検証して「Video unavailable」という誤った結論に達した（フォントで大文字Iと小文字lが酷似したための
    誤読。正しくは「gygAJWAo**l**Lc」）。この結論は誤りであり、以下が訂正後の確定した原因。
  - 正しいIDでEagleライブラリを確認したところ、この動画は**実際には2件とも正常に保存済み**だった
    （`youtube-youtube.com-gygAJWAolLc-video.mp4`、454,346,960 bytes、2560×1440、再生時間約17分、
    保存先 `07_作品_ドルフロ2_01`）。item `MS7FDLJJ74SW9`（Eagle内部作成 20:23:22 JST、現在ゴミ箱）と
    item `MS7FE5V6V8L49`（Eagle内部作成 20:23:50 JST、現在有効）で、どちらもクリックから約1分で
    保存が完了していた。
  - 武田さんへの確認で、(a) この動画で「動画を保存」を2回以上実行した（ポップアップを閉じて開き直す等）、
    (b) 「保存中」表示を見ている間に他のタブへ切り替えるなどの操作をして、あとで戻って確認した、
    ことが判明。
  - コード確認（`manifest.json` の `action.default_popup`）により、動画保存ポップアップ（`popup.html`）は
    標準のツールバー・ドロップダウンポップアップであり、右クリック画像保存小窓（`save.html`、
    `background.js` が `windows.create()` で独立ウィンドウ化）と違って独立ウィンドウ化されていないと
    確認した。標準のツールバーポップアップは他のタブへ切り替える等でフォーカスを失うと閉じる仕様のため、
    「保存中」表示のまま他のタブへ切り替えた時点でポップアップのJS実行は打ち切られたと考えられる。
    一方サーバー側（`tools/x-eagle-video-helper/server.js` の `saveVideo()`）はクライアントの切断と無関係に
    yt-dlp取得とEagle登録を最後まで実行するため、**保存自体はポップアップが閉じた後も裏で成功していた**が、
    それを表示する相手（ポップアップ）が既に無く、ユーザーには何も返らなかった。ユーザーが「失敗した」と
    誤認して拡張機能を開き直し再度保存した結果、独立した2回のリクエストがそれぞれ約1分後に成功し、
    同じ動画がEagleに2件登録された。
  - `popup.js` を全体確認し、「動画を保存」ボタンのクリックハンドラは1箇所のみで登録され、自動リトライや
    二重登録は無いことを確認した。よって2件保存はコードの二重送信バグではなく、上記の運用（2回操作）が
    原因と特定できた。
  - 副次的に確認した既知の設計ギャップ（今回の事象の原因ではない）: `buildYtDlpArgs()` はXの動画
    （`sourceKind === "x"`）のときだけ `--cookies-from-browser` を付与し、YouTube含む非X動画には
    一切クッキーを渡さない。ログイン必須・年齢確認必須のYouTube動画は原理的にこの経路では取得できない。
  - 未対応（当時）: 動画保存ポップアップの独立ウィンドウ化（`save.html`と同じ`windows.create()`方式）、
    保存中の多重実行防止、進捗表示の追加。→ v0.5.43で対応（下記）。
- v0.5.43で実装・自動試験済み・実機未確認: `/plan-gate`で計画承認のうえ、上記のボトルネック
  （保存の進捗・結果を表示する仕組みが、動画取得という長時間処理と同じ生存期間＝ポップアップが
  開いている間だけに縛られていたこと）そのものを直した。
  - 実際の`/save-video`リクエストを`popup.js`から`background.js`へ移した。ポップアップが閉じても
    バックグラウンド側が処理を続け、結果が失われない。
  - 保存の進捗・結果を`storage.session`の`xEagleVideoJobs`（動画URLをキーにしたジョブ状態:
    `saving`/`done`/`error`）へ記録する。ポップアップは開くたびにこの状態を読み、進行中なら
    経過時間付きで、完了済みなら「◯分前に保存済み」、失敗していればエラー内容を表示する
    （`videoJobStatus`欄、`popup.js`の`refreshCurrentVideoJob()`/`renderVideoJobStatus()`）。
  - 同じ動画URLの保存が進行中に「動画を保存」を再度押しても、`background.js`の
    `handleStartVideoSave()`が既存ジョブを見て弾き、二重にfetchしない
    （`alreadyRunning: true`を返すのみ）。今回のような多重保存を防ぐ。保存済みの動画への再保存は
    `window.confirm()`で確認を挟む。
  - 保存完了・失敗時に`notifications`権限（新規追加）でブラウザ通知を出す
    （`background.js`の`notifyVideoSaveResult()`）。ポップアップを閉じていても気づける。
  - `manifest.json`を`0.5.43`に上げ、`permissions`へ`notifications`を追加した。
  - 自動試験: `node --check`（`background.js`/`popup.js`）、
    `node tools/tests/test_x_eagle_save_extractor.js`（新規`assertBackgroundVideoSaveJobFlow()`で、
    background.jsを実際のイベントリスナー付きでロードし、(1)保存中の同一URLへの二重開始が
    fetchを増やさないこと、(2)完了時にstorageが`done`へ更新され通知が呼ばれることを確認）、
    `node tools/tests/test_x_eagle_video_helper.js`、`web-ext lint --self-hosted`
    （errors 0 / notices 0 / warnings 1、既知のFirefox未対応`background.service_worker`警告のみ）。
    署名なし`.xpi`（`dist/x-eagle-snapshot-saver-0.5.43-unsigned.xpi`）を生成し、
    アーカイブ内`manifest.json`が`0.5.43`かつ`notifications`権限を含むことを確認した。
  - 未確認: Firefox実機で、(a)ポップアップを開いたまま保存完了を見る、(b)保存中に他タブへ
    切り替えてあとで戻って状態表示を確認する（今回問題になった操作そのもの）、(c)保存中に
    もう一度「保存」を押しても多重保存されないこと、(d)完了・失敗時のブラウザ通知表示、
    (e)初回の`notifications`権限許可ダイアログ。
  - 署名・公開済み: `bash tools/x-eagle-save-extension/scripts/publish-firefox-update-git.sh`で
    AMO署名済み`.xpi`（`tools/x-eagle-save-extension/dist/5f5a5887db534a7e979f-0.5.43.xpi`、
    SHA-256 `0d1695159e5c20899c217cbde5151ea5fce8a015ebf17a4a9549521e13295a53`）を作成し、
    GitHub Pages（commit `678b17c`）へ公開済み。公開`updates.json`はversion `0.5.43`、公開
    `.xpi`（`asset-4ad1c27b6152da8b-0.5.43.xpi`）はHTTP 200、SHA-256が`update_hash`と一致することを
    確認済み。ただし配布物が公開されたことの確認であり、Firefox実機で実際に`0.5.43`が動いた・
    上記(a)〜(e)が期待どおりかの確認ではない（区別は2026-07-09の教訓どおり）。
  - 2026-08-03、武田さんがFirefoxのアドオンを更新し`0.5.43`を導入済み。ただし更新時点で保存したい
    YouTube動画が手元になく、上記(a)〜(e)の実機確認はまだ行っていない。問題が起きた場合は改めて
    報告予定。**現時点のステータスは「配布・導入済み・実機動作未確認」のまま**（実機確認済みへの
    更新はまだしない）。
- v0.5.45 プロフィールスクロール干渉の根本修正・自動試験済み・署名済み・公開済み・実機確認済み:
  2026-08-03、FirefoxでXプロフィールを下へスクロール中に上方向へ戻される症状を調査した。
  起動中Firefoxは`345r7qby.default-release`を使用し、`extensions.json`とインストール済みXPIは
  v0.5.42を有効としていた。拡張保存領域の`xEagleProfileImageOnlyMode`は`true`で、プロフィール
  絞り込みが実際にオンだった。v0.5.42は投稿のDOM更新を監視し、除外投稿を`display:none`で高さごと
  消すため、Xの仮想化された投稿一覧のスクロール位置再計算と衝突する。
  根本修正として、`profile-image-filter.js`の配布、`content-script.js`の投稿監視、`content.css`の
  投稿非表示、ポップアップの切替欄を外した。画像・動画保存、右クリック、ドラッグ&ドロップは残した。
  自動試験は構文検査、保存抽出試験、動画補助試験、Firefox拡張検査（errors 0）を通過。
  Mozilla署名済みv0.5.45を自動更新先へ公開し、公開XPIのSHA-256
  `ba4d5fe383ba0be1dec268feef7fd9d777a0ebb391ea6d7dabdc34286627a16d`が更新情報と一致し、
  `profile-image-filter.js`を含まないことを確認した。
  同日、通常プロフィールの`extensions.json`を再確認し、Firefox実機でもv0.5.45が有効と確認。
  武田さんが同条件で実機検証し、「問題ない。より動作も軽い感じがして印象がいい」と報告したため、
  この件は実機確認済み・運用開始可能へ更新する。詳細は[[firefox-x-profile-scroll-jump-root-cause-2026-08-03]]。

> [!warning] 矛盾あり
> 直前の会話記録はv0.5.43導入済みとしているが、2026-08-03のディスク上の現物はv0.5.42だった。
> 現在版の判断ではディスク上の`extensions.json`とインストール済みXPIを優先する。

### v0.5.45で使わなかったもの・落とした情報

- **何を捨てたか**: 「プロフィール資料探し: 本人の画像だけ」1機能。
- **手元でどう変わるか**: ポップアップの切替欄が消え、通常プロフィールは文章・リポスト・返信・動画も
  X本来の並びで表示される。投稿の高さを途中で消す処理は走らない。2026-08-03の実機確認では、
  スクロールが勝手に上へ戻る症状は出ず、動作も軽く感じるとの報告があった。
- **戻せるか**: v0.5.43 XPIと残した`profile-image-filter.js`から復元可能。ただし同じスクロール症状も
  戻るため、再開時はXの投稿一覧を直接消さない別画面方式として再設計する。

## 方針変更（2026-07-12）

- 武田さんの実運用確認では、重複処理は期待どおりには機能していないと判断した。
- このプロジェクトの優先順位を見直し、**「重複を増やさないこと」より「保存時に止まらず、シームレスに保存できること」** を先に置く。
- そのため、今後の主目的は「保存導線の安定運用」に戻す。重複画像が一定数増えることは、当面は許容する。
- v0.5.31 以降で試した保存前重複検知、既存項目への非破壊追記、解像度違い候補レビューは、
  実験・検証の経緯としては残すが、**現時点では運用の前提にしない**。
- 今後このページで「運用開始可能」と呼ぶ条件は、重複統合の賢さではなく、武田さんの通常保存フローで
  迷わず使え、保存失敗や待ち時間によるストレスが少ないことを優先する。

## 関連リンク

- [[eagle-save-script-use-cases-2026-06-17]]
- [[current-projects-todo-clarifications-2026-06-15]]
