---
type: build
name: KeyClack
aliases: [keyclack, typing-sound-app]
tags: [macos-app, swift, audio, menubar, klakk-replacement]
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-11
---

# KeyClack

## 現在の統合見解

KeyClack は klakk（800円の打鍵音アプリ）を置き換える自作 macOS メニューバー常駐アプリ。
klakk が持つ「無線イヤホンの切断→再接続で有線スピーカーへの出力が復帰しない」バグを
CoreAudio デバイス固定＋自動復帰で解消。無料・ローカル・非配布。

実装場所: `~/dev/keyclack/`（全コード `Sources/main.swift` 単一ファイル）
正式利用パス: `~/Applications/KeyClack.app`

## 機能（v0.5.2 — 2026-07-11）

- **15 サウンドパック**: Mechvibes 由来。multi 形式（個別 wav）と single 形式（1 wav + オフセット定義）の両方に対応。Topre Purple Hybrid（HHKB 系・スコスコ音）を v0.3 で追加
- **キーごとの音の対応**: config.json の対応表に従い、物理キーコードごとに正しい録音を再生。Return は Return の音、Space は Space の音。single/multi 両形式で対応
- **無音トリミング**: single 形式の音区間の先頭にある無音部分を自動除去。ゴミ区間（ほぼ無音の録音）もフィルタで除外
- **出力先の固定**: CoreAudio デバイス UID で出力先を記憶。システム既定が変わっても追従しない
- **自動復帰**: CoreAudio デバイス一覧・既定出力の変化を検知し、debounce（0.3秒）付きで音声エンジンを再構築して固定先へ繋ぎ直す（v0.5.1 で AVAudioEngineConfigurationChange の監視は削除。理由は下記 v0.5.1 根拠参照）
- **キーリピート抑制**: CGEvent の auto-repeat フラグで長押し連打を除外（1 押下 = 1 音）
- **修飾キーフィルタ**: Cmd / Ctrl / Option 付きイベントは無音（ショートカット・左手デバイスのノブ操作対応）
- **音量 2 段構え**: 全体のマスター音量（5 段階、デフォルト「大」1.5 倍）＋ パック別音量（5 段階、デフォルト「中」1.0 倍）。パック切替で音量も連動
- **アプリ別ミュート**: 最前面アプリの bundleIdentifier でミュート判定。メニューから追加/解除
- **前面アプリ無音状態の可視化と解除**: 最前面アプリがミュート対象なら、メニューに警告行と `前面アプリの無音を解除` を表示して、その場で解除できる
- **メニューバー UI**: グループ化されたパック一覧（日本語 vibe 表示）、音量、無音アプリ、出力先、リスタート
- **入力監視の限定再試行**: 起動直後に `CGEventTap` 作成が失敗したとき、2.5 秒間隔で最大 3 回だけ再試行。成功しなければ警告表示のまま停止
- **起動ログの強化**: `resourceURL`、サウンドパック数、選択パック、固定デバイス UID、tap 作成成否、音声エンジン start 成否を `NSLog` に出す
- **固定先不在時の既定出力フォールバック**: 保存済みデバイス UID が一時的に消えていても値は保持し、起動時は既定出力でしのいで、機器復帰後の再接続を待つ

## 技術構成

- Swift 単一ファイル（`swiftc` 直コンパイル、Package.swift 不要）
- `CGEventTap`（listen-only）でキー検知。Input Monitoring 許可が必要
- `AVAudioEngine` + `AVAudioPlayerNode` プール（8 ノード）で低遅延再生
- `kAudioOutputUnitProperty_CurrentDevice` でデバイス固定
- Ad-hoc コード署名（`codesign --force --deep --sign -`）。ビルドのたびに署名が変わるため入力監視の再設定が必要
- 配置補助: `build.sh` は `build/KeyClack.app` を作るだけ。`deploy.sh` が `~/Applications/KeyClack.app` へコピーして再署名する

## 運用方針

- 自動起動は LaunchAgent ではなく macOS のログイン項目を使う。理由は、状態が見えやすく、普段使いでの確認・停止・削除が軽いから
- ログイン項目には `KeyClack` だけを残し、`Klakk` と `Mechvibes` は外す
- 旧 `klakk.app` は依存がないことを確認できたので撤去対象。音源は `~/dev/keyclack/Resources/` 側が正本
- `deploy.sh` 実行後に入力監視が外れた場合は、システム設定の入力監視から KeyClack を一度削除し、`~/Applications/KeyClack.app` を再追加する
- 「音が鳴らない」ときは、まず最前面アプリが無音対象に入っていないかを確認する。2026-07-11 の実障害では、起動自体は正常で、`mutedApps` に残っていた `com.google.Chrome` が真因だった

## 再許可手順

1. `~/dev/keyclack/deploy.sh` を実行する
2. KeyClack の入力監視が効かない場合、システム設定 → プライバシーとセキュリティ → 入力監視 を開く
3. 既存の KeyClack が残っていれば削除する
4. `~/Applications/KeyClack.app` を追加し直す
5. KeyClack を起動し、メニューバー表示と打鍵音を確認する

## 根拠

- klakk のバグ: 武田さんが長期間遭遇していた実害。CoreAudio の構成変化イベントへの対応不足が原因
- 音源: klakk バンドルの Mechvibes パック（GPL）+ Mechvibes GitHub リポジトリの Topre パック
- v0.2 MVP: 武田さん実機検証済み（音・パック切替・デバイス切替すべて OK）
- v0.3: 武田さんの 4 要望（音量・キーリピート・アプリミュート）+ HHKB 追加要望を反映。武田さん検証済み
- v0.4: Topre 音ズレ修正（無音トリミング + ゴミ区間除外）、全パックでキーコード→録音の正式対応
- v0.5（2026-07-01）: 実機クラッシュ 2 件を `~/Library/Logs/DiagnosticReports/` で確認し修正。
  原因は `SoundEngine.rebuild()` で音声エンジン起動直後に `AVAudioPlayerNode.play()` を
  一斉呼び出ししていたこと。無線イヤホン等でハードウェア側の接続確立が
  `engine.start()` の返答より遅れることがあり、その間に `play()` を呼ぶと
  AVFAudio が例外を投げてアプリごと落ちる（`Abort trap: 6`、Swift の try/catch では
  捕捉不能な Objective-C 例外）。修正: エンジン起動後に 0.15 秒待ってから、その時点で
  まだ有効なエンジンか再確認して再生開始するよう変更
- v0.5.1（2026-07-01）: v0.5 の直後、武田さんの実機検証で「音が全く出ない」と報告。
  調査の結果、**もっと根本的な既存バグ**が判明: `rebuild()` が音声エンジンを起動する
  こと自体が `AVAudioEngineConfigurationChange` 通知を発生させ、それをアプリが
  「外部で音声環境が変わった」と誤解してまた `rebuild()` を呼ぶ、という**自己発火の
  無限ループ**が 0.4 秒間隔で常時回っていた（診断ログで実測確認）。v0.2 の頃から
  存在していたと見られるが、以前は再生開始が同期・即時だったため毎サイクルで
  一瞬鳴っており「不安定・たまにラグ」程度で気づかれなかった。v0.5 で再生開始に
  0.15 秒の遅延を挟んだことで、破棄されるタイミングと重なりやすくなり無音化した。
  「動作が不安定」「クラッシュ」「再開直後のラグ」はすべてこの単一のループが
  原因だったと考えられる。修正: 自己発火の原因だった `AVAudioEngineConfigurationChange`
  の監視を削除。実際の外部機器変化は別に張っている CoreAudio デバイス一覧・既定出力の
  監視（自己発火しないことを診断ログで確認済み）で引き続き検知する。
  **武田さん実機確認済み（2026-07-01、「動作良好です」）**
- v0.5.2（2026-07-11）: 安定化・復元・再発防止の運用整理。`~/Applications/KeyClack.app` を正式利用パスに固定し、LaunchAgent ではなくログイン項目運用に統一。アプリ側には `CGEventTap` の限定再試行と起動ログ強化を追加
- 2026-07-11 実運用での無音再発: 起動ログを追加した状態で Terminal から直接起動し、`resourceURL` 正常、15 パック検出、保存デバイス UID の固定成功、音声エンジン start 成功、event tap 有効化成功を確認。さらに一時的な debug ログで keydown が受理されていることも確認したため、「起動失敗」「権限失敗」「音声エンジン不起動」は否定できた。`defaults read ~/Library/Preferences/local.takeda.keyclack.plist` を確認すると `mutedApps = ("com.google.Chrome")` が残っており、Chrome が前面の間だけアプリ仕様どおり無音化していたことが真因と判明。`mutedApps` を空へ戻し、メニューに前面アプリ無音状態の可視化・解除を追加した後、武田さんの実機確認で復旧

## 矛盾・未確定

- デバイス切替の長期耐久性は時間経過でしか確認できない（v0.2 検証時点で「いい感じ」）
- `player.volume > 1.0` はデジタルクリッピングの可能性あり（打鍵音は短波形なので実害は薄い見込み）
- 修飾キーフィルタはノブのキー割当が Cmd+Shift+; / Cmd+Shift+- であることを前提（武田さんの記憶ベース）。外れた場合はアプリ別ミュートが控え
- v0.5 直後に「音が出ない」と報告された際、武田さんは入力監視をオフ→オン切替のみで
  リストからの削除→再追加をしていなかった、との申告あり（2026-07-01）。ただし無音の
  実測ログは Terminal から直接起動した診断で取得しており、入力監視の許可状態に関係なく
  再現する `AVAudioEngineConfigurationChange` 自己発火ループが原因と特定できている。
  削除→再追加の不足が症状に追加で影響した可能性はゼロではないが、未検証・確認不能
  （ループ修正後に武田さんが正しい手順で再設定して「動作良好」に至ったため、両者を
  切り分ける再試験はしていない）
- アプリ別ミュートは前面アプリ単位で完全無音になるため、ユーザー体感としては「アプリが壊れた」と区別しづらい。今回のメニュー可視化を入れる前は、`mutedApps` の中身を設定ファイルか defaults で見ないと即断できなかった

## 変遷

- v0.5 で「エンジン起動直後の play() 呼び出し」が実機クラッシュ 2 件の原因と特定し修正
- v0.5 適用直後に「音が全く出ない」の新規報告 → 別の既存バグ（AVAudioEngineConfigurationChange
  自己発火ループ、v0.2 から潜在）が真因と判明し v0.5.1 で修正
- v0.5.1 適用後、武田さん実機確認済み「動作良好です」（2026-07-01）
- v0.5.2 で正式配置・ログイン項目運用・入力監視再試行・起動ログを追加（2026-07-11）
- 2026-07-11 に「音が鳴らない」再報告。起動ログと一時 debug で起動系は正常と切り分け、`mutedApps` に残っていた `com.google.Chrome` を真因と特定
- 同日、`mutedApps` を空へ戻して復旧し、前面アプリ無音状態をメニューに出す改修を追加。武田さん再確認で動作中

## 2026-07-11 障害対応記録

1. 症状: 武田さんから「音が鳴らない」と報告
2. 切り分け: 起動ログで resource、サウンドパック、固定デバイス、engine start、event tap の正常を確認。一時 debug で keydown も受理していることを確認
3. 真因: `~/Library/Preferences/local.takeda.keyclack.plist` の `mutedApps` に `com.google.Chrome` が残っており、Chrome 前面時だけ仕様どおり無音化していた
4. 復旧: `mutedApps` を空に戻して再起動
5. 再発防止: メニューに「前面アプリが無音対象である」警告と解除アクションを追加。以後は「鳴らない」時の初手確認項目として front app mute を固定

## 関連リンク

- メモリ `build_mini_keyboard_macropad` — 左手デバイス（ノブのキー割当が要望#4 の背景）。wiki 内に専用ページは無く、詳細はメモリ側に記録
