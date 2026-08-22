---
type: analysis
title: X→Eagle v0.5.30後の重複検知再発分析
created: 2026-06-28
sources:
  - x-eagle-free-save-pilot
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-06-28
---

# X→Eagle v0.5.30後の重複検知再発分析

## 現在の確認結果

2026-06-28時点で、ローカルコード `tools/x-eagle-save-extension/manifest.json` は v0.5.30。
Firefox の通常プロファイル `345r7qby.default-release` に入っている
`x-eagle-snapshot-saver@takedayousuke.local.xpi` も v0.5.30 で、公開配布 `.xpi` と SHA-256 が一致していた。
公開 `updates.json` も v0.5.30 を返す。

したがって、通常プロファイルに限れば「古い v0.5.29 以下が残っているため重複ダイアログが出ている」
可能性は低い。

## v0.5.30の実際の挙動

v0.5.30 は、保存前に Eagle API の `item/list?keyword=...` で既存候補を探し、
高確信度の候補が見つかった場合だけ `addFromURL` を呼ばず、`/api/item/update` で既存項目の
メモとフォルダを更新する。

高確信度として扱われる主な条件は、生成予定ファイル名、既存メモ内の `capture_key`、
`post_id + target + image_url`、media key、投稿URLの status ID など。
ただし、候補収集自体は Eagle の keyword 検索で拾えた項目に限られる。

## 再発原因の本命

Eagle API の keyword 検索は、メモ欄全文を素直には検索していない可能性が高い。
実機確認では `keyword=capture_key` と `keyword=【LLM用】` は0件だった。

さらに、直近項目 `MO0THSO94QFX1` は以下の状態だった。

- name: `画像`
- url: `https://x.com/Turi_sasu/status/2035684199278252471/photo/1`
- annotation: `capture_key: x:2035684199278252471:image-1-of-1:HEAz-SraEAArygI` を含む

しかし、同じ投稿の保存前候補探索を再現すると、候補数6件にもかかわらず対象は
`target-not-found` になった。投稿ID、media key、作者ID検索ではこの `画像` 項目が拾えず、
名前 `画像` でしか出ないため、v0.5.30 の重複スキップ分岐に入れない。

この型では、拡張機能は既存項目を見つけられず `addFromURL` へ進む。
その後、Eagle 本体は画像内容または内部情報で重複を検知できるため、重複ダイアログが出る。

## 5.30以前との関係

- v0.5.10: 重複ダイアログ後に拡張が固まる問題を避けるため、保存後の存在確認を廃止した。
- v0.5.18〜0.5.24: Eagle 重複ダイアログ後、既存項目を特定できた場合にメモ追記する方向で改善した。
- v0.5.29: 同じ `capture_key` でも、新しい保存時点の情報を上へ積むようにした。
- v0.5.30: 保存前に既存候補を見つけられた場合、`addFromURL` 自体をスキップして重複ダイアログを出さないようにした。

現在の再発は、v0.5.30が無効になったのではなく、v0.5.30の前提である
「保存前に Eagle API 検索で既存項目を拾える」が崩れる項目で起きている可能性が高い。

## 追加で見えた不整合

`tools/tests/test_x_eagle_save_extractor.js` は、`manifest.version` の期待値が `0.5.29` のままで、
現行 `0.5.30` と不一致で落ちた。v0.5.30実装後にテスト更新が一部漏れている。

また README も、ポップアップ下部の版説明が `0.5.29` のままで、v0.5.30の
「保存前重複スキップ」を説明しきれていない。

## 判断

現状の要点は「v0.5.30は入っているが、重複スキップはメタデータ検索ベースなので、Eagle側が
画像内容で検知できる重複を必ず事前検出できるわけではない」。

特に、既存項目の名前が `画像` のような汎用名で、Eagle keyword 検索に投稿ID・media key・作者IDが
出ない項目は、今後も重複ダイアログ再発対象になる。

## 次の修正候補

1. 既存項目更新時に、可能なら名前を `x-<author>-<post_id>-NN` 形式へ寄せる。
   次回以降の保存前検索で拾いやすくなる。
2. 保存前探索で候補が見つからない場合、選択フォルダ内の最近項目や `画像` 名項目を限定的に確認する。
   ただし全体検索で `画像` を使うと候補が多すぎるため、フォルダ・更新時刻・URLなどの制限が必要。
3. もし Eagle API が hash / duplicate 用の検索口を持つなら、それを使う。現行コードでは未使用。
4. テストと README を v0.5.30前提へ更新する。

## 未検証

今回の分析は、ユーザーが見た重複ダイアログの対象投稿を直接指定されていない状態で行った。
そのため、実際に再発した個別項目が上記の `画像` 名・検索漏れ型かは未確定。
ただし、現在のEagle実データに同型が存在し、同型で保存前候補探索が `target-not-found` になることは確認済み。
