---
type: analysis
title: 一本化revision 4 U0〜U3 実装承認受領書
status: active
confidence: high
evidence_level: user-stated+source-backed
last_reviewed: 2026-09-01
parent: ../_index.md
---

# 一本化revision 4 U0〜U3 実装承認受領書

## 判断

- 承認カード回答: `U0〜U3を承認 (Recommended)`
- 承認時刻: 2026-09-01T23:09:43+09:00
- 承認資料SHA-256: `f99dc8de1fea587b8b637e2ed9c4c754cb80e5ddaa6961e5db31a3ebca0e02ea`
- タスク入口SHA-256: `7a2eba7eb378acc146244c223b77eb2c46437bb7d53b7ee2e9393b1270844c52`

武田さんから、主担当がこのセッション内でLuna、Sol、Claude Opusを呼び分け、別タスクへの手動指示や結果運搬を求めない運用であることを説明した後に承認を得た。

## 許された次状態

- U0: 固定12入力の再測定とdrift判定。
- U1: 一時領域での監査一式実装、正常・単一変異・復元試験、writer分類の独立確認、H0157限定接続。
- U2: f154候補とG10/S6/S8 gapの読み取り比較、Claude Opusによる因果審査。
- U3: 第1search-contract 1件の作成と別sessionによる独立review。

U3の独立review済み第1search-contractを提示した時点で、再び承認待ちへ戻る。

## 許されていないもの

- 実探索、f154展開、f166全量走査。
- `helen-h0157-repro.blend` の変更、Blender前面GUI。
- H0157以外のアクション、水着化、U4以後。
- `/Users/takedayousuke/.codex/skills/brainstorm/` と共有 `/Users/takedayousuke/.codex/hooks.json` の直接変更。

共有hooksのHelen 3枝は候補差分までとし、現物への適用には環境軸の終了時SHA一致と別承認が必要。

## U0結果

- 結果: PASS。
- 12入力の既存11ファイルは固定SHAと一致。
- project `quality-gate.json` のfull file SHAも固定値と一致。
- `/execution_audit/current_state_inputs` はU1導入前のため未存在。固定projection SHAをU0で生成した。
- 旧shared quality gateの`plan`はPASS。
- 詳細: `/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz/06_repro-v51/audit/runs/20260901T230943+0900/u0-snapshot.json`

## 証拠の境界

この受領書はU0〜U3の実装承認とU0同一性確認の証拠であり、監査導入、実hook到達、原作一致、Blend完成、運用開始の証拠ではない。

## 使わなかったもの・落とした情報

- **捨てたもの**: 武田さんが別エージェントへ個別に指示し、結果をこの会話へ運ぶ方式。
- **手元でどう変わるか**: 武田さんの操作は本会話の承認判断だけになり、モデル間の仕事の受渡しは主担当が行う。3Dの見た目は変わらない。
- **戻せるか**: 戻せる。モデル分担を変更する場合は、U1/U2/U3の役割と停止条件を修正して別承認を取る。
