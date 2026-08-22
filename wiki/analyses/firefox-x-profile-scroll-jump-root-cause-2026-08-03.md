---
type: analysis
title: FirefoxのXプロフィールでスクロール位置が上へ戻る原因調査
created: 2026-08-03
prompted_by: FirefoxでXプロフィールを下へスクロールすると意図せず上方向へ戻る症状の原因調査と根本修正
sources:
  - x-eagle-free-save-pilot
related:
  - x-eagle-free-save-pilot
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-03
---

# FirefoxのXプロフィールでスクロール位置が上へ戻る原因調査

## 問い

FirefoxでXアカウントのプロフィールを下へスクロール中、意図せず上方向へ戻されるのは、
過去の自作成果物・設定・拡張機能の干渉か。原因を特定し、再発を防ぐ修正を行う。

## 回答（要約）

原因は、[[x-eagle-free-save-pilot|X to Eagle Snapshot Saver]] v0.5.42に含まれる
「プロフィール資料探し: 本人の画像だけ」機能である可能性が高い。これは一般的な推測ではなく、
現在使用中のFirefoxプロフィール、拡張機能の保存状態、配布中コードを突き合わせた結論である。

この機能はXが投稿を追加・更新するたびに画面内の`article`（投稿1件の表示要素）を監視し、
条件外の投稿へ`display:none`を付けて高さごと消していた。一方、Xのプロフィール欄は表示中の投稿を
入れ替えながらスクロール位置を再計算する。スクロール中に既存投稿の高さが消えると、この再計算と
衝突して閲覧位置が上方向へ戻る。

根本修正としてv0.5.45では、生きたX投稿一覧を監視・非表示にする機能を配布物から外した。
画像・動画のEagle保存、右クリック保存、ドラッグ&ドロップ保存は残している。

## 根拠

- Firefoxの起動中プロセスは通常プロフィール`345r7qby.default-release`を使用していた。
- 同プロフィールの`extensions.json`とインストール済みXPI（Firefox拡張の本体ファイル）は、
  `X to Eagle Snapshot Saver` v0.5.42が有効であることを示した。
- 同拡張の保存領域には`xEagleProfileImageOnlyMode`があり、Firefoxの保存形式を展開すると
  真偽値は`true`だった。つまり問題のプロフィール絞り込みが実際にオンだった。
- v0.5.42の`content-script.js`はX全体のDOM更新（画面要素の追加・変更）を監視し、
  `content.css`は除外投稿へ`display:none !important`を適用していた。
- Wikiにもv0.5.40〜v0.5.42でこのプロフィール絞り込みを追加・再調整した記録があり、
  「読み込みが遅い」「過剰なDOM監視」といった関連症状が残っていた。[[x-eagle-free-save-pilot]]

> [!warning] 矛盾あり
> Wikiの直前記録ではv0.5.43をFirefoxへ導入済みとしていたが、2026-08-03の実ファイル確認では
> `extensions.json`とインストール済みXPIの両方がv0.5.42だった。会話上の導入報告より、現在の
> ディスク上のファイルを現行状態として採用する。

## 実施した修正

- `manifest.json`の配布対象から`profile-image-filter.js`を外した。
- `content-script.js`からプロフィール投稿の監視・分類・非表示処理を外した。
- `content.css`から投稿を高さごと消す規則を外した。
- `popup.html` / `popup.js`からプロフィール絞り込みの切替欄と保存状態の参照を外した。
- 手動注入、Firefox用ビルド、署名処理から同機能を外した。
- v0.5.45としてMozilla署名済みXPIを作り、既存のFirefox自動更新先へ公開した。

## 検証結果

- JavaScript構文検査: 通過。
- `test_x_eagle_save_extractor.js`: 通過。
- `test_x_eagle_video_helper.js`: 通過。
- Firefox拡張検査: errors 0 / notices 0 / warnings 5。警告は既知の
  Firefox未対応`background.service_worker`と、配布しないシェルスクリプトへの警告。
- 署名なし版・Mozilla署名版・公開版の拡張本体一覧が一致。
- 公開`updates.json`はv0.5.45を返した。
- 公開XPIのSHA-256は更新情報の値と一致:
  `ba4d5fe383ba0be1dec268feef7fd9d777a0ebb391ea6d7dabdc34286627a16d`。
- 公開XPIに`profile-image-filter.js`が入っていないことを確認。
- 2026-08-03、通常プロフィール`345r7qby.default-release`の`extensions.json`を再確認し、
  `X to Eagle Snapshot Saver` v0.5.45が有効であることを確認。
- 2026-08-03、武田さんが同じ症状条件でFirefox実機検証を行い、「問題ない。以前より動作も軽い感じがする」
  と報告。少なくとも今回の再現条件では、スクロールが勝手に上へ戻る症状は再発しなかった。

現時点は**実装済み・自動試験済み・署名済み・公開済み・実機確認済み**である。
今回の症状に対しては、**運用開始可能**として扱う。

## 使わなかったもの・落とした情報

- **何を捨てたか**: 「プロフィール資料探し: 本人の画像だけ」1機能。本人の静止画像以外を
  Xの通常プロフィール欄から自動で隠す処理。
- **手元でどう変わるか**: 拡張機能の小窓から同名の切替欄が消え、Xプロフィールには文章投稿・
  リポスト・返信・動画などもX本来の並びで表示される。その代わり、投稿の高さを途中で消す処理が
  走らない。2026-08-03の実機確認では、スクロールが勝手に上へ戻る症状は出ず、以前より軽く感じる
  との報告があった。
- **戻せるか**: 戻せる。公開済みv0.5.43 XPIと`profile-image-filter.js`に旧実装が残るため復元可能。
  ただし旧版を再導入すると今回の症状も戻る。再開する場合は、Xの投稿一覧を高さごと消さず、
  別画面へ候補だけ並べる方式として再設計する。

## 矛盾・未確定

- Firefox更新後も症状が残る場合は、他のX表示変更拡張を一つずつ無効化する分離試験へ進む。
  現時点では自作拡張の有効状態と症状を生む処理が直接一致しており、他拡張を第一原因とは扱わない。

## 関連リンク

- [[x-eagle-free-save-pilot]]
