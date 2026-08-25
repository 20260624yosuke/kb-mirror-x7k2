---
type: analysis
title: "ひづるめ B1 群(4章)映像 ingest 独立レビュー報告・条件付き承認"
date: 2026-08-25
reviewer: 独立レビュアー(別セッション・opencode)
targets: [coloso-hizurume-ch07-composition, coloso-hizurume-ch09-light-shadow-color, coloso-hizurume-ch13-illusion-and-lies, coloso-hizurume-ch14-simplification]
verdict: conditional-approve
status: active
confidence: high
evidence_level: source-backed
sources: [coloso-hizurume-ch07-composition, coloso-hizurume-ch09-light-shadow-color, coloso-hizurume-ch13-illusion-and-lies, coloso-hizurume-ch14-simplification]
---

# ひづるめ B1 群(4章)独立レビュー報告(2026-08-25)

## 総合判定: **条件付き承認**

- 差し戻し級の問題(虚偽・時刻不一致・解釈混入・大量欠落)は **0 件**。
- 修正必須は **ch09 の 1 件のみ**(未収載スライドの追加)。ch13/ch14 は記載補完レベル。
- 修正適用は **実行セッションの仕事**。適用後、別セッションの独立レビュアーによる修正確認を経て承認確定とする(前例: hide ch06・marse ch08 と同じ流れ)。

## 検証方法(レビュアー自身の実行による)

1. 機械検証の再実行: `tools/video_ingest_gate.py check --phase complete` を 4 章分自実行 → すべて PASS(警告「retrofit 実行のため本文非破壊は節の存在確認のみ」は 4 章共通・snapshot が retrofit 済みのためで FAIL ではない)。
2. 動画との突き合わせ: 観測表から合計 28 枚を無作為に選び ffmpeg で同時刻フレームを自前抽出して照合(ch07:6/ch09:7/ch13:8/ch14:7・分割動画は両パートから選択)。
3. 抽出漏れ検査: 全帯域 10 秒間隔スイープを自分で実施(ch07 53 点/ch09 p1 94+p2 41/ch13 p1 92+p2 65/ch14 53 点)し観測表と照合。疑義箇所は個別抽出+原寸クロップで確定。
4. 量の妥当性と台帳整合(index 行・log・frontmatter `visual_ingested`・manifest `completed`)を確認。

## 章ごと判定

### ch07 構図 — 承認

- gate PASS。突合 6 枚(ev-002/011/019/030/039/041)すべて一致。スイープ補完行 ev-038〜041 は正しい時刻に正確。
- ev-019 の「選択フォルダー221」はカーソル被りのため原寸クロップで検証 → 220 行の下の「21?」行で観測どおり正確(レビュアー初見の「220」はハイライト行の上の行との混同)。
- スイープで未観測画面なし。保存フレーム 00m00s(プレイヤーUI・「00:00 / 17:16」)/00m10s(節タイトル)の検証で ev-001/ev-038 の時刻対応も正確。プレイヤー表示 17:16 ≒ 実尺 522.5 秒の 2 倍速収録で ch13 の記録と整合。
- 軽微な指摘(修正不要・次回 log 触る際に整理推奨): log の ch07 スイープ追記(「追記(ch07)」行)が無関係な「Raycast File Search スコープ拡張」エントリの箇条書き内に混入している。内容は正しい。

### ch09 光と影と色 — 条件付き承認(修正 1 件必須)

- gate PASS。突合 7 枚(ev-005/029/035/057/061/069/090)すべて一致。スイープ補完 ev-090(フィルターギャラリー)も正確。
- **修正必須**: p2 01:43〜01:51 に未収載スライド「色収差」がある(下記 修正指示 A)。新規知識(色収差の物理的原因の一文+図)を含む画面で、10 秒スイープのサンプル(01:40/01:50)が両方外れた。
- それ以外の問題なし。台帳整合(index 90 枚/log 89 枚+ev-090 追記/frontmatter/manifest)。

### ch13 錯覚と嘘 — 条件付き承認(軽微・記載補完のみ)

- gate PASS。突合 8 枚(ev-004/013/024/038/066/086/094/104)すべて一致。焼き込みプレイヤーUI の「タイマーはファイル時間の約 2 倍で進む」記載を 4 枚で数値照合し整合(00:30→00:57/05:29→10:55/09:20→18:36/09:33→19:04)。
- スイープ補完 ev-100〜104 は正しい時刻に正確(ev-104 の Safari 画面は目次の各講座時刻まで一致)。
- **軽微な指摘**: p2 00:02〜00:13 に「とにかく、見る相手に連想させよう…」スライド(p1 ev-055 と同内容)の継続表示があり未記載(下記 修正指示 B)。既収録内容の再表示で新規知識ゼロのためフレーム追加は不要。
- 台帳整合(index 104 枚/log 99 枚+ev-100〜104 追記)。

### ch14 シンプルとは洗練の極み — 条件付き承認(軽微・記載補完)

- gate PASS。突合 7 枚(ev-005/013/025/029/039/044/049)すべて一致。スイープ補完 ev-047〜051 すべて正確(ズーム 53.3%・球の無彩色化・フォルダー 703/レイヤー 1398 まで検証)。スイープ読取を統括側直接読取で代替した点は recheck.method に明記済みで問題なし。
- **軽微な指摘**: 動画末尾に未記載画面 2 件 — 08:35 前後の水色フェードアウト途中フレーム(薄文字判読不能+透かし「web_in_box_mail-973965」)、08:38 の「Coloso.」エンドカード(下記 修正指示 C)。知識情報ゼロだが動画終端の画面として記載があるべき。
- 台帳整合(index 51 枚/log 46 枚+ev-047〜051 追記)。

## 共通所見

- 観測精度は高い: 突合 28 枚すべて時刻・被写体・文字・レイヤー状態まで一致。観測文への解釈・推論・助言の混入 0 件。「判読不能」処理は適切(無理読みなし)。原寸クロップ確定事項(円数・レイヤー番号等)の再検証でも誤りなし。
- 量の妥当性: 4 章ともスライドの状態遷移を追う構成で重複過多なし(41/90/104/51 枚)。
- 台帳: index 行・log・frontmatter `visual_ingested`・manifest `completed` は 4 章とも整合。

---

## 修正指示(実行セッション用・パスとコマンドつき)

KB ルート: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
以下、KB ルートからの相対パス。実行前に `git status` 等で対象ファイルが本レビュー時点(2026-08-25・修正未適用)から変わっていないか確認すること。

### A. ch09(必須): 未収載スライド「色収差」の追加

1. フレーム抽出と本保存:
   ```
   ffmpeg -ss 01:46 -i "raw/_coloso/2026_05_31_ひづるめ/_attachments/09_02.mp4" -frames:v 1 "wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/hizurume-ch09-02-01m46s.png"
   ```
2. `wiki/sources/coloso-hizurume-ch09-light-shadow-color.md` の映像観測表の最終行(ev-090 の次)に ev-091 行を追加。観測文は実フレームから確認して記載すること(下記はレビュアー確認済みの骨子):
   - 白背景スライド、タイトル「色収差」。本文「色収差はレンズ内の色の波長の違いによって起きます。」
   - 中央にレンズを通る光線の線図(複数色の光線がレンズで屈折して交差・分離する X 字状)+右側にイラスト(首〜肩のクロップ)。
   - 選択レイヤー・ツール状態は実フレームで確認して補う。判読できないものは「判読不能」。
   - 行末に「(10秒間隔スイープでは 01:40/01:50 のサンプルが両方外れたため未検出・独立レビューで発見)」の注記を推奨。
3. `wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/manifest.json`: observations に ev-091 を追加・note の枚数を 90→91 枚に更新。
4. `index.md` 116 行目: 「映像観測90枚」→「映像観測91枚」。
5. `log.md` に修正追記(何を・なぜ追加したか)。
6. gate 再実行:
   ```
   python3 tools/video_ingest_gate.py check --manifest "wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/manifest.json" --source "wiki/sources/coloso-hizurume-ch09-light-shadow-color.md" --snapshot "wiki/assets/frames/coloso-hizurume-ch09-light-shadow-color/snapshot.json" --phase complete --index index.md --log log.md
   ```

### B. ch13(推奨): p2 冒頭の継続スライド表示の追記(フレーム追加不要)

- `wiki/sources/coloso-hizurume-ch13-illusion-and-lies.md` の ev-057 観測文の末尾(または凡例行)に追記:
  「(直後〜00:13 までは前パート末尾スライド「とにかく、見る相手に連想させよう…」[ev-055 と同内容]の継続表示・既収録のため追加フレームなし)」
- 変更後に gate 再実行(A と同じコマンド形式で ch13 のパスに読み替え)。

### C. ch14(推奨): 末尾 2 画面の記載補完

- 方式 1(推奨): `ffmpeg -ss 08:38 -i "raw/_coloso/2026_05_31_ひづるめ/_attachments/14_01.mp4" -frames:v 1 "wiki/assets/frames/coloso-hizurume-ch14-simplification/hizurume-ch14-08m38s.png"` で 1 枚保存し、ev-052 行を追加:
  「黒背景に白文字「Coloso.」(講座ロゴのエンドカード・知識情報なし)。直前の 08:35 前後は水色地のフェードアウト途中フレーム(薄文字判読不能・透かし「web_in_box_mail-973965」)。(独立レビューで発見・補完)」
  → manifest・index(51→52 枚)・log 更新+gate 再実行。
- 方式 2(最小): フレーム追加なしで、凡例の「末尾(08:40)は暗転フレームで知識情報なし」を「末尾(08:35〜)は水色フェードアウト→「Coloso.」エンドカード→暗転で知識情報なし」に訂正し gate 再実行。

### 修正後の手順(共通)

1. gate `--phase complete` が PASS することを確認。
2. 修正確認を別セッションの独立レビュアーに依頼(前例: `review/2026-08-25-marse-ch08-pilot-review-and-fix-confirm.md` と同じ流れ)。確認 PASS をもって B1 群の承認確定。
3. 承認確定後、`wiki/builds/coloso-visual-ingest-batch2/quality-gate.json` の該当 family(hizurume B1)に承認記録を入れる(修正適用前の本レビュー時点では未記録・条件付き承認のため)。

## 本レビューで確認済みの検証証跡(再確認用)

- 突合に使った自前抽出フレーム: `/tmp/hz-review/{ch07,ch09,ch13,ch14}*/`(セッションの一時領域・消えている場合は各章の指示どおりに再抽出してよい)。
- ch09 未収載スライドの存在確認: p2 01:46 抽出で「色収差」スライドを視認済み(01:40 はハロー現象・01:52 は「色収差の使い方」で挟まれる)。
- ch13 継続表示の存在確認: p2 00:05/00:10 抽出で「とにかく、見る相手に連想させよう…」を視認済み(00:00 は真っ黒・00:15 は「絵の嘘と色の心理」タイトル)。
- ch14 末尾の存在確認: 08:30 は ev-051 どおり・08:35 は水色フェードアウト・08:38 は「Coloso.」エンドカード・08:40 は ev-046 どおり暗転。
