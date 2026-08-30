---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-30
---

# 仕組みA（指摘の台帳＋A10）と 検査D1 の実装記録（2026-08-30）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

承認の出どころ: 親メモ `## 実装への申し送り` の先頭節（2026-08-30・実行の承認）。

## 結論

**3本とも作り、完成条件5つをすべて満たした。**
D1 は O0 の本物のログに対して **3項目とも FAIL** を返す。つまり武田さんが目で見つけた
「レイヤー全表示」を、機械が先に出せる状態になった。

## 作ったもの

| 実体 | 場所 | 役割 |
|---|---|---|
| 指摘の台帳 | `output/gf2-helen-swimsuit/review-findings.json` | 指摘を1件ずつ記録し、変換先の検査を書く |
| 関所 A10 | `tools/plan_audit.py`（追記） | `open` の指摘が残っていれば FAIL |
| 検査 D1 | `tools/deliverable_checks.py`（新規） | 成果物の表示セットの排他性。Blender を起動しない |

台帳の初期投入は親メモ `## 機械化した指摘` の4件（F001〜F004）。
F001 は `target: D1` / `status: converted`、F002 は `target: A10` / `converted`、
F003 は `human`（人間判断として残す）、F004 は `A9`（実装済み）。

## 実行したコマンドと結果

### 1. D1 の変異試験（検出力の確認）

```
python3 tools/deliverable_checks.py --mutation-test
```

- 基準（壊していない最小構成）: D1a・D1b・D1c すべて PASS
- `D1a` 別の版を1つ足す → **検出**
- `D1b` 同じ部品の別派生（General）を足す → **検出**
- `D1c` 体をもう1つ足す → **検出**
- 結果: **3 / 3 検出**（終了コード 0）

### 2. D1 を O0 の本物のログにかける

```
python3 tools/deliverable_checks.py "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/o0-verify/build-log-o0.json"
```

- `[D1a] FAIL` 表示中 **43 / 全 75 個** / 版 `HelenSSR01` 16個・`HelenSSR0101` 27個
- `[D1b] FAIL` `HelenSSR0101 cloth2_lod0` の **(無印)・Bend・Flat・General** が同時
- `[D1c] FAIL` **体 3個**（`P1_body_lod0` / `HelenSSR0101 body_lod0` / `HelenSSR01 body_lod0`）、
  **髪 2個**
- 結果: **0 / 3 PASS**（終了コード 1）。2026-08-30 の実測値と一致。

### 3. A10 の効きめ（壊した版で1回、直した版で1回）

```
python3 tools/plan_audit.py --only A10
```

- 壊した版（F001 の `status` を `open` へ戻した）→ **FAIL**
  「F001: 未変換のまま残っている」／終了コード 1
- 直した版（元に戻す）→ **PASS**「指摘 4 件（converted 3、human-kept 1）」／終了コード 0

### 4. 監査の全項目

```
python3 tools/plan_audit.py
```

**10 / 10 PASS**（約56秒）。A1〜A9 は従来どおり、A10 が加わって表記が 9/9 → 10/10 になった。
A3 の変異試験8種はすべて「検出」のまま。

## 記録として残したファイル

- `output/gf2-helen-swimsuit/run-20260830-plan-audit.txt` — 監査 10/10 の全文
- `output/gf2-helen-swimsuit/run-20260830-d1.txt` — D1 の変異試験と本番判定の全文

## D1 が測っていないもの（検査の説明にも書いた）

見た目の正しさ・模様の貼り間違い・姿勢・材質。**これらは引き続き武田さんの目に残る。**
加えて D1c は役割を**名前から推定**しているため、`cloth2` のように名前と中身が一致しない
部品は分類できない（Helen の体は `cloth2_lod0_*` という名前で入っている）。

## やらなかったこと（申し送りの禁止事項どおり）

- D2〜D5（高さ範囲・穴・SHA台帳・来歴規約）は作っていない。
- D1 を Blender 起動で実装していない。入力は build-log だけ。
- 「1体に絞る」作業はしていない。D1 は「絞れていない」と言う検査で、絞るのは成果物を作る工程の側。
- 既存の A1〜A9・封鎖・却下案照合には触っていない。

## 説明ページ（人が読む用）

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/20260830-mechanization-implemented.html`

## 次に取る承認（申し送りのとおり）

1. **「1体に絞る」工程をどう入れるか**（どの版・どの派生を残すか。新しい設計判断なので実装側で決めない）。
2. **工程O1（溶接した座標を原本の形へ戻す）へ進んでよいか。**
