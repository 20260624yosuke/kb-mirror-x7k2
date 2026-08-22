---
type: source
name: Obsidian Canvas パイロット: 2026-05-29 長乳xOLxアスナ 01
aliases: [art-canvas-pilot-2026-05-29-asuna-01]
tags: [art-canvas, obsidian-canvas, eagle, image-usage, pilot]
sources: []
status: uncertain
confidence: medium
evidence_level: source-backed
stage: pilot
workflow_status: test
last_reviewed: 2026-05-30
raw_source: raw/_MY_ART/2026_05/2026_05_29_長乳xOLxアスナ_01/2026_05_29_長乳xOLxアスナ_01.canvas
sidecar_json: wiki/sources/art-canvas-pilot-2026-05-29-asuna-01.usage.json
---

# Obsidian Canvas パイロット: 2026-05-29 長乳xOLxアスナ 01

> [!warning] パイロット
> このページは Canvas ingest ワークフロー検証用のパイロットであり、確定運用ではない。一次観測と LLM 推論を分離し、Eagle 照合は sha256 完全一致のみ `confirmed` とする。寸法一致・画素類似は `candidate` に留める。

## 要約

`raw/_MY_ART/2026_05/2026_05_29_長乳xOLxアスナ_01/2026_05_29_長乳xOLxアスナ_01.canvas` を対象に、Obsidian Canvas の構造を AI が読める形へ試験的にコンパイルした。

この Canvas は、1 作品・1 ラフ単位のエスキース用資料ボードとして使われている。Canvas 上には file node 4 件、group node 2 件、text node 1 件、edge 1 件が存在する。添付フォルダには 7 ファイルあるが、Canvas で参照されている file node は 4 件のみ。

Eagle 照合では、Canvas 上の JPG 3 件が Eagle ライブラリ内の元ファイルと sha256 完全一致し、`confirmed` として扱えることを確認した。PNG 1 件は sha256 不一致だが、Eagle 側に同寸法・低差分の候補があるため `candidate` とした。

## ソース内の一次観測

### Canvas 構造

- group node `fb52433f4209a5f1`
  - label: `エスキース_起点`
  - bounds: x=-1000, y=-326, width=1123, height=1046
  - file node 4 件を範囲内に含む。
- group node `a600bb0e2f30b910`
  - label なし
  - bounds: x=-380, y=-271, width=398, height=878
  - file node 3 件を範囲内に含む。
- text node `d3431f0a610c2e2f`
  - text:

```text
2026_05_29
現在xで伸びてるアカウント。
今時の絵柄っていう認識をしてる。
```

- edge `256c042c511c4232`
  - from: text node `d3431f0a610c2e2f` bottom
  - to: group node `a600bb0e2f30b910` top
  - 一次観測として、上記テキストは group `a600bb0e2f30b910` に接続されている。

### 画像ノードと Eagle 照合

| Canvas node | Canvas file | 一次観測 | Eagle 照合 |
|---|---|---|---|
| `922f0acf3cb00794` | `Pasted image 20260529200015.png` | `エスキース_起点` group 内。単独で左側に大きく配置。 | `candidate`: `M3JEGJE9H08H5`。sha256 不一致、寸法一致、画素差分小。 |
| `5c49c9318e733ef4` | `@rasec_asdjh 忙しい時間～ kronillust.jpg` | `エスキース_起点` と無名 group の両方に含まれる。接続テキストの影響範囲内。 | `confirmed`: `MPMQ29OXJMDCZ`。sha256 完全一致。 |
| `31438a659514b75f` | `@archinoer サマーフィット～.jpg` | `エスキース_起点` と無名 group の両方に含まれる。接続テキストの影響範囲内。 | `confirmed`: `MPMQADE6VT7I0`。sha256 完全一致。 |
| `daae3a4eef8740d8` | `@DotTheBot18 Just a business meetingkronillust.jpg` | `エスキース_起点` と無名 group の両方に含まれる。接続テキストの影響範囲内。 | `confirmed`: `MMWDUU32UCFFU`。sha256 完全一致。 |

## LLM 推論

以下は一次観測ではなく、Canvas 上の配置・グループ・テキストからの二次推論である。

- group `a600bb0e2f30b910` 内の 3 画像は、接続されたテキストから見て「現在 X で伸びているアカウント」「今時の絵柄」の参照群として置かれた可能性が高い。
  - confidence: medium
  - evidence_level: inferred
- `Pasted image 20260529200015.png` は、`エスキース_起点` group 内で大きく配置されているため、ラフの起点・主題・構図の中心参照として扱われた可能性がある。
  - confidence: low
  - evidence_level: inferred
- JPG 3 件は Eagle item と confirmed なので、今後の Eagle 自動整理では「この作品のエスキースで、今時の X 絵柄参照として使われた」という使用文脈を Eagle 側の長期画像ライブラリへ戻せる可能性がある。
  - confidence: medium
  - evidence_level: inferred

## 抽出されたエンティティ

- Obsidian Canvas — 今回の制作中資料ボード。
- Eagle — 長期画像ライブラリ。sha256 完全一致で 3 件を確定リンク。
- [[pureref]] — 乗り換え元の制作中資料ボード用途。今回のパイロットは PureRef から Obsidian Canvas への移行検証の一部。

## 抽出された概念

- 1作品・1ラフ単位の資料ボード。
- Canvas node 単位の画像使用台帳。
- 一次観測と LLM 推論の分離。
- Eagle item との sha256 完全一致リンク。
- `confirmed` / `candidate` / `unmatched` の照合状態分離。

## 不確実・要確認

- `Pasted image 20260529200015.png` は Eagle item `M3JEGJE9H08H5` と同寸法・低差分だが、sha256 は一致しない。確定リンクにはしない。
- 添付フォルダ内の `Pasted image 20260529200313.png` / `Pasted image 20260529200319.png` / `Pasted image 20260529200322.png` は Eagle 由来画像の PNG 変換候補に見えるが、Canvas file node では参照されていないため、今回の使用台帳には含めない。
- Obsidian Canvas の使用感を PureRef 相当に近づけるプラグイン構成は未検証。
- この形式で複数作品を継続 ingest した場合の JSON スキーマ安定性は未検証。

## 関連リンク

- Sidecar JSON: `wiki/sources/art-canvas-pilot-2026-05-29-asuna-01.usage.json`
- 元 Canvas: `raw/_MY_ART/2026_05/2026_05_29_長乳xOLxアスナ_01/2026_05_29_長乳xOLxアスナ_01.canvas`
- [[pureref-personal-fork]] — 画像意図データ層の既存構想。
- [[pureref]] — 現行の制作中資料ボード用途。
