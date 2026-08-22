---
type: build
title: ヘレン「星夜のワルツ」3D資料化
created: 2026-07-15
sources:
  - gf2-helen-starlit-waltz-mmd-quickstart-investigation-2026-07-15
  - gf2-helen-starlit-waltz-3d-materialization-plan-2026-07-15
  - gf2-helen-starlit-waltz-project-artifacts-2026-07-15
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-07-24
---

# ヘレン「星夜のワルツ」3D資料化

## 目的

ヘレン「星夜のワルツ」の公式 MMD モデルを、**MMD 制作ではなく作画資料として見る**状態まで持っていく。
主眼は胸部・ドレス・横向き/斜め横の前方突出シルエット観察。普段の運用は Blender 常駐ではなく、
必要角度を PNG 化して Eagle で参照し、Blender は追加角度が欲しい時だけ開く。

## 現在の状態

- 公式配布アーカイブを取得し、`00_source/original/` と `00_source/extracted/` に保管済み。
- `Blender 4.5.11 LTS` と `MMD Tools 4.5.13` で PMX を読み込み済み。
- Blender の画面と説明は日本語化済み。
- Blend を開いた直後から全ビューポートが `マテリアルプレビュー` になるよう保存済み。
- `helen-neutral.blend` と `helen-observation-30deg.blend` を分離し、中立状態を壊さない構成にした。
- `helen-reference-capture.py` で 2 状態 x 10 方向、合計 20 枚の 2048x2048 PNG を再生成できる。
- 再実行時も中立版 SHA-256 は不変で、変更骨は `右ひじ / 右腕 / 左ひじ / 左腕` の 4 本だけと確認済み。
- v2 では元PMXの剛体274個・ジョイント392個と胸剛体 `Chest_L` / `Chest_R` を読み込み、物理付きの新規 `helen-physics-reference.blend` を分離した。
- 180フレームの重力動作MP4、6姿勢 x 2方向のPNGシート、胸剛体の移動グラフ、BodySkin監査シートを生成済み。
- 物理初期化後の動作全体の最大相対移動は約3.7mm、最終15フレームの残差比は約3.4%。自動判定は合格だが、重量感として十分読めるかはユーザーレビュー待ち。
- 2026-07-24 に、チョーカーのラッピングライン観察用として `helen-hide-head-for-choker.py` を追加・修正済み。初期 `MODE = "choker"` で頭部まわりと肩掛けジャケットを一時非表示にし、チョーカー候補材質は残す。武田さんが Blender で検証し「問題ない」と確認済み。
- Eagle へ入れる推奨 8 枚は選定済み。**Eagle 実登録完了のみ未確認**。

## v2 物理・自然ポーズ資料

- 成果物: `03_output/reference_v2/gravity-motion.mp4`、`gravity-keyframes.png`、`living-pose-sheet.png`、`body-mesh-audit.png`。
- 動作: 30fps・180フレーム。前傾して停止、収束、上体ひねりから停止、収束を全身斜め前と胸部真横で同時表示する。
- 姿勢: 片足重心、前傾、上体ひねり、片腕上げ、歩き出し、停止直後を斜め前45度と真横で収録した。
- 数値注釈: 黄色は解剖学的重心ではなくMMD設定上の胸剛体中心。赤は重力方向、シアンは胸郭側の接続範囲、マゼンタは最大変位方向を示す。
- 胴体監査: 胸から腹部は連続しており胸部水着の適合試験には使える。首・両上腕・右大腿には衣装前提の切断面があり、全裸の完成資料や全身水着の土台には使えない。
- 自動検証: H.264、1920x1080、30fps、180フレーム、6秒を確認。代表レンダーに黒画面・欠損テクスチャ色はなく、元PMX・既存Blend・既存PNG 20枚のハッシュも不変。
- 制約: 作者物理の変位は小さい。数値合格と「作画上の重量感が読める」は別であり、後者は未承認。

## チョーカー観察用の一時非表示

2026-07-24、武田さんは描いているエスキースの資料として、首まわりのチョーカーがどう巻き付くかを Blender 上で観察したいと依頼した。初回実装では髪・顔材質を隠したが、後頭部周りが残り、肩に掛けたジャケットも資料として邪魔だったため修正した。

- 成果物: `02_scripts/helen-hide-head-for-choker.py`。
- 運用: `01_blend/helen-neutral.blend` などを開き、Blender の「スクリプティング」で実行する。初期値は `MODE = "choker"`。
- `MODE = "choker"` は、髪・サングラス・顔・目・口・表情、`BodySkin` の頭部側、肩掛けジャケットを一時非表示にする。
- `BodySkin` は全身肌材質なので、材質ごと消さず、頂点グループ `頭` のウェイト `0.01` 以上を持つ面だけを隠す。座標しきい値で首を切る案より、頭部だけを狙うための判断。
- 肩掛けジャケットは `P1-Cloth1-Cape` として扱う。
- チョーカー候補材質 `P1-Cloth2-Metal`、`P1-Cloth2-TopCloth-Bras`、`P1-DiamondsA`、`P1-DiamondsB`、`P1-Cloth3-Diamonds` は非表示対象に入れない。
- 追加モードとして `head`、`hair`、`jacket`、`restore` を残し、戻す時は `MODE = "restore"` または編集モードの `Alt+H` を使う。
- 自動検証では `choker` 25,248面、`head` 15,628面、`hair` 9,923面、`jacket` 9,620面、`restore` が Blender 4.5.11 LTS バックグラウンドで正常実行。`helen-neutral.blend` の更新時刻は変化せず、Blend ファイル保存は発生していない。
- 武田さんが実機で確認し、実装成果物に問題なしと判断した。

## 経緯

1. 出発点では、武田さんの要求は「公式モデルがあれば Blender で開き、自由角度スクショを作画資料にする」だった。動画制作は非対象。[[gf2-helen-starlit-waltz-mmd-quickstart-investigation-2026-07-15]]
2. 次に、公式配布 PMX を使い、中立状態保持・観察ポーズ別保存・カメラ自動生成まで含む導入計画を組んだ。[[gf2-helen-starlit-waltz-3d-materialization-plan-2026-07-15]]
3. 実装では、一時導入のまま終わらせず、`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/` に**元配布物 / blend / scripts / renders / reports** を分けた固定配置へ整理した。
4. 配布物は `AplayBox` の公式ページから取得し、RAR 展開前に危険な格納名が無いことを確認したうえで保存した。`GirlsFrontline HelenSSR0101.pmx` と主要テクスチャは正常に揃っていた。`spa` だけは PMX 内参照由来の既知の欠落扱いで、完成レンダーには支障が無い。[[gf2-helen-starlit-waltz-project-artifacts-2026-07-15]]
5. 読み込み直後、ユーザー側で「モノクロっぽい」「実用イメージが湧かない」という詰まりが出たため、運用を「Blender を常用する」のではなく「PNG 20 枚を主資料、Blender は不足角度の補助」に整理した。これはユーザー対話由来の運用判断で、成果物 README にも反映されている。
6. 2026-07-24、エスキース用にチョーカーのラッピングラインを観察したいという目的が追加された。頭部と髪が邪魔になるが、チョーカー自体を壊してはいけないため、材質名と頂点グループを使った一時非表示スクリプトを作った。
7. 初回検証後、武田さんは「後頭部周りが残っている」「肩掛けジャケットも邪魔」と報告した。修正版では `BodySkin` の頭部側を頂点グループ `頭` で追加非表示にし、ジャケット `P1-Cloth1-Cape` も `MODE = "choker"` で隠すようにした。
8. 修正版は Blender 上でユーザー検証済み。作画資料としてのチョーカー観察補助は実用可能になった。

## 実装したもの

- 配布物保存: 元 RAR、展開済み PMX、テクスチャ、検証用 manifest。
- Blender 資産: `helen-neutral.blend`、`helen-observation-30deg.blend`。
- スクリプト: 読み込み、検査、マテリアルプレビュー固定、カメラ生成、再検証。
- チョーカー観察補助: 頭部まわりと肩掛けジャケットを一時非表示にし、チョーカー候補材質を残す `helen-hide-head-for-choker.py`。
- レンダー: `neutral` と `observation-30deg` の 2 系列 x 10 方向 = 20 枚。
- レポート: 配布物、読み込み、骨、レンダー条件、blend 再読込、Eagle 選定、造形観察メモ。
- 運用 README: 再撮影 3 操作、表示崩れ時の対処、Eagle 登録候補を日本語で記載。

## 詰まった点と解決

- 表示がモノクロ/白っぽく見える問題:
  `マテリアルプレビュー` に固定保存して解消。開いた直後からカラーで見える前提にした。
- Blender が実用的に見えない問題:
  20 枚の定番 PNG を先に生成し、「普段は Eagle / 追加角度だけ Blender」という役割分離に変更した。
- 中立状態を壊す不安:
  [[3d-asset-keep-original]] の考え方に寄せ、中立版と観察版を別 blend に分離。再検証で中立版 SHA-256 不変を確認した。
- ポーズ差分が広がる不安:
  変更骨を 4 本に限定し、左右上腕 30 度・肘 10 度を計測値付きで固定した。
- テクスチャ欠落の判定:
  `spa` 参照だけが既知の欠落扱いで、髪・肌・胸部・ドレスの主要材質はレンダーで正常と確認した。

## 実用上の使い方

- 日常運用の主資料は `03_output/renders/` の 20 枚。
- 最低限の基準画像は、`observation-30deg__chest-front45__p85.png`、`observation-30deg__chest-side__p85.png`、
  `neutral__full-side-ortho__ortho.png`、`observation-30deg__dress-front__p70.png`。
- Blender を開く用途は「20 枚に無い角度を追加で見る時」に限定する。
- 再撮影は `01_blend/helen-neutral.blend` を開き、`02_scripts/helen-reference-capture.py` を実行するだけでよい。
- チョーカーの巻き付き観察は `01_blend/helen-neutral.blend` を開き、`02_scripts/helen-hide-head-for-choker.py` を `MODE = "choker"` のまま実行する。戻す時は `MODE = "restore"`。

## 未完了・未検証

- Eagle への 8 枚の手動登録と、登録後の再表示確認は未記録。
- Blender を閲覧専用にさらに簡略化した別 UI 版（骨非表示、カメラプリセットだけ露出など）は未作成。
- ~~この手順を別キャラ/別衣装へ一般化するテンプレート化も未着手。~~
  → 2026-07-26 に [[mmd-library-blender-import]] として実装済み。ただし本プロジェクトの
  スクリプトは `helen-model-map.json`(アーマチュア名・胸/ドレスの高さ比・ボーン名 `左腕`)
  に依存しており**そのままでは他モデルに使えない**ことが判明したため、あちらでは
  カメラを外接箱から自動決定する方式に作り直している。流用できたのは外接箱の考え方・
  EEVEE 設定・SHA-256 検証まで。
- v2 のレビュー条件「胸の遅れと収束が読める」「6姿勢が作画へ使える」はユーザー未承認。
- 水着資産の探索・ヘレンへの移植はレビュー合格後の第2段階であり、まだ開始していない。

## 関連

- [[gf2-helen-starlit-waltz-mmd-quickstart-investigation-2026-07-15]]
- [[gf2-helen-starlit-waltz-3d-materialization-plan-2026-07-15]]
- [[gf2-helen-starlit-waltz-project-artifacts-2026-07-15]]
- [[3d-asset-keep-original]]
