# コンテキストの基本

## 要旨

Claude Codeのコンテキストウィンドウは、Claudeが一度に「見える」テキスト全体。会話履歴、ファイル内容、ツール出力、システム指示がすべてこの中に収まる必要がある。容量を超えると自動圧縮が発生し、古い情報から失われる。

## 根拠（公式ドキュメントからの抽出）

### コンテキストウィンドウの構成要素

出典: https://code.claude.com/docs/en/how-claude-code-works

コンテキストウィンドウに含まれるもの：
- 会話履歴（ユーザーの質問とClaudeの応答）
- ファイルの内容（読み込まれたコード、ドキュメント）
- コマンド出力（bash実行結果）
- CLAUDE.md（プロジェクト指示）
- 読み込まれたスキルの定義
- システム指示
- MCPサーバーのツール定義

### 自動圧縮の仕組み

出典: https://code.claude.com/docs/en/how-claude-code-works

> Claude Code manages context automatically as you approach the limit. It clears older tool outputs first, then summarizes the conversation if needed. Your requests and key code snippets are preserved; detailed instructions from early in the conversation may be lost.

自動圧縮の優先順位：
1. 古いツール出力を最初に削除
2. 必要に応じて会話を要約
3. ユーザーのリクエストと重要なコードスニペットは保持
4. 初期の詳細な指示は失われる可能性あり

### コンテキスト使用量の確認方法

出典: https://code.claude.com/docs/en/costs

- `/cost` — トークン使用量と実行時間を表示
- `/context` — コンテキストウィンドウの使用状況の詳細（MCP含む）
- `/mcp` — MCPサーバーごとのコンテキストコスト

## スライドで使う主張候補

1. **コンテキストウィンドウ = Claudeの「視界」** — この中に入らない情報はClaudeに見えない
2. **コンテキストは有限資源** — 会話が長くなるほど古い情報が消える
3. **自動圧縮は便利だが制御不能** — 何が消えるかを完全にコントロールできない → 能動的な管理が必要
4. **MCPサーバーは「見えないコンテキスト消費者」** — ツール定義が毎リクエスト追加される

## デモ候補

- `/cost` と `/context` を実行して、現在のコンテキスト使用量をリアルタイムで見せる
- MCPサーバーを複数接続した状態で `/mcp` を実行し、コンテキストコストの差を見せる
