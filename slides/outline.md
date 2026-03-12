# Claude Code コンテキストマネジメント — スライド構成案

## 全体ストーリー

**概念 → 問題 → 予防 → 対処 → 分離 → 永続化 → まとめ**

聞き手が「コンテキストって何？」から始めて、「明日から自分のワークフローに組み込める」まで到達するのがゴール。

---

## スライド構成

### 1. タイトル
- **Claude Code コンテキストマネジメント入門**
- サブタイトル: 「Claudeの視界を制御して、最大限の成果を引き出す」

---

### 2. 今日のゴール（1枚）
- この資料を読むと分かること:
  - コンテキストとは何か
  - なぜマネジメントが必要か
  - 具体的にどうすればいいか
- 出典は公式ドキュメント（code.claude.com）のみ

---

### Part 1: 概念 — コンテキストとは何か

### 3. コンテキストウィンドウ = Claudeの「視界」（1枚）
- Claudeが一度に見えるテキストの全体
- この中に入らない情報は**存在しないのと同じ**
- 出典: https://code.claude.com/docs/en/how-claude-code-works

### 4. 何がコンテキストに入るか（1枚）
- 図解: コンテキストウィンドウの構成要素
  - 会話履歴（質問と応答）
  - 読み込んだファイル内容
  - コマンド出力（bash結果）
  - CLAUDE.md（プロジェクト指示）
  - MCPサーバーのツール定義
  - スキル定義
  - システム指示
- **ポイント**: あなたが見えるものだけでなく、裏で読み込まれるものもある
- 出典: https://code.claude.com/docs/en/how-claude-code-works

### 5. 確認方法（1枚）
- `/cost` — トークン使用量
- `/context` — コンテキスト使用状況の詳細
- `/mcp` — MCPサーバーごとのコスト
- **デモ**: 実際に `/context` を実行した画面
- 出典: https://code.claude.com/docs/en/costs

---

### Part 2: 問題 — なぜマネジメントが必要か

### 6. コンテキストが溢れると何が起きるか（1枚）
- 初期の指示が失われる → 「言ったはずなのにやってくれない」
- 応答品質の低下 → 関連情報がノイズに埋もれる
- トークンコストの増加 → 無駄な課金
- CLAUDE.md の遵守率が下がる
- 出典: https://code.claude.com/docs/en/best-practices

### 7. 自動圧縮 — 便利だが制御不能（1枚）
- 自動圧縮の優先順位:
  1. 古いツール出力を削除
  2. 会話を要約
  3. ユーザーのリクエストとコードスニペットは保持
  4. **初期の詳細な指示は失われる可能性**
- 原文引用: "detailed instructions from early in the conversation may be lost"
- **メッセージ**: 自動圧縮は保険であって戦略ではない
- 出典: https://code.claude.com/docs/en/how-claude-code-works

### 8. 公式見解: "Manage context aggressively"（1枚）
- 公式ベストプラクティスが **"aggressively"** と明言している
- つまり受動的な管理では不十分
- → ではどうすればいいか？ まず「膨らませない」、次に「膨らんだら対処する」
- 出典: https://code.claude.com/docs/en/best-practices

---

### Part 3: 解決策① — そもそも膨らませない

### 9. Explore first, then plan, then code（1枚）
- 公式推奨の4フェーズワークフロー:
  1. **Explore** — Plan Mode（`Shift+Tab`）で調査。読み取り専用、変更なし
  2. **Plan** — 実装計画を作成。`Ctrl+G` でエディタで編集可能
  3. **Implement** — Normal Mode に戻して実装
  4. **Commit** — コミットとPR作成
- **なぜ効くか**: 計画なしに実装 → 失敗 → 修正 → 失敗... の繰り返しがコンテキストを最も汚す
- 原文引用: "Letting Claude jump straight to coding can produce code that solves the wrong problem"
- **いつ使うか**: アプローチが不確実 / 複数ファイルにまたがる変更 / 不慣れなコード
- **いつ省略するか**: 1文で差分を説明できるタスク
- 出典: https://code.claude.com/docs/en/best-practices, https://code.claude.com/docs/en/common-workflows

---

### Part 4: 解決策② — 膨らんだ時のセッション制御

### 10. 手段の全体マップ（1枚）
- フローチャート形式:
  - 作業を**続けたい**が膨らんでいる → `/compact`
  - **別タスク**に切り替えたい → `/clear`
  - **間違った方向**に進んだ → `rewind`（Esc+Esc）
  - **中断した作業**を再開したい → `--continue` / `--resume`
  - **ちょっと確認**したいだけ → `/btw`

### 11. /compact — コンテキスト圧縮（1枚）
- 基本: `/compact`
- 焦点指定: `/compact focus on the API changes`
- CLAUDE.md での永続設定: `# Compact Instructions` セクション
- rewind の「Summarize from here」 → 特定ポイントからの精密圧縮
- **Tips**: 焦点を指定しないと重要な決定事項が消える可能性あり
- 出典: https://code.claude.com/docs/en/best-practices

### 12. /clear — 完全リセット（1枚）
- `/clear` でコンテキストを完全にリセット
- CLAUDE.md は再読み込みされる
- **使いどき**: 別タスクへの切り替え / 2回以上同じ修正を繰り返した時
- 原文引用: 公式が「2回以上同じ問題で修正を繰り返したら /clear」を推奨
- 出典: https://code.claude.com/docs/en/best-practices

### 13. Rewind — セッション内巻き戻し（1枚）
- `Esc + Esc` でメニューを開く
- 5つのオプション（Restore code and conversation / conversation only / code only / Summarize from here / Never mind）
- チェックポイント: ユーザープロンプトごと + ファイル編集前に自動作成
- **注意**: Bashコマンド（rm, mv等）の変更は追跡されない
- **デモ**: rewindメニューの実演
- 出典: https://code.claude.com/docs/en/checkpointing

### 14. Resume — セッション再開（1枚）
- `claude --continue` (-c) — 最新セッション再開
- `claude --resume` (-r) — セッション選択
- `--fork-session` — セッション分岐（元は変更なし）
- `/rename "oauth-migration"` — セッション命名
- **Tips**: セッションをブランチのように扱う
- 出典: https://code.claude.com/docs/en/how-claude-code-works

### 15. /btw — 会話履歴に残らないサイド質問（1枚）
- `/btw この関数の引数の型は何？`
- 会話履歴に追加されない → メイン会話の履歴を汚さず、長いタスクを脱線させにくい
- **ただし**: 現在の会話全体は見える（既にコンテキストにある情報で回答）
- **ただし**: ツールは使えない（ファイル読み込み、コマンド実行は不可）
- 追加コストは最小限（プロンプトキャッシュ再利用）
- Claudeの作業中でも使える

| | /btw | サブエージェント |
|---|------|----------------|
| 会話コンテキスト | 親の全会話が見える | 空から開始 |
| ツール | 使えない | 使える |
| 用途 | 既知の情報を確認 | 新しい情報を取りに行く |

- 原文引用: "/btw is the inverse of a subagent"
- 出典: https://code.claude.com/docs/en/interactive-mode#side-questions-with-btw

### 16. 私の使い分け — 開発サイクルに沿った運用（1枚）
- **commit したら → `/compact`**
  - 1つの作業単位が完了した区切り。試行錯誤の履歴はもう不要
- **別の作業に移るなら → `/clear`**
  - 前のタスクのコンテキストは新しいタスクにとってノイズ
- **プロンプトを間違えた・補足が必要だったら → `Esc+Esc`（rewind）**
  - 失敗した試行をコンテキストに残さない

```
[コーディング] → commit → /compact → [次の作業単位]
                                          ↓
                                    全部終わった → /clear → [別タスク]

[プロンプト送信] → 「あ、間違えた」 → Esc+Esc → [正しいプロンプトで再送]
```

---

### Part 5: 解決策③ — コンテキストの分離

### 17. 分離という発想（1枚）
- Part 4は「1つのコンテキストをどう管理するか」
- Part 5は「そもそもコンテキストを分ける」
- 2つのレイヤー:
  - **ファイル分離** → Worktree
  - **コンテキスト分離** → サブエージェント

### 18. Worktree — 並列作業環境（1枚）
- `claude --worktree feature-auth` / `claude -w bugfix-123`
- 内部的にgit worktreeを使用
- 自動クリーンアップ（変更なし→自動削除 / 変更あり→選択）
- **ユースケース**: ターミナル1で機能開発、ターミナル2でバグ修正
- **注意**: 新しいworktreeでは `npm install` 等の環境初期化が必要
- **注意**: 同じリポジトリの worktree は Auto Memory を共有する（メモリまでは分離しない）
- 出典: https://code.claude.com/docs/en/common-workflows

### 19. サブエージェント — コンテキスト分離（1-2枚）
- 本質: 独立したコンテキストウィンドウで実行 → 結果のみ要約でメインに返る
- 図解: メイン会話 → サブエージェント → 要約結果のみ返却
- 組み込み: Explore（Haiku/高速）, Plan, General-purpose
- カスタム: `.claude/agents/` に定義ファイル
- `Ctrl+B` でバックグラウンド化
- **メッセージ**: サブエージェントは「要約フィルター」
- 出典: https://code.claude.com/docs/en/sub-agents

---

### Part 6: 解決策④ — 永続コンテキスト

### 20. 「毎回言い直す」からの脱却（1枚）
- セッションは消えるが、永続コンテキストは残る
- 3つの手段: CLAUDE.md / Auto Memory / スキル
- **メッセージ**: 「毎回言い直す」は最悪のコンテキスト管理

### 21. CLAUDE.md — 明示的な永続指示（1枚）
- 配置場所の優先順位（more specific = higher precedence）:
  - **プロジェクト**（`./CLAUDE.md`）→ 最も高い
  - **ユーザー**（`~/.claude/CLAUDE.md`）→ 中
  - **組織 Managed Policy** → 最も低い（ただし除外不可）
- 200行以下を推奨（超えると遵守率低下）
- `@` インポート構文で分割管理
- `.claude/rules/` でパス指定の条件付きルール（必要時のみ読み込み）
- 出典: https://code.claude.com/docs/en/memory

### 22. Auto Memory & スキル（1枚）
- **Auto Memory**: Claudeが自動で学習内容を記録（管理コストゼロ）
  - gitリポジトリ単位で1つ → worktree間で共有される
  - マシンローカル（クラウド/他マシンとは非共有）
- **スキル**: CLAUDE.md（常時読み込み）→ スキル（オンデマンド読み込み）
- **メッセージ**: 「常時コンテキスト消費」vs「必要時のみ消費」の使い分け
- 出典: https://code.claude.com/docs/en/memory, https://code.claude.com/docs/en/skills

---

### Part 7: まとめ

### 23. コンテキスト管理 早見表（1枚）

| やりたいこと | 手段 | キー/コマンド |
|-------------|------|-------------|
| 使用量を確認 | /context, /cost | — |
| そもそも膨らませない | Plan Mode | `Shift+Tab` |
| 圧縮して続行 | /compact | `/compact <焦点>` |
| 完全リセット | /clear | `/clear` |
| 巻き戻し | rewind | `Esc+Esc` |
| 再開 | resume | `claude -c` / `claude -r` |
| 履歴に残さず確認 | /btw | `/btw <質問>` |
| ファイル分離 | worktree | `claude -w <名前>` |
| コンテキスト分離 | サブエージェント | プロンプトで指示 |
| 永続化 | CLAUDE.md | ファイル編集 |
| 自動学習 | Auto Memory | 自動 |

### 24. 明日からできること（1枚）
- Level 1: `/context` でコンテキスト使用量を意識する
- Level 2: Plan Mode で「探索→計画→実装」を分ける
- Level 3: タスク切り替え時に `/clear`、長い作業では `/compact`
- Level 4: `/btw` で既知の情報をコンテキスト外で確認
- Level 5: サブエージェントで調査をコンテキスト外に委譲
- Level 6: CLAUDE.md / rules / スキルで永続コンテキストを設計

### 25. 参考リンク（1枚）
- https://code.claude.com/docs/en/how-claude-code-works
- https://code.claude.com/docs/en/best-practices
- https://code.claude.com/docs/en/checkpointing
- https://code.claude.com/docs/en/interactive-mode
- https://code.claude.com/docs/en/sub-agents
- https://code.claude.com/docs/en/memory
- https://code.claude.com/docs/en/skills
- https://code.claude.com/docs/en/common-workflows

---

## スライド枚数: 約25枚
## 想定所要時間: 30-40分（デモ含む）

## デモ計画

| スライド | デモ内容 | 準備 |
|---------|---------|------|
| #5 | `/context` 実行 | 適当なプロジェクトでセッション開始 |
| #9 | `Shift+Tab` で Plan Mode 切り替え | — |
| #13 | Esc+Esc でrewindメニュー | 事前にいくつかの変更を加えておく |
| #14 | `claude --resume` でセッション一覧 | 事前に名前付きセッションを数個作成 |
| #15 | `/btw` で質問 → 会話履歴に残らないことを確認 | 同一セッション内で実行 |
| #17 | `claude -w` でworktree作成 | gitリポジトリが必要 |
