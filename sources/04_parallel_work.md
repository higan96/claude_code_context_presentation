# 作業分離: Worktree / サブエージェント

## 要旨

コンテキスト管理の上位戦略として「そもそもコンテキストを分離する」アプローチ。Worktreeはファイルシステムレベルの分離、サブエージェントはコンテキストウィンドウレベルの分離を提供する。

## 根拠（公式ドキュメントからの抽出）

### Worktree（ワークツリー）— 並列作業環境

出典: https://code.claude.com/docs/en/common-workflows

```bash
claude --worktree              # 自動名でworktree作成
claude --worktree feature-auth # 名前指定でworktree作成
claude -w bugfix-123           # 短縮形
```

仕組み：
- 内部的にgit worktreeを使用
- `.claude/worktrees/<name>/` にディレクトリ作成
- ブランチ名は `worktree-<name>`
- デフォルトリモートブランチから分岐

自動クリーンアップ：
- **変更なし**: worktreeとブランチが自動削除
- **変更あり**: 保持or削除をプロンプトで選択

手動管理（上級者向け）：
```bash
git worktree add ../project-feature-a -b feature-a
cd ../project-feature-a && claude
git worktree remove ../project-feature-a
```

注意点：
- `.claude/worktrees/` を `.gitignore` に追加すること
- 新しいworktreeごとに開発環境の初期化が必要（`npm install` 等）

**効く場面**: 機能開発とバグ修正を並行したい、実験的なアプローチを分離したい
**効かない場面**: 単一ファイルの小さな変更（オーバーキル）
**誤用リスク**: 環境初期化を忘れるとビルドエラーになる

### サブエージェント — コンテキスト分離

出典: https://code.claude.com/docs/en/sub-agents

サブエージェントの本質：**独立したコンテキストウィンドウで実行され、結果のみがメインに返る**

```
メイン会話              サブエージェント
┌──────────────┐       ┌──────────────┐
│ 会話履歴     │       │ 独立の       │
│ 作業コンテキスト│ →生成→ │ コンテキスト │
│              │       └──────────────┘
│ ← 要約結果のみ │              ↓
└──────────────┘       詳細出力は破棄
```

組み込みサブエージェント：

| 種類 | モデル | ツール | 用途 |
|------|--------|--------|------|
| Explore | Haiku | 読み取り専用 | コードベース検索（高速・低コスト） |
| Plan | 継承 | 読み取り専用 | 計画立案 |
| General-purpose | 継承 | 全ツール | 複雑な多段階タスク |

カスタムサブエージェント作成（`.claude/agents/`）：
```markdown
---
name: security-reviewer
description: セキュリティレビュー実施
tools: Read, Grep, Glob, Bash
model: sonnet
disallowedTools: Write, Edit
---

セキュリティの専門家としてレビューしてください。
```

サブエージェントでのworktree分離：
```yaml
---
name: parallel-worker
isolation: worktree
---
```

バックグラウンド実行：
- `Ctrl+B` で現在の実行をバックグラウンド化
- メイン会話を続行しながらサブエージェントが並行処理

**効く場面**: 大量のテスト実行、コードベース全体の調査、複数モジュールの並列リサーチ
**効かない場面**: 単純な1ファイルの読み取り（サブエージェント起動コストが上回る）
**誤用リスク**: サブエージェントを過度に使うと管理が複雑になる

## スライドで使う主張候補

1. **Worktree = ファイル分離、サブエージェント = コンテキスト分離** — レイヤーが違う
2. **サブエージェントの本質は「要約フィルター」** — 詳細はサブ側で消化し、結論だけメインに返す
3. **Explore エージェントは「低コストの目」** — Haikuモデルで高速・安価にコードベースを調査
4. **チームでサブエージェント定義を共有** — `.claude/agents/` をリポジトリにコミット

## デモ候補

- `claude --worktree feature-x` で新しいworktreeを作成し、元のディレクトリと並行作業する様子
- サブエージェントに大規模な調査を依頼し、メインのコンテキストが膨らまないことを `/context` で確認
- カスタムサブエージェント定義ファイルの作成と使用
