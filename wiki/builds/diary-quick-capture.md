---
type: build
name: 殴り書きメモ後継クイックキャプチャ
aliases: [diary-quick-capture, freeze-diary]
tags: [macOS, iOS, shortcuts, iCloud, launchd, journaling, automation]
sources: []
status: superseded
confidence: high
evidence_level: user-stated
last_reviewed: 2026-06-06
---

# 殴り書きメモ後継クイックキャプチャ

## 現在の統合見解

2026-06-06 時点の現行運用は、Apple 純正メモへ日別ページを作る、開発元配布の元ショートカットを再取得して使用する方式。再取得後、保存先フォルダだけを `殴り書きメモ` に変更すると正常動作へ復旧した。

後継として実装した「iCloud Drive へ1メモ=1不変ファイルで保存するシステム」は、Mac側の実装と自動試験まで完了しているが、当初目的に対して大きすぎたため現在は採用運用しない。成果物は削除せず、将来必要になった場合の候補として残す。

## 現行ショートカットの動作

- 入力本文と現在日時から `title` / `content` を作る。
- Apple 純正メモ内で、名前が `title` を含み、フォルダが `殴り書きメモ` と等しいメモを検索する。
- 作成日が新しい順、取得数1件。
- 該当メモが無ければ新規作成し、あれば追記する。

## 障害と復旧

- 症状: 2026-05-06 頃から新しい日付ページを作れず、既存の日付ページへ追記され続けた。
- 確認事実: `2026/05/09🗓` が最後に作られた日付ページで、2026-06-04 まで更新されていた。
- Codex調査: 元ショートカットを書き出して内部処理を確認し、日付生成と検索条件を変更した修正版を作成したが、修正版も正常動作しなかった。
- 原因: ショートカット内部の何らかの破損が疑われるが、未特定。日付生成部分だけが原因とは確認できなかった。
- 解決: 開発元ページからショートカットを再ダウンロードし、デフォルト設定から保存先フォルダだけを `殴り書きメモ` に変更したところ、正常動作へ復旧した。
- 現行の復旧方針: 同様の破損時は、既存ショートカットの複雑な修理より、開発元の正常なコピーを再取得して必要最小限の設定だけ変更する。

## 実装

- 仕様: `diary-quick-capture-proposal.md` v4
- 本体: `tools/diary_quick_capture.py`
- テスト: `tools/tests/test_diary_quick_capture.py`
- インストーラー: `tools/install_diary_quick_capture.sh`
- Mac 入力UI: `tools/diary_capture_mac.sh`
- Mac アプリソース: `tools/diary_capture_mac.applescript` / `tools/freeze_diary_mac.applescript` / `tools/resend_diary_outbox_mac.applescript`
- Mac アプリ実体: xcord の `殴り書きメモ.app` / `日記をBrain Baseへ固定.app` / `殴り書きメモoutbox再送.app`
- iPhone/iPad Shortcut 手順: `tools/diary-quick-capture-shortcut-setup.md`
- LaunchAgentテンプレ: `tools/com.takedayousuke.diary-quick-capture.plist`
- LaunchAgent実体: `~/Library/LaunchAgents/com.takedayousuke.diary-quick-capture.plist`
- 状態/操作入口: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/xcord/diary-quick-capture/`

## アーキテクチャ

```text
iPhone / iPad Shortcut または Mac capture
  -> iCloud Drive/殴り書きメモ/yyyy-MM-dd_HHmmss_<UUID>.md
  -> LaunchAgent: _daily/yyyy-MM-dd.md (mutable derived)
  -> user-side freeze-diary
       -> rawを正本台帳として再走査
       -> 新UUIDだけをtemp+fsync+atomic no-clobber publish
       -> raw外manifest cacheを再構築
       -> 未ingest rawを再提示
  -> Brain Base /llm-wiki ingest
       -> voice: self または voice: mixed source
       -> wiki/self
```

## 重要保証

- capture UUID は canonical UUID、内部比較は小文字正規化。
- aggregator と freeze は同じ `flock` を共有する。
- freeze は `_daily` ではなく安定確認済み capture を読む。
- immutable raw が正本。manifest は raw 外の再構築可能キャッシュ。
- raw publish は temp + fsync + `link()` で atomic no-clobber。
- freeze と ingest を区別し、未 ingest raw は毎回再提示する。
- outbox 再送は元日時・同じUUIDを再利用し、成功確認後だけ削除する。
- 日記は原則 `voice: self`。外部文章を貼った日記は `voice: mixed`。

## 検証

2026-06-06 に Python自動テスト7件、Mac実iCloud保存・debounce・derived生成、保存失敗outbox・同UUID再送、同秒並列起動、共有lock待ちを確認した。

## 後継システムの状態

- Mac側は実装・自動試験済み。
- iPhone/iPad Shortcut、3台実機パイロット、初回 freeze / ingest は未実施。
- 現在は開発を継続せず、既存成果物を将来候補として保管する。
