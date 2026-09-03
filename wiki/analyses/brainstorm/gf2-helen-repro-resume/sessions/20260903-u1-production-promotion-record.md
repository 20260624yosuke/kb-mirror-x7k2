---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-03
parent: ../_index.md
---

# 2026-09-03 U1正規導入記録（原子操作）

承認：カードで「正規導入へ進む」＋「はい、この選択でよい」を受領。範囲は一時作業場（stage）の候補の本番移設のみ。
共有 `hooks.json` への適用・Blend変更・U2/U3は含まない。

## 退避（導入前の旧版・復元用）

場所：`06_repro-v51/audit/runs/20260901T230943+0900/promotion-backup/`

| ファイル | 旧SHA |
|---|---|
| `06_repro-v51/scripts/a10_quality_gate.py` | `a56a24d84e111cd133df78fa917c2f2e7383b964a7ab2f713377f5d599ad9c8d` |
| `tools/project_quality_gate.py`（共有・他案件も使用） | `7f28b1171c4c8cbb1797e817b5600b6dbb43a54025cb5a5c664891e4e4151d97` |
| project root `quality-gate.json` | `f7b29ca63f0d93d28f19f3fa34d54789c09493ad42ee26541b7f93ef191ffa96` |

## 導入内容

- 新規：`06_repro-v51/audit/` へ11件（契約3・台帳・証拠・fixture・所見・状態・writer台帳・検証受領書）、`scripts/` へ `audit_guard.py`・`writer_scan.py`、
  `tools/` へ `helen_route_hook.py`・`project_quality_gate_required_audits.json`。試験ファイルは配置 layout の前提が stage 専用のため本番へ置かず、実行証拠は stage のまま。
- 置換：`a10_quality_gate.py`（旧 `a56a24d8...` → 新 `c0076e5e...`）、共有 `tools/project_quality_gate.py`
  （旧 `7f28b117...` → 新 `2dea2a28...`）。差分は追加のみ＋ `check` の検査が1行だけ拡張（未登録の台帳には何も足さないため他案件への影響なし。KB台帳で回帰確認済み）。
- 追加：本番 `quality-gate.json` へ `project_id` と `execution_audit` 節（新版基準の12入力、投影SHA `c7ba2586...`、集合SHA `eb371050...`）。
  現物SHAは `479f8a1d...`。
- 台帳：`project_quality_gate_required_audits.json`（現物 `c353c4a0...`）の入口を本番1件に切替え。stage入口は run 証拠として残し、本番照合の対象から外した
  （同一IDで2経路あると `EA_CANONICAL_PATH_MISMATCH` で止まる設計のため）。

## 導入直後の再検査（すべてPASS）

- 本番関所 plan：PASS（修正前は分類停止→所見停止を経て開通）。
- KB共有台帳 plan：PASS（共有ツール置換の回帰なし）。
- 自動試験20件（stage）：PASS。
- 復元試験：退避3件のSHA一致と一時場所への戻し書きを検証してPASS。戻すときは退避コピーを上書きし、上の旧SHAと照合する。

## 証拠の境界

本記録はU1導入の完了であり、実hook接続の本番証拠・原作一致・Blend完成・U2/U3の証拠ではない。
共有hooksへの3枝接続は環境軸の別承認が必要で未実施。
