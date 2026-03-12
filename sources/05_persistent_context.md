# 永続コンテキスト: CLAUDE.md / Auto Memory

## 要旨

セッションは消えるが、CLAUDE.mdと自動メモリは残る。「毎回言い直す」のではなく「一度書いて永続化する」ことで、コンテキスト管理の負担を構造的に軽減する。

## 根拠（公式ドキュメントからの抽出）

### CLAUDE.md — 明示的な永続指示

出典: https://code.claude.com/docs/en/memory

配置場所と優先順位（more specific locations take precedence）：

| スコープ | 配置場所 | 用途 | 優先度 |
|---------|----------|------|--------|
| プロジェクト | `./CLAUDE.md` または `./.claude/CLAUDE.md` | チーム共有の指示 | **最も高い**（最も具体的） |
| ユーザー | `~/.claude/CLAUDE.md` | 個人の好み（全プロジェクト共通） | 中 |
| 組織（Managed Policy） | `/Library/Application Support/ClaudeCode/CLAUDE.md`（macOS） | 全社的なポリシー | 最も低い（ただし除外不可） |

**注意**: 組織レベル（Managed Policy）は優先度は最も低いが、`claudeMdExcludes` で除外できない特別な扱い。つまり「必ず適用されるが、矛盾時はプロジェクト指示が勝つ」。

読み込みの仕組み：
- セッション開始時に作業ディレクトリから上位ディレクトリをたどり、すべてのCLAUDE.mdを読み込む
- サブディレクトリのCLAUDE.mdはそのディレクトリのファイルを読むときにオンデマンド読み込み

ベストプラクティス：
- **200行以下を目指す** — 長いほど遵守率が下がる
- **検証可能な指示を書く** — ❌「品質に注意」 ✅「2スペースインデント」
- **矛盾する指示を排除** — 複数ファイルで矛盾があるとClaude が任意に選択

インポート機能：
```markdown
@docs/testing-guide.md
@~/.claude/my-project-instructions.md
```

### .claude/rules/ — 条件付きルール

出典: https://code.claude.com/docs/en/memory

パス指定で特定ファイル編集時のみ読み込まれるルール：
```yaml
---
paths:
  - "src/api/**/*.ts"
---
# API開発ルール
- すべてのエンドポイントに入力検証を実装
```

コンテキスト節約効果：必要な時だけ読み込まれるため、常時コンテキストを消費しない

### Auto Memory（自動メモリ）

出典: https://code.claude.com/docs/en/memory

- Claudeがセッション中に自動で学習内容を記録
- ビルドコマンド、デバッグの洞察、コード風格の好みなど
- ユーザーが明示的に管理する必要がない

保存場所：
```
~/.claude/projects/<project>/memory/
├── MEMORY.md          # インデックス（最初の200行がセッション開始時に読み込み）
├── debugging.md       # トピック別詳細
└── ...
```

有効化/無効化：
```bash
/memory                                    # 確認・編集
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1          # 環境変数で無効化
```

### Auto Memory のスコープと Worktree の関係

出典: https://code.claude.com/docs/en/memory#storage-location

> The `<project>` path is derived from the git repository, so all worktrees and subdirectories within the same repo share one auto memory directory.

**重要**: Auto Memory は **git リポジトリ単位で1つ**。同じリポジトリの worktree は Auto Memory を共有する。
- Worktree はセッション/作業ディレクトリを分離するが、**Auto Memory までは分離しない**
- つまり worktree A で学習した内容は worktree B でも参照される
- Auto Memory はマシンローカル（クラウド環境や他のマシンとは共有されない）

### CLAUDE.md vs Auto Memory

| 項目 | CLAUDE.md | Auto Memory |
|------|-----------|-------------|
| 書き手 | ユーザー | Claude |
| 内容 | 指示とルール | 学習とパターン |
| スコープ | プロジェクト/ユーザー/組織 | gitリポジトリ単位（worktree間で共有） |
| 読み込み | 完全読み込み | MEMORY.md最初の200行 |
| マシン間共有 | ソース管理で共有可能 | マシンローカルのみ |

### スキル — オンデマンドコンテキスト

出典: https://code.claude.com/docs/en/skills

CLAUDE.mdに書くと常時コンテキストを消費する指示を、呼び出し時のみ読み込ませる仕組み：
```yaml
---
name: deploy
description: アプリをプロダクションにデプロイ
---
デプロイ手順：...
```

CLAUDE.md（常時読み込み）→ スキル（オンデマンド読み込み）への移行で、コンテキスト節約が可能

## スライドで使う主張候補

1. **「毎回言い直す」のは最悪のコンテキスト管理** — CLAUDE.mdに書けば1回で済む
2. **CLAUDE.mdは200行の制約がある** — 超えると逆効果 → rules/ やスキルに分散
3. **Auto Memoryは「勝手に覚えてくれる」** — ユーザーの管理コストゼロ
4. **スキルはコンテキストの遅延読み込み** — 使う時だけ消費する設計

## デモ候補

- `/memory` で現在のCLAUDE.mdと自動メモリの内容を見せる
- `.claude/rules/` にパス指定ルールを追加し、対象ファイル編集時のみ読み込まれることを確認
- スキルの作成と `/skill-name` での呼び出し
