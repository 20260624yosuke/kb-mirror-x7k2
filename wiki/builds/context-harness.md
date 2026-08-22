---
type: build
status: uncertain
confidence: high
evidence_level: source-backed
last_reviewed: 2026-08-13
sources:
  - tools/context_harness/
  - tools/context_harness/evidence/product-status.json
  - tools/context_harness/evidence/real-observe-only.md
  - tools/context_harness/evidence/claude-auth-recheck.md
  - tools/context_harness/evidence/product-gate.json
  - tools/context_harness/evidence/synthetic-soak.json
  - /Users/takedayousuke/llm-uploads/20260812-231108--Context-Harness-v1-承認済み修正版計画.md
  - https://learn.chatgpt.com/docs/hooks
  - https://code.claude.com/docs/en/hooks
---

# Context Harness v1

## 現在の統合見解

Context Harnessは、Codex CLI・Codex Desktop・Claude Codeのタスク状態を、会話履歴とは別のJSONへ保存する共通基盤。正本は `tools/context_harness/`、Mac本体の固定releaseは `~/.agents/context-harness/releases/0.1.13/`、状態は `~/.agents/context-harness/state/`。

現時点では全製品を `observe-only`（観測と記録だけ）に固定している。状態保存・競合拒否・所有権移譲・破損復元・二重実行拒否は実装済みだが、完全自動の新規メインセッション移行と日常運用相当の製品別長時間試験が成立していないため、どの製品も「日常運用可能」ではない。

## 実装

- UUIDによるproject/task識別。pathだけで同一タスクと判断しない。
- 0600/0700権限、file lock、revision一致、原子的置換、直前版保持。
- `OBS / DOC / REQ / INF / DEC / UNK`、完了・未完了、却下仮説、成果物、検証、例外、停止条件を分離保存。
- 一時点一writer。旧sessionの全public mutatorを拒否し、target session初期化・CLI init・handoffの競合も同じsession lockで直列化。
- promptとassistant本文は保存せず、SHA-256とbyte数だけを保存。
- plan-gateの同一session状態を検出した場合はobserve-onlyに固定。
- fixed release、ownership manifest（Harnessが所有する設定一覧）、無関係設定を残すアンインストール、直前releaseへ戻すrollback-release。
- session metadata、Claude transcript entrypoint、hookの実parent processでhostを判別し、Claude Desktopを対象外として除外する。継承環境変数だけではCodex Desktop判定をせず、metadata未flushのephemeral `codex exec`もCLIとして識別する。旧誤分類stateは削除せず、製品証拠から隔離する。

## 製品別検証状態

| 製品 | 実装 | 自動試験 | 実機 | 長時間 | 日常運用 |
|---|---|---|---|---|---|
| Codex CLI | 済 | 66件合格 | `0.1.13`で分類、lifecycle、SIGTERM/SIGKILL中断検出・reconcile復旧を確認。`codex: command not found`はsymlinkで解消済み | 5連続の通常dispatch bounded smokeは確認。日常長時間は未確認 | 不可 |
| Codex Desktop | 済 | synthetic adapter合格 | app-created新規local taskで`SessionStart/UserPromptSubmit/Stop/SessionEnd`を記録 | 未確認 | 不可 |
| Claude Code | 済 | 66件合格 | `0.1.11` terminal CLIで認証失敗pathを確認。`2.1.228`の`/status`と`auth status`はLogin Expired。Desktopは別認証経路でv1対象外 | 未確認 | 不可 |

共通coreのsynthetic soakでは、1,000イベント・10回の再open・5例外・却下仮説・未完了作業・成果物・Codex→Claude handoffを保持した。これは製品固有の長時間運用証拠には使わない。

## 根拠

- 破棄可能profileのPRODUCT_GATEで、install、設定保持、24並列初回hookを1 taskへ集約、installed release単独起動、rollbackを直接確認。
- real Codex CLI stateで、全hookがobserve-only、prompt/message本文非保存、manual Pre/PostCompact、終了後owner stoppedを直接確認。
- real Claude stateで、`StopFailure(authentication_failed)`後のSessionEndでも最終statusが`interrupted`のまま残ることを直接確認。
- 401の発生源をterminal Claude Code CLIに限定した。Desktop `1.26832.0`はembedded Claude Code `2.1.222`へhost側のOAuth token/base URLを注入しており、terminal CLIの保存済み`/login`認証とは別経路。秘密値は読み取っていない。
- sourceとinstalled `context_harness.py`のSHA-256一致を確認。
- 診断payloadにparent command引数が平文保存される欠陥を独立監査で検出し、実行ファイル・subcommand・digest・byte数だけへ修正した。既存3 JSONLから引数本文を除去し、回帰試験を追加した。
- `0.1.11`でClaude Desktopの明示markerが旧誤分類mappingより優先され、classification markerなしのlegacy mappingを再利用しないことを直接確認。対象外Desktopの過去2 state、terminal Codex CLIの誤分類1 state、再現中に作られた重複legacy state 1件は保持したまま証拠から隔離した。
- OpenAI公式仕様と通常起動実測により、`~/.codex/hooks.json`のnon-managed command hookはreview/trust前には実行されないことを確認。Codex CLI hook browserでContext Harness 8件とplan-gate hookを一時trust後、bypassなしの通常`codex exec` session `019ffb4d-69dd-7443-ba95-c828dd49bc04`がContext Harnessへ記録された。ただしDesktop環境から起動したため、外部terminal-only CLIの長時間証拠には使わない。この一時trustはユーザー承認前にいったん復元した。
- ユーザーTerminalログの`codex: command not found`は、`~/.local/bin/codex`をChatGPT.app同梱Codexへ向けるsymlink追加で解消した。login bashで`codex-cli 0.147.0-alpha.6.5`を確認した。
- ユーザー承認後、Context Harness 8件のtrusted hashを永続追加した。Desktop originator環境変数を外したlogin bashの通常`codex exec` session `019ffb9d-2f27-7d52-b473-832357ac1222`が`codex-cli`としてContext Harnessへ記録された。
- 同条件の通常`codex exec`を5連続実行し、sessions `019ffba3-a64a-78b1-af79-b306c196dcae` / `019ffba3-c5ce-7c30-aa00-475fecbab1a2` / `019ffba3-e021-70e2-b782-a10103a9912f` / `019ffbb4-c676-7242-8e20-ad1c963bcfa4` / `019ffbb4-d9e8-71b3-ab68-7ba1b85c6a42`が各4 lifecycle event、runtime `0.1.11`、product `codex-cli`、owner status `stopped`として記録された。最後のstateではoperationの再開・完了・重複拒否も実測した。これは短いbounded smokeであり、日常運用相当の長時間検証ではない。
- v0.1.13のCodex CLI強制終了probe `019ffbba-8e92-7fc3-aba2-da47d7a23aae`では、SIGTERM後に`check-stale`が`active-owner-process-exited`を検出し、`reconcile`でtask `54f3aea2-f21e-4f37-9f0c-aee92de95f31`を`interrupted`、ownerを`stopped`へ復旧した。
- v0.1.13のCodex CLI異常終了probe `019ffbc0-2210-7a92-ae92-764fffa6c18f`では、SIGKILL後に`check-stale`が`active-owner-process-exited`を検出し、`reconcile`でtask `abecedcb-b645-4466-8e90-1d38948282ae`を`interrupted`、ownerを`stopped`へ復旧した。復旧後のoperation begin/completeは成功し、完了済みoperationの再beginはexit 3で拒否された。
- Codex Desktopのapp-created新規local task `019ffba6-0306-7c73-a061-e9d17618e84b`は指定応答を返し、probe task archive後にHarness state `d380c8ce-72c2-4f0a-8a77-d842150aef33.json`へ`SessionStart/UserPromptSubmit/Stop/SessionEnd`、runtime `0.1.11`、product `codex-desktop`が保存された。

## 矛盾・未確定

- Codex Desktopのapp-created新規local taskではreal hook lifecycleを確認したが、長時間と自動main session移行は未確認。
- Codexのexact hook definitionはユーザー承認後にContext Harness 8件のみ永続trust済み。自動自己trustによる安全境界の迂回は実装していない。
- 対象内のterminal Claude Code CLIは、`claude auth login`でCLIログインを更新するまで通常応答・compact・長時間試験を行えない。Claude Desktopの再認証は不要で、同製品はv1対象外。
- Codex CLIのauto-trigger compact、各製品の全中断経路、日常運用相当の製品別長時間利用は未確認。Codex CLIの5連続通常dispatch bounded smokeとSIGTERM/SIGKILL中断復旧だけ確認済み。
- 公式CLIにはCodexの`fork`、Claudeの`--fork-session`があるが、hookから安全に新しいメイン対話面へ移り、ユーザー操作ゼロで仕事を続ける経路は未証明。よって自動session移行は未実装扱い。
- quality gateの`complete`は未通過。代表結果のユーザー受け入れ、Desktop実機、Claude再認証後試験、長時間、automatic main sessionが残る。

## ロールバック

- 全撤去: `python3 tools/context_harness/installer.py rollback --runtime-home ~/.agents/context-harness --codex-hooks ~/.codex/hooks.json --claude-settings ~/.claude/settings.json`
- releaseだけ直前の0.1.10へ戻す: `python3 tools/context_harness/installer.py rollback-release --runtime-home ~/.agents/context-harness`
- 全撤去でも状態ファイルは削除せずread-onlyで残る。Harness hookが導入後に書き換えられていた場合は上書きせず、差分証拠を保存して停止する。
- Terminalの`codex`コマンド対応だけ戻す場合は、追加したsymlink `/Users/takedayousuke/.local/bin/codex` を削除する。ChatGPT.app同梱の実体 `/Applications/ChatGPT.app/Contents/Resources/codex` は削除しない。
- Context Harness hook trustだけ戻す場合は、`~/.codex/config.toml`内の`/Users/takedayousuke/.codex/hooks.json:`で始まる8件（`pre_compact`、`post_compact`、`session_start`、`session_end`、`user_prompt_submit`、`subagent_start`、`subagent_stop`、`stop`）を削除する。plan-gate既存7件は対象外。

## 使わなかったもの・落とした情報

- 成果物の内容として捨てた入力・ログ: なし。会話本文は仕様どおり保存対象外で、手元には要約値だけが残る。
- 制御有効化は見送った。そのため現在は「記録」はされるが、自動で新しいメインセッションを作る・復旧を止める・日常運用を肩代わりする動作は起きない。
- Claude Desktop由来と判明した過去2 state、terminal Codex CLI由来と判明した誤分類1 stateと重複legacy 1 stateは削除せず、製品別合格根拠としての利用を捨てた。そのため過去イベント数は残るが、Claude Code長時間・Codex Desktop実機の合格件数には加算されない。戻す必要はなく、誤分類の監査証拠として保持する。
- 戻せるか: 未通過ゲートを実機で満たし、独立監査とユーザー受け入れを得た製品機能だけ、`control.json`の製品単位で有効化できる。現時点では有効化しない。

## 関連リンク

- [[plan-gate-skill]]
- [[llm-project-quality-gate]]
- [[llm-state-transition-gate]]
