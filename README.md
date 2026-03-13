# Claude Code コンテキストマネジメント入門

社内向けプレゼン資料。Claude Code のコンテキスト管理を体系的に解説する。

## 構成

```
slides/
  draft.md          # Marp スライド（ソース）
  output.html       # HTML 出力
  output.pdf        # PDF 出力
  images/           # スライド内画像（SVG, webp）

demo/
  script.md         # デモ台本（10シーン）

sources/
  00_source_index.md  # 情報収集の索引
  01_*.md 〜 06_*.md  # 公式ドキュメントからの調査メモ

資料作成のプロンプトとやりとり.md  # この資料を作る際のプロンプト例と経緯
```

## スライドの再ビルド

```bash
npx @marp-team/marp-cli --no-stdin slides/draft.md --html -o slides/output.html
npx @marp-team/marp-cli --no-stdin slides/draft.md --html --pdf --allow-local-files -o slides/output.pdf
```

## この資料の作り方

この資料自体が Claude Code で作成されています。
`資料作成のプロンプトとやりとり.md` に、最初のプロンプトや作成過程の記録があります。

## 出典

すべての情報は [Claude Code 公式ドキュメント](https://code.claude.com) に基づく。
公式根拠がない内容には「資料内の運用整理」と明示している。
