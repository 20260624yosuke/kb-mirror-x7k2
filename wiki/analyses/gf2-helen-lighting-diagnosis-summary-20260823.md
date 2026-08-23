---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-23
sources:
  - gf2-helen-repro-v51-handoff
---

# ヘレン再現 照明診断サマリ（2026-08-23）

成果物blend `helen-h0157-repro.blend`（SHA `e0ba175651c20251…`）を**一切変更せず**実測した照明診断の要点。
全数値は `f110_lighting_stack_audit.py` の実測。詳細版はプロジェクトの
`reports/LIGHTING-DIAGNOSIS-2026-08-23.md`（パスはページ末尾）。

<div style="border:2px solid #c9403f;border-radius:8px;padding:12px 16px;margin:10px 0;">
<b>いちばん大事な発見</b><br>
武田さんが開いて見ているマテリアルプレビューには、<b>シーンの灯が1本も点いていない</b>。
保存されたビューポート設定は10画面すべて <code>use_scene_lights=false</code>・<code>use_scene_world=false</code>。
見えている照明は Blender 同梱の森の写真 <code>forest.exr</code> の1枚だけ。
「成果物には光がある感じの描画がない」という指摘は、<b>見ている経路そのものが正しかった</b>。
</div>

## 武田さんの2つの質問への回答

<div style="border:1px solid rgba(127,127,127,.4);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>Q1「素材がちゃんと揃ってない？」</b><br>
→ <b>両方ある</b>。<br>
・手元に無くて揃えられないもの: 原作の主光・補助光の値（方向/色/強度）= 実在依存186バンドル中 <b>0件</b>。
scene root・prefab root・ヘレン本体の材質設定も不検出。<br>
・あるのに使っていないもの: 寮の環境光(SH8プローブ)・RampSetting 10件×4帯・ReflectionProbe cubemap 3枚・
lightmap 2枚・画面効果24項目・LUT(<code>test.v103</code>)。
</div>

<div style="border:1px solid rgba(127,127,127,.4);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>Q2「基準が曖昧で監査を抜けている？」</b><br>
→ <b>抜けていない。「照明の合否基準そのものが存在しなかった」が正直な答え。</b><br>
GATE 14 PASS / 1 FAIL は形状・骨・輪郭・ramp所蔵を判定するもので、照明の原作忠実度はどこにも定義されていない。
判定不能だったものを放置した結果が「品質の悪い絵のまま先へ進んだ」現状。
</div>

## 技術的な発見（上から順に重要）

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>① 見ている絵に光が無い（表示経路の問題）</b><br>
マテリアルプレビュー = シーン灯OFF + 世界OFF + 森のHDRI1枚。シーンの3灯(主600W/補200W/裏250W)は
レンダリング(F12)か「ビューポートのライト使用をON」にしたときだけ効く。
f43・白飛び事故と同じ場所で3回足を取られた経路。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>② 陰影の段差が灯に追従しない（構造未実装）</b><br>
37材質すべての階調入力が <code>clamp(dot(法線,(0,0,1)))</code> の固定Z軸。
灯をどれだけ動かしても全身の明るさが変わるだけで、アニメ調の段差の位置は永遠に動かない。<br>
原作は逆で、<b>灯ごとに N·L を計算して階調表(RampMap)の帯を読み替える</b>
(主光=V 0.125 / フレスネル系=0.625 / 追加灯=0.875。シェーダ行番号付きで18/18機械確認済み)。
これが「ゲーム内では光がある感じの描画なのに成果物はそうじゃない」の技術的な正体。
</div>

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>③ 既定の黒→白グラデーションが22材質に残存</b><br>
顔(<code>face_lod0</code>)・素肌(body/body1)・cloth3系・目・舌など22材質が Blender 既定の2要素rampのまま
（handoff §9 の既知欠陥22件と実数一致）。P2/P3_body や hand には本物の32段rampが焼き込み済みなので、
<b>原作rampの機械適用自体は実績あり</b>。残る原因は renderer→material 対応表の未回収。
</div>

## 光の3層マトリクス（要約）

<table style="border-collapse:collapse;width:100%;">
<tr style="text-align:left;">
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">層</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">原作の仕組み</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">原作データ</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">成果物側</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">合否基準</th>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>主光</b></td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">N·L → V0.125帯 → 主光色を乗算</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;color:#c9403f;"><b>未回収(blocked)</b></td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">仮置き AREA 600W</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">無し</td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>補助光</b></td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">灯ごとにループ → V0.875帯 → 灯色を乗算</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;color:#c9403f;"><b>未回収(blocked)</b></td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">仮置き 200W＋原作に無い裏250W</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">無し</td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>環境光</b><br>(SH+Probe+fog)</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">SH評価+輝度保存 / SpecCube0 / fog混合</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;color:#1a7f37;"><b>部分回収</b>(SH8他)</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>皆無</b>(候補blendのみ)</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">無し</td>
</tr>
</table>

## 次の一手

<b>既存の停止点</b>: v2候補blend(meansh/low)の目視判断待ち — 採用 / 調整 / 現行維持。
この診断はその判断の材料です（新しい承認点は作っていない）。

本診断からの新提案（判断は武田さん）:

<ol>
<li><b>表示経路の修正</b> — 成果物blendの保存ビューを「シーン灯ON」に変えるか。
失うもの: 開いた瞬間から仮置き3灯の絵になる。得るもの: 「灯無し」と「仮置き灯」の取り違えがなくなる。</li>
<li><b>照明の合否基準を定義</b> — (a)直接光パラメータ突合(回収後) (b)原作フレーム比(参考値)
(c)同構図シート目視 の3段。GATE外の運用規則として明文化するか。</li>
<li><b>ramp方向追従化(②の解消)</b> — 直接光の実値が無くても「灯の向きに段差が追従する」構造自体は
実装可能。原作感に一番効く候補。</li>
</ol>

## 元データ（コピー用）

<div style="background:rgba(127,127,127,.12);border-radius:6px;padding:8px 12px;font-size:.9em;">
詳細レポート:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/LIGHTING-DIAGNOSIS-2026-08-23.md</code><br><br>
実測証拠JSON:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f110-lighting-stack.json</code><br><br>
実測スクリプト:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f110_lighting_stack_audit.py</code>
</div>

## 関連リンク

- [[gf2-helen-repro-v51-handoff]] — 引き継ぎ資料の正本（§6 停止点 / §8 照明 / §9 階調表）
