---
type: analysis
name: Obsidian Canvas 用 自作プラグイン MVP 設計レビュー
aliases: [obsidian-canvas-plugin-mvp-review]
tags: [obsidian-canvas, plugin, typescript, pureref-migration]
sources: [art-canvas-pilot-2026-05-29-asuna-01, pureref]
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-05-30
---

# Obsidian Canvas 用 自作プラグイン MVP 設計レビュー

## 現在の統合見解

MVP 2 機能は技術的には実装可能。ただし安定度は分かれる。

- 画像ノードの表示上の左右反転は堅い。`.canvas` の node に `flipX: true` を保存し、Canvas node DOM にクラスを当て、CSS で画像だけ `scaleX(-1)` にする。
- ズームリミット解除は可能だが脆い。Obsidian 1.12.7 の実装では、`zoomBy` / `setViewport` だけを patch しても最終フレーム処理で `tZoom` が `-4..1` に戻される。MVP では最終フレーム処理と touch pinch の `tZoom` クランプを差し替え、`zoomToBbox` だけは線形スケール値を扱うため個別 override するのが、単位を混ぜずに済む。

この結論は [[art-canvas-pilot-2026-05-29-asuna-01]] の PureRef 代替 Canvas 運用を前提に、ローカル Obsidian 1.12.7 (`/Applications/Obsidian.app`) の `obsidian.asar/app.js`、Obsidian 公式 `canvas.d.ts`、Better Canvas Lock、Advanced Canvas の実装例を照合したもの。

## 根拠

### `.canvas` の未知キー保持

Obsidian 公式 `canvas.d.ts` は、`CanvasData` / `CanvasNodeData` / `CanvasEdgeData` に arbitrary key を許容している。ローカル Obsidian 1.12.7 の実装でも、Canvas node は座標・サイズ・色以外を `unknownData` として保持し、`getData()` で再展開していた。したがって Obsidian 本体で開いて保存する限り、node の `flipX: true` は保持される見込みが高い。

推奨永続化先:

- 主: `.canvas` JSON の file node に `flipX: true`
- 補助: プラグイン `data.json` はズーム上下限・将来の移行設定用。反転状態の正本にはしない

理由: `.canvas` に残せば、Canvas ingest や外部 JSON 処理が「この参照画像は制作中に反転表示されていた」と読める。`data.json` に逃がすと、Canvas ファイル単体の可搬性と AI 読解精度が落ちる。

### ズームクランプ箇所

ローカル Obsidian 1.12.7 では、Canvas の通常ズームは以下の内部メソッドと状態で動く。

- `canvas.zoomBy(delta, center?)` — `tZoom` を増減し、`markViewportChanged()` を呼ぶ。
- `canvas.setViewport(x, y, zoom)` — `x/y/zoom` と `tx/ty/tZoom` を設定する。
- `canvas.zoomToBbox(bbox)` — `Math.clamp(Math.min(widthZoom, heightZoom), -4, 1)` を使う。ただし第 1 引数は `tZoom` ではなく線形スケール値。
- `canvas.requestFrame()` 内の RAF コールバック — `t.tZoom = Math.clamp(d, -4, 1)` が最終クランプ。
- touch pinch 側 — `Math.clamp(T, -4, 1)` がある。

`tZoom` は log2 値なので、既定範囲 `-4..1` は実倍率で `1/16..2`。`zoomBy` だけを差し替えても `requestFrame()` で戻されるため、MVP では以下の 2 段に分ける。

- `requestFrame()` と touch pinch: `Math.clamp` wrapper を `min === -4 && max === 1` の Canvas ズーム専用パターンに限定し、`minZoomExp/maxZoomExp` に差し替える。
- `zoomToBbox(bbox)`: 直接 override し、`Math.min(widthZoom, heightZoom)` を `2^minZoomExp..2^maxZoomExp` の線形スケール範囲で clamp してから `Math.log2()` する。

### ノード単位の描画フック

反転は以下の内部面を使うのが最も小さい。

- `canvas.nodes: Map<string, CanvasNode>`
- `canvas.getSelectionData()`
- `CanvasNode.getData()` / `CanvasNode.setData()`
- `CanvasNode.nodeEl` / `contentEl`
- `Canvas.addNode(node)` と `CanvasNode.setData(data)` への patch

Advanced Canvas も `addNode` / `setData` / `getSelectionData` / `node.nodeEl` を前提にしているため、現実にプラグイン実装で使われている面ではある。ただし公式 API ではない。

## 実装方針

### ファイル構成

```text
<vault>/.obsidian/plugins/canvas-reference-tools/
├── manifest.json
├── package.json
├── tsconfig.json
├── esbuild.config.mjs
├── main.ts
├── styles.css
└── src/
    ├── canvas-internals.ts
    ├── flip-image-nodes.ts
    ├── zoom-limits.ts
    └── settings.ts
```

### 設定

- `minZoomExp`: 例 `-8`。実倍率 `2^-8 = 1/256`
- `maxZoomExp`: 例 `4`。実倍率 `2^4 = 16`
- `enableZoomPatch`: 既定 true
- `enableFlipX`: 既定 true

UI 表示は「実倍率」も併記する。内部値は `tZoom` と同じ log2 に揃える。

### 反転コマンド

コマンド名: `選択した画像ノードを左右反転`

処理:

1. active leaf が `canvas` か確認。
2. `canvas.getSelectionData().nodes` から `type === "file"` かつ画像拡張子の node だけ抽出。
3. 選択画像のどれかが未反転なら全て `flipX: true`、全て反転済みなら `flipX` を削除。
4. `node.setData(nextData)`、`applyFlipClass(node)`、`canvas.requestSave()`。

CSS:

```css
.canvas-node.canvas-ref-tools-flip-x .canvas-node-content.media-embed img {
  transform: scaleX(-1);
  transform-origin: center center;
}
```

ノード枠、ラベル、リサイズハンドルまで反転すると操作感が壊れるため、反転対象は画像要素に限定する。

### patch 箇所

- `active-leaf-change` / `layout-change` で canvas view を検出。
- Canvas ごとに一度だけ `canvas.addNode` を patch し、新規 node に `patchNode(node)` と `applyFlipClass(node)` を実行。
- 既存 `canvas.nodes` を起動時に走査。
- `node.setData` を patch し、データ変更後に `applyFlipClass(node)`。
- ズームは `around(Math, { clamp })` で `min === -4 && max === 1` の場合だけ `minZoomExp/maxZoomExp` に差し替える。ただし `zoomToBbox` は線形スケール値なので、`canvas.zoomToBbox` を個別 override して `2^minZoomExp..2^maxZoomExp` で clamp する。起動時に一度、実 Canvas の `requestFrame.toString()` が `Math.clamp` と `-4,1` を含むか検査し、外れていれば zoom patch を無効化する。

## 矛盾・未確定

- `Math.clamp` wrapper は小さいがグローバル patch である。ローカル Obsidian 1.12.7 の `app.js` では Canvas ズームの `(-4, 1)` 呼び出しは 3 箇所だけだったが、将来バージョンで変わる可能性がある。
- `screenshotting = true` を常時使う案は避ける。クランプは外れるが、Canvas の virtualization が全 node attach になり、画像-heavy Canvas で重くなる。
- 完全に堅い公式 API 経路は現時点ではない。MVP は「壊れたら feature disable」前提の内部 API 利用。

## 関連リンク

- [[canvas-reference-tools]]
- [[art-canvas-pilot-2026-05-29-asuna-01]]
- [[pureref]]
