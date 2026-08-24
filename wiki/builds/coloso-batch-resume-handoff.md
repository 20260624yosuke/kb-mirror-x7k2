---
type: build
title: "Coloso 映像ingest バッチ再開 引き継ぎ資料(2026-08-24)"
sources: []
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-24
---

# Coloso 映像ingest バッチ再開 引き継ぎ資料(2026-08-24)

## 目的と現在地

ox(opencode) サーバーエラーで死亡した **8/23 の並列 ingest バッチ群**(hide/sasa/marse/ye_jji の
4講座同時走行)の再開点を、ディスク実測で固定する。会話履歴は正本にならない(棚卸し
[[coloso-visual-ingest-resume-inventory]] の教訓)ため、本資料の記載はすべて 2026-08-24 の
ファイル実測に基づく。

固定パス(本資料内で使う):

- `<KB>` = `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`
- `<TEMP>` = `/var/folders/mx/08ffsjl11dnc3yxc_76clv940000gn/T/opencode` — **揮発性領域**。
  OS 再起動や一定期間で消え得る。temp 成果物に依存する章から先に処理すること。

## 前提ルール(作業中ずっと有効)

1. **ディスク実測だけが正本**。死亡セッションの完了主張・本資料の推測より、実際のファイルを確認する。
2. `raw/` は read-only。書き込みは `wiki/` 側のみ。
3. 品質ゲートは常時必須(設計 v2.2/v2.3 正本 [[video-visual-ingest-design]]):
   - 抽出前 snapshot — **対象6章は全て実行済み**(`wiki/assets/frames/<slug>/snapshot.json` or
     `snapshot-pre.json` 在)。再実行不要。
   - 完成報告前 check — `python3 <KB>/tools/video_ingest_gate.py check <rawページ> --phase complete`
4. **第2読者の扱い**: marse ch05/ch06 の obs と sasa ch02 の観測テーブルは、別セッション
   (サブエージェント)の盲検読取である。反映時に主読取と食い違ったら、不一致として両方記録する
   (片方を無言で採用しない)。確定できるときは corrected、できないときは要確認+marked-uncertain。
5. 映像観測節には **画面上で確認できた事実のみ** 書く。推論・助言を混ぜない。
6. 完了条件: source 反映+ゲート通過+`visual_ingested` 付与+manifest complete+index/log 更新。
   ゲートが落ちたら「完成」と報告しない。

## 現在地サマリ(2026-08-24 実測)

| # | 章(slug) | 状態 | 成果物所在 |
|---|---|---|---|
| 1 | coloso-hide-ch05-male-female-proportion | **反映待ち**(読取・manifest draft 完成済み) | `<KB>/wiki/assets/frames/coloso-hide-ch05-male-female-proportion/`(56枚 PNG+manifest.json status=draft・観測56件) |
| 2 | coloso-marse-ch05-fetish-face | **反映待ち**(盲検19枚読了済み) | `<TEMP>/marse-visual/obs-ch05.md` + `<TEMP>/marse-visual/ch05/`(フレーム20) |
| 3 | coloso-marse-ch06-fetish-upper-body | **反映待ち**(盲検19枚読了済み) | `<TEMP>/marse-visual/obs-ch06.md` + `<TEMP>/marse-visual/ch06/` |
| 4 | coloso-sasa-ch02-insight-memo | **読取途中**(00:00〜10:00 済み/総長17:51) | `<TEMP>/sasa-batch/ch02-observations.md` + `<TEMP>/sasa-batch/ch02/`(フレーム37+manifest.json) |
| 5 | coloso-ye-jji-ch05-texture-basic | **読取途中**(p1〜p3+p4-H 済み/p4-I のみ未実施) | completed 12件=`<TEMP>/yejji-ch05-recovered/`、p4 フレーム=`<TEMP>/yejji-ch05/p4/frames/`(28枚)、wiki frames 14枚+snapshot.json |
| 6 | coloso-marse-ch07-fetish-lower-full-body | **未読取**(抽出のみ) | `<TEMP>/marse-visual/ch07/`(フレーム20) |
| 7 | coloso-hide-ch04-body-basics | **B修復待ち**(PNG 全滅) | source 観測表7件のみ残存。PNG・manifest 無し。snapshot-pre.json 在 |

A群完全健康9章(hide ch02/ch03・hizurume ch11/ch12・marse ch04・sasa ch01・ye_jji ch02/ch03/ch04)、
D群 blocked 約45章(chan_02/nekojira ほぼ全体=動画なし)は変化なし。hizurume は承認済み計画
[[hizurume-visual-ingest-handoff-plan]] あり・着手痕跡なし(C群扱いのまま)。

---

## タスク1: 反映待ち3章の完成(まずここから)

temp 依存が最も薄く、hide ch05 は KB 内で完結する。marse 2章は obs が temp にあるため**最初に
`<TEMP>` → `<KB>` へ退避してから作業する**。

### 1-1. coloso-hide-ch05-male-female-proportion

- raw ページ: `raw/_coloso/2026_05_31_hide_01/coloso_hide_05 男女の比率の違い.md`
- 済み: 盲検読取56枚・manifest.json(status=draft・reader_model 記録済み)・wiki frames 本保存56枚
- 残作業: source ページへ映像観測節追記 → ゲート check → manifest status を complete へ →
  frontmatter に `visual_ingested: 2026-08-24` → index/log 更新
- 観測本文は manifest.json の `observations` 配列に入っているので転記でよい

### 1-2 / 1-3. coloso-marse-ch05-fetish-face / coloso-marse-ch06-fetish-upper-body

- raw ページ: `raw/_coloso/2026_05_30_マーセ/coloso_マーセ_05 フェチとは何か・顔に関するフェチの入れ方.md`
  / `..._06 上半身に関するフェチの入れ方.md`
- 済み: wiki 側 snapshot.json・盲検読了(obs-ch05.md / obs-ch06.md とも「19枚すべて読了」の
  サブエージェント報告)・temp 抽出フレーム
- 残作業: **①temp frames+obs を `<KB>` 側へ退避** → ②フレーム本保存(命名
  `marse-ch05-p1-HHmMMs.png` 形式) → ③manifest.json 新規構築(テンプレ=
  `<TEMP>/build_manifest_hide_ch05.py`。sha256・動画情報・観測・reader_model を記録) →
  ④source 反映 → ⑤ゲート check → ⑥flag → ⑦index/log
- obs は第2読者報告のため、反映時の観察文は画面事実のみに整えて載せる

## タスク2: 読取途中3章の続き

### 2-1. coloso-sasa-ch02-insight-memo

- raw ページ: `raw/_coloso/2026_05_31_佐々/coloso_佐々_02 成長を加速する“気づきメモ”.md`
- 位置: 観測 00:00〜10:00 まで完成(`<TEMP>/sasa-batch/ch02-observations.md`)。動画総長 17:51、
  抽出フレームは 11m40s まで37枚(`<TEMP>/sasa-batch/ch02/`、temp 内に manifest.json もあり)
- 残作業: **11m20s 以降のフレーム読取から再開**(temp フレームを Read で盲検読取) → 既存観測と
  結合して source 反映 → ゲート → flag → index/log
- 注意: 既存観測テーブルの 06m00s 行に「キ/ク判別難(要確認)」があり 07m00s で解決済み。
  この行は解決済みとして載せてよい

### 2-2. coloso-ye-jji-ch05-texture-basic

- raw ページ: `raw/_coloso/01_coloso_ye_jji/ye_jji_05. 多様なテクスチャー描写_01〜_04.md`(分割4本)
- 位置: oxloop 分割読取のうち p1〜p3+p4-H が completed(completed 12件。p1 の error 2件は
  再試行で回収済み)。**p4-I(2/2)=14枚(04m00s〜)だけ prompt 未実行**
  (`<TEMP>/yejji-ch05-recovered/TASK_22_ch05-p4_読取I(2_2).prompt.txt` に対象フレーム一覧あり)
- 残作業: p4-I の14枚を読取 → 全観測を結合 → wiki frames 本保存 → manifest 構築 → source 反映
  (raw が分割ページのため、source 側で対応関係を明記) → ゲート → flag → index/log

### 2-3. coloso-marse-ch07-fetish-lower-full-body

- raw ページ: `raw/_coloso/2026_05_30_マーセ/coloso_マーセ_07 下半身と全身に使えるフェチ.md`
- 位置: 抽出のみ・未読取(wiki snapshot.json 済み)
- 残作業: `<TEMP>/marse-visual/ch07/` のフレームを盲検読取 → 以降はタスク1-2 と同じ正規手順

## タスク3: B修復(coloso-hide-ch04-body-basics)

- source 本文の観測表7件は健在で flag のみ付与済み。**参照 PNG 7枚が全滅**・manifest 無し。
- 残作業: 動画からフレーム再抽出(source 観測表の時刻と一致させる) → PNG 本保存 →
  manifest 構築 → source の PNG 参照パス確認 → ゲート check → flag 維持のまま index/log 更新
- source 本文は原則無変更(PNG 復元が目的)

---

## 共通の正規完成手順(各章共通)

1. temp 成果物を `<KB>` 側へ退避(temp 揮発対策。退避先例: `<KB>/wiki/assets/frames/<slug>/` 直下
   または `_staging` 相当の場所。最終的に孤児画像を残さないこと)
2. フレーム本保存: `wiki/assets/frames/<slug>/<講座>-chNN-pP-HHmMMs.png`(既存命名に合わせる)
3. manifest.json 構築: `<TEMP>/build_manifest_hide_ch05.py` をテンプレに。schema
   `video-visual-ingest-manifest-v1`・動画 sha256/容量/時間・抽出記録・観測(evidence_id,
   timestamp, frame, frame_sha256, confidence, observation)・reader_model を記録
4. source ページへ `## 映像観測(YYYY-MM-DD)` 節を追記。全フレームを
   `![[wiki/assets/frames/...]]` で参照付け。画面上で確認できた事実のみ
5. ゲート: `python3 <KB>/tools/video_ingest_gate.py check "<rawページ>" --phase complete`
6. source frontmatter に `visual_ingested: YYYY-MM-DD`(ゲート通過後に付ける)
7. manifest status=draft → complete
8. `index.md`(該当 source 行)+ `log.md`(`## [YYYY-MM-DD] ingest | ...` 形式、触ったページ列挙)更新

## コピペ用指示文(新セッション用)

以下を新しいセッションにそのまま貼り付けて使う:

```text
coloso 映像ingest の中断バッチ再開をお願いします。
まず引き継ぎ資料を読んでください:
/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/coloso-batch-resume-handoff.md

資料の「共通の正規完成手順」に従い、次の順で進めてください:
1. 反映待ち3章(hide ch05 / marse ch05 / marse ch06)の完成処理(marse 2章は最初にtemp成果物を退避)
2. 読取途中3章の続き(sasa ch02 は11m20s以降 / ye_jji ch05 はp4-I(2/2)の14枚 / marse ch07 は未読取)
3. hide ch04 のフレームPNG復元(B修復)
会話履歴ではなくディスク実測を正本にし、ゲート検査(check --phase complete)と台帳更新(index.md/log.md)を省略しないでください。
各章が完成したら都度、成果物を tools/inbox.py add で申告してください。
```

## 対象外

- nekojira ch03(snapshot 済み・抽出前): 動画入手待ち(D群)と同じ扱い。本バッチ対象外。
- hizurume ch01〜10・13〜26 等 C群未着手: 承認済み計画 [[hizurume-visual-ingest-handoff-plan]]
  に従う別工程。本 handoff の完了後に着手。

## 使わなかったもの・落とした情報

- ye_jji ch05 の completed 12件の中身(観測テキスト本体)と sasa ch02 の観測テーブル全文、
  marse ch05/ch06 の obs 全文は、本資料へ**転記しなかった**(temp 参照のみ)。
  影響: temp が消えるとその分の盲検読取結果を失い、当該パートの再読取が必要になる。
  戻す方法はない(temp は復元不可)ため、タスク1・2 の冒頭で必ず永続退避を先に行う。
- `<TEMP>/yejji-ch05-recovered/ch05-p2_読取2_4.running.txt` は TASK_20 再試行完了済みの
  死文件と判断し、参照対象から外した(実害なし)。

## 関連リンク

- [[coloso-visual-ingest-resume-inventory]] — 8/23 棚卸し(全体地図)
- [[video-visual-ingest-design]] — 映像ingest 設計正本(v2.2/v2.3)
- [[hizurume-visual-ingest-handoff-plan]] — hizurume 承認済み計画(次工程)
- [[oxloop-parallel-agent-loop]] — 並列実行基盤(ye_jji ch05 の分割読取で使用)
