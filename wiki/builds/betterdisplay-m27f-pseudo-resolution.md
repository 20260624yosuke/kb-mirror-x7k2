---
type: build
title: BetterDisplay による HP M27f 擬似解像度アップ（2560×1440 化）
created: 2026-06-19
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-06-24
related:
  - "[[desk-display-setup-2026-06]]"
  - "[[takeda-yohsuke]]"
tags: [display, betterdisplay, resolution, eagle, m27f, mac-mini-m1]
---

# BetterDisplay による HP M27f 擬似解像度アップ（2560×1440 化）

## 目的

HP M27f（27型 FHD / 1920×1080）で Eagle のサイドバー（フォルダツリー＋X メタデータパネル）を
両方開くと画像表示エリアが狭くなる問題を、物理モニター買い替えなしで改善する。
M27f は資料閲覧専用で創作自体には使わないため、擬似解像度で文字が多少ぼやけても許容できる。

## 構成

- **PC**: M1 Mac mini
- **対象ディスプレイ**: HP M27f FHD（27型、物理 1920×1080、VESA 非対応）
- **液タブ**: Kamvas 24plus（2560×1440、変更なし）
- **ツール**: BetterDisplay 4.3.4（無料版）— Homebrew でインストール（`brew install --cask betterdisplay`）

## 仕組み

BetterDisplay で **仮想スクリーン**（M27f-HiRes）を作り、HP M27f にミラーリングする。
macOS は仮想スクリーンの解像度（2560×1440）で描画し、物理 1080p パネルに縮小表示する。
結果として 1920×1080 → 2560×1440 分の作業スペースが得られる。

### 設定手順（CLI）

```bash
# 1. BetterDisplay 起動
open -a BetterDisplay
sleep 3

# 2. 非HiDPI の仮想スクリーン作成
betterdisplaycli create -type=VirtualScreen -aspectWidth=16 -aspectHeight=9 \
  -virtualScreenName="M27f-HiRes" -virtualScreenHiDPI=off

# 3. 接続
betterdisplaycli set -name="M27f-HiRes" -connected=on
sleep 3

# 4. tagID 確認（毎回変わる可能性あり）
betterdisplaycli get -name="M27f-HiRes" -identifiers
# → tagID (Display) の値を確認（例: 8）
# HP M27f の tagID も確認（例: 3）
betterdisplaycli get -name="HP M27f FHD" -identifiers

# 5. 解像度設定 + ミラーリング（tagID は実際の値に置き換え）
betterdisplaycli set -tagID=<Display tagID> -resolution=2560x1440
betterdisplaycli set -tagID=<Display tagID> -mirror=on -targetTagID=<HP M27f tagID>

# 6. HiDPI 有効化（文字のにじみ軽減）
betterdisplaycli set -tagID=<VirtualScreen tagID> -virtualScreenHiDPI=on
betterdisplaycli set -tagID=<Display tagID> -resolution=2560x1440 -hiDPI=on
```

### 最終状態

| 項目 | 値 |
|---|---|
| 仮想スクリーン名 | M27f-HiRes |
| 内部描画解像度 | 5120×2880（HiDPI） |
| 論理解像度（UI） | 2560×1440 |
| 物理パネル出力 | 1920×1080（縮小表示） |
| ミラー方式 | Hardware Mirror |
| フォントスムージング | 強（`AppleFontSmoothing = 3`） |

## 結果

- **Eagle の UI**: サイドバー両開きでも画像エリアに十分な余裕。情報量・視認性が向上。武田さん確認済み
- **文字のにじみ**: HiDPI + フォントスムージング強で改善したが、ネイティブ 1080p と比べると多少ぼやける。
  これは物理パネルの限界であり、ソフトウェアでの改善はここが天井
- **Space（仮想デスクトップ）**: 初回設定時、HiDPI の on/off 切り替えとメインディスプレイ変更で
  Space が再編され操作不能になった。2回目は途中切り替えを避けて一発設定したところ Space は安定

## 注意事項

- **BetterDisplay を終了すると元の 1920×1080 に戻る**。仮想スクリーンは BetterDisplay 内に保存されるため、次回起動時に復元される可能性がある
- **メインディスプレイ変更は避ける**。Space の再編が起き、全ウィンドウが見えなくなるリスクがある
- **HiDPI の on/off を途中で切り替えない**。同様に Space 再編のリスクがある
- **通知バナー**: 仮想スクリーンを物理 M27f にミラーリングしているため、macOS の
  「ディスプレイをミラーリングまたは共有中の通知を許可」がオフだと、Codex / Claude Code
  などの LLM 回答完了通知が通知センターには残るが、画面右上のバナーとして出ない。
- **費用**: 仮想スクリーン + ミラーは無料版の機能。`stream` / `PiP` などは Pro（有料）だが今回は不使用
- Eagle 専用で解像度を変えることはできない（macOS はディスプレイ単位）。文字を読む作業は Kamvas 側で行う運用が現実的

## 2026-06-24 通知バナー非表示の調査

### 症状

Codex や Claude Code の回答生成完了通知が、通知センター（通知の履歴一覧）には残る可能性があるが、
デスクトップ右上のバナー（画面に一時表示される通知小窓）として見えない状態になった。
ユーザーの仮説は、M27f の解像度変更と通知表示がぶつかっている、というものだった。

### 確認した構成

- `M27f-HiRes` が主ディスプレイとして扱われていた。
- `HP M27f FHD` は `M27f-HiRes` とミラーリング（同じ画面を別ディスプレイに出す設定）されていた。
- `Kamvas 24plus` は左側の別ディスプレイとして接続されていた。
- BetterDisplay の設定上、`M27f-HiRes` は仮想スクリーン（物理モニターではなく、ソフトウェアが作る画面）で、
  物理 `HP M27f FHD` にリンクされていた。
- macOS 上では、物理 M27f が FHD（1920×1080）にもかかわらず、HiDPI（高精細表示用の拡大縮小）で
  2560×1440 UI / 5120×2880 内部描画として扱われていた。

### 原因

テスト通知を `osascript` で出したところ、画面上のバナーは見えず、通知センターには残った。
macOS のログでは、通知自体は `Presenting ... as banner` としてバナー表示候補に入った後、
`muted by display state (displayMirrored)`、`resolutionReason: display mirrored` と記録されていた。

結論として、通知が画面外へ飛んでいたのではなく、macOS が「ディスプレイをミラーリング中」と判断して、
バナー表示と通知音を意図的に止めていた。

### 対応

ユーザーが macOS の通知設定で「ディスプレイをミラーリングまたは共有中の通知を許可」に相当する設定をオンにした。

### 検証結果

- 設定変更後のテスト通知で、画面右上にバナーが表示された。
- 音量設定を戻した後の再テストで、通知音も鳴った。
- よって、今回の通知見落としの直接原因は、M27f の BetterDisplay 仮想スクリーン運用そのものではなく、
  その運用が macOS から「ミラーリング中」と扱われ、通知バナーが抑止されていたこと。

### 残る注意

この設定をオンにすると、画面共有やミラーリング中にも通知内容が見える可能性がある。
LLM の回答完了通知に気づける一方で、他人に画面を見せる場面では通知内容の露出に注意する。

## 復旧手順

画面が見えなくなった・Space が飛んだ場合:

```bash
# 安定優先の復旧。仮想 HiDPI とミラーを外し、物理 1080p へ戻す。
betterdisplaycli set -name="HP M27f FHD" -resolution=1920x1080 -hiDPI=off
betterdisplaycli set -name="M27f-HiRes" -connected=off
osascript -e 'tell application "BetterDisplay" to quit' || pkill -f BetterDisplay
```

## スリープ復帰でウィンドウ配置が崩れる問題（2026-07-27 調査）

### 症状

数日前から、スリープ復帰のたびに 27 インチ（HP M27f）と Kamvas 24plus の両方で
ソフトのウィンドウ配置がぐちゃぐちゃになる（武田さん報告）。

### 観測（WindowServer ログ、source-backed）

- **仮想スクリーンが復帰のたびに新規作成されている**。3 日間で `Virtual-2` から `Virtual-13` まで
  12 個。いずれも「復帰の 30〜40 秒後」に、前回とは別の display id で生成される。
  つまり同じ画面が戻るのではなく、macOS にとって毎回「初対面の新しい画面」が現れている。
- 加えて **HP M27f（display id 2）自体も復帰のたびに 1 秒前後 out → in している**
  （例 2026-07-27 18:12:07.114 out → 18:12:07.567 in）。
- macOS は画面が消えると、その画面上のウィンドウを残った画面へ強制退避させる。退避したウィンドウは
  画面が戻っても元に戻らない。これが配置崩れの機序。

### 除外できた容疑者

- **Display Maid**（常駐中）: `autoRestoreLaunch = 0` / `autoRestoreAppLaunch = 0` のため自動では動かない。
- **yabai**: `~/.yabairc` は `layout float` のみ。自動整列しない。

### 対応（2026-07-27）

BetterDisplay は 4.3.4 で動作中だったが、**4.3.5 が 2026-07-24 07:58 にダウンロード済みのまま
3 日間適用されずに保留**されていた（Sparkle が終了待ち）。時期が症状の発生と一致するため、
アプリを終了 → 更新適用 → 再起動した。現在 4.3.5（ビルド 50105、旧 50021）。
再起動後、仮想スクリーンとミラー構成は自動で復帰した。

> [!warning] 未検証
> 修正されたかどうかは **スリープ復帰を 1 回行うまで判定できない**。判定方法は
> WindowServer ログに新しい `Virtual-14` が生成されるかどうか。増えていれば未解決。

### 2026-08-17 追記: 4.3.5 では止まっていない（実測・調査は保留）

上の「要スリープ復帰試験」への回答。WindowServer ログを見たところ、
**2026-08-17 10:46:15 に `Virtual-19` が生成されていた**（2026-07-27 時点は `Virtual-13`）。
更新後も 6 枚増えており、**復帰のたびの仮想スクリーン再生成は 4.3.5 でも継続している**。
同時刻に `display 1 has become stuck after 10.00735s of unreadiness (554 failures)` も記録。

武田さんの判断（2026-08-17）: 「直感的に OS の不具合と感じているので、まずは保留。
どうしても気になったら調査の要望を出す」。したがって**この件の調査・対策は行っていない**。
上記は調査ではなく、別件（復元スクリプトの不具合）を調べる過程で観測できた事実の記録。

## 未確定

- 上記スリープ復帰時の仮想スクリーン再生成が 4.3.5 で止まるか（要スリープ復帰試験）
- HP M27f の復帰時 1 秒の抜き差しの原因（ケーブル / ハブ側の可能性。BetterDisplay とは別軸）
- BetterDisplay の起動時自動復元の挙動（次回 Mac 再起動時にどうなるか未検証）
- フォントスムージング変更の効果（アプリ再起動 or 再ログイン後に反映。未確認）
- 根本解決は物理モニターの 1440p 以上への買い替え（[[desk-display-setup-2026-06]] フェーズ1）

## 関連リンク

- [[desk-display-setup-2026-06]] — デスク環境全体の設計詰め
- [[takeda-yohsuke]]
