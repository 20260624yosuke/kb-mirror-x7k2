---
type: build
status: active
confidence: high
evidence_level: source-backed
last_reviewed: 2026-09-05
supersedes: wiki/builds/gf2-helen-swimsuit-handoff-20260904.md
---

# 水着版ヘレン 引き継ぎ書（2026-09-05）

---

## 武田さんが見るのはこの節だけです

**今日、成果物（Blend）を作り直しました。** 見た目が変わっています。絵は下記に出しています。

**あなたに残っている判断: 1件だけ。**

> **レンダー画像を見て、これでよいか。**
> `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/img-20260905-shell/`
> （正面 `_front.png` / 斜め45度 `_left45.png` / 横 `_side.png`）

### 今日の成果（相手＝原作の裸の上半身）

| | お手本（原着装） | 昨日までの成果物 | **今日の成果物** |
|---|---|---|---|
| 密着 G3b | 86.67 | 91.99 | **99.77** |
| **肌に隠れる面積** | 7.32% | 7.30% | **0.00%** |
| **カップの面内倍率** | ×1.000 | ×1.034 ✕ | **×1.499 ○**（明言 S006 1.40〜1.50） |
| カップのふくらみ | ×1.000 | ×0.664 ✕ | **×1.275 ○** |
| 布の裂け / めくれ / 潰れ | 0/0/0 | 0/0/0 | **0/0/0** |
| 判定の不合格 | — | 2件 | **1件（G9a厚み のみ）** |
| 面数 | 4006 | 4006 | 6136（新しく作った面 2130） |

Blend sha256 `74c9d48d6bf77f57b036be53da489a238a9458b0687731da3c7a9f6ec1a3a0b2`
（退避 `blends/swimsuit/_bak-20260905/`）

それ以外は次のエージェントが自分で進めます。以下はエージェント向けです。

---

## −1. 【最重要】2026-09-05 に私が犯した誤り2件。同じ轍を踏まないこと

### 誤り① メッシュ名だけで実体を判断した

**`cloth2_lod0_General` の submesh 0 が「原作の裸の上半身」。** 名前は cloth2 だが**布ではない**。
肌アトラス `body1_d` を UV で引くと**乳輪の位置まで一致する**（2026-08-31 実測・描画で確認済み）。

**`c_HelenSSR01_slg_body_lod0` は「礼服専用の露出肌の殻」で、胸に前を向いた面が1枚も無い。**
ここへ水着を合わせると胸の裏側へ吸い寄せられる（最深 −65.5mm・肌に隠れる面積 58.7%）。

私はこれを取り違えて半日を無駄にした。**台帳 `output/gf2-helen-swimsuit/visible-set-swimsuit.json`
の `role` と `is_torso` を読むこと。名前で判断しない。** 検査 **V10c** が機械で止める。

### 誤り② 「承認が5日間実行されていない」と誤報告した

実行されていた。台帳を読まずにメッシュ名だけで判断したため。**台帳を読むこと。**

## −0.5 2026-09-05 に入った機械監査（LLM 運用全般）

| 記号 | 何を止めるか | 道具 |
|---|---|---|
| **D1** | ファイルを送ったのに、そのパスを本文に書かずに閉じる | `tools/deliverable_path_guard.py` |
| **D2** | 判断を求める語を書いたのに、本文にパスが1つも無い | 同上 |
| **H1** | **引き継ぎ資料を書き換えたのに、そのパスを本文に出さずに閉じる** | 同上 |
| **V10a/b/c** | 承認された相手以外に合わせている／名前で実体を判断している | `tools/fit_target_check.py` |
| **P1e/P1f** | 発言の適用範囲を落として台帳へ写す／見出し以外の承認が台帳に無い | `tools/statement_ledger_check.py` |

D/H は Claude Code（`~/.claude/settings.json`）と Codex（`~/.codex/hooks.json`）の Stop に登録済み。
検出力 9/9・配線 3/3。**opencode は会話終了のフックが無いため未対応。**

## 0. 実行の前提

- **python3 は `/opt/anaconda3/bin/python3`。**
- **Blender は
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender`。**
- 作業ディレクトリは
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01`。
- `timeout` コマンドは無い。
- **`implementation_agent: separate-session` を正本メモへ書かないこと。**

### 引き継ぎの到達性監査が「70行の指摘」で止まる。**この案件のものではない。**

2026-09-03 / 09-04 に切り分け済み。**この案件の指摘は 0 件。**出どころは
brainstorm-skill-portability など別案件のメモ。**武田さんの判断で「触らずに進める」。**

## 1. ゴール（S010・粒度を下げてはならない）

> 俺は、ヘレンが、ドゥルシーヌヴイみたいなビキニを着たらどうなるかわかる
> 創作のための資料を作れって言ってる。

**イラストを描くための 3D 資料。** 監査を通すことでも、検査を作ることでもない。

## 2. 進捗の数え方（S009・粒度を下げてはならない）

**検査の本数・合格数を進捗として報告してはいけない。**
進捗は `python3 tools/goal_coverage.py` が出す **「未測定の側面の個数」** ただ1つ。

現在 **5 個**（昨日 4 個から**増えた**）。減らなかった理由:
- カップの大きさの穴を1つ埋めた
- **対応表に無かった目標 GOAL-BODY-FIT を新設**し、その未測定の側面
  「帯・肩ひも・垂れが体に合わせて作り替えられているかを見る検査が無い」を新たに数えた

**増えたのは後退ではなく、これまで数えていなかった穴が見えたため。**

## 3. 2026-09-05 に分かったこと（すべて実測・詳細は正本メモ）

### 3.1 昨日の §5「矛盾」は、検査 P1c のバグだった

面内の大きさの倍率（1.40〜1.50）と厚み比の合格線（0.95〜1.05）という**別々の量**を、
対象名が同じ「カップ」だからという理由で突き合わせていた。
**P1c を量の名前（`machine.quantity`）で引くように直した。** 変異試験 8/8。

**この誤りを次の引き継ぎで再生産しないこと。** 明言と合格線を比べる前に、
**同じ量を比べているかを必ず確かめる。**

### 3.2 武田さんの 1.40〜1.50 は正しい。2026-08-29 の「訂正」の方が誤り

正本メモ「2026-08-29 私の誤り」表の **#4 の訂正（1.13 が正しい）を取り消した。**
あれは `radius_median_at_apex`（胴体の断面全体の半径の中央値）で、**胸を測っていない。**

新しい測り方 `cup_footprint_span`: 原着装のカップが体表面のどこに載っていたかを体の UV で
取り出し、同じ場所をヘレンの体で求めて広がりを比べる。UV 対応 100% / 往復誤差 0.00mm。

    面内 ×1.587 ／ 前への張り出し ×2.66

### 3.3 いまの成果物は S006 を両方とも守っていない

    面内 ×1.034（明言 1.40〜1.50）／ 厚み ×0.664（明言「潰さない」）

原因: `helen_swimsuit_fit_p.py` は `placement='bone'` のとき
`self.A = np.repeat(np.eye(3)[None], ...)` で**役割ごとの伸びを恒等に戻している**。
2026-08-31 のコメントの「骨＋恒等の方が全項目で良い」の「全項目」は
**ドナーの形の保存を測る項目**であって、ヘレンに合っているかを測る項目は当時 0本だった。

### 3.4 法則版のめくれ 28枚は解けた

**ならしの回数を 10 → 20 に上げるだけで 0 枚**（裂け 0・潰れ 0 も維持）。

| 版 | 面内 | 裂け | めくれ | 潰れ | G3b | G4a | G4b | 最深 | 厚み |
|---|---|---|---|---|---|---|---|---|---|
| 原着装（正解） | ×1.000 | 0 | 0 | 0 | 86.67 | 2.900 | 3.66 | −8.85 | 1.000 |
| いまの成果物 | **×1.034** | 0 | 0 | 0 | 91.99 | 5.330 | 8.45 | −5.04 | **0.664** |
| 法則 ならし10 | ×1.712 | 0 | **34** | 0 | 98.76 | 2.046 | 15.69 | −13.62 | 1.716 |
| **法則 ならし20** | ×1.681 | 0 | **0** | 0 | 96.39 | 1.622 | **23.95** | −12.74 | 1.694 |
| 法則 ならし20 押出し50 | ×1.686 | 0 | 8 | 0 | 98.93 | 1.688 | 22.83 | −10.53 | 1.698 |

**残る壁は「めり込み（G4b）23.95」**。押し出しを強めても 22.7 で頭打ち。ならしを増やすと
面内は 1.55 まで下がるが G4b は 43.68 まで悪化する。**トレードオフ未解決。**

再現:

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --cup-y-lam 5e-4   # いまの成果物
```

法則版は `Case.solve(use_law=True, law_smooth=20, law_clamp=True, lam_base=5.0, lam_pen=5.0)`。
**判定するだけで成果物は書き換わらない。**

## 4. 今日 新しく入った道具

| 道具 | 何をするか | 接続 | 変異試験 |
|---|---|---|---|
| `tools/cup_fit_scale.py` | **G12a/G12b** カップがヘレンのバストに合わせて大きくなっているか。**合格線は明言台帳 S006 から読む**（ソースに直書きしない） | A21 | 5/5 |
| `tools/statement_ledger_check.py` | **P1d** 絶対の明言の数値に、それを合格線として使う検査が在るか。**P1c を量の名前で引くよう修正** | A20 | 8/8 |
| `tools/plan_audit.py` | **A21** G12 が働くかを工程監査へ接続 | — | 3/3 |

**P1d が本命。** 「武田さんが数値で決めたのに、それを測る検査が1本も無い」を機械が止める。
S006 は承認から 7日間、誰も成果物を照合していなかった。

## 4.5 【取り消し】「素肌に合わせ直す」は誤り

2026-09-05 の午前にこの節へ「載せている相手が承認された相手と違う」と書いたが、**誤り**。
§−1 のとおり、`cloth2_lod0_General` submesh 0 が原作の裸の上半身で、**相手は正しかった**。
`--body general` のままでよい。誤った作業の産物は
`output/gf2-helen-swimsuit/_wrong-skin-20260905/` へ退避した。

## 5. いま最優先でやること

1. **武田さんのレンダー確認を待つ**（§ 武田さんの節）。
2. 残る不合格 **G9a厚み 1件**（帯 ×1.676 / 肩ひも ×0.720）。未着手。
3. 決定C（形に触っていない回は検査づくりを後回し）の機械化。設計していない＝**未達**。

### 採用した作り方（再現・この順で走らせる）

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
BLENDER="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/02_ソフトウェア/Blender.app/Contents/MacOS/Blender"
$P tools/helen_swimsuit_fit_p.py --body general --rigid-roles all --cup-y-lam 5e-4 --lam-pen 1.0 --save
$P tools/swimsuit_shell_restore.py --body general --cup-spread 1.6
$P tools/swimsuit_material_folder.py
"$BLENDER" -b --python "$EX/scripts/ce_build_blend.py" -- \
  --intermediate "$EX/intermediate-swimsuit/Helen.HelenSSR01-swimsuit" \
  --out "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --build-log "$EX/blends/swimsuit/build-log-swimsuit.json"
"$BLENDER" -b --python "$EX/scripts/render_char_sheet.py" -- \
  --blend "$EX/blends/swimsuit/Helen-swimsuit-flat.blend" \
  --outdir wiki/_attachments/helen-swimsuit-status/img-20260905-shell
```

**効いた3つ**（2026-09-05 に作った）:

1. `--lam-pen 1.0`（体へ入った頂点を押し出す強さを既定の 500 倍）
2. `cup_shell.build` の投影を**最近傍の三角形へ**（距離の場は近似で、0 でも表面から離れる）
3. `cup_shell.spread_cup`（カップを**解かずに**広げる。解く側で広げるとめくれる）

**採らなかったもの**（相手を取り違えていた間に作った。消していないが使わない）:
`garment_law.rebuild_cylindrical` / `garment_law.push_out` / `--law` 全般。

## 6. 検査の走らせ方（全部）

```
cd "/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01"
P=/opt/anaconda3/bin/python3
EX="/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract"
$P tools/deliverable_checks.py "$EX/blends/swimsuit/build-log-swimsuit.json"   # D1
$P tools/swimsuit_visible_checks.py                                            # V1〜V6
$P tools/swimsuit_wear_checks.py                                               # V7・W1〜W12
$P tools/swimsuit_inventory_checks.py                                          # N1〜N3
$P tools/plan_audit.py                                                         # A1〜A22
$P tools/measurement_label_check.py                                            # M1
$P tools/doc_layout_check.py --all                                             # L1
$P tools/doc_structure_check.py --all                                          # L2・L3
$P tools/statement_ledger_check.py                                             # P1a〜P1f
$P tools/fit_target_check.py                                                   # V10a・V10b
$P tools/cup_fit_scale.py                                                      # G12a・G12b
$P tools/version_compare.py                                                    # B1・B2（版の採否）
$P tools/goal_coverage.py                                                      # C1〜C4（進捗）
$P tools/doc_timeline_check.py --all
```

**2026-09-05 時点の状態**:
D1 1/1 ／ V 4/6（V3・V4）／ W 8/12（W7・W8・W11・W12）／ N 0/3 ／ **A 21/21** ／
M1 PASS ／ L1 指摘0 ／ L2・L3 指摘0 ／ **P1 4/4 PASS**（昨日の P1c FAIL は偽の矛盾。解消）／
**G12a FAIL**（面内 ×1.034。承認された拡大が入っていない＝**本物の不合格**）／ G12b PASS ／
未測定の側面 **5 個**。

**W12 の FAIL は G9a厚み が落ちていることに由来する。** 上限を外すかの判断（§ 武田さんの節）が
決まるまでは、この FAIL を例外扱いにしないこと。

## 7. 成果物の場所（今日は書き換えていない）

- Blend:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-char-extract/blends/swimsuit/Helen-swimsuit-flat.blend`
  sha256 `87da83409ee99fdadb5e500ca26a39c9a9f4bc857b2367946149885cb4689f15`
- 台帳:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/visible-set-swimsuit.json`
- **入れる前に必ず退避すること**（`_bak-<日付>/` と `.bak-<日付>`）。

成果物へ入れる手順は前日の引き継ぎ書 §8 と同じ（変更なし）。

## 8. 絶対にやってはいけないこと

- **合格線を、成果物が通るように動かさない。**
- **明言と合格線を比べる前に、同じ量を比べているか確かめる**（2026-09-05 に偽の矛盾を作った）。
- **手順の約束を例外にして通さない**（`METHOD_PROMISES`。W12 が止める）。
- **部品の名前を検査に直書きしない。** 台帳・登録表から引く。
- **合格線を検査のソースに直書きしない。** 明言台帳から読む（G12 の作り方）。
- **「多分こういう物だろう」で部品を消さない。**
- **原本（`intermediate/Helen.HelenSSR01`）へ書き込まない。**
- **成果物・台帳を退避せずに上書きしない。**
- **検査の本数・合格数を進捗として報告しない**（S009）。
- **明言台帳に決定がある論点を聞き直さない**（S011。P1 が止める）。
- **変異試験用の架空の ID を、そのソースに直書きしない。**

## 9. 捨てた案と理由（蒸し返さない）

前日の引き継ぎ書 §10 の表をそのまま引き継ぐ。今日 追加:

| 案 | 理由（実測） |
|---|---|
| カップの伸びだけ掛ける（`--cup-stretch`・法則なし） | 体からの目標が原着装のままなので、大きくした分が**体にめり込む**（G4b 8.45→26.26 / 最深 −27.97mm） |
| ならしを増やして面内を下げる | 面内 1.712→1.550 と引き換えに**めり込みが 15.69→43.68** |
| 押し出しを強める | G4b 22.7 で頭打ち。しかもめくれが 0→8 に戻る |

## 10. 残っている作業

1. **G9a厚み の上限の扱い**（武田さん判断待ち）
2. **法則版のめり込み 23.95 を下げる**（原着装 3.66）
3. 帯・肩ひも・垂れが体に合わせて作り替えられているかの検査（**GOAL-BODY-FIT の未測定の側面**）
4. 帯の厚み（法則版 1.585／いまの成果物 1.174）。原因未特定
5. 検査 R1・R2・R3・V8・V9・W2（設計だけで未実装。W11 が 6 件と数えている）
6. 台帳 21 件の仕分けの仕組み（承認済み・未着手）
7. 首以外の隙間 3 か所（Y0.898 / 1.200 / 1.226）

## 11. 武田さんとのやり取りの作法

前日の引き継ぎ書 §12 と同じ。特に:

- **報告は3分割**（①今すぐやること ②終わったこと ③まだ終わっていないこと）＋ 成果物の場所。
- **監査の詳細・道具の説明を報告に書かない**（2026-09-04「不毛」）。
- **選択肢には必ず「それを選ぶと失うもの」を書く。**
- **武田さんに考えさせない。** 決められることは自分で決めて宣言する（S011）。

## 12. 関連ファイル（実パス）

- 正本メモ:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`
- 目標と検査の対応表:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-goal-map.json`
- 明言台帳:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/output/gf2-helen-swimsuit/explicit-statements.json`
- 採用手順の計画書:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-fit-plan-20260829.md`
- 前日の引き継ぎ書（この文書が置き換える）:
  `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-swimsuit-handoff-20260904.md`
