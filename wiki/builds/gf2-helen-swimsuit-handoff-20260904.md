---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-04
supersedes: wiki/builds/gf2-helen-swimsuit-handoff-20260903.md
---

# 水着版ヘレン 引き継ぎ書（2026-09-04）

---

## 武田さんが見るのはこの節だけです

**今日、成果物（Blend）は 1 度も書き換えていません。** 見た目は昨日のままです。

**あなたに残っている判断: 1 件だけ。それも「急ぎではない」。**

> **カップの大きさについて、2つの記録が食い違っています。**
> ・あなたの 2026-08-29 の承認 →「ヘレンのバストに合わせて **1.40〜1.50 倍にふくらませる**」
> ・計画書 3.3 の手順の約束 →「カップの厚みは **変えない（1.0 倍）**」
>
> **どちらを採るかで、出来上がりの胸まわりの見え方が変わります。**
> 次のエージェントがこの矛盾を示したうえで、改めて質問します。**いま答える必要はありません。**

それ以外は次のエージェントが自分で進めます。以下はエージェント向けです。

---

## 0. 実行の前提（ここでつまずいた）

- **python3 は `/opt/anaconda3/bin/python3`。** `python3` だと numpy の無い方に解決される。
- **Blender は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender`。**
- 作業ディレクトリは
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`。
- `timeout` コマンドは無い。
- **`implementation_agent: separate-session` を正本メモへ書かないこと。** 2026-09-03 に前任が
  書き、翌日この案件の実行が全面的に止まった。武田さんが明示したときだけ書く。

## 1. このプロジェクトのゴール（S010・粒度を下げてはならない）

> 俺は、ヘレンが、ドゥルシーヌヴイみたいなビキニを着たらどうなるかわかる
> 創作のための資料を作れって言ってる。

**イラストを描くための 3D 資料。** 監査を通すことでも、検査を作ることでもない。

## 2. 2026-09-04 に武田さんから出た指摘（4件・すべて機械化済み）

| 指摘 | 機械化した先 |
|---|---|
| 成果物が目標に近づいていない。ただ貼り付けただけ | 目標と検査の対応表（**A18**） |
| html の構成が悪い。目次と本文の主従が決まってない | 骨組みの並び（**L2**） |
| 監査をクリアすることがアジェンダになっている | ゴールが動いたかの明示（**L3**） |
| 決めたことを読まずに聞き直す | 明言台帳の網羅と優先度（**P1 / A20**） |

**「監査の詳細を説明されても不毛」と言われている。報告に検査の内訳を書かないこと。**

## 3. 進捗の数え方（S009・粒度を下げてはならない）

**検査の本数・合格数を進捗として報告してはいけない。**
進捗は `python3 tools/goal_coverage.py` が出す **「未測定の側面の個数」** ただ1つ。

現在 **4 個**（載せ方の法則／沿い具合／全身の輪郭／全身が揃っているか）。
埋めたら消すのではなく `埋めた記録` へ移す（黙って消すと A18 が落ちる）。

## 4. いま最優先でやること

**カップを 1.40〜1.50 に収める**（§ 武田さんの節の矛盾が解けたら）。
その前に、下の「法則版」を仕上げる。

### 法則版の現在地（2026-09-04 実測）

「載せ方の法則」＝原着装で水着が **体のどの場所に・どれだけ離れて** 載っていたかを
体の UV から取り出し、ヘレンの体の同じ場所で組み直す方式。正本は `tools/garment_law.py`。

合格線: G3b 69.05〜100 / G4a 1.20〜5.41 / G4b 0〜3.36 / 最深 −14.14以上 / 厚み 0.95〜1.05

| 版 | G3b | G4a | G4b | 最深 | 裂け | めくれ | カップ厚 |
|---|---|---|---|---|---|---|---|
| 原着装（正解） | 86.670 | 2.900 | 3.66 | −8.85 | 0 | 0 | 1.000 |
| **いまの成果物** | 91.987 | 5.330 ✕ | 8.45 ✕ | −7.67 ○ | 0 | **0** | 0.664 ✕ |
| 法則版 lam=5 | 98.765 | **2.046 ○** | 15.69 ✕ | −13.62 ○ | 0 | 28 ✕ | 1.716 ✕ |

- **前進**: 体からの距離が 5.330 → 2.046 で合格線に入り、原着装 2.900 に最も近い。
  ＝「浮いている」の直接の指標が直った。布の裂けも 0。
- **差し替えない理由**: **面のめくれ 28枚が新しく出る**（いまは 0）。見た目に出る。
  めり込みの割合も 8.45 → 15.69 と悪化。

### 再現コマンド

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --cup-y-lam 5e-4   # いまの成果物
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --law              # 法則版
```

`Case.solve(use_law=True, law_smooth=10, law_clamp=True, lam_base=5.0, lam_pen=5.0, cup_soft=...)`
で細かく振れる。**判定するだけで成果物は書き換わらない。**

### 法則版で分かっている性質（振り直す前に読むこと）

- **ならし（`law_smooth`）は 5 回以上必要。** 0 回だと辺が 31 倍まで伸び、裂けが 79 本出る。
  回数は原着装の辺長比 1.000 を基準に選んだ（合格線には合わせていない）。
- **押し戻し（`law_clamp`）の深さは原着装の最深 −8.85mm から取る。** 合格線 −14.14 ではない。
- **`cup_soft` を下げるとめくれは 0 になるが、最深が −21.59mm まで悪化する。**
  0.5→20枚 / 0.2→8枚 / 0.05→0枚。トレードオフが未解決。
- **カップ厚 1.7 の原因は体格差。** 胸まわりの張り出し（同じ測り方 apex_band）は
  ドゥルシーヌヴイ 前 71.6mm・後 96.0mm、ヘレン 前 81.1mm・後 169.1mm。
  **大きい胸に密着させれば必ず膨らむ。** これが § 武田さんの節の矛盾につながる。

## 5. 見つかった矛盾（次のエージェントが解く）

**P1c が毎回この 1 件を出し続ける。消さないこと。**

```
S006「カップ」の明言は 1.4〜1.5 だが、検査 G9a厚み の合格線は 0.95〜1.05。重なりが無い。
```

- 武田さんの 2026-08-29 の承認: カップは **1.40〜1.50 にふくらませる**／
  作り方は「内側をヘレンの肌に沿わせ、外側を厚み分だけ押し出す」
- 計画書 3.3 の手順の約束: カップの法線方向は 1.0（**厚みを変えない**）→ **W12 が守れと言う**

**合格線を勝手に動かさない。** 矛盾を武田さんに示して選んでもらう。
その際 §「武田さんが見るのはこの節だけです」の書き方にならい、**見え方がどう変わるか**を添える。

## 6. 今日 新しく入った道具（すべて変異試験つき・工程監査へ接続済み）

| 道具 | 何をするか | 接続 |
|---|---|---|
| `tools/goal_coverage.py` | 目標と検査の対応。**未測定の側面の個数**を出す | A18 |
| `tools/doc_structure_check.py` | 説明ページの骨組みの並び＋ゴールが動いたかの明示 | A19 |
| `tools/statement_ledger_check.py` | 明言台帳の網羅・優先度・合格線との食い違い | A20 |
| `tools/garment_law.py` | 載せ方の法則の取り出しと組み直し | （実装側） |
| `wiki/builds/gf2-helen-swimsuit-goal-map.json` | 目標 10 件と検査 55 本の対応表 | A18 |

`output/gf2-helen-swimsuit/explicit-statements.json` に明言 11 件（うち **絶対 4 件**）。
**絶対** は粒度を下げてはならない核心（S006 カップ / S009 進捗の数え方 / S010 ゴール /
S011 決まったことを聞き直さない）。

## 7. 検査の走らせ方（全部）

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
$P tools/deliverable_checks.py "$EX/blends/swimsuit/build-log-swimsuit.json"   # D1
$P tools/swimsuit_visible_checks.py                                            # V1〜V6
$P tools/swimsuit_wear_checks.py                                               # V7・W1〜W12
$P tools/swimsuit_inventory_checks.py                                          # N1〜N3
$P tools/plan_audit.py                                                         # A1〜A20
$P tools/measurement_label_check.py                                            # M1
$P tools/doc_layout_check.py --all                                             # L1
$P tools/doc_structure_check.py --all                                          # L2・L3
$P tools/statement_ledger_check.py                                             # P1
$P tools/goal_coverage.py                                                      # C1〜C4（進捗）
$P tools/doc_timeline_check.py --all
```

**2026-09-04 時点の状態**（変えていないものは前日と同じ）:
D1 1/1 ／ V 4/6（V3・V4）／ W 8/12（W7・W8・W11・W12）／ N 0/3 ／ **A 20/20** ／
M1 PASS ／ L1 指摘0 ／ L2・L3 指摘0 ／ P1 は P1c のみ FAIL（§5 の矛盾。**意図した FAIL**）／
未測定の側面 **4 個**。

## 8. 成果物の場所（今日は書き換えていない）

- Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/swimsuit/Helen-swimsuit-flat.blend`
  sha256 `87da83409ee99fdadb5e500ca26a39c9a9f4bc857b2367946149885cb4689f15`
- 台帳:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/visible-set-swimsuit.json`
- **入れる前に必ず退避すること**（`_bak-<日付>/` と `.bak-<日付>`）。

### 成果物へ入れる手順（フィットの版を差し替える場合）

```
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
BLENDER="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender"
$P tools/helen_swimsuit_fit_p.py --body general <選んだ設定> --save
$P tools/swimsuit_restore_original_form.py --body general
$P tools/swimsuit_material_folder.py
"$BLENDER" -b --python "$EX/scripts/ce_build_blend.py" -- \
  --intermediate "$EX/intermediate-swimsuit/Helen.HelenSSR01-swimsuit" \
  --out "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --build-log "$EX/blends/swimsuit/build-log-swimsuit.json"
"$BLENDER" -b "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --python tools/blend_probe.py -- --out output/gf2-helen-swimsuit/blend-probe
# そのうえで §7 の検査を全部
```

## 9. 絶対にやってはいけないこと

- **合格線を、成果物が通るように動かさない。**
- **手順の約束を例外にして通さない**（`METHOD_PROMISES`。W12 が止める）。
- **部品の名前を検査に直書きしない。** 台帳・登録表から引く。
- **「多分こういう物だろう」で部品を消さない。**
- **原本（`intermediate/Helen.HelenSSR01`）へ書き込まない。**
- **成果物・台帳を退避せずに上書きしない。**
- **検査の本数・合格数を進捗として報告しない**（S009）。
- **明言台帳に決定がある論点を聞き直さない**（S011。P1 が止める）。
- **変異試験用の架空の ID を、そのソースに直書きしない。**
  2026-09-04 に踏んだ。C1 が「そのファイルに在る」と判定して素通りする。その場で組み立てる。

## 10. 捨てた案と理由（蒸し返さない）

| 案 | 理由（実測） |
|---|---|
| 法則の位置へ頂点を直接置く | 辺10倍超 79本・面の裏返り 210面。ARAP はそこから戻せない |
| 法則の位置から開始して整形 | 目標＝出発点なので動かない。裂けも消えない |
| `out_anchor`（カップを群ごと法線方向へ押し出す） | 最深 −19.3→−40.7mm。W5 が再投入を止める |
| 役割ごとに剛体で置く（`--rigid-roles role`） | 継ぎ目の細い辺が 39.7mm に裂ける（24〜30本） |
| 高さごとの伸び率 | めり込みは改善するがカップ厚が 0.775 へ落ちる |
| 胸の頂点の高さで全体を合わせる | 肩ひもがあごの高さまで伸びた。W5 が止める |
| 一様拡縮で体格差を吸収 | 計画書 section 6 で却下済み |

## 11. 残っている作業（未着手）

1. **カップを 1.40〜1.50 に収める**（§5 の矛盾が解けてから）
2. **法則版のめくれ 28枚とめり込み 15.69 を同時に救う**
3. 帯の厚み（法則版 1.585／いまの成果物 1.174）。原因未特定
4. 検査 R1・R2・R3・V8・V9・W2（設計だけで未実装。**W11 が 6 件と数えている**）
5. 台帳 21 件の仕分けの仕組み（承認済み・未着手）
6. 首以外の隙間 3 か所（Y0.898 / 1.200 / 1.226）

## 12. 武田さんとのやり取りの作法

- **報告は3分割**: ①今すぐやること ②終わったこと ③まだ終わっていないこと ＋ 成果物の場所。
- ③には **LLM 側の残作業だけ**。武田さんの目でしか確認できないものは①へ。
- **選択肢には必ず「それを選ぶと失うもの」を書く。**
- **見え方が変わる捨て方をしたら3点セット**（何を捨てたか／手元でどう変わるか／戻せるか）。
  見ていないなら「未確認」と書く。
- **監査の詳細・道具の説明を報告に書かない**（2026-09-04「不毛」）。
- **武田さんに考えさせない。** 決められることは自分で決めて宣言する（S011）。
- 説明ページは `data-role`（decision / record / context）で節の役割を宣言し、記録は
  `<details>` に畳む（L1）。骨組みは `.mb-main` → `aside` の順（L2）。
  `doc-goal-moved` を必ず書く（L3）。タイムラインの図（3段）と `figure-zoom.js`（A15）。
- **数値には測り方の名前（`measured_by`）を付ける**（M1）。

## 13. 関連ファイル（実パス）

- 正本メモ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
- 目標と検査の対応表:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-goal-map.json`
- 明言台帳:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/explicit-statements.json`
- 採用手順の計画書:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`
- 今日の説明ページ（武田さん向け）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/20260904-why-audits-miss-the-goal.html`
- 今日の実行記録（全経緯）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/run-20260904-goal-map.txt`
- 前日の引き継ぎ書（この文書が置き換える）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-handoff-20260903.md`
