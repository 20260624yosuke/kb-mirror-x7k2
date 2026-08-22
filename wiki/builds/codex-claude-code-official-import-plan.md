---
type: build
title: Claude Code環境をOpenAI公式ImportでCodexへ試験導入する計画
created: 2026-08-13
sources: []
status: active
confidence: medium
evidence_level: source-backed
last_reviewed: 2026-08-13
---

# Claude Code環境をOpenAI公式ImportでCodexへ試験導入する計画

## 目的

Claude Codeで使っている設定・skills・plugins・hooks・最近の作業を、OpenAI公式の
`Import from another agent` でChatGPT/Codexへ試験導入する。

この計画のゴールは「全部入ったと言い切ること」ではない。既存Codex環境を壊さず、1つの実使用
プロジェクトでImportの結果を確認し、問題がなければAutomatic updatesをONにできる状態まで持っていく。

## 高リスク判定

この作業は高リスク扱いにする。理由は、正解がコード外の実機状態・外部アプリ・OpenAI公式Import機能にあり、
自動試験だけでは成功判定できないため。

- 正解の所在: OpenAI公式ドキュメント、Claude Codeの実ファイル、Codexの実ファイル、ChatGPT DesktopのImport画面、Import後のCodex実状態。
- 欠けうる入力: ChatGPT Desktop側のログイン状態、macOSのAccessibility/Screen Recording権限、Import UIで表示される実候補、認証が必要なplugin/MCP接続。
- 対象群: user-level setup、project-level setup、recent chats、post-import setup、automatic updates。
- 代表例: `LLM Knowledge Base _01` だけを最初の試験対象にする。
- 比較方法: Import前の実測一覧とImport後のCodex設定・AGENTS.md・skills/plugins/MCP/hooks/chats表示を照合する。
- 停止条件: 既存Codex設定の上書き疑い、認証情報の露出リスク、Import候補の不一致、CLI制限で必要チャットを落とす場合、GUI権限不足、公式ドキュメントと違う挙動。

## 根拠として確認済みの事実

- OpenAI公式ドキュメントは、Desktop appがClaude Code/Claude Cowork/Cursorから、Codex CLIがClaude Code/CursorからImportできるとしている。
- OpenAI公式ドキュメントは、instructions、settings、skills、plugins、projects、recent workをImport対象としている。
- OpenAI公式ドキュメントは、Importしても既存agent setupは変更・削除されないとしている。ただし、これは公式機能の仕様であり、実機検証は別に行う。
- OpenAI公式ドキュメントは、Desktop appではSettings > Importから開始し、automatic updatesはSettings > Importで後からONにできるとしている。
- OpenAI公式ドキュメントは、Codex CLI `/import` では直近30日・最大50 chatsという制限があるとしている。
- 依頼元ファイルは、Desktop Importを優先し、CLIはDesktopが使えず安全に完了できる場合だけ使うよう指定している。
- このMacではClaude Code `2.1.228` とCodex CLI `0.147.0-alpha.6.5` を確認済み。
- Claude側には `/Users/takedayousuke/.claude/settings.json`、global `CLAUDE.md`、skills `eagle-personalize` / `llm-wiki`、enabled plugin `swift-lsp@claude-plugins-official` がある。
- Claude hooksは `PostCompact` / `PreCompact` / `SessionEnd` / `SessionStart` / `Stop` / `StopFailure` / `SubagentStart` / `SubagentStop` / `UserPromptSubmit`。
- Codex側には `/Users/takedayousuke/.codex/config.toml`、global `AGENTS.md`、hooks、plugins、既存projects、過去Import履歴20件がある。
- `LLM Knowledge Base _01` はClaude側の直近30日トップレベルchatが67件で、最近もっとも使われている対象。
- 同じプロジェクトは既にCodex projectとして登録済みで、`AGENTS.md` と `CLAUDE.md` の内容は別物。

## 今回やらないこと

- 非公式の独自変換スクリプトでImport機能を再実装しない。
- 最初から全プロジェクトをImportしない。
- Automatic updatesを最初からONにしない。
- 認証トークンや秘密値をバックアップ成果物へ平文保存しない。
- CLI `/import` をDesktop Importと同等扱いしない。CLIは50 chats制限があるため、Desktopが使えない場合の予備線。

## 実行手順

### 0. 品質ゲートを置く

Import実行を始める直前に、プロジェクト直下へ `quality-gate.json` を作る。構造は
`tools/quality-gate.template.json` に合わせる。

その後、次を通す。

```bash
python3 tools/project_quality_gate.py check quality-gate.json --phase plan
```

失敗したらImportを始めず、不足事実を調べる。

### 1. Import前の証拠を固定する

以下を一覧化する。ここでは設定変更しない。

- Claude Code本体、Claude settings、Claude global/project instructions、skills、plugins、MCP、hooks、slash commands、subagents、project memory、recent chats。
- Codex側の `config.toml`、global/project `AGENTS.md`、hooks、skills/plugins、MCP、既存projects、Import履歴。
- `LLM Knowledge Base _01` の `AGENTS.md` / `CLAUDE.md` 差分。

この段階の成功条件: 「Import前に何が存在したか」を、ファイルパス・件数・ハッシュで説明できる。

### 2. 最小バックアップを作る

保存先は次の形にする。

```text
/Users/takedayousuke/Documents/Codex/backups/claude-import-YYYYMMDD-HHMMSS/
```

保存するもの:

- `/Users/takedayousuke/.codex/config.toml`
- `/Users/takedayousuke/.codex/AGENTS.md`
- `/Users/takedayousuke/.codex/hooks.json`
- `/Users/takedayousuke/.codex/.codex-global-state.json`
- `/Users/takedayousuke/.codex/external_agent_session_imports.json`
- `/Users/takedayousuke/.codex/computer-use/config.json`
- Codex skills/pluginsのmanifest一覧とハッシュ一覧
- 対象projectのImport前 `AGENTS.md` / `CLAUDE.md` ハッシュ

保存しないもの:

- `.codex/cache/` の巨大キャッシュ本体
- `.codex/sessions/` の会話履歴本体
- `auth.json` の秘密値本体

この段階の成功条件: バックアップからImport前のCodex設定と対象project instructionsを復元できる見込みがある。

### 3. Desktop Importを本線として実行する

ChatGPT Desktopで `Settings > Import` を開く。Import項目は次に固定する。

- Import元: Claude Code
- Tools & setup: ON
- Projects: `LLM Knowledge Base _01` のみ
- Recent chats: 対象projectに関係するrecent chats
- Automatic updates: OFFのまま

前面GUI操作が必要な場合は、実行時に次の3択を出す。

- 推奨: ユーザーが短い手順でImport画面だけ開き、私が画面内容を読んで次手順を判断する。失うもの: 完全自動ではない。
- 私が前面GUIを操作する。失うもの: 画面フォーカスを私が使うため、作業中はMac操作を占有する。
- ユーザーが全画面を手動操作して結果だけ共有する。失うもの: ユーザー負担が最大になる。

この段階の成功条件: Desktop Importで対象項目を選び、完了画面または完了後状態を確認できる。

### 4. CLI `/import` は予備線に限定する

Desktop Importが使えない、またはGUI権限で止まった場合だけ検討する。

CLIを使う前に確認すること:

- 現在のCodexがrunning task、remote session、local app-server daemon接続中でないこと。
- 対象projectのrecent chatsは67件あり、CLI制限の50件を超えること。

CLIを使った場合に失うもの:

- 対象projectの直近30日chat 67件のうち、少なくとも17件はCLI制限でImportされない可能性がある。
- Desktop UIで見えるplugin/connection setupカードを同じ形で確認できない可能性がある。

この段階の成功条件: Desktop不能の理由と、CLIで落ちるものを記録したうえで、CLI使用が依頼の目的をまだ満たすか判断できる。

### 5. Import後監査を行う

Import直後に、次をImport前の証拠と照合する。

- instructions: `CLAUDE.md` 由来がCodex側でどう扱われたか。既存 `AGENTS.md` が弱体化・上書きされていないか。
- settings: Claude settingsのうち、Codex `config.toml` へ入ったもの・入らなかったもの。
- Skills: `eagle-personalize` / `llm-wiki` が認識されるか。権限や参照パスが壊れていないか。
- Plugins: `swift-lsp@claude-plugins-official` がどう扱われたか。追加setupが必要か。
- MCP: 認証・headers・env・transportに重大エラーがないか。
- Hooks: Claude hook eventとCodex hook eventの差。特に`StopFailure`はCodex側に同等イベントがあるか確認する。
- Slash commands: コマンド型promptがskills等へ変換された場合、引数・ファイルパス前提が壊れていないか。
- Subagents: Claude project配下のsubagent記録が、Codex agent/subagentとして使える状態か、単なるchat履歴扱いか。
- Project Memory: Claude project memoryがCodex memoryへ入ったか。内容が混ざっていないか。
- Project: 既存Codex project登録と重複していないか。
- Recent chats: Desktopなら67件相当が候補・結果に入ったか。CLIなら最大50件制限の不足を記録する。

この段階の成功条件: 「Importされたもの」「Importされなかったもの」「手作業setupが必要なもの」を分けて説明できる。

### 6. 非破壊の動作確認を行う

実行する確認:

- Codexで対象projectを開ける。
- `AGENTS.md` を読める。
- Imported skills/plugins/agentsの存在を一覧で確認できる。
- MCP設定に即時の重大エラーが出ない。
- 既存Codex hooksとplan-gate hooksが無効化されていない。

やらない確認:

- 外部サービスへ実データを書き込む確認。
- hooksの破壊的コマンド実行。
- 認証情報の平文表示。

この段階の成功条件: 既存Codex設定を壊さず、Importした対象projectで最低限の作業開始ができる。

### 7. Automatic updatesのON判定

次をすべて満たす場合だけONにする。

- Import後監査でmajor問題がない。
- 既存Codex設定の弱体化・上書きがない。
- `AGENTS.md` と `CLAUDE.md` の扱いが説明できる。
- Skills/plugins/MCP/hooksの未解決setupがない、またはAutomatic updatesに影響しないと説明できる。
- Desktop Importの履歴画面で対象Importを確認できる。

1つでも満たさない場合はOFFのまま止める。

## ロールバック方針

ロールバック対象は「今回Importで増えた/変わったCodex側の状態」だけにする。Claude側は触らない。

戻す前に、Import後の状態も別途保存する。戻す対象はバックアップで保存したCodex設定・対象project
instructions・Import履歴に限定し、巨大cacheや会話履歴を雑に削除しない。

## 最終報告の形

完了時の報告は次の5項目だけにする。

- Importしたもの
- Importしなかったもの
- 検証結果
- Automatic updates: ON / OFF
- 私が追加で操作する必要: あり / なし

## 使わなかったもの・落とした情報

- CLI `/import` を本線から外した。手元で変わること: Desktop Importが使える限り、50 chats制限でrecent chatsを落とす可能性を避ける。戻せるか: Desktopが使えない場合だけCLI予備線へ戻せる。
- 巨大cacheと会話履歴本体の丸ごとバックアップを外した。手元で変わること: バックアップ容量と時間は減るが、過去会話そのものの完全複製にはならない。戻せるか: 必要になった場合だけ対象を絞って追加バックアップする。
- Automatic updatesの即ONを外した。手元で変わること: Import直後にClaude側変更がCodexへ自動反映されない。戻せるか: 監査が通った後、Settings > ImportでONにできる。
