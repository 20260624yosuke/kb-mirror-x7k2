---
type: analysis
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-07
sources:
  - https://github.com/andmev/tahoe-wallpaper-switcher
brainstorm_status: ready
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/tahoe-wallpaper-switcher/_index.md
  - /Users/takedayousuke/Library/Scripts/wallpaper-switch.js
  - /Users/takedayousuke/Library/Scripts/wallpaper-switch-config.json
  - /Users/takedayousuke/Library/Scripts/wallpaper-switch-state.json
  - /Users/takedayousuke/Library/LaunchAgents/com.user.wallpaper-switch.plist
background_paths:
  - https://github.com/andmev/tahoe-wallpaper-switcher
---

# macOS Tahoe の壁紙を太陽位置で切り替える導入

## 武田さんの考え

- 2026-09-07: 「このツールを使えば、macのタホの壁紙をタイナミックに疑似的にできるって聞いたんだけど合ってる？これ導入したいから手伝って」。Tahoe の4種類の壁紙を時刻ではなく太陽位置に合わせて切り替え、導入を手伝ってほしい意図。誤記の「タイナミック」は「ダイナミック」の意味として扱う。
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=1 input=a03353cd3780bf05fa1ce931f029f0e5c2f0e1b681f3e986f7adcbd5e006e3f turn=79b8ff8286cc502ec7798ef627176b387bedc4a3bbf1ec2fac8c8867d5514fff -->
- 2026-09-07: 既存の近いbrainstorm親は見つからなかったため、この専用親で続ける選択と確認を受領した。

## 決まったこと

- GitHub README上、このツールは macOS Tahoe 26+向けで、`Tahoe Morning / Day / Evening / Night` の4壁紙を、現在地の緯度経度から計算した日の出・日没の時間帯に応じて切り替える。ダーク／ライトモードも連動する。
- Appleの公開された単一のダイナミック壁紙APIを使うものではなく、JXA（JavaScript for Automation）とPythonでAppleの壁紙管理ファイルを書き換え、WallpaperAgentを再起動する「疑似的な」方式。
- このMacは macOS 26.6.2、arm64 なので、OS要件には一致することを読み取りで確認した。
- インストーラはユーザー領域の `~/Library/Scripts` と `~/Library/LaunchAgents` にスクリプト・位置設定・LaunchAgentを置き、15分ごととログイン時に実行する。現時点では未導入。

## まだ決まってないこと

- 4種類のTahoe壁紙が実際にダウンロード済みか。
- 位置設定をどの市区町村・緯度経度にするか（初期値はApple Parkなので、そのままでは日本の太陽時刻にならない）。
- リモートのcurlインストーラを使うか、スクリプトを確認してから手動導入するか。
- 壁紙設定ファイルの変更とWallpaperAgent再起動をこのMacで実行してよいか。これは前面の見え方と常駐設定を変えるため、実行承認が必要。
- 完成条件は、導入済み・日本の位置に設定済み・手動一回実行で現在時間帯の壁紙とダークモードが変わること、LaunchAgentの状態とログで確認できること、とする候補。
- 読み取り結果では、壁紙マニフェストは存在するが、4枚のうち `Tahoe Night` だけがローカル動画として取得済みで、Morning / Day / Evening は未取得。同名のスクリプト・設定・LaunchAgentは未導入。したがって今すぐインストーラを実行しても完了せず、先に残り3枚のダウンロードが必要。
- 位置は未指定。東京（緯度35.6762、経度139.6503）を仮設定にするか、実際に合わせたい市区町村を決める必要がある。

## 捨てた案と理由

- 「macOS標準の動的壁紙を有効化するだけ」という理解は採用しない。このリポジトリは標準APIではなく、4つの動画壁紙を時間帯で切り替える方式だから。
- いきなりcurlの一行インストールを実行する案は、まず現在の壁紙ダウンロード状況・位置・インストール内容を確認すべきため保留。

## 直した記録

- 2026-09-07: 新規親メモを作成。成果物本体やmacOS設定は変更していない。
- 2026-09-07: macOS 26.6.2 arm64、Tahoeマニフェストあり、Nightのみ取得済み、既存の同名導入なし、LaunchAgent未登録であることを読み取り確認。設定変更はしていない。
- 2026-09-07: 太陽位置は東京（35.6762, 139.6503）で進める選択と確認を受領。残り3枚のダウンロード完了が次の前提。
- 2026-09-07: 壁紙準備は、短い手順を案内して武田さんが手動で行う方式を選択・確認。次はシステム設定で Morning / Day / Evening を取得し、完了後にこちらで再読取する。
- 2026-09-08: 「ダウンロードしました。タスクを続けてください」と受領したため再確認したが、実ファイルで取得済みなのは Morning と Night の2枚だけ。Day と Evening は未取得のまま。同名の導入物は引き続き存在せず、インストールは開始していない。
- 2026-09-08: `BS_MEMO_REQUIRED / BS_CARD_REQUIRED` フック指示を受領。親メモ必須・カード必須の状態を維持し、Day / Evening未取得の技術的停止点から再開する。実行承認は未取得。
- 2026-09-08: 再確認で Evening は動画取得済みに変化したが、Day はサムネイルだけで動画本体が未取得。4種類を壁紙設定で選択した事実は、インストーラが読む動画ファイルの存在をまだ証明しない。必要条件は Day の動画本体取得。
- 2026-09-08: リポジトリのインストーラと同じ候補選択を再実行。Dayの候補はID `4C108785-A7BA-422E-9C79-B0129F1D5550` の1件だけで、対応する `.mov/.mp4/.m4v` は0件。Morning / Evening / Nightは各約4.5億バイトの`.mov`が存在する。別ID・別拡張子の見落としではない。
- 2026-09-08: Dayの取得完了まで待って再確認する選択と確認を受領。Dayを壁紙設定で再選択し、ダウンロード完了後に再読取する。実行承認はまだ無い。
- 2026-09-08: 武田さんから「ダウンロードした。ダウンロードした前提で進めていい。dlは本当にしてます」と明示された。実ファイルの既存観測とは食い違うが、本人の現物確認を前提に、固定版インストーラの実行許可カードへ進む。インストーラの事前チェックがDay不足で停止した場合は、そこで技術的停止とする。
- 2026-09-08: 「この会話で実装する」および確認「はい、この選択でよい」を受領。固定コミット版のインストーラ、東京設定、LaunchAgent、初回実行、ログ確認の範囲で実行許可済み。範囲外の修正やDay動画の代替取得はしない。
- 2026-09-08: 固定コミット `31c7eea079bbb46ee098b5dab2b804c77ea88684` のインストーラを実行したが、exit 1。`TAHOE_ID_MORNING/DAY/EVENING/NIGHT` を作る一方、チェック側は `TAHOE_ID_TAHOE_MORNING` 等を参照するバグで、4枚を誤って未取得扱いした。スクリプト本体とLaunchAgentは未作成、東京設定JSONのみ作成済み。修正版作成・再実行は元の承認範囲外なので技術的停止。
- 2026-09-08: 一時修正版で変数参照を直して再実行した結果、Morning / Evening / Nightは合格したが、Dayだけは `NOT downloaded` で停止。修正後もDay動画本体が無いという判定は変わらず、スクリプト本体とLaunchAgentは未作成。
- 2026-09-08: 武田さん提供のスクリーンショット `/Users/takedayousuke/Library/Mobile Documents/com~apple~CloudDocs/ダウンロード/02_スクショ保存/ 2026-09-08 0.13.01.jpg` を確認。macOS壁紙画面には昼・朝・夕・夜の4種類が表示され、4種類を選択したという本人報告を支持する。ただしDay動画本体はツールの期待パスに無く、画面選択とローカルキャッシュの不一致が確定。precheckを迂回する手動導入は別案で、Day切替失敗のリスクを明示して別承認が必要。
- 2026-09-08: Dayキャッシュ確認を迂回した手動導入について「この会話で実装する」と確認「はい、この選択でよい」を受領。固定版スクリプト本体、東京設定、LaunchAgent登録、初回実行、実機結果の確認までを実施する。Day切替失敗は未達として報告する。
- 2026-09-08: 手動導入を実施。固定版 `wallpaper-switch.js` を `~/Library/Scripts/` に配置、東京設定JSONを使用、`com.user.wallpaper-switch.plist` を `~/Library/LaunchAgents/` に作成・loadした。初回実行は終了コード0で `00:15 | Tokyo | night | wallpaper→night`、state JSONもnightのIDを記録。stderrは空。LaunchAgentは登録され、runs=1 / last exit code=0を確認した。画面上で壁紙が実際に表示されたかは未確認、Day切替も未確認。
- 2026-09-08: 使用感のフィードバック「システム環境のテーマカラーがホワイトにされる。ブラックの方がいいからそこだけ封鎖して」を受領。壁紙の時間帯切替は維持し、外観テーマの自動切替だけを止めて常時ブラック（ダーク）に固定する。
- 2026-09-08: `wallpaper-switch.js` の `wantDark` を `true` に変更し、手動実行で終了コード0。外観テーマの実値をJXAで読み、`true`（ダーク）を確認。壁紙の状態はday、東京設定とLaunchAgentは維持。LaunchAgentはruns=37、last exit code=0、stderrは空。画面の見た目そのものは未撮影だが、テーマ設定値の固定は確認済み。
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=3 input=9253fddd9ab99176b22bd910b2a87353fda464f25b2550696e8a679ed9d205ab turn=1bb5302be440373c71d5a0c580921664f3c4c5412a556dd1165e1536db018600 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=2 input=9bef9131bd9774edbbc03fc25dc204aba2df5654a068bc54f4c3fe21afdac0cc turn=3497173c3b1ddf5bf46dbb00a15e37e7ea095296b314d39d8077aab912c2fd41 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=2 input=9bef9131bd9774edbbc03fc25dc204aba2df5654a068bc54f4c3fe21afdac0cc turn=3497173c3b1ddf5bf46dbb00a15e37e7ea095296b314d39d8077aab912c2fd41 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=2 input=9bef9131bd9774edbbc03fc25dc204aba2df5654a068bc54f4c3fe21afdac0cc turn=3497173c3b1ddf5bf46dbb00a15e37e7ea095296b314d39d8077aab912c2fd41 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=2 input=9bef9131bd9774edbbc03fc25dc204aba2df5654a068bc54f4c3fe21afdac0cc turn=3497173c3b1ddf5bf46dbb00a15e37e7ea095296b314d39d8077aab912c2fd41 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=1 input=a03353cd3780bf05fa1ce931f029f0e5c2f0e1b681f3e986f7adcbd5e006e3f turn=79b8ff8286cc502ec7798ef627176b387bedc4a3bbf1ec2fac8c8867d5514fff -->

## 再開の入口（実パス）

- 最初に読む親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/tahoe-wallpaper-switcher/_index.md
- 参照したリポジトリ: https://github.com/andmev/tahoe-wallpaper-switcher

## 実装への申し送り

- 実行承認なし。承認前は壁紙・LaunchAgent・ユーザー領域の設定を変更しない。
- 導入する場合も、まず4壁紙の存在、現在の既存LaunchAgentや同名ファイル、リポジトリ固定版の内容、東京などの位置設定を確認する。curlのmain最新版を無固定で実行しない。
- 実機確認では、導入前の壁紙・ダークモードを記録し、導入後に一回実行、ログと画面変化を別々に確認する。確認できない項目は完了扱いにしない。

## 現在地

導入と初回自動実行は確認済み。画面の見た目は未確認。4枚のうちDay動画キャッシュが無い状態でprecheckを迂回したため、Day時間帯の切替だけは未検証で、運用開始可能とはまだ断定しない。

- 2026-09-08: 用語確認。`Day` は昼、`Evening` が夕方。先の「Day切替未確認」は夕方ではなく、昼の壁紙切替を指していた。
- 2026-09-08: 「壁紙が切り替わっていない気がする。時間帯的に昼の壁紙は違和感がある。場当たり的な回答は禁止」と受領。18:33 JST時点で期待値・state・macOSの壁紙設定IDがすべてNightに一致することを確認。System Eventsのdesktop pictureは3台とも`missing value`で、画面上の実壁紙の直接証拠にはならない。entry_pathsに再開対象の実ファイルを追加した。
- 2026-09-08: 武田さんが「現在の壁紙は昼だと明言する。間違いない」と実機観測を確定。以後、実画面はDay、内部の期待値・state・macOS設定IDはNightという不一致を現在状態として扱う。Night指定済みを実画面反映済みとは扱わず、原因未確定のまま追加修正をしない。
- 2026-09-08: 「どうやったら解決できると思う？」を相談として受領。現行スクリプトはstateがNightなら`wallpaperChanged=false`となり、macOS内部設定がNightでも実画面がDayなら再適用しない。まず一回だけ強制再適用して画面を確認し、Nightになるならstateガードの stale（古い状態を正しいと扱う）問題、NightにならないならWallpaperAgent／Space／ディスプレイ反映問題と切り分ける。恒久修正はこの試験結果後に決める。
 - 2026-09-08: 別エージェントへの委任が現実的か、またコピペで渡せる指示を求められた。委任対象は現状の再照合・一度だけの強制Night再適用・内部ログの収集・試験結果に応じた最小修正までとし、実画面がNightになったかの最終判定は武田さんの実機観測に残す。無人で完全完了とは扱わず、実画面確認を一回の停止点にする。
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=8 input=083d0a56ee9adb3eb5279a15087f1514ebe8f4e2e057fc73588558c16580c4ad turn=9cc2cd18086507f6462d75d95ed9dfc4cd8ec0594576a4520883fbfc11da9cd9 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=7 input=d459f08f4df014d6f3c233dc9437e8da7526aa5db7c1b65faf3c7edcf22a3ab1 turn=441a4ee98d4e2478720f26389c75d90c32ec66ade9ae9b4b1983887babe4728c -->
- 2026-09-08: 到達性フックの原因を確認。`entry_paths` は存在していたが、frontmatter終了区切り `---` が欠落していたため、YAMLとして解釈されていなかった。終了区切りを追加し、再開対象の絶対パスをfrontmatterとして有効化した。
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=6 input=686280a12b56791338a7ad2db25f9a0c2e831fcce55798078b4c302b9798e91b turn=b4cc0325910a4ddb12e07ca2bcc68194d89862eacc81cadc387fc41ac8e93561 -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=5 input=df3b306dc7982c63177ba2f43e17eb274824c6a78998f3f81d16c4a4aa659f63 turn=d28500803179904b65c9e2f90bf96771451f744531ec5923add75a177d0d359f -->
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=4 input=fac9d43b1145327438459f4e634630ad142a8fb42fd1885b06bf69585c524030 turn=695d5ae436289efb276be8285a17c24a8437296966b54c9a1f139b4774b7d7a3 -->
