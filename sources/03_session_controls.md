# セッション制御: compact / clear / rewind / resume / btw

## 要旨

コンテキストを能動的に管理するための3つの主要手段。`/compact` で圧縮、rewind で巻き戻し、resume で再開。それぞれ「いつ使うか」「何が保持されるか」が異なる。

## 根拠（公式ドキュメントからの抽出）

### /compact — コンテキスト圧縮

出典: https://code.claude.com/docs/en/best-practices

```bash
/compact                              # デフォルト圧縮
/compact focus on the API changes     # 焦点を指定して圧縮
```

- 古いツール出力をクリアし、会話を要約
- カスタムプロンプトで「何を保持するか」を指示可能
- CLAUDE.md に `# Compact Instructions` セクションを追加すると、圧縮時のデフォルト焦点を設定可能

```markdown
# Compact Instructions
When you are using compact, please focus on test output and code changes
```

**効く場面**: 長い会話の途中でコンテキストが膨らんでいるが、作業は継続したい
**効かない場面**: 根本的にタスクが変わった場合（→ `/clear` が適切）
**誤用リスク**: 焦点を指定しないと、重要な決定事項が要約で消える可能性

### /clear — コンテキスト完全リセット

出典: https://code.claude.com/docs/en/best-practices

```bash
/clear
```

- コンテキストウィンドウを完全にリセット
- 会話履歴をすべてクリア
- CLAUDE.md は再読み込みされる

**効く場面**: 別タスクへの切り替え、失敗したアプローチのリセット
**効かない場面**: 現在の作業を続けたい場合（→ `/compact` が適切）
**誤用リスク**: なし（やり直しコストのみ）

### Rewind（リワインド）— セッション内巻き戻し

出典: https://code.claude.com/docs/en/checkpointing

```bash
Esc + Esc    # rewindメニューを開く
```

復元オプション：
1. **Restore code and conversation** — コードと会話の両方を復元
2. **Restore conversation** — 会話のみ復元（コードは現状維持）
3. **Restore code** — コードのみ復元（会話は現状維持）
4. **Summarize from here** — 選択メッセージ以降の会話を圧縮
5. **Never mind** — キャンセル

チェックポイントの仕組み：
- ユーザープロンプトごとに自動作成
- ファイル編集前に自動作成
- Bashコマンド（`rm`, `mv`等）によるファイル変更は追跡されない

**効く場面**: 間違った方向に進んだ実装を巻き戻したい、異なるアプローチを試したい
**効かない場面**: Bashで直接ファイル操作した変更の復元
**誤用リスク**: 外部プロセスやBashコマンドの変更は戻せないことを理解しておく必要あり

### Resume（レジューム）— セッション再開

出典: https://code.claude.com/docs/en/how-claude-code-works

```bash
claude --continue          # 最新セッションを再開（-c）
claude --resume            # セッション選択メニューを表示
claude --resume "auth-fix" # 名前で指定して再開（-r）
claude --continue --fork-session  # 最新セッションを複製して再開
```

セッション命名：
```bash
/rename "oauth-migration"  # セッションに名前を付ける
```

**効く場面**: 中断した作業の再開、翌日の作業継続
**効かない場面**: コンテキストが既に大きい場合（再開してもコンテキストは膨らんだまま）
**誤用リスク**: 同じセッションを複数ターミナルで再開するとメッセージが混在する

### --fork-session — セッション分岐

出典: https://code.claude.com/docs/en/how-claude-code-works

```bash
claude --continue --fork-session
claude -r "session-id" --fork-session
```

- 新しいセッションIDを作成し、元のセッションは変更なし
- 並行して異なるアプローチを試す際に有効

### /btw — サイド質問（会話履歴に残らない）

出典: https://code.claude.com/docs/en/interactive-mode#side-questions-with-btw

```bash
/btw この関数の引数の型は何？
/btw あのAPIのエンドポイントは？
```

- 回答がdismissibleなオーバーレイに表示される
- **会話履歴に追加されない** — 質問と回答はエフェメラル（一時的）で、メイン会話の履歴を汚さない
- **現在の会話全体は見える** — 既にコンテキストにある情報を元に回答する
- **ツールは使えない** — ファイル読み込み、コマンド実行、検索はできない。既にコンテキストにある情報のみで回答
- **追加コストは最小限** — 親会話のプロンプトキャッシュを再利用
- **Claudeの作業中でも使える** — メインの処理を中断しない
- フォローアップ不可（1回の応答で完結）
- Claude Code 2.1.72（2026年3月）で追加

公式の位置付け：
> `/btw` is the inverse of a subagent: it sees your full conversation but has no tools, while a subagent has full tools but starts with an empty context.

**サブエージェントとの対比**:
| | /btw | サブエージェント |
|---|------|----------------|
| 会話コンテキスト | 親の全会話が見える | 空のコンテキストから開始 |
| ツール | 使えない | 使える |
| 用途 | 既知の情報を確認 | 新しい情報を取りに行く |

**効く場面**: 作業中の「ちょっとした確認」— Claudeが既に読んだコードや過去の決定について質問
**効かない場面**: 新しいファイルを読んだりコマンドを実行する必要がある場合（→ サブエージェント）
**誤用リスク**: 重要な決定事項を `/btw` で確認すると、後で参照できない（履歴に残らないため）

## スライドで使う主張候補

1. **3つの手段を使い分ける判断基準**: 続けたい→compact、切り替えたい→clear、戻したい→rewind、再開したい→resume
2. **rewind の「Summarize from here」はcompactの精密版** — 特定のポイントから要約できる
3. **resume + fork-session で「セーブポイント」的運用** — 異なるアプローチを安全に試せる
4. **セッション命名は未来の自分への投資** — `/rename` しておけば後で探しやすい
5. **`/btw` はサブエージェントの逆** — 既知の情報を確認するなら /btw、新しい情報を取りに行くならサブエージェント

## デモ候補

- Esc+Esc でrewindメニューを開き、各オプションの違いを見せる
- `claude --resume` でセッション一覧を見せ、名前付きセッションの探しやすさを示す
- `--fork-session` で同じ出発点から2つのアプローチを試す
- `/btw` で質問し、会話履歴に残らないこと・長いタスクを中断せずに確認できることを実演
