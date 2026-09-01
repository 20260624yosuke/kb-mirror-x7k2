---
type: analysis
title: 一本化revision 4 U0〜U3 実装承認資料
status: draft-unapproved
confidence: medium
evidence_level: user-stated+source-backed+inferred
last_reviewed: 2026-09-01
parent: ../_index.md
---

# 一本化revision 4 U0〜U3 実装承認資料

## この資料で判断すること

一本化revision 4を実行へ移す最初の範囲として、U0〜U3だけを承認するかを判断する。
ここで承認しても、実コード探索、f154展開、f166全量走査、Blend変更、原作一致の受入れ、他アクションへの展開は始めない。

承認対象は次の4段階である。

1. U0: 現行入力12件を再測定し、1件でも違えば停止する。
2. U1: 監査一式を一時場所で実装・試験し、復元可能な形でH0157へ限定接続する。
3. U2: f154候補とG10/S6/S8の未解決点を読み取りだけで比較する。
4. U3: 最初に調べるコードを1契約だけに絞り、別役割が根拠を直接照合した後、実探索前で承認待ちに戻す。

## 承認対象の固定版

| 対象 | SHA-256 | 意味 |
|---|---|---|
| 一本化revision 4 | `04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a` | 今回の実装仕様 |
| 現在位置 | `5bb60fb5fab92d7fa8c8d310b4318f6121ef67df8aadca9b932d7b61f56ad87e` | 再開入口 |
| 具体計画 | `cee7c93ba0233d9cb6bdf035b1abfe9f1687f5d2184ec43ac3d5d4993fd3ab3f` | 現行入力拘束と試験 |
| 最終独立review受領書 | `83ecf2868e835bd3cd6466d19c42be58a3ad85533a0f4a4bff4d58f03a41b7ea` | Critical 0 / Major 0 / Minor 0 |
| 計画承認受領書 | `637db144b2f67a2778b2228914f4251f9895b5f1939ac598f8675988466a150f` | U0〜U3承認資料作成まで許可 |

計画本文・現在位置・具体計画・最終review受領書は凍結する。実装記録、承認受領、drift reportは別ファイルへ保存し、レビュー対象へ書き戻さない。

## U0で再測定する12入力

| input_id | 実パス | 現在のSHA-256 |
|---|---|---|
| `kb-current` | `wiki/builds/gf2-helen-repro-v51-current.md` | `5bb60fb5fab92d7fa8c8d310b4318f6121ef67df8aadca9b932d7b61f56ad87e` |
| `kb-cleanup` | `wiki/builds/gf2-helen-cleanup-task-entry.md` | `6a390e6d1ddf87f702550a4e4dbaa236813f714336ea45dd6765c1e1acec6d3a` |
| `project-run-state` | `06_repro-v51/run-state.json` | `b176b17bb1d1cb9c61573f8ab070fe67170ed57c69ed2fcc7f2e21e60839fc8e` |
| `kb-audit-rev4` | `wiki/builds/gf2-helen-repro-execution-audit-plan-20260830.md` | `c690d7be9986eca7f24930ffdeb45255a0f7e3b596fb0264879a2bab9b9fa7d5` |
| `kb-unified-rev4` | `wiki/builds/gf2-helen-deliverable-unified-route-plan-20260831.md` | `04521a242adfb896980e0a0bd7fab2c61960bff4a528c1ce07b1b4bd3447333a` |
| `project-quality-gate` | project root `quality-gate.json` | 現物SHA `f7b29ca63f0d93d28f19f3fa34d54789c09493ad42ee26541b7f93ef191ffa96`。登録値は `/execution_audit/current_state_inputs` を除外した固定canonical projectionをU0で生成 |
| `h0157-blend` | `06_repro-v51/blends/helen-h0157-repro.blend` | `04ef8b79b3fa5b64b9d7e3496a9adc184f10c07d9ee9758caebd289ddbb6d7f5` |
| `p0-writers` | project root `.audit-bootstrap-20260830/audit/writers.json` | `e09ca43104e6d163bf6b4b825d1e13be3e50084b7aaa7b3e4c826f6642302aee` |
| `p0-evidence-index` | project root `.audit-bootstrap-20260830/audit/evidence-index.json` | `829a73ec998e37befb9656f280c5e51614d1771bc3a9587c5f60379787e3f0b9` |
| `p0-review-findings` | project root `.audit-bootstrap-20260830/audit/review-findings.json` | `3333d01abb9f5417992b8b44d3bf7d3f8590fb796d0712bff8fda6063c83ebaf` |
| `p0-bootstrap-status` | project root `.audit-bootstrap-20260830/bootstrap-status.json` | `c5be5ee4a7a2f53421c88479824791c7c4ec5813cf609c73b0255224f4ca1110` |
| `kb-final-review` | `sessions/20260901-unified-route-revision4-independent-review.md` | `83ecf2868e835bd3cd6466d19c42be58a3ad85533a0f4a4bff4d58f03a41b7ea` |

1件でもSHA、絶対パス、件数、IDが違えば `EA_KB_SNAPSHOT_STALE` とdrift reportを残し、U1へ進まない。自動で新しいSHAへ追従しない。

## model実IDと役割

| actor role | 実ID | 推論強度 | 許す作業 | 許さない作業・停止条件 |
|---|---|---|---|---|
| `u0-fixed-inventory` | `gpt-5.6-luna` | medium | 固定12入力の存在・SHA・件数・JSON pointer転記 | 原因、優先順位、承認、原作一致を決めない |
| `u1-implementer` | `gpt-5.6-sol` | xhigh | schema、guard、登録簿、hook本体、試験fixtureを承認範囲内で実装 | 未登録capability、Helen成果物変更、無断の範囲追加を実装しない |
| `u1-independent-verifier` | `gpt-5.6-sol` | xhigh | 別actor IDで入力・コード・試験結果を直接照合 | 制作actorの成功報告を根拠にしない。自分の実装を自分で受領しない |
| `u2-mechanical-inventory` | `gpt-5.6-luna` | medium | f154/G10/S6/S8の実在入力、既知署名、重複状態を読み取り列挙 | 因果、候補採用、探索順を決めない |
| `u2-causal-review` | `claude-opus-5` | 実行環境のhigh相当 | f154候補とG10/S6/S8 gapの因果接続を原典とKBから審査 | Claudeが利用不能ならGPTへ無断代替せず技術的停止 |
| `u3-contract-author` | `claude-opus-5` | 実行環境のhigh相当 | 第1search-contractを1件だけ作る | 実探索、展開、抽出、候補採用はしない |
| `u3-independent-verifier` | `claude-opus-5` | 実行環境のhigh相当 | 別session・別actor IDで契約と原典を直接照合 | 著者と同じsession、自己受領、契約外探索を禁止 |

Claude Codeの現在設定は alias `opus`、CLI版は `2.1.228`、実環境で観測済みの解決IDは `claude-opus-5`。実行開始時に必ず `requested_model=opus` と `resolved_model_id=claude-opus-5` を記録する。別IDへ解決された場合は能力の高低を推測せず停止し、配分変更の承認を取り直す。

代替順は設定しない。Lunaの上限到達時は固定作業だけを主担当が引き取れるが、Claude必須の因果審査と契約作成は無断代替しない。

## U1で許す最小capability

`approved_capabilities[]` に次だけを登録する。

| capability_id | 許す結果 |
|---|---|
| `u0.snapshot.verify` | 12入力の再SHA、集合検査、drift report |
| `u1.bootstrap.stage` | 一時ディレクトリへの監査一式生成と検査 |
| `u1.audit.begin-finish` | 登録操作のbegin/finish、read-set再照合、append-only履歴 |
| `u1.writer.classify` | P0 writer 54本の独立分類とreview receipt。自動promoteはしない |
| `u1.hook.observe` | H0157パス限定のPreToolUse/PostToolUse/Stop実イベント記録 |
| `u1.rollback.verify` | 既存設定・既存成果物の復元とSHA照合 |
| `u2.inventory.compare` | f154/G10/S6/S8入力の読み取り比較 |
| `u3.search-contract.design` | 第1search-contract 1件の作成と独立review |

未登録CLI、schema項目、hook分岐、actionは `EA_OPERATION_UNAUTHORIZED`。新capabilityは計画差分と別承認が無ければ追加しない。

## Codex hook設定の提案差分

変更対象は `/Users/takedayousuke/.codex/hooks.json` だけ。開始時の現物SHAは `e2e70d262b040e6e2210de4b7c002bb78b922db35f734447584c735b1d1b083a`。既存のContext Harness、brainstorm adapter、prose guardは削除・置換しない。

`tools/helen_route_hook.py` を作成してから、次の3枝だけを追加する。script自身が対象パス外を即時no-opにし、他プロジェクトの操作内容・状態を変更しない。

```json
{
  "PreToolUse": {
    "matcher": "*",
    "command": "python3 \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/helen_route_hook.py\" pre-tool --project-root \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz\"",
    "statusMessage": "Checking Helen H0157 operation authorization",
    "timeout": 30
  },
  "PostToolUse": {
    "matcher": "*",
    "command": "python3 \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/helen_route_hook.py\" post-tool --project-root \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz\"",
    "statusMessage": "Recording Helen H0157 operation result",
    "timeout": 30
  },
  "Stop": {
    "command": "python3 \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/tools/helen_route_hook.py\" stop --project-root \"/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/01_イラスト/07_3D資料/gf2-helen-starlit-waltz\"",
    "statusMessage": "Checking Helen H0157 terminal evidence",
    "timeout": 30
  }
}
```

これは設定ファイルそのものの完成JSONではなく、既存配列へ追加する3要素の内容を示す差分仕様である。実装時はJSON構文検査後に、既存各hookが残っていることをキー・command・matcher単位で比較する。

## U0〜U3の書込み境界と成果物

| 段階 | 書いてよい場所 | 完了時に必要な実物 | 停止点 |
|---|---|---|---|
| U0 | `06_repro-v51/audit/runs/<run_id>/` のsnapshotとdrift reportだけ | 12件のpath/SHA/size/mtime、set SHA、取得コマンド | 差分があればU1前停止 |
| U1 | `06_repro-v51/audit/`、承認済みWiki `tools/`、project root `quality-gate.json`のexecution_audit節、上記hook設定 | state、registry、evidence、guard、fixture、model-routing、writer分類receipt、実hook試験、rollback試験 | いずれかの正常→変異FAIL→復元PASSが欠ければenforcement_readyにしない |
| U2 | audit run配下の読み取りinventoryと比較receipt | f154/G10/S6/S8ごとの入力・gap・重複状態・因果審査。実G10はP3B blockedを維持 | 実在要件・gapへ結べない、入力欠損なら停止 |
| U3 | audit run配下の `search-contract.candidate.json` と独立review receipt | requirement/family/gap、分母、陽性/陰性対照、duplicate key、rejected/blocked閉鎖を持つ1契約 | 実探索を始めず、別承認待ち |

一時生成先で全試験を通す前に正規 `audit/` やhookへ導入しない。導入は旧SHA記録、候補生成、独立検査、全PASS時だけ置換、置換直後再検査の順にする。途中失敗は旧ファイルへ戻し、復元SHAを記録する。

## 実装・接続の必須試験

1. 静的検査: JSON構文、schema、絶対パス、12member、model ID、approved capabilityの相互参照。
2. 正常・単一変異: C01〜C09を同じfixtureで正常PASS→1項目の所期FAIL→復元後PASS。
3. hook実イベント: 登録コマンド許可、無許可コマンド拒否、別ツール入口、hook故障、古いgeneration、再開後generationを実際のPreToolUse/PostToolUse/Stopで観測。
4. 他プロジェクト非影響: H0157外の一時ファイルに対してHelen state・ログ・拒否結果が変わらない。
5. rollback: hook設定、quality-gate、正規auditの導入前SHAへ復元し、既存Context Harness・brainstorm・prose guardが同じ設定で残る。

unit testや合成fixtureのPASSは、実hook接続、実G10回収、原作一致、Blend完成の証拠にしない。

## 今回は変更・実行しないもの

- `helen-h0157-repro.blend` とBlender前面GUI。
- f154のコンテナ展開、f166の修理・全量走査。
- G10/S6/S8コードの実探索、抽出、候補生成。
- 実G10 P3Bのblocked解除。P3AのG10型隔離合成fixtureは監査の正常対照だけ。
- 原作入力の欠損を推定で埋めること。
- H0157以外のアクション、水着化、静止した創作資料の条件。
- U4以後、change-contract、隔離実験、候補Blend、受入れ、完成台帳更新。
- review-loop環境修理。これは別タスク `codex-brainstorm-review-loop-prevention-task-entry` の管轄。

## 承認した場合に手元で起きる変化

- 最初に12入力の同一性が検査される。違えばコードを作らず、何が変わったかの記録だけが残る。
- 一致すれば監査コードとH0157限定hookが追加されるため、未承認のコマンドや古い入力を使う操作が機械的に止まる。
- U3まで成功しても3Dの見た目は変わらない。手元に増えるのは、監査一式、実イベント証拠、最初の1探索契約である。
- 第1探索契約を実行するかは、内容を見た次の承認で決める。

## 使わなかったもの・落とした情報

1. **捨てたもの**: U0〜U7の一括承認、f154またはG10へ即着手する順序、Lunaによる原因判断、Claude不在時のGPT自動代替、他14アクションへの展開。
2. **手元でどう変わるか**: 今回の承認だけではBlend・画像・原作再現の見た目は変わらない。先に監査導入と1探索契約の提示が入り、実コード探索までにもう一度承認が必要になる。その分、即探索の速さを失う。
3. **戻せるか**: 監査・hookは導入前SHAとrollback試験で戻せる。探索順を変える場合は、U3の候補契約を不承認にして別の1契約を作る。H0157以外への展開は別計画・別承認でのみ再開できる。

## この承認の終端

U3の独立review済み第1search-contractを提示した時点で停止する。承認をU4の実探索、U5の因果確定、U6のBlend実験へ流用しない。
