---
type: analysis
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-24
sources:
  - gf2-helen-repro-v51-handoff
---

# ヘレン再現 原作照明データ抽出 実行結果サマリ（2026-08-24）

承認済み計画 v2（E0〜E4）の実行結果。**成果物blend・既存ファイルは一切変更していません**。
前ページ [[gf2-helen-lighting-diagnosis-summary-20260823]] の「あるのに未適用」素材を実際に取り出す工程。

<div style="border:2px solid #1a7f37;border-radius:8px;padding:12px 16px;margin:10px 0;">
<b>いちばん大事な結果</b><br>
寮の焼き込み照明 <b>lightmap 2枚(1024×1024)・ReflectionProbe cubemap 3枚・LUT を実画像として取り出せた</b>
（<code>reports/lighting-extract/</code> 984項目・正対照合格）。一方で、
<b>画像を「どこに置くか」の情報（lightmap→rendererのバインド・probeの位置）は scene root 依存で回収不能</b>。
つまり現状のまま Blender に貼るなら <b>approximation(近似) として明示する運用が必須</b>。
</div>

## 前提として確定したこと（W1〜W3調査）

<div style="background:rgba(127,127,127,.10);border-radius:8px;padding:10px 14px;margin:8px 0;">
<b>「動いた寮シーンの実体」はローカルに存在しない</b> — 名前(展開レベル706シーン名にDormなし)・
CAB・サイズ2世代照合(0本)の3面から確定。有効な否定として記録済み。<br>
その上で有望な新発見: ①焼き込み照明資産の所在特定済み(21617d0e / 98ab47f2 = 手元実在)
②ポストエフェクト24種は実値まで台帳済み(loungeプロファイル)
③実行ログに新パターン <code>LoadRoomById:&lt;N&gt;</code> を発見(N=101/104/202)。
</div>

## 各工程の結果

<table style="border-collapse:collapse;width:100%;">
<tr style="text-align:left;">
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">工程</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">結果</th>
<th style="border:1px solid rgba(127,127,127,.4);padding:6px;">証拠</th>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>E0</b> 既回収実値の一式化</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">post24実値・LUTチェーン・SH8参照を1つのJSONへ。checks all_pass</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><code>logs/e0-post-values.json</code></td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>E1</b> 焼き込み照明の抽出</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">lightmap light×341/shadowmask×337・cubemap×350/310/28・LUT×1 をPNG化。ASTC手動デコード成功・寸法台帳一致・正対照(LUT=98ab47f2由来)合格。<b>欠損2列</b>: bind/probe位置/RenderSettings/直接光 = scene root依存で欠落</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><code>logs/e1-baked-lighting.json</code><br><code>reports/lighting-extract/</code>(984項目)</td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>E2</b> コード内文字列探索</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">cache版DLLの#USに <code>LoadRoomById:{0} begin</code> のログ書式が<b>実在(3件)</b> — 先行実測「0件」と部分一致で食い違い(完全一致は0)。room APIの存在裏づけ。陽性対照両版合格</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><code>logs/e2-code-strings.json</code></td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>E3</b> 寮部屋追跡</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;">RoomById 18件(Load8/Release10)を再計測し近接表を作成。計数はW2と完全一致(probe208/Task1160/timeline26)。join断定はしない</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><code>logs/e3-room-trace.json</code></td>
</tr>
<tr>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>E4</b> backup volume走査<br>(2026-08-24朝 完走)</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><b>ゲームAssetBundles無し</b> — HDD_バックアップはゲームコンテナ自体は存在するが LocalCache が空(TimeMachineのキャッシュ除外)。*.bundle 486本は全てApple等の非ゲーム由来。macbookpro側(05-27)はゲーム導入前で痕跡ゼロ。欠損104本の一致0件 → scene root 回収ルートは「将来の配信待ち」のみ残る</td>
<td style="border:1px solid rgba(127,127,127,.4);padding:6px;"><code>logs/e4-backup-volume-scan.json</code></td>
</tr>
</table>

## 残っていること・次の一歩

<ol>
<li><b>抽出物のBlender適用は別計画</b> — lightmap/probe/LUT/post24/SH8 の材料は揃った。
ただし bind・probe位置が無いため「どこに何を置くか」は候補から選ぶ判断が残り、適用物は approximation 明示。</li>
<li><b>E4 完走(08-24)</b> — backup volume にゲームデータ無しと確定。scene root の回収ルートは将来の配信待ちのみ。</li>
<li><b>既存の停止点は変わりなし</b> — v2候補blend(meansh/low)の目視判断待ち。
本抽出はその判断材料の補強（SH環境光の原作側データ強化）。</li>
</ol>

## 元データ（コピー用）

<div style="background:rgba(127,127,127,.12);border-radius:6px;padding:8px 12px;font-size:.9em;">
計画正本:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/LIGHT-DATA-EXTRACTION-PLAN-2026-08-23.md</code><br><br>
E1 manifest:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/e1-baked-lighting.json</code><br><br>
抽出画像フォルダ:
<code>/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/reports/lighting-extract/</code>
</div>

## 関連リンク

- [[gf2-helen-repro-v51-handoff]] — 引き継ぎ資料の正本（§6 停止点 / #56 抽出実行）
- [[gf2-helen-lighting-diagnosis-summary-20260823]] — 前段の診断サマリ
