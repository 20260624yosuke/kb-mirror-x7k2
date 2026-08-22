---
type: build
name: 複数サイト画像検索（一括）
aliases: [multi-site-image-search, 画像検索ショートカット]
tags: [macOS, raycast, chrome, applescript, image-search, reference, automation]
sources: []
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-06-15
---

# 複数サイト画像検索（一括）

## 現在の統合見解

絵の資料を探すとき、**検索ワードを 1 回入力するだけで、複数のサイトに同じワードを流して
一括検索する**ための Mac 用ツール。入力経路は **Raycast の Script Command → bash →
Chrome の新規ウィンドウに 5 タブをまとめて開く**。

- 元は Apple ショートカットで作っていたが挙動が不安定だったため、[[clip2md]] /
  [[google-tasks-quickadd]] と同じ **Raycast 起動系**に寄せ、Apple ショートカットの層を外した。
- v1 の対象サイトは **Pinterest / X / Instagram / danbooru / Google 画像** の 5 つ。
  まずこの構成で使用感を見て、サイトや開き方をチューニングする方針(2026-06-14 武田さん決定)。
- 出口は **毎回あたらしい Chrome ウィンドウ**に 5 タブをまとめて開く(検索セッションが
  散らからない)。Chrome 操作には AppleScript を使う。
- 正本は `tools/`、実体は `~/.config/raycast-scripts/`(外付け SSD を外していても動く)。

## アーキテクチャ

```text
Raycast（コマンド名を検索 → 検索ワードを入力 → Enter）
  -> multi_site_image_search.sh （Raycast Script Command, 引数 = 検索ワード $1）
       1. ワードを 3 通りにエンコード（python3 で UTF-8 パーセントエンコード）
            q     … 通常（スペースは %20）
            q_us  … danbooru 用（スペース → アンダースコア）
            q_tag … Instagram タグ用（スペース除去・小文字化）
       2. 5 サイトの検索 URL を組み立てる
       3. osascript で Chrome に「新規ウィンドウ + 5 タブ」を作る
  -> Google Chrome（新規ウィンドウに検索結果 5 タブ）
```

設計上の決定(2026-06-14、grill-build で確定):

- 端末は **Mac 専用**(Raycast が前提)。iPhone/iPad は対象外。
- 入力は **Raycast に直接打ち込む**(Script Command の引数)。Apple ショートカットの
  入力プロンプトは使わない。
- まずは **1 コマンドに 5 サイトまとめて**。サイト一覧はスクリプト内の配列として持ち、
  あとで用途別コマンドへ分割・増減しやすくする。
- 開き方は **毎回あたらしい Chrome ウィンドウ**(タブ追加ではない)。

## サイトと検索 URL（v1・チューニング前提）

| サイト | 検索 URL の形 | ワードの扱い | 備考 |
|---|---|---|---|
| Google 画像 | `google.com/search?tbm=isch&q={q}` | そのまま | ログイン不要 |
| Pinterest | `pinterest.com/search/pins/?q={q}` | そのまま | 時々ログインを促すが結果は見える |
| X | `x.com/search?q={q}&f=image` | そのまま | 画像タブ。Chrome のログインを利用（要ログイン） |
| danbooru | `danbooru.donmai.us/posts?tags={q_us}` | スペース → `_` | タグ検索。未ログインは 2 タグまで |
| Instagram | `instagram.com/explore/tags/{q_tag}/` | スペース除去・小文字 | タグページ。検索ではない。要ログイン |

## 実装

- 正本スクリプト: `tools/multi_site_image_search.sh`
  - Raycast Script Command(先頭の `# @raycast.*` メタコメントで Raycast に認識される)。
  - `@raycast.argument1` で検索ワードを 1 つ受け取り `$1` に入る。空なら何もせず終了。
  - UTF-8 を固定(`LC_CTYPE`)し、最小環境でも日本語が落ちないようにする([[clip2md]] の教訓)。
  - URL は全てパーセントエンコード済み(安全な ASCII のみ)なので、AppleScript への
    文字列埋め込みでも壊れない。
  - `MSIS_DRYRUN=1` を付けて実行すると、Chrome を開かず**生成される 5 URL を表示**(検証用)。
  - コマンド表示名は `@raycast.title`(既定: `画像検索（複数サイト）`)。変えたい場合はこの 1 行を編集。
- インストーラ: `tools/install_multi_site_image_search.sh`
  - 正本 → 実体(`~/.config/raycast-scripts/multi_site_image_search.sh`)へコピーし実行権限付与。
  - **正本を編集したら再実行して実体へ反映する**(正本 = tools/、実体 = ~/.config/)。

## セットアップ手順

1. `bash "tools/install_multi_site_image_search.sh"`(実体へ配置)。
2. Raycast 設定 → Extensions → Script Commands → **Add Directories** で
   `~/.config/raycast-scripts` を追加(1 回だけ)。
3. Raycast で「画像検索」を呼び出し、検索ワードを入力して Enter。
4. **初回のみ** macOS が「Raycast が Chrome を操作してよいか」の許可を求める → 許可。
5. X / Instagram は、Chrome でログイン済みなら検索結果/タグページが表示される。

## 既知の制約（= チューニングの当たり所）

- **X / Instagram**: Chrome でログイン済み前提。ログアウト中はログイン画面に飛ぶ。
- **danbooru**: タグ検索のため、普通の文章では狙い通りに出ないことがある。未ログインは 2 タグまで。
- **Instagram**: 「検索」ではなく「その単語のタグページ」。スペースは詰める。
- これらは使ってみて、外す / 置き換える / URL を変える、で調整する。

## 検証状態

- `実装済み`(2026-06-14): スクリプト 2 本作成。`bash -n` 構文チェック通過。`MSIS_DRYRUN=1`
  のドライランで、日本語 + スペースを含むワードから 5 URL が正しく生成される(通常エンコード /
  danbooru のアンダースコア化 / Instagram タグ整形)ことを確認。
- `実機確認済み`(2026-06-15、武田さん): Raycast から実際に呼び出し、Chrome の新規ウィンドウで
  複数サイト画像検索が機能することを確認。v1 は運用開始可能。サイト別の検索結果品質は、
  実際の使用感に応じて今後チューニングする。

## 将来候補（今回やらない）

- 用途別コマンドへの分割(「一般画像」「イラスト・二次」「ポーズ・写真」など)。
- 起動時のグループ選択、サイトの追加・除外を設定ファイル化。
- Instagram のキーワード検索(タグページ以外)への対応。

## 関連リンク

- [[clip2md]] — 同じ Raycast 起動系の自作ツール
- [[google-tasks-quickadd]] — Raycast → スクリプトの正本 / 実体 + SSD 非依存パターンの先例
