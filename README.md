# Claude Code コンテキストマネジメント入門

社内向けプレゼン資料。Claude Code のコンテキスト管理を体系的に解説する。

## 構成

```
slides/
  draft.md          # Marp スライド（ソース）
  output.html       # HTML 出力
  output.pdf        # PDF 出力
  images/           # スライド内で使用する画像

demo/
  script.md         # デモ台本（9シーン）

sources/
  00_source_index.md  # 情報収集の索引
  01_*.md 〜 06_*.md  # 公式ドキュメントからの調査メモ
```

## スライドの再ビルド

```bash
npx @marp-team/marp-cli slides/draft.md --html -o slides/output.html
npx @marp-team/marp-cli slides/draft.md --html --pdf -o slides/output.pdf
```

## 出典

すべての情報は [Claude Code 公式ドキュメント](https://code.claude.com) に基づく。
