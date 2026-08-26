---
type: build
title: "coloso-intake ― HDD未取り込みColoso講座の逐語移植パイプライン設計"
status: active
confidence: high
evidence_level: source-backed
sources: []
created: 2026-08-25
last_reviewed: 2026-08-25
tags: [build, coloso, intake, pipeline]
---

# coloso-intake 設計(v2)

外付けHDDにある未取り込みColoso講座を、LLM Wiki の `raw/_coloso/` 構造へ**本文を一切書き換えず**移植するための機械パイプライン。ハルシネーション対策の核は「LLMが判断しないことを増やす」ことで、全判断を本書のルールIDと監査スクリプトの検査IDに固定する。

- 正本スクリプト: `tools/coloso_intake.py`(骨格生成) / `tools/coloso_intake_audit.py`(監査) / `tools/coloso_transcribe.py`(文字起こし・symlinkパッチ済み)
- 品質ゲート: [[coloso-intake-quality-gate]] 扱いの `wiki/builds/coloso-intake/quality-gate.json`
- 独立レビュー: 2026-08-25 サブエージェント審査 verdict「修正後に採用可」の must-fix 5件(M1〜M5)・should-fix 6件(S1〜S6)をすべて反映(本書の各所に記載)

## 対象(第0チェック A0 の正本)

`/Volumes/HDD_02/01_イラスト　データ/coloso/` 配下のうち、**動画があり未取り込み**の7講座。KB側slugはコロンを使わない既存流儀(`YYYY_MM_DD_<講師>`)に統一する(N3)。

| HDDフォルダ | KB slug | 実測動画数 | 備考 |
|---|---|---|---|
| `2025:09:27_イクシー_2` | `2025_09_27_ixy_2` | 23 | **パイロット**(講師名正綴 ixy は武田さん凍結済み) |
| `2024:04:22_ixy` | `2024_04_22_ixy` | 82 | |
| `2024:04:24_ne-on` | `2024_04_24_ne-on` | 40 | |
| `2024:04:24_晃田ヒカ` | `2024_04_24_晃田ヒカ` | 22ペア | 講師名正綴「晃田ヒカ」凍結済み。**ペアモード**: 1ページ=動画(mp4)+音声(m4a)(下の例外注記) |
| `2024:04:24_雨傘ゆん` | `2024_04_24_雨傘ゆん` | 118 | |
| `2024:06:26_2SHAM` | `2024_06_26_2SHAM` | 33 | |
| `2024:06:27_ODIA ACADEMIY` | `2024_06_27_ODIA_ACADEMIY` | 67 | スペース→アンダースコア |

- 対象外: 動画の無い2023年7講座(HAREN/血羅/ホ・ソンム/gatan/chan/モ誰/チョー=資料のみ)、および既取り込み7講座(chan-02/hide/ひづるめ/マーセ/nekojira/佐々/ye_jji)。
- 上の動画数は拡張子セット(`mp4/mov/mkv/m4v/webm/mp3/m4a/wav`)での再帰カウント。A0で毎回再計数し一致を見る。
- **例外(2026-08-25 武田さん承認・同日再決定)**: 晃田ヒカのみ**ペアモード**。番号フォルダ01〜22ごとに動画(mp4画面録画)+音声(m4a)が1組ずつあり、1フォルダ=1ページで両方を `_attachments/NN<ext>` symlink し、本文は `## Transcript` + 動画埋め込み + 音声埋め込みの2行(順序固定)。**transcript は音声側から生成する**(mp4 の音声トラックは無音の実測: mean -81.9dB/-72.7dB、max -36.7dB/-21.1dB。m4a は音声レベル: mean -25.0dB/-27.4dB、max 0.0dB)。監査は A0=ペア数一致、A1=ページ数==ペア数==symlink数÷2、A4=ペアモード時のみ埋め込み2行を許可。A5/A6 は transcript が NN 名で生成されるため無変更。ツール側は対象表の `pair` フィールドと mapping.json schema v2(`pairs` 配列)で対応。当初は m4a除外案だったが代表文字起こし失敗(Whisper 本文ゼロ+幻聴ループ「ご視聴ありがとうございました」)で音量実測→前提覆り武田さんが再決定した(変遷参照)。

## ルール(R1〜R8・v2)

- **R1 1動画=1ページ**: 元動画ファイル1本につき講座ページ1枚。監査 A1。
- **R2 実体コピー禁止・symlink参照**: KB側 `_attachments/NN<ext>` は HDD 実体へのシンボリックリンク。KB側SSDへの動画コピーはしない(SSD空き333GiBに対し対象動画200GiB超のため)。size+mtime を mapping.json に記録し、監査で照合する(S5)。HDDの同名差し替え痕跡(`_h265_pilot` 等)を検知可能にするため。監査 A3。
- **R3 命名**: ページ名 `coloso_<講師>NN <タイトル>.md`。タイトル未確定は `(未確認)` を使う。**推測タイトル禁止**。タイトル確定源は①Coloso公式カリキュラム ②武田さん入力のみで、確定したら frontmatter `title_source` を `official-curriculum` / `user-input` に更新する(M5)。監査 A2。
- **R4 初期本文の構造制約**: frontmatter 直後の本文は `## Transcript` 見出し + 全vaultパス埋め込み `![[raw/_coloso/<slug>/_attachments/NN.<ext>]]` のみ。要約・解説・章立てを書かない。監査 A4(余分な非空行があれば不合格)。
- **R5 文字起こしエンジン固定**: `tools/coloso_transcribe.py` のみ(独自再実装禁止)。
- **R6 逐語保持**: 本文は whisper 出力ベースの逐語。ツール内の機械的silence-bug除去(無音幻聴行の削除・連続重複行の間引き)以外の編集禁止。監査 A6 が `.json/.vtt` から逐語本文を**再計算**しページと一致することを機械検証する(S1)。
- **R7 完了定義**: 文字起こし完了 = `.txt/.vtt/.srt/.json/.tsv` の5種が KB側 `_attachments/` に生成され非空、かつページへ transcript 節が追記済み。5種揃わないものは「未完」と記録し成功扱いしない(S4)。監査 A5。
- **R8 スコープ分離**: 本パイプラインは `raw/_coloso/` への逐語移植まで。`wiki/sources/` の要約ページ作成は含まない(transcriptスキルの「wiki/に触らない」規律との衝突回避・二重管理防止)(M4)。要約は従来どおり `/llm-wiki ingest` で別工程として行う。

### NN割当(M2)

- パイロット講座の元ファイルは `CamX 2025-9-26 03.30.37.mov` 形式で章番号を持たないため、**撮影タイムスタンプ昇順**で NN を採番する(ソートキーは intake 実行時に固定し mapping.json に記録)。
- NN↔元ファイル(サブフォルダ名込み)の対応表を検収時に武田さんが承認する。**サブフォルダ名(`いくしー2_3`等)が章番号を意味するかは推測しない。**
- 桁数は `max(2, 本数の桁数)`(雨傘ゆん118本なら3桁)。
- 他講座のソートキーは各講座のパイロット承認時にその都度凍結する。

## ディレクトリ契約

既定はフラット構成。**章サブフォルダ構成は講座ごとのユーザー承認時に選択できる**(2026-08-25 雨傘ゆんで初適用・B方式承認)。章名は HDD の `ビデオ/` 直下フォルダ名(`01`・`04_01`等)をそのまま使い、`ビデオ/` 直下に孤立したファイルは自身のファイル名接頭辞(`16_03_編集.mov`→`16_03`)から機械導出する(推測なし)。

```
raw/_coloso/2025_09_27_ixy_2/          ← フラット(既定)
raw/_coloso/2024_04_24_雨傘ゆん/        ← 章サブフォルダ(承認済み)
├── mapping.json                  ← NN ↔ 元ファイル対応(size+mtime込み/S5)
├── _attachments/
│   ├── 001.m4a → /Volumes/HDD_02/...(シンボリックリンク/R2・全NNがここに集約)
│   └── ...
├── 01/
│   └── coloso_雨傘ゆん_001 (未確認).md   ← intakeページ(R3/R4)
├── 16_03/
│   ├── coloso_雨傘ゆん_082 (未確認).md
│   └── coloso_雨傘ゆん_084 (未確認).md   ← 直下ファイルも章ラベルから帰属
└── 20_02/
    └── coloso_雨傘ゆん_118 (未確認).md
```

- 監査 A1 はフラットと章サブフォルダの両配置を収集して照合する。
- intake スクリプト自体はフラット生成のみなので、章サブフォルダ承認講座では intake 後に移動工程を実施する(mapping.json の original パスからの機械導出)。

## 監査項目(tools/coloso_intake_audit.py)

| ID | 検査内容 | 対応ルール |
|---|---|---|
| A0 | 対象7講座の実測動画数が対象表と一致 | 対象定義 |
| A1 | 講座ごとに ページ数=symlink数=mapping件数 | R1 |
| A2 | ページ名正規表現+frontmatter必須(title/created/tags/title_source)+`(未確認)`⇔title_source整合 | R3 |
| A3 | symlinkの状態分類: OK / LINK_MISSING / TARGET_MISSING / TARGET_OUTSIDE(宣言ルート外) / SIZE_MTIME_MISMATCH / ENV_BLOCKED(HDD未マウント=環境ブロック扱いで欠陥としない・再実行待ち)(S2) | R2 |
| A4 | 本文構造制約(frontmatter+`## Transcript`+埋め込み1行のみ) | R4 |
| A5 | transcript節を持つページで5種ファイル存在+非空 | R7 |
| A6 | 逐語本文の再計算照合(build_verbatim_body結果≡ページ内textブロック) | R6 |

- 終了コード: 0=合格 / 1=不合格あり / 3=ENV_BLOCKED(環境ブロックのみで不合格なし)。
- 不合格が1件でもある講座の量産・展開は停止条件(quality-gate 参照)。

## 検収(ユーザー受入)ポイント

1. NN↔元ファイル対応表の承認(順序決めつけの防止/M2)
2. symlink動画が Obsidian で実際に再生できることの目視確認(**未実証**のため必須/N1)
3. 代表1〜2本の文字起こしページの目視(逐語であること・幻聴除去の範囲感)
4. 監査レポート(A0〜A6)全項目 PASS の確認

## 決定ログ(独立レビュー反映)

- M1: `coloso_transcribe.py` が symlink を resolve し transcript 出力が HDD 側に飛ぶ問題 → 直パスが symlink 且つ存在する場合は resolve しない最小パッチ。出力はKB側 `_attachments/` に落ちる。
- M2: パイロット実物が `CamX …mov` 形式で NN 無し → symlink側改名+NN採番規則+対応表検収承認。
- M3: 講座ごと=別ファミリ。イクシー_2の承認は他講座へ流用不可。残り6講座も各講座で代表1〜2本の文字起こし+監査+承認を挟む。
- M4: intakeページ設置層を `raw/_coloso/<slug>/` に限定。wiki/sources 要約は本パイプライン外。
- M5: 講師名表記揺れ(イクシー/いくしー/ixy)→ 正綴を武田さんが凍結(ixy)+frontmatter `title_source` で監査可能に。
- S1/S2/S3/S4/S5/S6: 逐語再計算照合/symlink3状態分類/対象表の第0チェック/5種必須明記/size+mtime記録/quality-gate設置場所を `wiki/builds/coloso-intake/` 配下に決定(既存 batch2 流儀準拠)。

## 変遷

- 2026-08-25: v1(R1-R8+段取り)を武田さんの指示でサブエージェントが独立レビュー → must-fix 5件反映の v2 を策定・武田さん承認。パイロット=イクシー_2、講師名正綴=ixy で凍結。
- 2026-08-25: 雨傘ゆん講座(118本)で intake・検収承認。実測の結果 RPReplay mp4 43本は無音収録(全数音量計測 mean −67〜−91dB＋6本のwhisper実走で幻聴のみ)と判明し、R7どおり「未完(音声なし)」として記録することを武田さん承認。同講座のみ章サブフォルダ配置(B方式)も武田さん承認し、監査A1を両配置対応に拡張。
- 2026-08-25: 晃田ヒカ講座の intake 開始。講師名正綴「晃田ヒカ」で凍結(カード回答)。章フォルダ内の mp4 と m4a がペアで再生時間ほぼ同一のため、**m4a 除外への変更を武田さん承認**(対象表 44→22、`coloso_intake.py` に `media_exts` フィールド追加・監査 A0 も同一ヘルパーに統一)。
- 2026-08-25: 晃田ヒカの代表文字起こし(NN01)が失敗(Whisper 本文ゼロ)。音量実測で **mp4=無音・m4a=講義音声本体** と判明し「内容重複」前提が覆った → 武田さんが **ペア統合モードへ再決定**。1ページ=動画+音声埋め込み、transcript は音声側から生成。`coloso_intake.py` に pair モード(mapping.json schema v2)追加、監査 A0/A1/A3/A4 を対応(A5/A6 無変更)。誤前提版の intake 成果物と whisper 幻聴副産物は削除済み(HDD側は未変更)。
- 2026-08-26: 文字起こし残り151本(ixy 69・雨傘ゆん 61・ne-on 20・晃田ヒカ 1)について、実測1本中央5〜6分＝**純計算13〜15時間・GPU占有＋メモリ約6GB** を武田さんへ提示 → **「リソースを回せない・プライオリティが低い」を理由に保留を武田さんが決定**。再開条件: Mac の空き時間帯（夜間放置）または高スペック機の導入後。完了済み134本はスキップ方式で保護されており、再開時は未完分のみ再計算。2SHAM/ODIA の2講座は移植自体も未着手。

## 関連リンク

- [[coloso-transcribe]] 相当の運用: `.claude/skills/transcript/SKILL.md`
- 品質ゲート: `wiki/builds/coloso-intake/quality-gate.json`
