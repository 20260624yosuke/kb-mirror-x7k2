---
type: analysis
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-09-07
sources:
  - https://github.com/andmev/tahoe-wallpaper-switcher
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/tahoe-wallpaper-switcher/_index.md
background_paths:
  - https://github.com/andmev/tahoe-wallpaper-switcher

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
<!-- bs-lite:v1 session=185460f9b3311d1150e4b4c272ad8446ede4ed1ed67c38172765c332549efdca counter=1 input=a03353cd3780bf05fa1ce931f029f0e5c2f0e1b681f3e986f7adcbd5e006e3f turn=79b8ff8286cc502ec7798ef627176b387bedc4a3bbf1ec2fac8c8867d5514fff -->

## 再開の入口（実パス）

- 最初に読む親: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/tahoe-wallpaper-switcher/_index.md
- 参照したリポジトリ: https://github.com/andmev/tahoe-wallpaper-switcher

## 実装への申し送り

- 実行承認なし。承認前は壁紙・LaunchAgent・ユーザー領域の設定を変更しない。
- 導入する場合も、まず4壁紙の存在、現在の既存LaunchAgentや同名ファイル、リポジトリ固定版の内容、東京などの位置設定を確認する。curlのmain最新版を無固定で実行しない。
- 実機確認では、導入前の壁紙・ダークモードを記録し、導入後に一回実行、ログと画面変化を別々に確認する。確認できない項目は完了扱いにしない。

## 現在地

方向性と導入方法の承認待ち。調査・内容確認の段階で、実装は未着手。
