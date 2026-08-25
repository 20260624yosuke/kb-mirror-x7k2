---
type: build
sources: []
status: active
confidence: high
evidence_level: user-stated
last_reviewed: 2026-08-25
---

# Raycast File Search スコープ拡張(SSD 全体 + HDD_02)

## 目的・背景

Raycast のファイル検索が弱い(既定ではホームフォルダ + `/Applications` のみが索引対象)ため、
①LLM 成果物の確認、②Blender ファイル(`.blend`)の検索〜展開までが億劫だった。
「レイキャストからファイルパスやファイル名で検索し、展開までできるようにしたい。日常使いなので
スコープはすべて」という依頼から開始。

## 調査で確認した事実(2026-08-25 実測)

- **Spotlight は全ボリュームで有効**(`mdutil -sa`)。外付 SSD は `.blend` 998 件を mdfind で 2.4 秒
  検出、KB 内 `.md` 2018 件を 0.35 秒検出 → OS 側の索引は既に健康だった。
- **Raycast 2.0.5** 導入済み。File Search v2 は Search Scopes(検索対象フォルダ追加)、パス絞り込み
  (`07_3D資料/ helen` 形式)、拡張子フィルタ(`*.blend`)、Open With・Quick Look アクションに対応。
- **Blender 本体は `/Applications` になく** `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app`
  に配置(SSD 依存アプリ)。初回調査で「未導入」と誤判定したのはここを見ていないため。
- CLI(`defaults`)からスコープを書き換える正規手段は無い(`fileSearch_fileSearchScope =
  kMDQueryScopeComputer` は旧エンジンの遺物)。非公式キー操作は壊れうるため不使用。

## 決定事項(武田さん承認)

1. 設定変更は **Raycast 設定画面での手作業**(LLM が手順案内・進捗監視・検収を担当)。GUI 自動操作・
   plist 直編集は不採用。
2. スコープ = **SSD_M.2 全体 + HDD_02**。バックアップ HDD 2 本(`HDD_バックアップ` /
   `HDD_バックアップ_macbookpro`)は同名重複ヒットによるノイズ・索引肥大・スピンアップの観点から
   意図的に除外(安全性の問題ではなく検索精度の問題であることを説明のうえ選択)。
3. Blender は既存(SSD 内配置)を使い新規導入しない。

## 実施内容

- 武田さんが Settings → File Search → Search Scopes へ上記 2 フォルダを追加。
- 索引構築は LLM 側が監視(Raycast プロセス CPU 合計値 + `~/Library/Application Support/
  com.raycast.macos/index` の du 推移を 30 秒間隔サンプリング)。設定画面には進捗 UI が出ないため、
  CPU 落下 + サイズ安定を完了条件とした。

## 確認結果

- 索引構築完了: CPU 180〜250% → 0% 台へ落下、index ディレクトリ 231MB で安定(構築前比 +63MB)。
- **`.blend` を Raycast から検索してそのまま開けた**ことを武田さんが実機確認
  (「blender ファイルパスから直で開いたから、だいぶ使用感いい」)。

## 未確認事項

- **md → Obsidian 表示**: KB vault 内 md をヒットさせ ⌘K → Open With → Obsidian で開けるはずだが
  実機未試行。vault 外 md は Obsidian の仕様上開けない(Quck Look 等で表示)。
- HDD_02 側ファイルの検索ヒットは未試験。

## 運用メモ

- **外付 SSD を抜いている間は `.blend` も Blender 本体も見えなくなる**(両方 SSD 内配置のため)。
- バックアップ HDD も入れたくなったら同じ Search Scopes に追加するだけ。
- 戻し方: Settings → File Search → Search Scopes で該当フォルダを削除(ファイル無変更・可逆)。
- 索引の進捗・完了は設定画面に出ない。確認したいときは Activity Monitor で Raycast の CPU を見るか、
  上記 du 監視を使う。

## 使わなかったもの・落とした情報

- **カスタム Raycast 拡張(mdfind/fd ラップ)**: 不採用。標準機能で目的を達成できたため開発コストを
  回避。影響: パスの途中一致検索・成果物 Inbox 一覧の Raycast 内統合表示はできない(名前検索 +
  フォルダプレフィックス絞り込みのみ)。必要になったら拡張を新規作成すれば戻せる。
- **GUI 自動操作による設定**: 不採用。アクセシビリティ権限付与の手間と誤操作リスクに対し、手作業は
  クリック数回しかないため見合わない。影響: なし(手作業で完遂済み)。
- **バックアップ HDD 2 本の索引化**: 意図的に外した。影響: バックアップ内の古い版は Raycast から
  検索できない(Spotlight の Finder 検索や mdfind なら従来どおり可能)。後から Search Scopes 追加で戻せる。

## 関連リンク

- [[deliverable-inbox]] — 成果物確認の正規導線。本設定により成果物ファイル自体も Raycast から引けるようになった
- [[obsidian-direct-open-entrypoint]] — md を Obsidian で開く既存入口。md→Obsidian 表示の補完候補
- [[multi-site-image-search]], [[google-tasks-quickadd]], [[window-layout-restore]] — 同じく Raycast を入口にする既存ワークフロー群
