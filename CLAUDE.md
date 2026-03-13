# プロジェクト概要

Claude Code コンテキストマネジメントの社内プレゼン資料リポジトリ。

## 技術スタック

- スライド: Marp（Markdown → HTML/PDF）
- ビルド: `npx @marp-team/marp-cli --no-stdin slides/draft.md --html -o slides/output.html`
- PDF出力: `npx @marp-team/marp-cli --no-stdin slides/draft.md --html --pdf --allow-local-files -o slides/output.pdf`

## ファイル構成

- `slides/draft.md` — Marp スライド本体
- `slides/images/` — スライド内画像（SVG, webp）
- `demo/script.md` — デモ台本
- `sources/` — 公式ドキュメントからの調査メモ（参照用、編集不要）

## コンテンツ方針

- すべての情報の出典は Claude Code 公式ドキュメント（code.claude.com）のみ
- 公式根拠がない内容を含める場合は「資料内の運用整理」と明示する
- スライド編集後は HTML と PDF の両方を再出力する

## 言語

- ドキュメント・コミットメッセージ・会話はすべて日本語
