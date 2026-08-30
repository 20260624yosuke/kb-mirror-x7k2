---
type: analysis
status: active
confidence: high
evidence_level: source-backed+user-stated
last_reviewed: 2026-08-30
---

# レビュー・ボトルネック論の機械化（2026-08-30・案1を承認）

親メモ:
`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/analyses/brainstorm/gf2-dusevnyj-bikini-to-helen/brainstorm-gf2-dusevnyj-bikini-to-helen.md`

## この回でやったこと

1. O0 の Blend を武田さんが開き、「クリスタでいうレイヤーが全表示の状態」という印象を得た。
2. その印象を**数値で裏づけた**（下記）。
3. 一次資料（ChatGPT との検討ログ）をこの案件へ当て直し、2026-08-28 の対応表が古いことを実測で確認した。
4. 仕組み化の案を3つ出し、**案1が承認された**（実行の承認）。

## 実測（Blend をコードで開いた）

- 表示メッシュ **43 / 75**、表示面数 129,076
- 版が2つ同時: `HelenSSR01` 16個 ＋ `HelenSSR0101` 27個
- 同じスカートの派生が4重: 無印 / `Bend` / `Flat` / `General` が各4個
- ほかに `MP443`（拳銃）2挺、旗の `Effect` 1つ
- 表示中の頂点 254,485、Z 範囲 -0.0012〜2.3828（**旗などを含む数字**。全身域の基準は未確定）
- **判定に要る情報は `build-log-o0.json` の `records` に全部ある**（Blender 不要）

## 2026-08-28 の分析のどこが古かったか

`wiki/analyses/llm-review-bottleneck-applied-2026-08-28.md` は「#9 人間の指摘→機械検証への変換は
存在しない」を最大の欠落としていたが、その後に実装が入っている。

- 却下案の照合: `wiki/builds/gf2-dusevnyj-p3-bikini-rejected-approaches.json` ＋
  `tools/rejected_approaches_check.py` ＋ `plan_audit.py` A7 → **実装済み**
- 承認の粒度: `output/gf2-helen-swimsuit/quality-gate.json` ＋ A9 → **実装済み**

**残る穴は2つ**:

1. 検査が「計画の文章」と「中間データ」に集中し、**武田さんが実際に開く Blend を見る検査がゼロ**。
   検査コード904行のうち Blend を見る行は0。だから今回の重なりは人の目まで届いた。
2. **指摘を検査へ変換する工程が仕組みになっていない**（却下案 JSON は計画の言い回ししか見ない）。

## 案2 の中身（選ばれなかった側の記録）

武田さんから「案2の説明が足りない」と指摘を受けて具体化した内容。**順番を後ろにしただけで捨てていない。**

| 検査 | 見るもの | 入力は今あるか | 先に要るもの |
|---|---|---|---|
| D1 表示の排他性 | 版・派生・体/顔/髪の重なり | **ある** | なし（今日すぐ本物で合否が出る） |
| D2 全身の高さ範囲 | トルソーだけになっていないか | 座標はある | **基準値が未確定**（O0 の Z 範囲は旗を含む） |
| D3 穴（開いた辺） | 腰などに穴が無いか | **水着入りの成果物がまだ無い** | Blend からメッシュを取り出す経路が未確認 |
| D4 SHA 照合 | 原本を壊していないか／Flat と General の取り違え | ハッシュは取れる | **照合相手の台帳が無い** |
| D5 来歴 | 承認していない「新しく作った面」が無いか | build-log の来歴は `bundle` と `path_id` の**2項目だけ** | 「新しく作った面」の欄の**新設＝新しい設計判断＝承認がもう1回** |

→ **5本のうち、いま本物のデータで検出力を確かめられるのは D1 だけ。**

## 承認された内容

**案1 = 仕組みA（指摘の台帳＋関所A10）＋ 検査 D1 の1本。** 詳細な完成条件・禁止事項は親メモの
`## 実装への申し送り` の先頭節。

## 説明ページ（人が読む用）

`/Volumes/SSD_M.2_Realtek RTL9210 NVME Media_/05_claude/claude_llm_wiki/LLM Knowledge Base _01/wiki/_attachments/helen-swimsuit-status/20260830-review-bottleneck-mechanization.html`
