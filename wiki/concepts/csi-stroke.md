---
type: concept
name: CSI ストローク(C / S / I 線の自己制限)
aliases: [csi-stroke, CSI stroke, CSI ライン, C・S・Iストローク, 3 ストローク制限]
tags: [drawing, line, fundamentals, nekojira, csi]
sources: [coloso-nekojira-ch04-observation-abstraction, coloso-nekojira-ch05-figure-practice-1, coloso-nekojira-ch06-figure-practice-2, coloso-nekojira-ch07-figure-practice-3, coloso-nekojira-ch12-summary-qa]
---

# CSI ストローク

## 定義

[[nekojira]] が ch04 で導入する **線の自己制限フレームワーク**。スケッチ・線画段階で **3 種の線**(C / S / I)のみを使用して対象を構築する。**Krenz Cushart から学んだ技法**として明示(ch04 6:52)。

3 種の線:

| 線 | 形 | 用途 | 頻度 |
|---|---|---|---|
| **C カーブ** | 文字 C のような曲線 | 大半の対象。「**調整が非常に簡単**」。初心者の第一選択 | 最頻出 |
| **S カーブ** | S 字、ランダム性 | 髪・流れる対象 | 中。初心者には「2 つの C カーブで代用可」と提案 |
| **I ライン** | 直線 | 硬い対象(立方体・骨格・足の硬い部分) | 中 |

## 哲学的根拠

**「自己制限が創造性を生む**」(ch04 16:40):
- ゲーム序盤でリソースが限られているのと同じ
- 「**自分を制限することで脳が働き始め、より興味深い**」
- スケッチが乱雑にならず、後の修正が容易

**「複雑なものを単純に分解する」**(ch07 9:19):
- 「**スキルとは頭内の CPU 使用率を下げるようなもの**」
- 単純化することで難しい問題解決により多くの脳細胞を使える

## 適用ワークフロー

1. **観察**: 対象の輪郭を見て、線の **変極点(turning point)** をマーク
2. **分解**: 各セグメントを C / S / I のどれに近いか判定
3. **記号化**: 「これは C カーブ、これは I ライン」と頭の中で言語化
4. **描画**: 3 種の線だけを使って構築。「**ストロークを複雑にしないように心がける**」(ch05 9:36)
5. **チェック**: 描いた後で「CSI でチェックしながら観察」(ch07 5:11)を再度行う

### 変極点(turning point)= 線が止まる位置

- ライブドローイング中、Nekojira の線が止まる位置はすべて変極点
- ここで C/S/I の **切り替えポイント** が発生
- 「**私の線の止まるポイントに気づきましたか?それらは実は転換点なんです**」(ch12 11:44)

## 主な実演適用

- **足の輪郭**(ch04 6:50) — 大 C カーブ + I ライン + 小 C カーブ
- **女性の体の曲線**(ch12 11:36) — C と S だけで構築、I は骨ばった部分のみ
- **建物・斜め脚**(ch04 6:37) — I + 「2 つの起こった顔」記憶術
- **目の 6 セグメント**(ch09 / [[eye-6-segment]]) — I 線を 6 つに分けたものと同型

## 練習法(ch12 から)

1. **ダイヤモンド形に分解 → CSI で線を綺麗に**
2. **3 種だけに制限**して対象を構築 → クリーンな線画になる
3. **形の精度** を練習する(線そのものより)
4. 例: 椅子を描く時、すべて S または C のいずれか。物の輪郭の正確さを目指す

## 関連

- [[nekojira]]
- [[abstract-mix-and-match]] — CSI で抽象化したパーツを組み合わせる手法
- [[observation-via-abstraction]] — CSI は観察の出力フォーマット
- [[eye-6-segment]] — 目に対する CSI の特殊版(I を 6 セグメントに分解)
- [[box-proportion-method]] — 顔・体の構築では CSI と並用される
- [[krenz-cushart]] — 出典(broken link)
- [[curve-drawing-practice]](Ye Jji ch23)— **粒度が逆方向**: Ye Jji = 「曲線の極率変化を 1 つ 1 つ数える」精密化 / Nekojira = 「C カーブで近似してから細部」粗化 → 両論を学ぶ価値あり

## 解釈の揺れ / 異論

- **粒度の問題**: Ye Jji 講座の [[curve-drawing-practice]](膝のカーブの極率変化を数える)とは **逆方向の精密化**。Nekojira は「**C カーブで粗化**」を推奨、Ye Jji は「**極率を細かく観察**」を推奨。両者を時系列で組み合わせれば「**まず CSI で粗化 → 慣れたら極率を数えて精密化**」というカリキュラムが組める

## 出典

- [[coloso-nekojira-ch04-observation-abstraction]] — 導入と全面実演
- [[coloso-nekojira-ch05-figure-practice-1]] — 全身ジェスチャー実装
- [[coloso-nekojira-ch06-figure-practice-2]] — パーツ抽象化実装
- [[coloso-nekojira-ch07-figure-practice-3]] — 重なりポーズへの応用 + CPU 比喩
- [[coloso-nekojira-ch12-summary-qa]] — Q&A 再強調 + ダイヤモンド練習法
