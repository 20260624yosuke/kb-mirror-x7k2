---
type: build
title: "coloso-intake 別セッション並列作業用ブリーフ"
status: active
confidence: high
evidence_level: source-backed
sources: []
created: 2026-08-25
last_reviewed: 2026-08-25
tags: [build, coloso, intake, handoff]
---

# coloso-intake 並列セッション用ブリーフ

イクシー_2(23本)パイロットは 2026-08-25 検収承認済み。**残り6講座は各セッション1講座ずつ担当**し、この文書の手順に従う。設計の正本は [[coloso-intake-design]](ルールR1-R8・監査A0-A6)。先に読むこと:

1. `wiki/builds/coloso-intake-design.md`(設計正本)
2. `.claude/skills/transcript/SKILL.md`(文字起こしの規律)
3. `AGENTS.md` の「1A 高リスク成果物」と「併用ルール」

## 各セッションの共通手順

1. `/hold` 付きで起動され、承認カード運用で進める。
2. **講師名正綴を武田さんに1回だけ質問して凍結**(M5。ページ命名とfrontmatterに使う。推測禁止)。
3. `python3 tools/coloso_intake.py --course <slug> --lecturer <正綴> --sort-key <キー> --dry-run` で NN 対応表を提示し、ソートキー(timestamp/name)を検収カードで承認を取る。
4. dry-run 同梱(--dry-runなし)で intake → `python3 tools/coloso_intake_audit.py` で PASS を確認。
5. **代表1〜2本**を `tools/coloso_transcribe.py` で文字起こし → 再監査 → 検収カード(NN対応表・Obsidian再生・逐語目視)。
6. 承認されたら残り全本を1本ずつ文字起こし。失敗・空文字は「未完」と記録し成功扱いしない(R7)。
7. 最終監査 → `wiki/builds/coloso-intake/reports/<日付>-<slug>-audit.md` に結果保存 → quality-gate.json の**自分の family ブロックのみ**(representative_input/output・comparison_evidence・user_accepted/batch_safe/approved_by/approved_at/approval_evidence・verifier.method="independent-tool"+report_path)を更新 → `python3 tools/project_quality_gate.py check wiki/builds/coloso-intake/quality-gate.json --phase batch` で自分の family への指摘ゼロを確認 → log.md に1行追記 → 成果物を inbox.py へ申告。

## 禁止事項(停止条件)

- タイトル・章番号・章順序・講師名の推測(R3/M2/M5違反)
- mapping.json が無い状態でのページ・symlink追加
- HDD_02 未マウント時の intake・文字起こし実行(ENV_BLOCKED)
- 監査不合格があるのに次工程へ進むこと
- **他講座の family への記録・他講座の承認流用**(M3)
- `wiki/sources/` 要約層の作成(R8: 本パイプライン外)
- 複数セッションが同時に quality-gate.json を書かない。自分の family ブロックへの targeted edit のみ

## 講座ブロック(残り6講座)

| slug | HDDフォルダ | 本数(A0実測済) | ソートキー |
|---|---|---|---|
| `2024_04_22_ixy` | `2024:04:22_ixy` | 82 | 未定(構造を調査して武田さんが選択) |
| `2024_04_24_ne-on` | `2024:04:24_ne-on` | 40 | 未定 |
| `2024_04_24_晃田ヒカ` | `2024:04:24_晃田ヒカ` | 22(mp4のみ。m4a音声22本は武田さん承認で除外/設計書の例外注記と変遷を参照) | 未定 |
| `2024_04_24_雨傘ゆん` | `2024:04:24_雨傘ゆん` | 118 | 未定 |
| `2024_06_26_2SHAM` | `2024:06:26_2SHAM` | 33 | 未定 |
| `2024_06_27_ODIA_ACADEMIY` | `2024:06:27_ODIA ACADEMIY` | 67 | 未定 |

## 武田さん貼り付け用コピペ文(slugだけ差し替える)

```text
/hold coloso-intake を開始。対象講座は <slug>。
正本は wiki/builds/coloso-intake-design.md と wiki/builds/coloso-intake/parallel-session-brief.md の該当ブロック。
まず講師名正綴を1回だけ質問して凍結し、--dry-run でNN対応表を出しソートキーの承認を取ってから、intake→監査→代表1〜2本の文字起こし→再監査→検収カード。承認後に残り全本。
タイトル・章順序・講師名の推測は禁止。監査不合格時は停止条件どおり止まって報告すること。
```

例: `/hold coloso-intake を開始。対象講座は 2024_04_24_ne-on。…`(以降同一)

## 変遷

- 2026-08-25: イクシー_2パイロット検収承認を受け、並列展開用として新規作成。
