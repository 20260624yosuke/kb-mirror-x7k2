---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-04
---

# 水着版ヘレン 引き継ぎ書（2026-09-03 作成 / 2026-09-04 更新）

**このページだけ読めば再開できるように書いた。** 迷ったら正本メモへ戻る:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
（5000行ある。**全部読まなくてよい**。日付つき見出しを新しい順に見る）

## 0. まず知っておくこと（ここでつまずいた）

- **python3 は `/opt/anaconda3/bin/python3` を使う。** `python3` は環境によって
  numpy の無い `/usr/bin/python3` に解決される。2026-09-03 に実際に踏んだ。
- **Blender は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender`。**
  `/Applications` には無い。
- 作業ディレクトリは
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`。
- **引き継ぎの監査が「53件の指摘」で止まることがある。これはこの案件のものではない。**
  2026-09-03 に切り分け済み: 指摘はすべて別案件
  （brainstorm-skill-portability と、llm-harness-parity / agent-positioning /
  askuserquestion-misclick-guard の `_index.md`）のもので、
  **この案件の正本メモへの指摘は 0 件**。
  原因は ① `~/.codex/skills/brainstorm/scripts/` に未作成のファイルを指している（44件）
  ② `_index.md` が保管庫内の `.claude/skills/brainstorm/SKILL.md` を指しているが、
  実際は `~/.claude/skills/brainstorm/SKILL.md` にある（書き間違い・6件ほか）。
  **武田さんの判断で「触らずに進める」と決まっている**（2026-09-03）。担当外なので直さないこと。
  確かめ方:
  `/opt/anaconda3/bin/python3 ~/.claude/skills/brainstorm/brainstorm_guard.py audit-handoff 2>&1 | grep -c 'gf2-dusevnyj-bikini-to-helen'`
  → **0 なら、この案件は通っている。**

## 1. 【解決済み 2026-09-04】適合の版の選択

**武田さんは「候補B」を選び、2026-09-04 に成果物へ入れた。**（実行記録:
`output/gf2-helen-swimsuit/run-20260904-fit-candidate-b.txt`）
以下の比較表は、判断の根拠として残す。**次のエージェントがここから聞き直す必要は無い。**

**差し替えで新たに悪くなったものが1件ある → §2 の【悪化1件】を必ず読むこと。**

合格線（**動かさないこと**）: G3b 69.05〜100 / G4a中央 1.20〜5.41 / G4b深い 0〜3.36 /
G4c 最深 −14.14 以上 / G9a厚み 0.95〜1.05

| | 現在の成果物 | 候補B | 候補A |
|---|---|---|---|
| 不合格 | **4件** | **2件** | 3件 |
| G3b 密着 | 69.213 | 91.987 | 88.752 |
| G4a 距離 | 6.329 ✗ | 5.330 ✓ | 3.956 ✓ |
| G4b めり込み | 19.728 | 8.455 | 29.169 |
| G4c 最深 | −9.57 ✓ | 合格 | ✗ |
| カップ厚み | 1.824 ✗ | 0.664 ✗ | **0.979 ✓** |
| 肩ひも厚み | 2.182 ✗ | 0.910 ✗ | 0.948 ✗ |
| 帯厚み | 1.140 ✗ | 1.174 ✗ | 1.787 ✗ |
| 中間厚み | 0.928 ✗ | 0.937 ✗ | 0.937 ✗ |
| 辺の裂け | 0 | 0 | 0 |

- **候補B は現在の成果物を全項目で上回る**（不合格が現在の部分集合。増えた不合格は無い）。
- **候補A はカップ厚みを合格させた唯一の版**だが、帯・めり込み・最深が悪化する。

**選ぶときに武田さんへ伝えたこと（失うもの）:**
- 候補B → カップ厚み 0.664 で、今度は**平たすぎる**側へ外れる。見た目で胸が押さえられた
  ように見える可能性（**実機未確認**）。← **これを選んだ**
- 候補A → 帯 1.140→1.787、めり込み 19.7→29.2、最深 −9.6→−19.3。
  カップ以外の部品が最深 −49.77mm まで体に沈む（候補B は −7.67mm）。
  **胸がカップを突き抜けて見える危険**（実機未確認）。

### 再現コマンド

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
# 【2026-09-04 以降】現在の成果物と同じ ＝ 候補B。既定（旗なし）は差し替え前の版
$P tools/helen_swimsuit_fit_p.py --body general
# 候補B
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --cup-y-lam 5e-4
# 候補A
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --cup-y-lam 5e-4 \
   --cup-soft 0.5 --cup-stretch
```

**注意: 上のコマンドは判定するだけで、成果物を書き換えない。** 成果物へ入れるには §4 の手順。

## 2. 成果物の場所と、いまの中身

- Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/swimsuit/Helen-swimsuit-flat.blend`
  48物体 / 表示22（2026-09-04 の作り直し後も同じ）
  ファイル自体の sha256 `87da83409ee99fdadb5e500ca26a39c9a9f4bc857b2367946149885cb4689f15`
  場面の指紋（canonical_manifest_sha256）
  `84dfd41e8cd3048e10eef34df76430eb92b74181e3516367b5f7d9b2797abbb7`
  ※「場面の指紋」は `build-log-swimsuit.canonical.json` と一致する値で、
  **Blend ファイルのバイト列の sha256 とは別物**。2026-09-03 版の引き継ぎ書は
  この2つを同じ名前で書いていたため 2026-09-04 に訂正した
  （差し替え前の値: ファイル `08d360c1e606…` / 指紋 `9c8da510b444…`）。
  退避: 同フォルダの `_bak-20260904/`（差し替え前の版）、`_bak-20260903/`（さらに前）
- 台帳（どれを出すか）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/visible-set-swimsuit.json`
  退避: 同フォルダの `visible-set-swimsuit.json.bak-20260904`（差し替え前）ほか
- 実行記録:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-20260904-fit-candidate-b.txt`（2026-09-04・候補Bの差し替え）
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-20260903-choker-restored.txt`（2026-09-03・1〜30節）

2026-09-03 に成果物へ入れたもの: **首の輪**（358面）、**眼鏡のフレーム**（1276面）、
**眼鏡のレンズ＋胸の中央の縦帯**（278面）。
**2026-09-04 に成果物へ入れたもの: 適合の候補B**（上衣をまるごと1つの剛体として置く
＋ カップの高さ lam=5e-4）。判定の不合格は 4件 → 2件。

### 【悪化1件】W7 首のひもの先端（2026-09-04 の差し替えで起きた）

  差し替え前: ひもの先端はヘレンの `Neck_M` より **+7.3mm**（ドナー比の差 21.5mm）
  差し替え後: **−9.3mm**（ドナー比の差 **38.0mm**・上限 20.0）＝ **16.6mm 下がった**

- **なぜ**: 上衣を「まるごと1つの剛体」として置くので、首まわりだけを合わせる項が無い。
- **手元でどう変わるか**: 首の後ろのひもの結び目が原作より下がって見える可能性。
  **Blender を開いて目で見た確認はしていない（未確認）。**
- **戻せるか**: 戻せる。`_bak-20260904/` の Blend・build-log・canonical と
  `visible-set-swimsuit.json.bak-20260904` を戻し、適合を既定（旗なし）で走らせ直す。

差し替えで良くなったもの: 密着 69.2→92.0 ／ 胸の中央の距離 6.33→5.33（合格へ）／
二面角 1.156→1.037（合格へ）／ めり込み 19.7→8.5 ／ 肩ひもの厚み 2.18→0.91 ／
カップの厚み 1.824→0.664（目標 1.0 への近さでは改善。ただし**平たすぎる側へ外れた**）。

## 3. 検査の走らせ方（全部）

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
$P tools/deliverable_checks.py "$EX/blends/swimsuit/build-log-swimsuit.json"   # D1
$P tools/swimsuit_visible_checks.py                                            # V1〜V6
$P tools/swimsuit_wear_checks.py                                               # V7・W1〜W12
$P tools/swimsuit_inventory_checks.py                                          # N1〜N3
$P tools/plan_audit.py                                                         # A1〜A17
$P tools/measurement_label_check.py                                            # M1
$P tools/doc_layout_check.py --all                                             # L1
$P tools/doc_timeline_check.py --all
```

変異試験はどれも `--mutation-test`。**検出力を示せない検査は合格に数えない。**

いまの状態（**2026-09-04 時点・候補Bの差し替え後**）: D1 1/1 ／ V 4/6（V3・V4）／
W 8/12（W7・W8・W11・W12）／ N 0/3 ／ A 17/17 ／ M1 PASS ／ L1 指摘0 ／
タイムライン 不足0。**合否の顔ぶれは差し替え前と同一で、新たに落ちた検査は無い。**
変異試験 D1 4/4・W 16/16・N 8/8・M1 4/4・L1 6/6・V 5/6（すべて再実行して確認）。

※差し替え直後は W6 が一度落ちた。`build-exception.json` の `accepted_fails` が
差し替え前の4件のままで、判定の不合格2件と食い違ったため。実測に合わせて
2件（G4b深い / G9a厚み）へ書き替えて解消した。**落とす項目を増やしたのではなく、
落ちる項目そのものが減ったための書き替え。**

**W12 は意図した FAIL。** カップ厚みが約束（1.0倍）を破っている間は落ち続ける。

## 4. 成果物へ入れる手順（フィットの版を差し替える場合）

```
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
BLENDER="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender"
# 1) 適合の出力を作る（--save で output/ へ座標を保存）
$P tools/helen_swimsuit_fit_p.py --body general <選んだ設定> --save
# 2) 原本の位相へ戻す
$P tools/swimsuit_restore_original_form.py --body general
# 3) 材料フォルダを組む
$P tools/swimsuit_material_folder.py
# 4) Blend を作る
"$BLENDER" -b --python "$EX/scripts/ce_build_blend.py" -- \
  --intermediate "$EX/intermediate-swimsuit/Helen.HelenSSR01-swimsuit" \
  --out "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --build-log "$EX/blends/swimsuit/build-log-swimsuit.json"
# 5) 検査用の数値を取り出す
"$BLENDER" -b "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --python tools/blend_probe.py -- --out output/gf2-helen-swimsuit/blend-probe
# 6) §3 の検査を全部走らせる
```

**入れる前に必ず Blend と台帳を退避すること**（`_bak-<日付>/` と `.bak-<日付>`）。

## 5. 絶対にやってはいけないこと

- **合格線を、成果物が通るように動かさない。** `BAND`（tools/helen_swimsuit_fit_p.py）と
  各検査の合格線は、原作またはドゥルシーヌヴイ原着装の実測から置いてある。
- **手順の約束を例外にして通さない。** `METHOD_PROMISES`（tools/swimsuit_wear_checks.py）に
  載せた3件（厚み1.0 / 縫い目0mm / 新しく作る面0）は `accepted_fails` へ入れられない。
  検査 W12 が止める。**通す道は実際に直すことだけ。**
- **部品の名前を検査に直書きしない。** 2026-09-03 に、これが原因で壊れていた検査が
  3本見つかった（D1の変異が素通り / V の変異が例外で停止 / A の一覧が数え打ち）。
  台帳・登録表から引く形にすること。
- **「多分こういう物だろう」で部品を消さない。** 首の輪と眼鏡がこれで消えた。
- **解く回数や重みを、合格線に合わせて選ばない。** 回数を12回に減らすとカップ厚みが
  0.877 まで戻るが、これは「成果に合わせて動かす」に当たるので採らない。
- **原本（`intermediate/Helen.HelenSSR01`）へ書き込まない。** 読むだけ。
- **成果物・台帳を退避せずに上書きしない。**

## 6. 捨てた案と理由（蒸し返さないこと）

| 案 | 理由（実測） |
|---|---|
| `out_anchor`（カップを群ごと法線方向へ押し出す） | 悪化。最深 −19.3→−40.7mm、左右に分けても −33.2mm。カップは半球状で法線が散るため縁が食い込む。**W5 が再投入を止める** |
| 役割ごとに剛体で置く（`--rigid-roles role`） | カップ厚みは 0.970 になるが、隣の役割と置き場所が 33mm 食い違い、継ぎ目の細い辺（1.9〜2.1mm）が 39.7mm に裂ける（24〜30本）。ならしを0〜12回振っても消えない |
| 高さごとの伸び率（1点の 1.076 の代わり） | めり込みは 29.2→13.0 に改善するがカップ厚みが 0.775 へ落ちる。soft 0.55〜0.68 でも両立しない |
| カップと帯の間へ橋渡しの面を足す | **不要になった**。裂けは布不足ではなく役割ごと剛体の副作用だった（まるごと剛体で 0 本） |
| 胸の頂点の高さで全体を合わせる置き方 | 2026-08-31 に肩ひもがあごの高さまで伸びた。W5 が再投入を止める |
| 一様拡縮で体格差を吸収する | 計画書 section 6 で却下済み |

## 7. 残っている作業（武田さん承認済み・未着手）

0. **【新規・最優先】カップの厚み 0.664 を 0.95〜1.05 へ戻す。** 候補Bを入れた結果、
   今度は**平たすぎる**側へ外れた。置いた直後は 13.7mm（原作と同じ）なので、
   崩しているのは**解く処理**。解く重み・回数を振っても 0.66〜0.88 で届かない。
   **回数を合格線に合わせて選ぶのは禁止**（§5）。
0b. **【新規】W7 首のひもの先端が 16.6mm 下がった**（§2 の【悪化1件】）。
   まるごと剛体の副作用。首まわりだけを合わせる項が無い。
1. ~~フィットの版を決めて成果物へ入れる~~ → **2026-09-04 に候補Bで完了。**
2. **帯の厚み 1.14〜1.79 の原因特定。** 伸びを与えても直らない（1.787→1.658）。**未特定。**
   （候補B 差し替え後の実測は 1.174）
3. **中間の厚み 0.93。** 現在の成果物からの持ち越し（差し替え後 0.937）。
4. **めり込み G4b。** 合格線 3.36 に対し、どの案でも 8.4 が最良（差し替え後 8.455）。
5. **検査 R1**（指摘1件につき出来上がりを測る検査を1本置かせる）— 有効・未実装
6. **検査 R2**（検査が台帳との一致だけを見ていないことを条件にする）— 有効・未実装
7. **検査 R3**（前回から新しく見えた部分を検査の守備範囲と突合）—
   **「検査の登録表」が先に要る**。無いまま作ると形だけになる
8. **検査 V8**（首の紐の浮き）— **合格線が未設定**。武田さんに何mmまで許すか聞くこと
9. **検査 W2**（材質未解決のメッシュを色で分類しない）— V1・V2 の作り直しとセット
10. **台帳21件の仕分けの仕組み**（承認済み）。基準は ①外すと開口が増えるか
    ②小物の形か（高さの幅に対して面積が小さく、離れた小片）。名前や見立てで決めない
11. **首以外の隙間 3か所**（Y0.898 / 1.200 / 1.226）。直すにはどれも武田さんの
    過去の決定の取り消しが要る（§ 正本メモ「首以外の隙間4か所」節）

## 8. 武田さんとのやり取りの作法（この案件で叱責が出たもの）

- **報告は3分割**: ①今すぐやること ②終わったこと ③まだ終わっていないこと ＋ 成果物の場所。
- ③には**LLM側の残作業だけ**。武田さんの目でしか確認できないものは①へ。
- **選択肢には必ず「それを選ぶと失うもの」を書く。**
- **成果物の見え方が変わる捨て方をしたら3点セット**（何を捨てたか / 手元でどう変わるか /
  戻せるか）。見ていないなら「未確認」と書く。
- 説明ページは `data-role`（decision / record / context）で節の役割を宣言し、
  済んだ記録は `<details>` に畳む。**L1 が書き込みの時点で止める。**
- 説明ページには**タイムラインの図（3段）と `figure-zoom.js`** が要る。A15 が見る。
- **数値には測り方の名前（`measured_by`）を付ける。** 別の測り方の数字を引き算して
  結論を誤った事故が 2026-09-03 に起きた。M1 が見る。

## 9. 関連ファイル（実パス）

- 正本メモ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
- 採用手順の計画書:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`
- 成果物までの道筋:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md`
- 今日の説明ページ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/20260903-choker-restored-and-exception-scope.html`
- 実行記録:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-20260903-choker-restored.txt`
