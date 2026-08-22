---
type: analysis
sources: []
status: uncertain
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-07-15
---

# 仮想デスクトップ状態復元の目的・実現性

## 現在の目的(user-stated)

- 現在の仮想デスクトップ配置が、用途別に作業を進めるための現時点の最適状態。
- Mac を再起動するとアプリは自動で開くが、ウィンドウの所属デスクトップと配置が崩れる。
- 現在の全仮想デスクトップ状態を保存し、再起動後にショートカット1回で戻したい。

## 最低限の完成条件(user-stated)

- 再起動後に開いた既存ウィンドウが、元の仮想デスクトップ・表示モニター・位置・大きさへ戻る。
- 最初の実用対象は Obsidian の `LLM Knowledge Base _01` 保管庫。メインウィンドウと、
  同保管庫のポップアウトウィンドウ約60個を、指定した状態で開けるようにする。
- `.obsidian/workspace.json` には、2026-06-16 時点でポップアウト64ウィンドウ・Canvasリーフ65個・
  ユニークCanvas 61件が保存されている。重複して開かれているCanvasは4件ある。

## 今回やらないこと

- 用途別の新しい固定配置や、アプリ構成を設計し直さない。
- Finder / Firefox / Eagle など他アプリを、初期パイロットの主対象にしない。
- 小規模試験と再起動後の実機試験を通す前に、運用可能または実現可能と断定しない。

## 確認できた現在状態

2026-06-16 の実環境調査で、以下を確認した。

- macOS 26.4.1、SIP(System Integrity Protection、macOS の保護機能)は有効。
- BetterTouchTool 4.204 がインストール・起動中。
- HP M27f 側に9個、Kamvas側に1個の仮想デスクトップがある。
- 創作系の1デスクトップでは、Obsidian 65窓、Finder 4窓、Firefox 1窓、Eagle 1窓を観測した。
- この約65窓を大量に常時開く**理由**(各Canvasが「育て中のアイデア」で、閉じると忘れる/着想のシームレスさ優先)は [[canvas-idea-cultivation-workflow]] に記録(2026-07-01 追記)。
- 現在のウィンドウと所属デスクトップを読み取る経路は確認できた。

## 実現性

- BetterTouchTool の組み込み保存・復元は、現在の仮想デスクトップ内の配置を扱う機能であり、
  全仮想デスクトップにまたがる現在状態の復元手段としては未検証。
- 別の補助ツールまたは自作処理で既存ウィンドウを仮想デスクトップ間に移す経路は候補だが、
  まだ1窓の小規模試験も、再起動後の終端試験も実施していない。
- 現在確認できたのは状態の読み取りまで。全体復元を「実現可能」と断定できる段階ではない。

## 実現性パイロット結果(2026-06-16)

`yabai v7.1.25` を試験用に `~/bin/yabai` へ配置し、SIP有効のままFinder 1窓で確認した。

- 表示中のFinder `llm-uploads` 窓は、画面上のデスクトップを切り替えずにSpace 3 → Space 1 → Space 3へ戻せた。
- 同じ窓をSpace 1へ置いた状態で `yabai` を再起動しても、`has-ax-reference: true` のまま取得でき、
  画面切替なしでSpace 3へ戻せた。
- 同じ窓を別モニターへ移動し、元のモニター・位置・大きさへ戻せた。
- 既に非表示デスクトップ上にあり `has-ax-reference: false` のFinder窓は、画面切替なしでは
  `could not locate the window to act on!` となり操作できなかった。

判定: 厳密な「画面切替なしで全デスクトップ復元」は、`yabai` 方式では現時点で不合格。
Phase 1の手動復元ツール実装へは進まない。

後始末として、`yabai` サービスは停止し、LaunchAgent は削除済み。`~/bin/yabai` と `~/.yabairc` は
試験用ファイルとして残している。

次に検討できる現実的な別案は、復元前に全デスクトップを1回ずつ表示して `has-ax-reference` を作る
「ウォームアップ」を許容する方式。ただし画面が数秒切り替わるため、当初条件とは別案として扱う。

## 計画レビュー(2026-06-16)

直前の実装計画は、最重要のデスクトップ間移動を実機確認する前に、自動保存、14日履歴、
Chromeプロファイル解析、Obsidianプラグイン改修、BetterTouchTool Launcher連携まで含めており、
実現性未確認の段階としては作業範囲が大きすぎた。

計画を以下の順番へ縮小した。

1. Finder 1窓を、画面上のデスクトップを切り替えずに別デスクトップへ移動・復元できるか試験する。
2. 合格した場合だけ、手動の `snapshot` / `restore` / `undo` を作る。
3. 実際の再起動後復元まで成功した場合だけ、BetterTouchToolショートカットと5分ごとの自動保存を
   追加する。

Chromeプロファイル解析、Obsidianによる不足Canvasの再生成、長期履歴、複数の操作入口は、
通常照合で実害が確認されるまで対応しない。Obsidianはまず、Obsidian自身が再起動時に復元した
既存窓を配置対象とする。

実現性パイロットの必須条件は、SIP有効のまま動くことに加えて、窓を1つ動かすたびに移動先の
デスクトップへ画面表示が切り替わらないこと。50窓以上の復元で画面切り替えを繰り返す方式は、
技術的に動いても使用感が現実的でないため不採用とする。

加えて `yabai` の公式仕様では、`yabai` 起動時に非表示デスクトップへ既に存在する一部窓は
`has-ax-reference: false` となり、そのデスクトップを一度表示するまで移動命令が動かない。
通常の往復試験だけでは再起動後の条件を確認できないため、窓を非表示デスクトップへ置いたまま
`yabai` を再起動し、画面切替なしで再取得・移動できることも必須条件とする。

## Obsidianの扱い

- 保管庫内には同じ基礎名を持つ `無題のファイル*.canvas` が複数あり、異なるフォルダの
  Canvas が同じ表示タイトルになる場合と、同じ Canvas が複数窓で開かれる場合がある。
- 2026-06-16 の訂正: 初期対象は少数アプリではなく、`LLM Knowledge Base _01` のメインウィンドウと
  約60個のポップアウトウィンドウ。まずこの保管庫のウィンドウ群を指定状態で開けることを確認する。
- `workspace.json` は「どのCanvasをどのObsidianポップアウトとして開くか」を保持しているが、
  macOS のどの仮想デスクトップ・モニターへ置くかは保持していない。そこは `yabai` などで別途保存・復元する。
- 復元前に全仮想デスクトップを1回ずつ表示して、`has-ax-reference` を作るウォームアップ方式は、
  ユーザーがまず検証用に許容した。
- 復元完了後の戻り先仮想デスクトップは、現時点では未定。
- 2026-06-16 の現状読取では、Space 4 に `LLM Knowledge Base _01` のObsidian窓65個、
  Space 5 に別保管庫のObsidian窓2個、Space 6 に別保管庫のObsidian窓1個を観測した。
  `LLM Knowledge Base _01` の復元では、Space 4/5 のような仮想デスクトップ分布も復元対象に含める。
- `LLM Knowledge Base _01` のポップアウト数は固定ではない。現在進行中の制作Canvas・参考Canvas・
  取り込み対象が変わるため、固定リストではなく「保存時点の開いているポップアウト集合」を
  スナップショットとして扱う必要がある。
- 復元時に保存時点には無かった余分なObsidianポップアウトが開いていても、自動では閉じない。
  初期版では報告だけに留める。作業中Canvasを誤って閉じたように見える事故を避けるため。

## 初期スクリプト(2026-06-16)

`tools/obsidian_llm_kb_window_layout.py` を作成した。

- `snapshot`: 現在の `LLM Knowledge Base _01` のObsidian窓を、Space・Space UUID・モニター・座標・サイズ付きでJSON保存する。
  既定の保存先は `tools/window-layout-state/latest.json`。
- `plan`: 保存済みスナップショットと現在のObsidian窓を照合し、移動・リサイズが必要な窓、欠けている窓、余分な窓を表示する。
- `restore`: 既定ではdry-run(実際には動かさない)。`--apply` を付けた場合だけ、ウォームアップ後に窓を移動・リサイズする。
- 安全方針: 余分なObsidian窓は閉じない。`yabai` は必要時だけ一時起動し、もともと起動していなければ終了後に停止・LaunchAgent削除する。
  （**2026-06-26 撤回**: yabai の都度削除は再起動後の cold-start 失敗原因のため廃止し、ログイン時自動起動の常駐サービスへ変更。末尾「変遷」2026-06-26 参照。）
- 安全追加: `restore --apply` 前に `tools/window-layout-state/backups/pre-restore-YYYYMMDD-HHMMSS.json`
  へ現在状態を退避保存する。欠けている窓、未確認の余分な窓、未確認の警告、必要Space不足がある場合は
  実移動を拒否する。
- 対象追加: `--all-obsidian` を付けると、`LLM Knowledge Base _01` だけでなく、全Obsidian保管庫の
  窓を同じスナップショットに保存する。

2026-06-16 の自動確認:

- `python3 -m py_compile tools/obsidian_llm_kb_window_layout.py` 成功。
- `/tmp/llmwiki-obsidian-layout-snapshot.json` へ一時スナップショット作成。
- 直後の `plan` で saved 65 / current 65 / matched 65 / need_move_or_resize 0 / missing 0 / extra 0。
- 重複タイトル `無題のファイル 2` で警告あり。同名Canvasが複数あるため、近い位置で対応付ける想定内の警告。
- 実際に窓を動かす `restore --apply` は未実施。前面GUI操作とSpace切替を伴うため、別途明示確認が必要。

2026-06-16 の安全化後の自動確認:

- `python3 -m py_compile tools/obsidian_llm_kb_window_layout.py` 成功。
- `/tmp/llmwiki-obsidian-layout-safety-check.json` へ一時スナップショット作成。
- 読み取り結果は saved 65 / current 65 / matched 65 / need_move_or_resize 0 / missing 0 / extra 0。
- Space分布は saved 4:65 / current 4:65 / target 4:65。現時点の読み取りでは
  `LLM Knowledge Base _01` のSpace 5配置は確認できない。
- `restore --snapshot /tmp/llmwiki-obsidian-layout-safety-check.json --require-target-spaces 4,5`
  のdry-runでは、警告未確認とSpace 5不足が実移動前の停止理由として表示された。
- `yabai` サービス停止とLaunchAgent削除を確認済み。

2026-06-16 の全Obsidian配置保存:

- ユーザー確認により、まずは現状のObsidian全体配置を再現対象にする。
- `tools/obsidian_llm_kb_window_layout.py --all-obsidian snapshot` で
  `tools/window-layout-state/latest.json` へ保存した。
- 保存対象はObsidian全体68窓。Space 4に65窓、Space 5に3窓。
- Space 4の65窓は `LLM Knowledge Base _01`。Space 5の3窓は別保管庫
  `LLM Brain Base_01` / `in_box_nsfw` / `knowledge用_inbox`。
- 直後の照合は saved 68 / current 68 / matched 68 / need_move_or_resize 0 /
  missing 0 / extra 0。Space分布は saved/current/target すべて 4:65, 5:3。
- `restore --require-target-spaces 4,5 --allow-warnings` のdry-runは通過。実移動は未実施。
- `yabai` サービス停止とLaunchAgent削除を確認済み。

2026-06-16 の全Obsidian実移動検証:

- ユーザーがObsidian 68窓を別Spaceへ移動した状態で、読み取り上は current 3:68、
  target 4:65, 5:3、need_move_or_resize 68 になった。
- `restore --require-target-spaces 4,5 --allow-warnings` のdry-runは通過した。
- ユーザーの明示確認後、`restore --apply --require-target-spaces 4,5 --allow-warnings` を実行。
  実行前状態は `tools/window-layout-state/backups/pre-restore-20260616-163355.json` に退避保存された。
- 実行後の照合は saved 68 / current 68 / matched 68 / need_move_or_resize 0 /
  missing 0 / extra 0。Space分布は current 4:65, 5:3 へ戻った。
- 判定: Obsidian全体のSpace 4/5配置復元パイロットは、手動で崩した状態からの実移動検証に合格。
  ただし、再起動後にObsidianが窓を開き直した直後の復元は未検証。

2026-06-16 のRaycast入口:

- LLM経由でなくても実行できるよう、Raycast Script Command として
  `/Users/takedayousuke/.config/raycast-scripts/restore_obsidian_layout.sh` を追加した。
- Raycast 上の表示名は `Obsidian配置を復元`。実体は
  `tools/obsidian_llm_kb_window_layout.py restore --apply --require-target-spaces 4,5 --allow-warnings`。
- 実行ログは `tools/window-layout-state/raycast-restore.log` に追記する。
- スクリプト構文確認 `bash -n` は成功。Raycast 画面からの手動起動はユーザーが検証済み。

2026-06-16 の用途別入口:

- Mac再起動後用: `tools/restore_after_mac_reboot.sh`。Obsidian窓が揃うまで最大300秒待ち、
  保存済みのSpace 4/5配置へ戻す。現時点の対象はObsidian全窓であり、Finder / Firefox / Eagle は未対応。
- Obsidian再起動後用: `tools/restore_after_obsidian_restart.sh`。Obsidian窓が揃うまで最大180秒待ち、
  保存済みのSpace 4/5配置へ戻す。この入口はObsidianを終了・再起動しない。
- 共通処理: `tools/restore_obsidian_layout_with_wait.sh`。待機、復元、復元後照合を担当する。
- Raycast入口:
  `/Users/takedayousuke/.config/raycast-scripts/restore_after_mac_reboot.sh` と
  `/Users/takedayousuke/.config/raycast-scripts/restore_after_obsidian_restart.sh` を追加した。
- Raycast非依存の実行入口:
  `/Users/takedayousuke/bin/restore-after-mac-reboot` と
  `/Users/takedayousuke/bin/restore-after-obsidian-restart` を追加した。
- 自動確認: 各シェルスクリプトの `bash -n` 成功。`RESTORE_DRY_RUN=1 WAIT_SECONDS=5 bash tools/restore_after_obsidian_restart.sh`
  で、待機からdry-run復元まで通過。直後の照合は saved 68 / current 68 / matched 68 /
  need_move_or_resize 0 / missing 0 / extra 0。
- Raycast非依存の入口も、`RESTORE_DRY_RUN=1 WAIT_SECONDS=5 ~/bin/restore-after-obsidian-restart` と
  `RESTORE_DRY_RUN=1 WAIT_SECONDS=5 ~/bin/restore-after-mac-reboot` が成功。
- 未確認: Mac再起動後、およびObsidianを実際に終了・再起動した直後の実機復元。

2026-06-16 の全アプリ全デスクトップ保存:

- `tools/all_window_layout_snapshot.py` を追加した。これは全デスクトップの全アプリ窓を保存・要約する
  スナップショット専用スクリプトであり、復元は行わない。
- `tools/window-layout-state/all-windows-latest.json` へ現在状態を保存した。
- 保存結果は全ウィンドウ100件、通常ウィンドウ99件、移動・リサイズ可能96件、
  `has_ax_reference` あり99件。
- Space分布は 1:1, 2:3, 3:9, 4:71, 5:3, 6:7, 8:2, 10:3。
- アプリ別では Obsidian 68、Finder 10、Google Chrome 3、Safari 2、CLIP STUDIO PAINT 2、
  その他は各1件。
- 保存ファイルサイズは約64KBで、Obsidian全体保存 `tools/window-layout-state/latest.json`
  の約70KBと同程度。容量面では全アプリ保存へ広げても問題ない。
- 判定: 保存は同じ方式・同じ容量感で可能。全アプリ復元は、アプリごとの窓識別が必要なため未実装。

2026-06-16 の対応済みアプリ復元枠:

- `tools/all_window_layout_restore.py` を追加した。全アプリ保存データを使い、保存済み窓と現在窓を
  アプリ名・ウィンドウタイトルで照合し、移動・リサイズの必要量をdry-runできる。
- Obsidianは既存の保管庫対応復元があるため、汎用復元からは既定で除外する。
- Obsidian以外を全件dry-runしたところ、Chromeの動画タブが保存時タイトルと合わず1窓不足、
  Safariは同じタイトルの2窓で警告になった。Chrome/Safariは初期対応から外す。
- 初期の対応済みアプリは Finder / Eagle / Firefox。dry-run結果は saved 12 / current 12 /
  matched 12 / need_move_or_resize 0 / missing 0 / extra 0。
- `tools/restore_supported_window_layout.sh` を追加し、Mac再起動後用の入口
  `tools/restore_after_mac_reboot.sh` は、Obsidian全窓 + Finder / Eagle / Firefox を
  1回で復元する構成に変更した。
- `RESTORE_DRY_RUN=1 WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` は成功。
  ただし Finder / Eagle / Firefox の実移動は未検証。

2026-06-16 の保存し直し入口:

- `tools/save_current_window_layout.sh` を追加した。Obsidian専用保存
  `tools/window-layout-state/latest.json` と、全アプリ保存
  `tools/window-layout-state/all-windows-latest.json` の両方を現在状態で更新する。
- Raycast入口 `/Users/takedayousuke/.config/raycast-scripts/save_current_window_layout.sh`
  と、Raycast非依存入口 `/Users/takedayousuke/bin/save-current-window-layout` を追加した。
- 実行確認済み。実行後の現在保存は、Obsidian 68窓、全ウィンドウ100件、通常ウィンドウ98件。

2026-06-16 の保存状態更新:

- ユーザーが不要なFinder 2窓を閉じたため、それらを復元対象から外す方針にした。
  Finder不足窓の自動開き直しは不要と判断し、実装途中の開き直し経路は本番入口に入れない。
- `save-current-window-layout` で現在状態を保存し直した。
- 更新後の保存状態は全ウィンドウ98件、通常ウィンドウ97件。アプリ別では Obsidian 68、
  Finder 8、Google Chrome 3、Safari 2、CLIP STUDIO PAINT 2、その他は各1件。
- 対応済み復元範囲は Obsidian 68窓 + Finder 8窓 + Eagle 1窓 + Firefox 1窓の合計78窓。
- dry-run確認は、Obsidian saved/current/matched 68、Finder/Eagle/Firefox saved/current/matched 10、
  missing 0 / extra 0 / need_move_or_resize 0 で通過。
- 判定: 現在の保存状態では、Mac再起動後用入口の対応済み範囲は安全条件を満たす。
  非Obsidian窓の実移動と、Mac再起動後の終端試験は未検証。

2026-06-16 のワークフロー化:

- `tools/all_window_layout_restore.py` の照合ルールを調整した。Google Chrome はウィンドウタイトル末尾の
  プロファイル名、Safari はSafari窓グループ、保存時点で1窓だけのアプリはアプリ名で対応付ける。
  これにより、ブラウザのページタイトルや1窓アプリの表示タイトルが多少変わっても、窓数が一致すれば
  近い位置の窓として復元できる。
- Mac再起動後用入口 `tools/restore_after_mac_reboot.sh` は、既定で Obsidian 68窓と、
  Finder / Google Chrome / Safari / メール / Google ToDo リスト / Notion / Notion Calendar /
  ChatGPT / Claude / Codex / Grok / ジャーナル / Eagle / Firefox / アクティビティモニタ /
  システム設定の26窓を扱う。合計94窓。
- CLIP STUDIO と CLIP STUDIO PAINT の3窓は既定対象から外した。現在の読み取りで `can_resize: false`
  のため、再起動後にサイズ差分が出ると復元全体を止める可能性があるため。
- 自動確認: `python3 -m py_compile` と対象シェルスクリプトの `bash -n` は成功。
  `RESTORE_DRY_RUN=1 WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` は、Obsidian 68窓と
  非Obsidian 26窓で missing 0 / extra 0 / need_move_or_resize 0。
- 実移動確認: Finder `llm-uploads` 窓を Space 3 から Space 4 へ一時移動し、
  `WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` を実行した。汎用復元側で
  need_move_or_resize 1 を検出し、Space 3 へ戻した。直後のdry-runでは
  Obsidian 68窓、非Obsidian 26窓とも need_move_or_resize 0。
- 退避保存: 実行時に `tools/window-layout-state/backups/pre-restore-20260616-211409.json` と
  `tools/window-layout-state/backups/pre-all-restore-20260616-211418.json` を作成。
- 判定: 現在保存済みの配置を、保存入口とMac再起動後用ワンアクション復元入口で運用する段階には到達。
  ただし、Macを実際に再起動した直後の終端試験、Obsidian単体を実際に再起動した直後の終端試験、
  CLIP STUDIO系3窓の復元は未検証。

2026-06-16 のMac再起動後初回試験:

- ユーザーがMac再起動後に復元を実行したところ、`復元に失敗しました` が表示された。
- ログ確認では、Obsidian 68窓は saved/current/matched 68 で揃っていたが、全窓が一度 Space 1 に
  集まっており、保存先 Space 4/5 へ移動が必要だった。
- 失敗原因: ウォームアップ中に、すでに表示中の Space 1 へ `yabai -m space --focus 1` を実行し、
  `cannot focus an already focused space` が返った。これは復元不能ではなく、同じSpaceを選び直しただけの
  無害な状態をエラー扱いした実装バグ。
- 修正: `tools/obsidian_llm_kb_window_layout.py` の `focus_space()` で、対象Spaceがすでに表示中なら
  何もしない。加えて `already focused space` は成功相当として扱う。再起動直後の `yabai` 起動待ちも
 6秒から30秒へ延長した。
- 確認: `python3 -m py_compile`、対象シェルスクリプトの `bash -n`、
  `RESTORE_DRY_RUN=1 WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` は成功。
- 未確認: 修正後の `--apply` 本番復元は未実施。次回は同じショートカットを再実行して終端確認する。

2026-06-16 の不足窓停止修正と実移動確認:

- 修正後の本番実行では、Obsidian 68窓は Space 4:65 / Space 5:3 へ戻ったが、
  非Obsidian側で保存済み窓の一部が現在存在せず、汎用復元が失敗扱いで止まった。
- 方針: 保存時点に存在したが今ない窓は自動で開き直さず、不足一覧としてログに残す。
  現在存在して照合できる窓は復元する。余分な現行窓は閉じない。
- 修正: `tools/restore_supported_window_layout.sh` の汎用復元呼び出しに
  `--allow-missing --allow-extra` を追加した。
- 実移動確認: `WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` を実行し、Obsidian側は
  saved/current/matched 68、非Obsidian側は saved 26 / current 24 / matched 22 /
  missing 4 / extra 2 で完了した。
- 復元後dry-run: Obsidian、非Obsidianとも `need_move_or_resize 0`。残る不足は
  Finder `“このMac”を検索`、Google ToDo リスト、Grok、ジャーナル。これは現在存在しない窓であり、
  自動復元対象外。
 - 判定: 現在開いている対応済み窓の配置復元は成功。保存時点の全26非Obsidian窓を自動で再生成する
  状態ではない。

2026-06-17 のユーザー運用検証:

- ユーザーが実際の運用手順で検証し、現時点では不満なしと報告した。
- 問題が見つかった場合は、発見次第ユーザーから報告される予定。
- 判定: 現在の範囲は暫定運用可能。ここでいう暫定運用可能とは、保存済み配置へ戻すワークフローとして
  使い始めてよいが、問題報告があれば個別に直す段階という意味。
- 残る既知の非対象: 保存時点にあったが再起動後に開いていない窓の自動再生成、
  CLIP STUDIO / CLIP STUDIO PAINT 3窓の復元。

## Obsidian全体4/5パイロット修正版計画(2026-06-16)

目的は全通常ウィンドウの復元だが、次の実装範囲はObsidianパイロットまでに縮小する。
ここで全アプリ対応スクリプト新設、ショートカット化、自動保存、履歴保存は行わない。

合格条件:

- Obsidian全体の現在配置を保存・照合できる。
- Space 4/5 の振り分け、表示モニター、位置、サイズが保存状態へ戻る。
- 実移動前に現在状態の退避スナップショットが作られる。
- 欠けている窓、未確認の余分な窓、未確認の警告、必要Space不足がある場合は停止する。
- 余分なObsidianポップアウトは閉じない。

進行順:

1. 現在のObsidian全体がSpace 4/5にどう分布しているかを読み取りのみで再確認する。
2. 分布がユーザー認識と違う場合は、実移動へ進まず原因確認で停止する。
3. `tools/window-layout-state/latest.json` にObsidian全窓の状態を保存する。
4. 実移動前に、画面切替・フォーカス占有・停止条件を明示して確認を取る。
5. 全Obsidian窓で復元を実施し、Space 4/5分布、モニター、位置、サイズを確認する。
6. 再起動後のObsidian復元まで成功した場合だけ、全アプリ対応を別フェーズで計画する。

## 2026-06-21 Mac実再起動後の復元失敗（待機ゲート起因）と修正

- 症状: Mac再起動後にRaycastの復元を実行しても配置が戻らなかった。実機ログ
  `restore-runner.log`（2026-06-21 04:37〜04:38）で、Obsidian側が
  `saved=70 current=70 matched=69 missing=1 extra=1 move=69 current_spaces=1:70`
  のまま待機を繰り返し、300秒でタイムアウト→全窓がSpace 1に取り残されていた。
  本調査時点でも未復元のまま（current_spaces 1:70）だった。
- 根本原因: 連鎖する3つのゲートが、保存後に1窓のタイトルが変わっただけで全停止していた。
  1. **待機ループ** `restore_obsidian_layout_with_wait.sh` の `wait_until_ready` が
     `missing==0 && extra==0` を要求。再起動後にどれか1窓の開いているノートが変わると
     タイトル照合が永久に一致せず、移動を一切始めないままタイムアウトした。
  2. **Python検証** `validate_apply_plan` が `missing_count>0` で無条件に復元拒否
     （Obsidian側には `--allow-missing` 相当が無かった）。
  3. **最終判定** が `missing==0 && extra==0` を成功条件にしており、後段の他アプリ復元へ
     進めなかった。
- 修正（`no-reactive-fixes` に従いパイプライン全体を通して一括）:
  1. `obsidian_llm_kb_window_layout.py` に `--allow-missing` を追加。指定時は照合できた窓
     だけ復元する（既存 `--allow-extra` と対称、既定は従来どおり拒否）。
  2. `wait_until_ready` の判定を「窓の数で待つ」方式へ変更。`current >= saved` かつ
     直前ポーリングと数が同じ（安定）なら進む。タイトル照合の `missing/extra` ではゲートしない。
  3. 復元呼び出しに `--allow-missing --allow-extra` を追加。最終判定を `move==0`
     （照合できた窓が全部所定位置）を成功条件に緩和し、残った `missing/extra` は注記して
     後段の他アプリ復元へ進む。
- 検証（本調査の実機）:
  - dry-run（`RESTORE_DRY_RUN=1`）が、修正前はハングしていたゲートを通過し exit 0。
  - 本番 `restore_supported_window_layout.sh` を `--apply` 実行。Obsidianは
    `current_spaces 1:70 → 4:67, 5:2`、`need_move_or_resize 0` で復元成功。他アプリ復元も
    `exit 0`。全パイプライン `exit 0`。
  - 取り残された1窓の正体: 同じ窓で開くノートを保存後に切り替えたもの（保存時
    「Xユーザーの The Wall Street Journal…」→ 現在「2026-06-21_How to fix your entire life…」）。
    タイトル照合で対応付かず、その1窓だけSpace 1に残った（設計上の既知の制限どおりの劣化）。
- 残課題: タイトルが変わって取り残された窓は手動でSpaceへ移すか、配置を保存し直す必要がある。
  根本対処（タイトル非依存の窓識別）は未実装。

## 2026-06-21 残存パターンの一括洗い出しと修正

過去のログ全件を分類し、上記の修正後にまだ残存するパターンを2件特定して修正。

1. **yabai ready タイムアウト（パターン1）**
   - 2026-06-16 21:43 に5回連続発生。再起動後のOS初期化が遅くて30秒で yabai が ready にならなかった。
   - 修正: Python 側のタイムアウトを 30s→60s に延長。加えて、bash 側（`restore_obsidian_layout_with_wait.sh`）で
     yabai の起動を1回だけ 60s 待ち、以降のポーリング・復元には `--leave-yabai-running` で起動済みの
     yabai を使い回す方式に変更。これにより、ポーリングのたびに yabai 起動待ちを繰り返す問題も解消。
   - `restore_supported_window_layout.sh` では Obsidian 側が起動した yabai を汎用側でも使い回し、
     全処理完了後に停止する構造に変更（`CLEANUP_YABAI` フラグで制御）。

2. **個別窓移動の実行時エラーで全停止（パターン9）**
   - コード上の構造的欠陥。70窓の移動中に1窓でも yabai コマンドが失敗すると `ToolError` が
     伝播して残り全窓が止まる。
   - 修正: `--skip-unmovable` 指定時は、移動ループ内の `ToolError` を try/except で捕まえ、
     失敗した窓をスキップして残りを続行する。スキップ数はログに `skipped_runtime_errors` として
     出力。Obsidian側・汎用側の両方に適用。

検証:
- 構文チェック（bash -n、Python ast.parse）: 全ファイル通過。
- dry-run（`RESTORE_DRY_RUN=1` で全パイプライン）: exit 0。
- 本番 apply（全パイプライン）: Obsidian 70窓中 69 窓を配置復元、他アプリ復元も exit 0。
- yabai ライフサイクル: 単独入口（Obsidian再起動後）では cleanup する、supported 入口では
  全処理完了後に cleanup する、どちらも確認済み。

## 2026-06-23 再起動後検証で見えた残課題

ユーザーが再起動前に `save_current_window_layout.sh` を実行し、`2026-06-23 21:20:51` に現在配置を保存した。
保存結果は Obsidian 70窓、全ウィンドウ97件。保存処理は `obsidian_snapshot_exit: 0` /
`all_window_snapshot_exit: 0` で成功。

`2026-06-23 22:10:26` に Mac再起動後の配置復元を実行した。ログ上の結果は以下。

- Obsidian側: 再起動直後は全70窓が Space 1 に集まっていたが、`Space 3:1 / 4:67 / 5:2` へ復元成功。
  `missing 0 / extra 0 / move 0` で完了。
- 非Obsidian側: 保存17窓のうち、現在15窓、照合14窓、移動必要9窓。実行自体は `exit 0`。
- Codex: 復元時点では `has_ax_reference: false` かつ `can_move/can_resize: false` と読まれ、
  `skip (no AX reference)` / `skip (cannot move/resize)` で移動されなかった。
- Firefox: 保存時は `Firefox` だが、再起動直後バックアップではアプリ名が小文字 `firefox` として読まれ、
  さらに保存時タイトル `(8) ホーム / Twitter` と一致しなかったため不足扱いになった。
- Safari: 2窓は復元対象に含まれていた。復元前バックアップでは全窓が `space_index: 0` /
  `has_ax_reference: false` / `can_move: false` と読まれており、macOS の補助操作参照が安定する前に
  復元処理が走った可能性が高い。
- CLIP STUDIO / CLIP STUDIO PAINT: 保存には含まれているが、既定の `SUPPORTED_APPS` には含めていない。
  また `can_resize: false` のため、現状の汎用復元では安全にサイズ復元できない。これは既知の非対象。

ユーザーは Safari と Firefox の挙動が変になったと報告し、再起動で解決した。調査時点では
`yabai` サービスは残存していなかった。BetterDisplay は起動中で、`M27f-HiRes` がメインディスプレイかつ
HP M27f へのミラーになっていた。これは [[betterdisplay-m27f-pseudo-resolution]] の注意事項
「メインディスプレイ変更は避ける」に当たるが、保存時と現在の座標系はどちらも 2560x1440 UI 前提で
一致しており、直接の座標崩れという証拠はない。むしろ、再起動直後に非Obsidian窓が
`space_index: 0` / `has_ax_reference: false` と読まれたことが主要な手がかり。

修正要否:

- 必須寄り: 非Obsidian復元の前に、対象アプリの窓が `space_index != 0` になり、補助操作参照が安定するまで
  待つゲートを追加する。現状は Obsidian 側の待機はあるが、非Obsidian側は十分に待っていない。
- 必須寄り: アプリ名の表記揺れを正規化する。少なくとも `firefox` → `Firefox` は必要。
- 推奨: 非Obsidian側でも専用 warmup（全Spaceを一度表示して補助操作参照を作る処理）を戻すか、
  それに相当する再照合ループを追加する。
- 任意: BetterDisplay の仮想画面が復元済みかを確認してからウィンドウ復元する待機を追加する。
  ただし現時点では直接原因とは断定しない。
- 任意: CLIP STUDIO系は「Space移動だけ」なら対応余地があるが、サイズ復元は `can_resize: false` なので
  対象に入れるなら別扱いにする。未保存作業への影響がありうるため、勝手には追加しない。

実装した再発防止(2026-06-23):

- `tools/all_window_layout_restore.py` に非Obsidian側の安定待ちを追加。`space_index: 0` や、移動が必要な窓の
  `has_ax_reference: false` が残っている間は待つ。復元後も再照合し、まだ移動・リサイズが残る場合は
  成功扱いにしない。
- `firefox` を `Firefox` に正規化し、再起動直後のアプリ名表記揺れで不足扱いにならないようにした。
- `yabai` のJSON読み取りが一時的に壊れた文字列を返す場合に備え、最大5回リトライするようにした。
- `move-frame`(位置移動)と `resize-frame`(サイズ変更)を分離。CLIP STUDIO系のようにサイズ変更不可でも、
  位置移動だけなら復元対象にできる。
- 既定の非Obsidian復元対象へ `CLIP STUDIO` / `CLIP STUDIO PAINT` を追加。ただしサイズ変更は行わず、
  現在開いていない書類窓は不足扱いのまま自動再生成しない。
- `CLIP STUDIO` / `CLIP STUDIO PAINT` はタイトルではなくアプリ内の窓グループとして照合し、
  再起動後にタイトルが `MainFrame` へ変わっても近い位置の窓として対応付ける。

検証(2026-06-23):

- `python3 -m py_compile tools/all_window_layout_snapshot.py tools/all_window_layout_restore.py tools/obsidian_llm_kb_window_layout.py` 成功。
- `bash -n tools/restore_supported_window_layout.sh tools/restore_after_mac_reboot.sh tools/restore_obsidian_layout_with_wait.sh` 成功。
- `python3 -m unittest tools.tests.test_all_window_layout_restore` 成功(4件)。
- `RESTORE_DRY_RUN=1 WAIT_SECONDS=5 POLL_SECONDS=2 bash tools/restore_after_mac_reboot.sh` 成功。
- `python3 -m unittest discover -s tools/tests -p 'test_*.py'` は既存のCanvas系テスト3件が `PIL`
  不足で import error。今回追加したテストとは別要因。

## 2026-07-15 27インチ側フリーズ後の復旧記録

### 起点

- ユーザー報告: メインディスプレイ側の27インチが長時間フリーズし、マウスを移しても画面反応が無い状態になった。
- その後ユーザーはMacを再起動した。したがって今回の記録は「再起動前の直接原因特定」ではなく、
  再起動後に崩れた表示構成・保存状態・ウィンドウ配置の復旧経緯を残すもの。
- 原因はこの時点で未確定。BetterDisplay、仮想スクリーン、復元スクリプト、個別アプリのどれが
  直接原因だったかは断定しない。

### 保存状態の上書きと復元

- `tools/window-layout-state/save-current-layout.log` では、正常側の保存が
  `2026-07-14 20:30:02`、崩れた状態での上書き保存が `2026-07-15 20:56:58` と確認できた。
- 正常側の保存内容:
  - `latest.json`: Obsidian 8窓、Space分布 `4:7, 10:1`
  - `all-windows-latest.json`: 通常窓35件、Space分布 `2:4, 3:7, 4:10, 6:7, 8:2, 10:5`
- 上書き後の崩れた保存内容:
  - `latest.json`: Obsidian 10窓、Space分布 `1:10`
  - `all-windows-latest.json`: 通常窓35件、Space分布 `1:32, 4:1, 10:1, 11:1`
- 対応として、`tools/window-layout-state/latest.json` と
  `tools/window-layout-state/all-windows-latest.json` を、上書き直前バックアップの内容へ戻した。
- 誤って上書きされた側のJSONも、比較用として `tools/window-layout-state/backups/` に退避した。

### 表示構成の復旧

- `system_profiler SPDisplaysDataType` の確認時点で、表示構成は [[betterdisplay-m27f-pseudo-resolution]] の
  仮想スクリーン運用へ戻っている。
- 記録時点の構成:
  - `M27f-HiRes`: `5120 x 2880`、UI `2560 x 1440 @ 60Hz`、Main Display、Mirror On
  - `HP M27f FHD`: `5120 x 2880`、UI `2560 x 1440 @ 75Hz`、Hardware Mirror
  - `Kamvas 24plus`: `2560 x 1440 @ 60Hz`、Mirror Off
- これは「元の安定構成へ戻した」のではなく、「今回のプロジェクト運用で使っていた仮想ミラー構成へ
  戻した」という意味である。27インチフリーズとの因果は未確定。

### 関連アプリの巻き戻し

- Blender は、プロジェクトの現行採用版として
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app`
  を `4.5.11 arm64` へ戻した。
- 旧版Intel系は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender-2.92-Intel.app`
  として別名退避のまま残した。

### ウィンドウ配置の復旧経緯

- `2026-07-15 21:02` に `WAIT_SECONDS=5 bash tools/restore_after_mac_reboot.sh` を実行した。
- Obsidian側:
  - 実行前は `saved 8 / current 10 / matched 7 / missing 1 / extra 3 / current_spaces 1:10`
  - 実行後は `move 0 / current_spaces 1:3,4:6,10:1`
  - つまり、照合できた7窓は保存時の位置へ戻せたが、保存時に無い余分窓は閉じず、保存時の1窓は不足のまま残った。
- 非Obsidian側:
  - 同じ実行で汎用復元へ進んだが、`generic apps restore exit=2` で終了した。
  - 主因は、保存時に存在したが現在開いていない窓(Finder複数、Chrome一部、アクティビティモニタ、
    システム設定)と、`CLIP STUDIO PAINT` / `Firefox` の補助操作参照不安定だった。
- その後の追加調整で、Firefox は保存時フレーム
  `x=0, y=30, w=1280, h=1410` へ手動補正済み。
- ただし `CLIP STUDIO PAINT` は、保存時の `2026-07-14 水着` 系書類と、現在開いている
  `2026-07-15 垂れ乳模写` 系書類が別であり、自動照合どおりに動かすと現在作業中の大窓を
  `800x600` へ縮める危険があるため、この時点では止めた。

### 記録時点の残状態

- `python3 tools/obsidian_llm_kb_window_layout.py --all-obsidian plan --snapshot tools/window-layout-state/latest.json`
  の記録時点結果は、
  `saved 8 / current 9 / matched 7 / need_move_or_resize 0 / missing 1 / extra 2 / current_spaces 4:6, 6:2, 10:1`
  だった。
- したがって、この時点の到達点は「保存データの巻き戻しは完了、表示構成は元の仮想ミラー運用へ復帰、
  ObsidianとFirefoxの大半は戻したが、CLIP STUDIO PAINT と現在開いていない窓の再生成は未完了」である。
- ユーザーはこの時点で、追加復旧より先に経緯の記録を優先すると判断した。

## 変遷

- 2026-07-15: 27インチ側フリーズ後、ユーザー再起動済みの状態から復旧を開始。`save-current-layout.log`
  で `2026-07-15 20:56:58` の崩れた保存上書きを確認し、`2026-07-14 20:30:02` 時点の
  `latest.json` / `all-windows-latest.json` へ巻き戻した。表示構成は
  [[betterdisplay-m27f-pseudo-resolution]] の仮想ミラー構成へ戻した。Obsidian は7窓を保存位置へ
  戻し、Firefox は保存時フレームへ手動補正した。一方で `CLIP STUDIO PAINT` は保存時と現在で
  別書類が開いており、現作業窓を誤縮小する危険があるため自動では触らず保留。原因は未確定のまま記録。

- 2026-06-28: Obsidian再起動後入口が「ずっと読み込み中」に見える原因を調査。実ログでは
  `2026-06-28 10:54` の実行で、保存済み74窓に対して現在73窓しか開かず、待機条件
  `current >= saved` を満たせないまま180秒待って失敗していた。さらに `2026-06-28 10:58` の実行では
  `yabai`(macOSのウィンドウ情報を読む補助コマンド)の問い合わせが無応答になり、時間制限なしで待つため
  約15分進まなかった。対処として、`tools/restore_obsidian_layout_with_wait.sh` は現在窓数が保存数未満でも
  一定回数安定したら照合済み窓だけ復元へ進むよう変更し、不足窓はログへ残す方式にした。また
  `tools/obsidian_llm_kb_window_layout.py` の `yabai` 呼び出しに timeout(待ち時間の上限)を追加し、
  無応答時は `--restart-service` → `--start-service` で復旧を試すようにした。確認は
  `py_compile`、`bash -n`、`python3 -m unittest tools.tests.test_all_window_layout_restore`、
  `RESTORE_DRY_RUN=1 WAIT_SECONDS=30 POLL_SECONDS=2 READY_STABLE_POLLS=2 bash tools/restore_after_obsidian_restart.sh`
  で成功。dry-run(実際には窓を動かさず実行計画だけ確認する試験)では、固まっていた `yabai` を復旧し、
  saved 74 / current 73 / matched 71 / missing 3 / extra 2 の安定部分まで進めた。実際に窓を動かす
  `--apply` は前面GUI操作を伴うため未実施。
- 2026-06-26: 電源断→Mac再起動後にワンアクション復元が `yabai did not become ready within 60s` で失敗。
  原因は、復元完了時に毎回 `cleanup_yabai`（`--stop-service` + `--uninstall-service`）で yabai の
  LaunchAgent を削除する設計だったため、再起動のたびに yabai が不在になり、復元時の60秒ゲート内で
  cold-start が間に合わなかったこと。対処として yabai をログイン時自動起動の常駐サービスへ変更し
  （`yabai --start-service` で `RunAtLoad=true` の LaunchAgent を恒久設置）、
  `restore_supported_window_layout.sh` と `restore_obsidian_layout_with_wait.sh` の `cleanup_yabai` を
  no-op 化して都度削除を撤回（Python 側は元から `--leave-yabai-running` でガード済み）。`.yabairc` は
  `layout float` のみで yabai は自発的に窓を動かさないため常駐の害はないと判断（ユーザー合意）。
  全パイプラインの dry-run は exit 0、実行後も yabai 同一PID生存を確認。
  **実再起動での自動起動はユーザー未検証**（設定上 RunAtLoad で起動するはずだが終端試験は次回再起動）。
  これにより本ページ上部の旧安全方針「yabai は必要時だけ一時起動し終了後に削除」は撤回。
- 2026-06-23: 非Obsidian側の再発防止を実装。安定待ち、Firefox表記揺れ正規化、yabai JSONリトライ、
  復元後再照合、CLIP STUDIO系の位置移動対応を追加。単体テスト4件とドライランを確認。
- 2026-06-23: Mac再起動後の配置復元で、Obsidian 70窓は復元成功。非Obsidian側は
  再起動直後に全窓が `space_index: 0` / `has_ax_reference: false` と読まれ、Codex はスキップ、
  Firefox は `firefox` 表記揺れとタイトル差で不足扱い、Safari は復元対象になった。Safari/Firefoxの
  挙動異常はユーザー再起動で解消。非Obsidian側の安定待ち・アプリ名正規化が次の修正候補。
- 2026-06-21: 過去ログ全件の失敗パターン洗い出し。残存2件（yabai ready タイムアウト、個別窓
  移動エラーで全停止）を修正。yabai 起動をbash側で1回管理、Python側は `--leave-yabai-running`
  で使い回す方式に統一。
- 2026-06-21: Mac実再起動後の復元が、保存後に1窓のタイトルが変わると待機ゲートで全停止する
  バグを修正。待機判定を窓数ベースへ、Obsidian復元を `--allow-missing` 許容へ、最終判定を
  `move==0` 成功へ緩和。実機の `--apply` でSpace 4/5配置の復元に成功（取り残し1窓は既知制限）。
- 2026-06-16: 添付資料の固定配置案を目的と誤認していたが、現在状態そのものを再起動後に
  戻すことが目的だと訂正。
- 2026-06-16: Obsidian の各ポップアウトを厳密に識別する案は、初期完成条件には不要と整理。
- 2026-06-16: 全体実装計画を、1窓の非表示切替移動を確認する実現性パイロット優先へ縮小。
- 2026-06-16: `yabai` 再起動後の非表示デスクトップ窓が操作可能かを、実現性パイロットの
  必須条件へ追加。
- 2026-06-16: 実現性パイロットで、既存の `has-ax-reference: false` 窓を画面切替なしに操作できず、
  厳密な `yabai` 方式を不合格と判定。
- 2026-06-16: 初期パイロット対象を、Finder / Firefox / Eagle の少数窓ではなく、
  Obsidian `LLM Knowledge Base _01` のメインウィンドウと約60個のポップアウトウィンドウへ訂正。
- 2026-06-16: ユーザー確認により、復元目標を A 案(現在の各Obsidianポップアウトを、再起動後に
  元のSpace・元のモニター・元の位置へ戻す)として固定。
- 2026-06-16: 復元時に余分なObsidianポップアウトが開いていても、自動では閉じない方針を確認。
- 2026-06-16: 初期スクリプト `tools/obsidian_llm_kb_window_layout.py` を作成。読み取り・照合までは自動確認済み。
- 2026-06-16: 計画v2をレビューし、次の実装範囲をObsidian 4/5復元パイロットへ縮小。
  実移動前の退避保存と安全停止条件を初期スクリプトへ追加。
- 2026-06-16: ユーザー確認により、まずは現状のObsidian全体配置
  (Space 4: `LLM Knowledge Base _01` 65窓、Space 5: 別保管庫3窓)を再現対象として保存。
- 2026-06-16: ユーザーがObsidian全体を別Spaceへ移した状態から、`restore --apply` で
  Space 4:65窓 / Space 5:3窓へ戻す実移動検証に成功。再起動後検証は未実施。
- 2026-06-16: LLM経由なしで復元できるよう、Raycast Script Command
  `Obsidian配置を復元` を追加。Raycastからの手動起動はユーザーが検証済み。
- 2026-06-16: Mac再起動後用とObsidian再起動後用の入口を分離。いずれも現時点では
  Obsidian全窓のSpace 4/5復元を対象とし、Mac再起動後・Obsidian実再起動後の終端試験は未実施。
- 2026-06-16: 全アプリ全デスクトップの保存スクリプトを追加。現状100窓を約64KBで保存できた。
  全アプリ復元は未実装で、別フェーズの対象。
- 2026-06-16: Mac再起動後用のワンアクション復元枠を、Obsidian全窓 + Finder / Eagle / Firefox
  まで拡張。dry-runは成功、非Obsidian窓の実移動は未検証。
- 2026-06-16: 現在配置を保存し直す入口を追加。Obsidian専用保存と全アプリ保存の両方を更新する。
- 2026-06-16: ユーザーが不要なFinder 2窓を閉じたため、保存状態を更新。対応済み復元範囲は
  Obsidian 68窓 + Finder/Eagle/Firefox 10窓の合計78窓。
- 2026-06-16: Mac再起動後用のワンアクション復元範囲を、Obsidian 68窓 + 非Obsidian 26窓の
  合計94窓へ拡張。Finder 1窓をわざと別Spaceへ移した実移動検証に成功。
  CLIP STUDIO系3窓と、Mac実再起動後の終端試験は未検証。
- 2026-06-17: ユーザーが運用手順で検証し、現時点では不満なしと報告。問題が見つかった場合に
  個別報告を受けて修正する暫定運用段階へ移行。
- 2026-06-16: Mac実再起動後の初回復元で、すでに表示中のSpaceへ再フォーカスして失敗する実装バグを確認。
  `focus_space()` を修正し、dry-run再確認までは成功。修正後の本番復元は未実施。
- 2026-06-16: 非Obsidian側で保存済み窓が現在存在しない場合でも、照合できた窓だけ復元するよう修正。
  実移動後のdry-runで、Obsidianと非Obsidianの既存対応窓はいずれも `need_move_or_resize 0` を確認。

## 関連リンク

- [[current-projects-todo-clarifications-2026-06-15]]
- [[pureref-session-restore]]
- [[canvas-reference-tools]]
