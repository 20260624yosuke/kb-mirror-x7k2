---
type: analysis
title: Mac (M1 / 16GB Mac mini) での AI 画像生成・LoRA 自作の現実度と方針
created: 2026-06-02
prompted_by: 「画像生成 AI に興味があり、資料が多いので自分で LoRA を作れたら創作の質が上がると思っている。素人。Mac (M1 / 16GB Mac mini) で ComfyUI / reForge 環境を作る場合、NVIDIA GPU との差は生成速度だけ? LoRA 学習は現実的な速度か? LLM はプロンプトや LoRA をコードを書くようにタスク実行できるか?」
sources: []
related: [[takeda-yohsuke]]
status: active
confidence: medium
evidence_level: ai-hypothesis
last_reviewed: 2026-06-02
tags: [ai-image-generation, lora, comfyui, mac, apple-silicon, hardware, web-research]
---

# Mac (M1 / 16GB Mac mini) での AI 画像生成・LoRA 自作の現実度と方針

> [!note] 根拠レベル
> 本ページは Web 検索ベースの一般情報 + 武田さんの環境(M1 / 16GB Mac mini)への当てはめによる **AI 推論(ai-hypothesis)**。
> ローカル wiki の source 由来ではない。数値は 2026-06 時点の外部記事の実測報告で、武田さんの実機検証ではない。

## 問い

素人前提で「手持ち資料を活かして自作 LoRA → 創作の質を上げたい」。
今の Mac(M1 / 16GB Mac mini)で AI 画像生成・LoRA 学習はどこまで現実的か。
NVIDIA との差は速度だけか。LLM はプロンプト/LoRA タスクをコードのように実行できるか。

## 現在の結論

- **生成(絵を出す)**: Mac でも動く。NVIDIA との差は主に速度(数倍遅い)だが、速度「だけ」ではない(下記)。
- **LoRA 学習(作る)**: SDXL / Illustrious / Anima 系の LoRA 学習は、**M1 / 16GB ではローカル非現実的**。速度ではなく **メモリ(ユニファイド 16GB)** がボトルネック。
- **方針**: 今の Mac は「生成のお試し」と「データセット準備」担当。**LoRA 学習はクラウド GPU(Colab / RunPod 等)に逃がす**のが、素人がつまずかない現実解。
- **LLM の役割**: プロンプト生成・データセット前処理(タグ付け/整理)・設定ファイルや ComfyUI ワークフロー(JSON)生成は LLM で代行可。**生成/学習そのものの計算(GPU の仕事)は LLM では実行できない。**

## 根拠

### 1. Mac vs NVIDIA は「速度だけ」ではない(生成フェーズ)

| 観点 | 中身 |
|---|---|
| 速度 | 最大の差。NVIDIA の数倍遅い(メモリ帯域差: M3 Max 400GB/s vs RTX4090 1,008GB/s) |
| ソフト互換性 | CUDA 前提機能が不可。`xformers` 非対応、一部カスタムノード/拡張が Mac で動かない or 回避フラグが必要 |
| メモリの考え方 | Mac はユニファイドメモリで大容量を積みやすく VRAM 不足で落ちにくい長所。ただし帯域が狭く遅い |
| 安定性のクセ | MPS では稀に真っ黒画像/NaN、fp16(半精度)無効化が必要な場面がある |

### 2. LoRA 学習の実測時間(外部報告、画像 30〜40 枚 / SDXL 系)

| 環境 | 1 本あたり |
|---|---|
| RTX 4090 | 20〜30 分 |
| M3 Max / M4・64〜96GB | 2〜4 時間(NVIDIA の 4〜6 倍遅いが実用域) |
| M2 Pro・32GB | 実用の下限 |
| **M1 / 16GB(武田さんの環境)** | **SDXL 系は実質不可(メモリ不足 OOM / 激重スワップ)。SD1.5 LoRA ならギリだが狙いの Anima/Illustrious とは別系統** |

- SDXL 級 LoRA 学習は実質 24GB 以上推奨。16GB は OS と共有で実効 ~10GB 強しか残らず、安定して回らない領域。
- 「現実度」の本質は 1 本の時間より **試行回数**。素人は 1 発で決まらず 3〜5 回やり直すのが普通で、遅い環境ほど総コストが膨らむ。

### 3. LLM ができること / できないこと

| やること | LLM の役割 |
|---|---|
| プロンプト(タグ整理・自然言語化・改善・ワイルドカード) | ◎ 得意 |
| データセット準備(資料の自動キャプション/タグ付け・選別・リネーム・重複除去) | ◎ マシン非依存・**手持ち資料が多い武田さんの強みが直結**。LoRA の質は前処理で大半決まる |
| ComfyUI ワークフロー(JSON)/ kohya 設定・学習コマンド生成 | ○ 生成・編集可 |
| 生成そのもの / 学習そのもの(画像出力・重み更新) | ✕ GPU の計算ジョブ。LLM のテキスト処理では実行不可 |

> 比喩: 「仕込み・レシピ・段取り = LLM / 加熱 = GPU」。

## 推奨ルート(M1 / 16GB 前提)

1. **データセット準備を今すぐ手元で開始**(資料のタグ付け・選別)。マシン非依存で、学習前の最重要工程。LLM 自動化と相性最高。
2. **LoRA 学習はクラウド GPU を 1〜2 回試す**(Colab / RunPod 等、数百円/回で 4090 級)。試行錯誤の段階こそ速いマシンが効く。
3. **生成体験は Mac で SD1.5 / 軽め設定から**慣れる。Anima/Illustrious(SDXL 系)生成も低解像度なら検証可。
4. 将来本格化するなら、96GB Mac 買い替えより **クラウド学習の方が費用対効果が高い**可能性。

## 矛盾・未確定

- 武田さんの実機(M1 / 16GB)での生成・学習の**実測は未検証**。本ページは外部記事の数値に基づく推定。
- 「Neo」= Forge Neo は Apple Silicon(MPS)非対応。Mac で Forge 系を使うなら reForge が相対的に向く、という外部情報がある(未検証)。
- Anima(CircleStone Labs × Comfy Org / 2026-05-15 正式版)は ComfyUI ネイティブ対応。Mac/MPS での実用速度は要検証。
- クラウド GPU の具体サービス選定・コスト試算は未実施(次の問いの候補)。

## 関連リンク

- [[takeda-yohsuke]]
- (将来エンティティ化候補: Anima / ComfyUI / Forge Neo / reForge / kohya_ss)

## 参照(外部 Web、2026-06 取得)

- Anima — Civitai 解説 / GIGAZINE(2026-05-15 正式版)
- Christina Souch — Qwen Image LoRA Training on Apple Silicon
- defraglife.net — Mac kohya_ss LoRA 学習ガイド
- Medium(tchpnk)— ComfyUI on Apple Silicon(xformers 非対応・MPS)
- Puget Systems — Stable Diffusion LoRA Training GPU Analysis
