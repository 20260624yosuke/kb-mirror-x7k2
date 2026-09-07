---
type: analysis
title: 指flip修正候補の計画
status: active
confidence: medium
evidence_level: user-stated
last_reviewed: 2026-09-05
brainstorm_status: active
scope:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51
entry_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-finger-fix/_index.md
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/finger-axis-decision.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f151_finger_axis_H.py
background_paths:
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/run-state.json
  - /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/builds/gf2-helen-repro-v51-handoff.md
---

# 指flip修正候補の計画

## 武田さんの考え

### 2026-09-05 摘み回しへの批判

> こういう会話は正直不毛に感じる。白飛び抑えても、白飛びなったのは別の問題解決のはずみだった場合、
> 白飛びをただ抑え込んだだけじゃ、問題解決ループで一生プロジェクトがゴールできないよね？

原因仮説・区別試験なしの候補作りをやめ、原因順序に戻す。以後は原因を数値で割ってから候補を作る。

### 2026-09-05 問題解決のやることと計画の要否の問い

> 問題解決のためのやることは理解できましたか？計画を作成する必要はありますか？

指flip修正候補の計画をこのメモに作る。実装先は武田さんが決める。

## 決まったこと

- 左手キーの反転式が回転量を保存していない（左人差指3・13コマで原作88.4度→適用171.7度、右手は完全一致）。
- 修正は反転式の再導出であり、見た目の微調整ではない。
- 本体への反映は別承認。候補は別名で作る。
- 画像認識は当座禁止、品質の合否は武田さんの目視のみ。

## まだ決まってないこと

- 修正候補の作成手順の方針承認（この回のカードで聞く）。
- 候補の目視結果と本体反映の可否。

## 捨てた案と理由

- 摘みの総当たり（主光・前段倍率の振り）は原因に触れないため止めた。
- 全300コマの目視確認は数値の区別試験で代替する。武田さんが見るのは flagged 枠のみ。

## 直した記録

- なし。

## 再開の入口（実パス）

- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-finger-fix/_index.md
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/ledger/finger-axis-decision.json
- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/scripts/f151_finger_axis_H.py

## 実装への申し送り

- 完成条件：5点の差が1度以内、候補の同一性記録、本体の無変更、手の門の再測定の記録。
- 絶対にやってはいけないこと：本体の保存形式の書換え、目視のみの断定、中立値の発明。
- 捨てた案と理由：摘みの総当たりは原因に触れないため採用しない。

```done-when
path: /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/blends/_candidate-finger-fix/helen-h0157-repro__04ef8b79b3fa5b64_f172flip.blend
run: python3 scripts/f171_three_task_status_gate.py check logs/f171-current-status.json ==> "ok": true
run: python3 scripts/f172_canonical_freshness_gate.py check ==> "ok": true
```

### 終わったら次に取る承認

- 本体への反映の承認（別途）。

## 機械化した指摘

- 摘みの総当たりは不毛 / 再発しうる / 機械判定は計画書の原因記載の有無 / 人間判断として残す（候補作成前の原因特定の必須化は未機械化）。
- なし（この回は上記1件のみ）。

## セッションメモ（子）

- /Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-helen-finger-fix/sessions/20260905-finger-flip-plan.md

## 関連リンク

- wiki/builds/gf2-helen-repro-v51-handoff.md の #73（D1指の鉤爪）
- wiki/analyses/brainstorm/gf2-helen-repro-resume/_index.md（H0157再開の親）
