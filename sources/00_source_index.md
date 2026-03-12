# ソースインデックス

## 収集基準

- **対象**: Claude Code公式ドキュメント（code.claude.com/docs/en/）のみ
- **引用ルール**: 1主張=1出典。各主張に対応する公式ドキュメントURLを必ず記載する
- **用語**: 公式ドキュメントの英語表記を正とし、日本語訳は括弧書きで補足する

## 用語表

| 公式用語 | 日本語表記（本プレゼンでの統一表記） | 説明 |
|---------|--------------------------------------|------|
| Context window | コンテキストウィンドウ | Claudeが一度に処理できるテキスト容量 |
| Token | トークン | テキストの最小処理単位 |
| Auto compaction | 自動圧縮 | コンテキスト上限接近時の自動要約処理 |
| `/compact` | `/compact` | 手動でコンテキストを圧縮するコマンド |
| `/clear` | `/clear` | コンテキストを完全リセットするコマンド |
| Rewind | リワインド | セッション内の以前の状態に戻す機能 |
| Resume | レジューム | 過去セッションを再開する機能 |
| `--continue` | `--continue` | 最新セッションを再開するCLIフラグ |
| `--fork-session` | `--fork-session` | セッションを複製して再開するCLIフラグ |
| Worktree | ワークツリー | git worktreeベースの並列作業環境 |
| Sub-agent | サブエージェント | 独立コンテキストで動作する専門エージェント |
| CLAUDE.md | CLAUDE.md | 永続的な指示ファイル |
| Auto Memory | 自動メモリ | Claudeが自動で学習内容を記録する仕組み |
| Checkpoint | チェックポイント | ファイル変更前の自動保存ポイント |
| `/btw` | `/btw`（サイド質問） | 会話履歴に残らないエフェメラル質問機能（v2.1.72で追加） |
| Skill | スキル | オンデマンド読み込みの再利用ワークフロー |

## 対象ドキュメント一覧

### コア（必須参照）

| # | ページ | URL | 対応ソースファイル |
|---|--------|-----|-------------------|
| 1 | How Claude Code works | https://code.claude.com/docs/en/how-claude-code-works | 01, 02 |
| 2 | Best Practices | https://code.claude.com/docs/en/best-practices | 02, 03 |
| 3 | Checkpointing | https://code.claude.com/docs/en/checkpointing | 03 |
| 4 | CLI Reference | https://code.claude.com/docs/en/cli-reference | 03 |
| 5 | Common Workflows (Worktree, Plan Mode) | https://code.claude.com/docs/en/common-workflows | 02a, 04 |
| 6 | Sub-agents | https://code.claude.com/docs/en/sub-agents | 04 |
| 7 | Memory (CLAUDE.md / Auto Memory) | https://code.claude.com/docs/en/memory | 05 |
| 8 | Skills | https://code.claude.com/docs/en/skills | 05 |

### 補助（必要に応じて参照）

| # | ページ | URL | 用途 |
|---|--------|-----|------|
| 9 | Costs | https://code.claude.com/docs/en/costs | トークンコストの説明 |
| 10 | Settings | https://code.claude.com/docs/en/settings | MCP/コンテキスト設定 |
