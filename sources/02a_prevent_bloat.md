# そもそも膨らませないコツ: Plan Mode と探索の分離

## 要旨

コンテキスト管理は「膨らんだ後の対処」だけでなく「そもそも膨らませない」ことが重要。公式は "Explore first, then plan, then code" のワークフローを強く推奨しており、Plan Mode で調査と実装を分離することで、不要なコンテキスト消費を防ぐ。

## 根拠（公式ドキュメントからの抽出）

### Explore first, then plan, then code

出典: https://code.claude.com/docs/en/best-practices#explore-first-then-plan-then-code

> Letting Claude jump straight to coding can produce code that solves the wrong problem. Use Plan Mode to separate exploration from execution.

推奨ワークフロー（4フェーズ）:
1. **Explore** — Plan Mode で調査。ファイルを読み、質問に答えるが、変更はしない
2. **Plan** — 詳細な実装計画を作成。`Ctrl+G` でテキストエディタで計画を編集可能
3. **Implement** — Normal Mode に戻して実装。計画に沿って検証しながら進める
4. **Commit** — コミットとPR作成

### Plan Mode とは

出典: https://code.claude.com/docs/en/common-workflows#use-plan-mode-for-safe-code-analysis

- `Shift+Tab` で Plan Mode と Normal Mode を切り替え
- Plan Mode 中は読み取り専用 — ファイルの読み込みと質問のみ、変更は行わない
- 探索結果がコンテキストに蓄積されるが、実装の試行錯誤によるゴミは入らない

### コンテキスト管理の観点での効果

出典: https://code.claude.com/docs/en/best-practices

- **失敗した実装のコンテキスト汚染を防ぐ** — 計画なしに実装 → 失敗 → 修正 → 失敗... の繰り返しがコンテキストを埋める
- **「2回修正したら /clear」ルールの予防版** — そもそも計画してから実装すれば修正の繰り返しが減る
- **サブエージェントとの使い分け**: 調査をサブエージェントに委譲すればメインコンテキストは更にクリーン

### いつ Plan Mode を使うべきか

出典: https://code.claude.com/docs/en/best-practices

> Planning is most useful when you're uncertain about the approach, when the change modifies multiple files, or when you're unfamiliar with the code being modified. If you could describe the diff in one sentence, skip the plan.

- **使うべき時**: アプローチが不確実、複数ファイルにまたがる変更、不慣れなコードの修正
- **不要な時**: 1文で差分を説明できるタスク（typo修正、ログ追加、変数リネーム）

## スライドで使う主張候補

1. **「膨らんだ後の対処」より「膨らませない設計」** — compact/clear は対症療法、Plan Mode は予防
2. **探索と実装を分けるだけでコンテキスト効率が劇的に改善** — 失敗した実装の残骸がコンテキストに入らない
3. **Plan Mode + サブエージェント = 最も効率的な調査パターン** — Plan Mode で方針を決め、サブエージェントで詳細調査

## デモ候補

- `Shift+Tab` で Plan Mode に切り替え、調査 → 計画 → 実装の流れを実演
- Plan Mode で探索した後の `/context` と、直接実装を始めた場合の `/context` を比較
