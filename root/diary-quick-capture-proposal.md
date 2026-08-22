# 殴り書きメモ後継ツール 実装仕様(Codex 実装反映版 v4)

- 作成: Claude Code, 2026-06-01
- v2: Codex 1次レビュー(P1–P7)反映
- v3: 2026-06-01 Codex 2次レビュー(R2-#1〜#4)反映
- v4: 2026-06-06 Codex 3次レビュー(R3-1〜R3-5)反映 + Mac 側実装・自動パイロット実施
- 対象: 殴り書きメモ後継のクイックキャプチャ + 自己理解ジャーナリング基盤
- 取り込み先 KB: **LLM Brain Base_01**(自己理解 KB / SSD 上 / Obsidian Sync)
- 計画・実装の作業場 / 記録先: **LLM Knowledge Base _01**(この KB)
- ステータス: **2026-06-06 に開発元配布の元ショートカットを再取得し、保存先フォルダだけ変更する方法で復旧したため、本後継案は現在の採用運用ではない。Mac キャプチャ補助・段階1・段階2ツールは実装/自動試験済みのまま将来候補として保管する。**

---

## 0. サマリ

- アーキ: **1メモ=1不変ファイル**(追記しない)。旧ツール破損の主因(追記/存在チェック/プレースホルダ取得失敗)を構造的に除去。
- キャプチャ: iCloud `殴り書きメモ/` に新規ファイル作成のみ。本文のみ。
- 保存境界を厳格化(可変 derived と不変 raw を分離):
  - 段階1(無AI・launchd): 可変な日次集約を **iCloud derived `_daily/`** に生成。
  - 段階2(ユーザー側 `freeze-diary` + 手動 ingest): **raw を上書きしない固定処理**で `raw/diary/` へスナップショット → `voice` 判定 → `wiki/self`。
- **LLM は `raw/` を書かない**(Brain Base 規約: raw は LLM にとって read-only)。raw の生成はユーザー側ツールが行う。
- 追加 AI/トークンコストは段階1で 0(iCloud/Sync/バックアップ費は別枠)。
- 実装:
  - 本体: `tools/diary_quick_capture.py`
  - インストーラー: `tools/install_diary_quick_capture.sh`
  - Mac 入力UI: `tools/diary_capture_mac.sh`
  - Mac アプリ: xcord `diary-quick-capture/殴り書きメモ.app` / `日記をBrain Baseへ固定.app` / `殴り書きメモoutbox再送.app`
  - Shortcut 手順: `tools/diary-quick-capture-shortcut-setup.md`
  - LaunchAgent: `~/Library/LaunchAgents/com.takedayousuke.diary-quick-capture.plist`
  - 状態/操作入口: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/diary-quick-capture/`

## 1. 背景・目的

- 旧ツールは iOS 純正ショートカットの日付別メモ。**「日付ごとにページを作成する部分」が破損**。使用感も別物に。
- 後継の目的は単なるメモでなく **自己理解ジャーナリングの言語化促進**。思いついた瞬間に1行、最終的に Brain Base へ構造化。
- 必須要件: iPhone/iPad/Mac の3台同期 / シームレス最優先(理想はアプリを開かない) / 日付さえ合えば他要素不要 / LLM と共有可能。

## 2. ユーザー確定要件

- メモした日時が分かる / 同日のメモは同じ日付にまとまる / 日付が変われば新単位 / 3台同期・シームレス。

## 3. 採用アーキテクチャ: A「1メモ=1ファイル(追記しない)」

各キャプチャは**毎回まっさらな新規ファイルを作るだけ**。既存ファイルへの追記・存在チェック・フォルダ作成を一切行わない。旧ツールが壊れた工程そのものを廃止し、iCloud のプレースホルダ取得失敗・同期競合・存在チェック分岐(§6)を構造的に除去する。「同日まとめ読み」は下流集約(§5)で復元。

## 4. 確定仕様(キャプチャ層)

| 項目 | 内容 |
|---|---|
| キャプチャ動作 | 入力ボックス → **新規ファイル作成のみ** |
| 保存先 | `iCloud Drive/殴り書きメモ/`(**フラット**) |
| ファイル名 | **`yyyy-MM-dd_HHmmss_<UUID>.md`**(小文字 TR35 書式 + UUID) |
| 中身 | **本文のみ** |
| トリガー | iPhone/iPad = ホーム画面 +(可能なら)アクションボタン/ロック画面ウィジェット。Mac = メニューバー or ホットキー |
| ショートカット同期 | 本体は iCloud Sync で3台共有。**トリガー設定は端末ごとに手動** |
| 可用性前提 | `殴り書きメモ` を全端末 **Keep Downloaded** |

**書式と一意性**:
- Shortcuts のカスタム日付書式は **Unicode TR35 準拠**。`YYYY`(週基準年)/`DD`(年内通算日)は誤り。規範書式は小文字 **`yyyy-MM-dd_HHmmss`**。
- ミリ秒だけでは別端末同時作成の衝突が理論上残るため、ファイル名に **`<UUID>`** を付与して一意性を保証(UUID 衝突確率は実用上無視可能)。

例:
```
殴り書きメモ/
├── 2026-06-01_081532_3f2a…e9.md   本文「今日なんか気が重い。締切のせいかも」
├── 2026-06-01_133004_a17c…02.md   本文「ラフ描いてたら人の目を気にしてる自分に気づいた」
└── 2026-06-01_231210_91be…7d.md   本文「…」
```

## 5. 集約(2段階)— 保存境界・所有権・差分管理を厳格化

### 段階1: 機械的マージ(自動・無AI・無課金 / derived のみ)

- Mac の **launchd スクリプト**が、入力を **厳格なファイル名パターンに一致する通常ファイルだけ**に限定して読む。UUID は大小文字を受理して小文字へ正規化する。
  - UUID: `[0-9A-Fa-f]{8}-[0-9A-Fa-f]{4}-[0-9A-Fa-f]{4}-[0-9A-Fa-f]{4}-[0-9A-Fa-f]{12}`
  - 全体: `^\d{4}-\d{2}-\d{2}_\d{6}_<UUID>\.md$`
  - `_daily/`・ドットファイル・非 `.md`・シンボリックリンク等の非通常ファイルは除外。
- **決定的全再構築**: 対象日の全 capture を **ソートキー `(capture日時, UUID)`** で安定ソートして連結(R2-#3: 同秒メモの順序を一意化)。
- 出力先は **iCloud derived `殴り書きメモ/_daily/yyyy-MM-dd.md`**。**Brain Base には一切書かない**(raw 不変原則を侵さない)。Files アプリ等で閲覧可。
- **launchd 堅牢化**: `WatchPaths` は低遅延用に限定 + `RunAtLoad` + 300秒定期フルスキャン + `flock` の **多重起動ロック** + 更新直後ファイルは次回へ回す(debounce) + 出力が変わる場合のみ**同一ディレクトリ内 atomic replace** + **増分追記しない**。
- **出典追跡**: 表示は `H:MM`、各エントリに**秒・capture ID(UUID)・元ファイル名**を機械可読コメントで保持(任意で本文 SHA-256 を付け、内容改変検証に使う)。

出力例 `殴り書きメモ/_daily/2026-06-01.md`:
```markdown
2026/06/01📅

8:15
今日なんか気が重い。締切のせいかも
<!-- capture: 2026-06-01_081532 | id: 3f2a…e9 | src: 2026-06-01_081532_3f2a…e9.md -->

13:30
ラフ描いてたら人の目を気にしてる自分に気づいた
<!-- capture: 2026-06-01_133004 | id: a17c…02 | src: 2026-06-01_133004_a17c…02.md -->
```

### 段階2: `freeze-diary`(ユーザー側ツール)+ 手動 ingest

**LLM は `raw/` を書かない**(Brain Base 規約)。raw 固定はユーザー側ツール **`freeze-diary`** が担う。

`freeze-diary` の実装保証(R2-#1, R2-#2, R3-1〜R3-5):
1. **集約と同じ `flock` ロック**を取得してから走る(段階1 と排他、プロセス終了時に自動解放)。
2. `_daily` に依存せず、**capture ファイルから同期フルスキャン**する。既定では全日付を走査し、遅着メモを落とさない。
3. capture は最終更新から15秒以上経過し、読み取り前後の size・mtime・inode が不変な場合だけ採用する。空、読取失敗、dataless/未取得、変化中のファイルは skip + warning。
4. **immutable raw ファイル群を正本台帳**とする。起動時に raw 内の機械可読コメントから UUID + SHA-256 を再走査し、raw 外の manifest cache を毎回再構築する。
5. 差集合 `現在の capture UUID − raw 正本台帳の UUID` だけを固定する。固定後の capture 本文 SHA-256 が raw 台帳と異なる場合は変更警告を出し、二重固定しない。
6. raw publish は対象ディレクトリ内 temp へ書く → `fsync` → `link()` による atomic no-clobber publish。初回は `raw/diary/yyyy-MM-dd.md`、遅着分は `yyyy-MM-dd_addendumN.md`。同名 raw を上書きしない。
7. publish 後に raw 台帳を再走査してから manifest を atomic replace する。raw publish 後・manifest 更新前に停止しても、次回起動時に raw から自己修復し、二重固定しない。
8. `wiki/sources/` の `source_path` + `ingested:` を確認し、**未 ingest raw を毎回 `pending_ingest` として再提示**する。freeze 成功を ingest 成功とは扱わない。

manifest cache: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/diary-quick-capture/frozen-captures.json`。`raw/` 外にあり、raw 正本台帳から再構築可能。

**provenance(P6)**: 日記は本人一次情報なので原則 `voice: self` の source として扱い、`wiki/sources/` に summary → `wiki/self/` へ反映。**`wiki/self` 直書きはしない**。**外部文章(他人の投稿等)を貼り付けた日記は `voice: mixed` 判定**にする(本人メモ=self / 引用本文=他人)。

### コスト

- 段階1 の**追加 AI/トークンコストは 0**(Claude/API を呼ばない・オフライン可)。
- iCloud ストレージ・Obsidian Sync・バックアップ費は**別枠**。

## 6. 旧ツール破損の真因の見立て

| 想定原因 | 本アーキで防げるか |
|---|---|
| (a) 日付書式がロケール/設定依存でズレ | ✅ 固定 `yyyy-MM-dd` + 追記しないので無関係化 |
| (b) `存在チェック→作成`分岐の破損 | ✅ 分岐を廃止 |
| (c) iCloud 対象が未DL(プレースホルダ)で掴めず失敗 | ✅ 既存ファイルを読まない + Keep Downloaded |
| (d) 月またぎ・TZ で生成先ズレ | △ TZ固定(ローカル)。§8 で要確認 |
| (e) 同期遅延中の追記で版が競合し欠落 | ✅ 追記しないので競合面が消える |

## 7. 実装前パイロット(強制失敗試験を含む)

「新規ファイルのみ」は競合を減らすが**書き込み成功は保証しない**。Keep Downloaded は既存取得対策であり新規書き込みの保証ではない。**列挙ではなく強制試験**として検証する(R2-#4):

基本:
- [ ] オフライン保存後に同期される
- [ ] 3台同時保存で全件残る
- [ ] 同名で上書きされない(UUID で実質回避だが要確認)
- [ ] `.md` が `.txt` に化けない
- [ ] **保存成功後だけ**成功通知を出す

強制失敗試験(R2-#4):
- [ ] iCloud 利用不可・権限拒否を模した保存失敗を**意図的に発生**させ挙動確認
- [ ] 入力キャンセル・空入力
- [ ] 各トリガー、**特にロック画面状態**での起動
- [ ] 二重起動
- [ ] 失敗時の退避データからの**再送**

退避方式(R2-#4/R3-4): **クリップボード退避は不採用**。端末ローカル outbox に元日時・UUID・本文・SHA-256を先に保存し、再送でも同じ日時・UUIDを使う。保存済みファイルの本文一致を確認してから outbox を削除するため、応答だけ失敗したケースでも重複しない。

### 2026-06-06 パイロット結果

自動/Macで実施済み:
- [x] Mac から iCloud production 保存先へ本文のみ・UUID名で保存し、本文一致を確認
- [x] 保存直後は debounce で除外、15秒後は日次 derived を生成
- [x] derived に日時・UUID・元ファイル名・SHA-256を保持
- [x] 保存先利用不可を模擬し、outbox 残存 → 同じ UUID で再送 → 成功後削除
- [x] 空入力を拒否
- [x] 同秒の並列2起動で異なるUUIDの2ファイルを保存
- [x] 共有 `flock` の排他待ちを確認
- [x] raw publish 後・manifest消失を模擬し、rawから復旧して二重addendumを作らないことを自動試験
- [x] 遅着メモのaddendum化、固定後本文変更のSHA-256警告、未ingest raw再提示を自動試験

実機で未実施:
- [ ] iPhone / iPad Shortcut 本体作成
- [ ] オフライン保存後のiCloud同期
- [ ] iPhone / iPad / Mac 3台同時保存
- [ ] ロック画面・アクションボタン等の端末トリガー
- [ ] iOS/iPadOS 上の権限拒否・outbox再送

## 8. 残課題・要確認

- TZ/深夜0時跨ぎ: ファイル名はキャプチャ端末ローカル時刻で固定。海外移動時の扱いは日記用途ゆえローカル可とするか要確認。
- バックアップ: iCloud は同期であって履歴付きバックアップではない。Mac 側で `殴り書きメモ` と `raw/diary` を Time Machine / rsync 対象に。
- 集約鮮度: 日次ページは Mac 集約後に最新化。書いた直後のモバイルでは未集約(役割分担として合意済み)。

## 9. 検討した代替案(不採用)

| 案 | 不採用理由 |
|---|---|
| B: Drafts アプリ | 「アプリを開かない」要件に反する。サードパーティ+一部 Pro。将来 Obsidian 直結時に再検討余地。 |
| C: iCloud 追記の堅牢化 | 追記式の残存競合(サイレント欠落)を受容することになり自己理解アーカイブに不適。 |
| 日付フォルダ方式 | 「日付ごとに入れ物を作る」工程が復活し破損箇所の再来リスク。 |
| 集約を Brain Base 内 staging に置く案 | BB に新トップ階層追加=構造変更が必要。iCloud derived で十分。 |

## 10. Codex 指摘への対応表

### 1次レビュー(v2 で対応)
| # | 指摘 | 対応 |
|---|---|---|
| P1 | raw 毎回再生成が「raw 不変」と衝突 | 可変集約を iCloud `_daily/` へ。raw 固定は freeze 時のみ。**§5** |
| P2 | ファイル名が一意でない / TR35 書式誤り | `yyyy-MM-dd_HHmmss_<UUID>.md`(小文字 TR35)。**§4** |
| P3 | 新規のみでも書き込み失敗は残る | 実装前パイロットで検証。**§7** |
| P4 | WatchPaths 単独では集約不正確 | RunAtLoad + 定期フルスキャン + ロック + 決定的全再構築 + atomic replace。**§5** |
| P5 | 集約で出典追跡を落としすぎ | 秒・capture ID・元ファイル名を機械可読コメントで保持。**§5** |
| P6 | 段階2が voice provenance 未対応 | `voice: self` source → wiki/self(直書きしない)。**§5** |
| P7 | Brain Base に wiki/builds 無し | 記録は KB `wiki/builds/`。**§11** |

### 2次レビュー(v3 で対応)
| # | 指摘 | 対応 |
|---|---|---|
| R2-#1 | addendum の差分抽出が未定義(二重取り込み/欠落) | manifest(固定済み UUID 集合)で差集合を取り、成功後に manifest 更新。**§5 段階2** |
| R2-#2 | raw 固定の所有者と no-overwrite 未確定 | ユーザー側 `freeze-diary` に分離。同名 raw は必ず失敗・上書き禁止、ロック共有、capture からフルスキャン。**§5 段階2** |
| R2-#3 | 決定的再構築のソートキー不完全 | ソートキー `(capture日時, UUID)`、入力を厳格パターンの通常ファイルに限定。**§5 段階1** |
| R2-#4 | パイロットが失敗を強制試験していない | 強制失敗試験を追加。退避はクリップボードでなく outbox ファイル。**§7** |

### 3次レビュー(v4 で実装)
| # | 指摘 | 実装 |
|---|---|---|
| R3-1 | raw/manifest間クラッシュで停止・二重固定 | rawを正本台帳化。起動時再走査、temp+fsync+link no-clobber publish、publish後再走査、manifest atomic更新。**§5 段階2** |
| R3-2 | iCloud同期途中を固定する危険 | 15秒debounce、size/mtime/inode安定確認、空/未取得/読取失敗skip、UUID+SHA-256台帳。**§5 段階2** |
| R3-3 | frozenとingestedの混同 | wiki sourceの`source_path`+`ingested:`から未ingest rawを毎回再提示。**§5 段階2** |
| R3-4 | outbox再送の重複 | 元日時・UUID・本文・SHAを保持し、同じUUIDで再送、成功確認後のみ削除。**§7** |
| R3-5 | UUID/lockが抽象的 | canonical UUID regexを明記・小文字正規化、共有`flock`実装。**§5** |

## 11. 実装状況

- [x] Mac キャプチャ補助(`diary-capture` / `diary-capture-mac`) + ローカルoutbox
- [x] Mac用 `殴り書きメモ.app`(Dock / Spotlight 起動)
- [x] iCloud 保存先 `殴り書きメモ/` 作成
- [x] launchd 集約スクリプト + LaunchAgent設置/起動
- [x] 再セットアップ用インストーラー
- [x] `freeze-diary` ユーザー側ツール + raw正本台帳 + raw外manifest cache
- [x] 自動テスト7件 + Macパイロット + 強制失敗/再送試験
- [x] 正式記録: KB `wiki/builds/diary-quick-capture.md`
- [ ] iPhone/iPad Shortcutを `tools/diary-quick-capture-shortcut-setup.md` に従って実機作成
- [ ] 各端末のトリガー設定・Keep Downloaded・3台実機パイロット
- [ ] バックアップ経路(Time Machine / rsync)
- [ ] 本人が `freeze-diary` を初回実行し、提示された `pending_ingest` を Brain Base で `/llm-wiki ingest` する
