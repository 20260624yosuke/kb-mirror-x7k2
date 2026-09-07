---
type: analysis
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-09-05
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/project-hub-index
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-fingerfix
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/finger-axis-decision.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f151_finger_axis_H.py
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f171-current-status.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md
---

# 指flip修正の候補化

## 武田さんの考え

- 全部試せばいいんじゃないか。そういう問題ではないか。
- タスクを続けて。
- 計画書は作らず指修正候補の作成に進めることを承認する。

## 決まったこと

- f151 の反転正規化の誤りを直す候補を作る。本体は触らない。
- 品質の合否は武田さんの目視のみ。機械の数値は関与しない。
- 計画書は作らない（残作業は機械的手順と目視判断だけ）。

## まだ決まってないこと

- 本体への反映（別承認待ち）。
- 選択4件（A/B/C/D）。
- 目視確認への進入。

## 捨てた案と理由

- 計画書の作成：残作業に設計の余地がなく、正本を増やして混乱の種になるため。
- 摘みの総当たり：原因が割れている箇所への対症は無限ループになるため。
- f173数値書換え：独立再計算で真の差0.00度と確定したため（project logs/t1-finger-diff-reverify.json）。私の差表は標本1つずれと順序違いの産物であり撤回する。

## 直した記録

- なし。

## セッションメモ（子）

- なし。

## 再開の入口（実パス）

- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/finger-axis-decision.json
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f173-finger-flip-fix.json

## 実装への申し送り

完成条件:

- 左手5点の差が原作正規化角度の1度以内であること。
- 候補の保存形式が別名で存在し、本体が無変更であること。
- 手の門の再測定結果が記録されていること。

絶対にやってはいけないこと:

- 本体の保存形式を承認なしに書換えない。
- 描画の数値判定をしない。品質判断は武田さんの目視のみ。
- 目視のみの断定を記録に残さない。

捨てた案とその理由:

- 反転枝の温存：回転量を保存しないことが数値で確定したため。
- 右手則の変更：完全一致しているため触らない。

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f173-finger-flip-fix.json
run: python3 -c "import json;print(json.load(open('/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/logs/f173-finger-flip-fix.json'))['criterion_met'])" ==> True
```

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/_candidate-finger-fix2/helen-h0157-repro__04ef8b79b3fa5b64_f173-fingerfix.blend
run: python3 -c "import os;print(os.path.exists('/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/_candidate-finger-fix2/helen-h0157-repro__04ef8b79b3fa5b64_f173-fingerfix.blend'))" ==> True
```

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend
run: python3 -c "import hashlib;print(hashlib.sha256(open('/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/helen-h0157-repro.blend','rb').read()).hexdigest()[:16])" ==> 04ef8b79b3fa5b64
```

### 終わったら次に取る承認

- 本体への反映の承認（別承認）。

## 機械化した指摘

- 独断の基準の持ち込み / 再発しうる / 機械判定できる / f171三工程説明門。
- 摘みの総当たり / 再発しうる / 機械判定できる / 原因順序の宣言（人間判断として残す）。
- 較準史の欠落 / 再発しうる / 機械判定できる / f172正本鮮度門。

## 関連リンク

- wiki/builds/gf2-helen-repro-v51-handoff.md
- wiki/builds/gf2-helen-repro-v51-current.md
- wiki/analyses/gf2-helen-agent-task-orders.md
