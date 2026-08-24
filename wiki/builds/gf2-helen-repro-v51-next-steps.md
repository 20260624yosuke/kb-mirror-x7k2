---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-24
sources:
  - gf2-helen-repro-v51-handoff
---

# 次にやること — HELEN-REPRO v5.1 ロードマップ（2026-08-24時点）

> [!info] このページの位置づけ
> 引き継ぎ正本は [[gf2-helen-repro-v51-handoff]]（停止点そのもの＝§6・触ってはいけないもの＝§10）。
> このページは **2026-08-24時点の「ここまでの経緯」と「次にやること」を優先順つきで俯瞰する**ための
> 展開ページ。正本と衝突したら正本が優先。各工程を実行したら、正本へ番号追記＋このページの
> 状態を更新する。

## 現在地のサマリ（ここまでの経緯）

成果物blend `helen-h0157-repro.blend`（SHA `e0ba1756…`）は **2026-08-23診断以降ずっと無変更**
（SHA前後一致を実測済み）。直近の経緯:

| 日時 | 何をした | 正本番号 |
|---|---|---|
| 08-22深夜〜08-23夜 | SH照明候補v2 blend(meansh/low)を作成→白飛び訂正版として再書き出し(`f109b`) | #54 |
| 08-23夜 | **照明診断(`f110`)**: ①見ている絵にシーン灯が無い(ビューポート10画面すべて `use_scene_lights=false`・森HDRIのみ)②ramp入力37材質とも固定Z軸で灯方向に追従しない③既定黒白ramp22材質残存④素材は「無い」系と「あるのに未適用」系の両方⑤照明の合否基準(GATE)自体が未定義 | #55 |
| 08-23深夜〜24未明 | 調査W1〜W4(カタログ60,434件/実行ログ/展開18,568本/計画レビュー)→**抽出計画v2承認**→E0〜E3実行: 焼き込み照明(lightmap2枚+cubemap3枚+LUT)を実画像984項目として抽出・post24実値とSH8参照を一式化・cache版DLLに`LoadRoomById`ログ書式3件実在を確認。**ただし bind/probe位置は scene root 依存で欠落 → 適用時は approximation 明示が必須** | #56 |
| 08-24朝 | **E4 backup volume走査**: FDA付与後に2台とも走査→ゲームAssetBundles無し(TimeMachineがLocalCache除外)。scene root `d128870a…` の回収ルートは「将来の配信待ち」のみ残る | #57 |

詳細な要約ページ: [[gf2-helen-lighting-diagnosis-summary-20260823]] / [[gf2-helen-light-extract-execution-20260824]]

## 次にやること（優先順）

<div style="border:2px solid #c9403f;border-radius:8px;padding:12px 16px;margin:10px 0;">
<b>A1. 表示経路の修正（最優先・小さなblend変更）</b><br>
本blend＋v2候補2本の保存ビューポート設定を <code>use_scene_lights=true</code>(シーン灯ON)へ変える
冪等スクリプト(f111想定)を実行する。<br>
<b>なぜ最優先か</b>: 診断#55①の通り、今開くと森HDRI1枚しか見えていない。
これを直さないと A2 の目視判断自体が正しくできない（f43・白飛びと同じ場所で3回足を取られた経路）。<br>
触るもの: 保存ビュー設定のみ(形状・材質・灯パラメータは触らない)。blend SHAは変わるので前後記録。<br>
<b>失うもの</b>: 開いた瞬間から仮置き3灯の絵になる。必要なもの: 武田さんの承認(計画→実行)。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>A2. v2候補blend(meansh/low)の目視判断</b> — 既存の停止点(§6最優先行)。
f109b訂正版を A1 の表示経路で開いて見比べる。分岐: 採用(本blend反映は別承認)／調整(第3版)／現行維持。
注意: <code>reports/stage1-review-sheet.png</code> は古い(#54)。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>A3. 同構図目視シートの再作成</b> — A2の材料。原作フレーム×旧灯×meansh×low を同カメラ・同解像度で並べる
(f107/f108レンダ流用)。E1でSH8/post24データが増えたぶん、原作側の材料も前回より強い。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>B. ramp方向追従化の試作（構造修正・効果大）</b> — 37材質共通の固定Z軸入力を
「灯方向 N·L」へ配線変更する候補blend試作。直接光の実値が無くても構造自体は実装可能
(方向は武田さんのDEC判断が残る)。前提: A1完了後。候補blend限定・本体に触れない。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>C. 抽出資産のBlender適用計画</b> — E0/E1の成果物(lightmap/cubemap/LUT画像・post24実値・SH8係数)を
再現パイプラインへ入れる別計画。bind・probe位置欠落のため <b>approximation 明示必須</b>。
post24(Tonemapping/Bloom/fog/LUT)のBlender近似(compositor/view transform)からが着手候補。
必要なもの: 武田さん承認(approximation受け入れの判断含む)。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>D. 照明の合否基準の明文化(I)</b> — 文書のみ。(a)直接光パラメータ突合(将来回収後)
(b)原作フレーム比(参考値)(c)同構図シート目視、の3段ドラフト。A2〜Cの判定で使う。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>E. 脇の境界の現状確認</b> — 武田さん指摘(08-23夜)への回答済み事実: 08-17に対応済み
(胸だけbody1・腕胴足はbody へ戻す・比較画像あり)だが「境界が消えた」確認記録は無い。
A1後の正しい表示経路で脇周辺の比較画像を作り確認する。
</div>

## 後回し・待ち（順序確定済み）

<ul>
<li><b>P2ストッキング</b> — 「第1段合格後の展開時」の約束どおり後回し(§6 階調表の行どおり)</li>
<li><b>scene root / prefab root / Helen材質設定</b> — 回収ルートは将来の配信待ちのみ(E4で確定)。
backup volume・CDN・attach は全て道尽きた/停止条件済み</li>
<li><b>silkstock ramp割当て</b> — 機械根拠が無いため推測禁止のまま(武田さん判断ならDEC)</li>
</ul>

## 判断待ちリスト（武田さん）

<ol>
<li><b>A1 の実施承認</b>（表示経路修正・小さなblend変更）— ← 最初に欲しい返事</li>
<li>A2 の目視判断（v2候補 meansh/low）</li>
<li>B の着手可否（ramp方向追従化試作）</li>
<li>C の可否（approximation 前提での抽出資産適用）</li>
</ol>

## 制約

- 触ってはいけないものは正本 §10 のまま（既存25 blend・raw/・GATE判定条件・ゲームデータ読み取り専用ほか）
- GATE f46〜f72 は毎回運用（起動保留ではない）
- 抽出資産を blend へ入れるときは必ず approximation 明示（#56 の欠損2列表）

## 元データ（コピー用）

<div style="background:rgba(127,127,127,.12);border-radius:6px;padding:8px 12px;font-size:.9em;">
引き継ぎ正本:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md</code><br><br>
診断レポート:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/LIGHTING-DIAGNOSIS-2026-08-23.md</code><br><br>
抽出計画(v2・実行完了):
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/LIGHT-DATA-EXTRACTION-PLAN-2026-08-23.md</code><br><br>
E1抽出画像:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/lighting-extract/</code>
</div>

## 関連リンク

- [[gf2-helen-repro-v51-handoff]] — 引き継ぎ正本（§6 停止点 / §10 触ってはいけないもの）
- [[gf2-helen-lighting-diagnosis-summary-20260823]] — 診断サマリ
- [[gf2-helen-light-extract-execution-20260824]] — 抽出実行結果サマリ
