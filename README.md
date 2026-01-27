## これは何？

連続的カルトグラムをJavaScript/D3.jsで実装するためのライブラリを利用した実装例です。
テーマ・データには、SSDSE-C（https://www.nstac.go.jp/use/literacy/ssdse/#SSDSE-C）を元に、米と食パンのみに加工したデータを利用しています。

## データの種類

- /data/japan.topojson...地図データ（編集しないでください）
- /data/theme.csv...テーマデータ（編集可能）

## カスタマイズ方法

テーマデータは「都道府県」列は必ず残した上で、現状の列を削除して、希望データの列を追加してください。画面上のドロップダウンは自動的に列名に入れ替わります。

## カラースキーム

このアプリケーションでは、D3.jsの色補間関数を利用した複数のカラースキームをサポートしています。

### 利用可能なカラースキーム

**ColorBrewer（単調色系）**
- Blues、Greens、Oranges、Purples、Reds

**Matplotlib（多色系）**
- Viridis、Inferno、Magma、Plasma、Cividis、Turbo

**Diverging（分岐型）**
- Red-Blue、Purple-Green、Purple-Orange、Pink-Yellow-Green

### 階級分類方法

本プロジェクトでは、表示モードに応じて異なる階級分類手法を自動的に使い分けています。

**Value モード（通常の値表示）**
- `scaleQuantile()` を使用
- **データの分布に基づいて分類**
- 各色に含まれるデータ数がほぼ等しい
- 分布が不均等なデータセットに適している

**Ranking モード（順位表示）**
- `scaleQuantize()` を使用
- **定義域を等間隔に分割**
- 最小順位から最大順位までを均等に分割
- 順位やランキングなど、均等な間隔が意味を持つデータに適している

### D3.jsの階級分類スケール

D3.jsには、以下の3つの主要な階級分類スケールが用意されています：

| スケール | 分類基準 | 各クラスのデータ数 | 用途 |
|---------|--------|-----------------|------|
| **Quantile** | データ分布 | ほぼ等しい | 分布が不均等なデータ |
| **Quantize** | 定義域の幅 | 異なる場合がある | 順位、一定範囲の値 |
| **Threshold** | 手動設定 | 任意 | 政策基準など固定値 |

## ビルド方法

このプロジェクトは、JavaScriptとCSSファイルをminifyしてデプロイするためのビルドシステムを備えています。

###  ローカルビルド

開発環境で JavaScript/CSS/HTML を minify し、`docs/` に出力します。GitHub Pages など `docs` を公開ルートにするフローに対応しています。

**前提条件:**
- Node.js 12以上がインストールされていること

**手順:**

1. 依存パッケージをインストール
```bash
npm install
```

2. ビルド実行
```bash
npm run build
```

3. 以下のminifyされたファイルが`docs/`に生成されます：
   - `docs/app.min.js` (topogram.js + main.js を連結・minify)
   - `docs/style.min.css` (style.css をminify)
   - `docs/index.html` (不要な空白/コメントを削除)

**ファイルサイズの削減:**
- topogram.js + main.js: 102KB → 41KB (約60%削減)
- style.css: 7.0KB → 5.6KB (約20%削減)

### ローカル・プレビュー

`docs/` を公開するには `npx serve docs` のように出力フォルダを指定してください。

http://localhost:3000/?auth_debug

### リモートビルド・デプロイ

Webサーバにデプロイする際は、以下のファイルをアップロードしてください：

**必須ファイル:**
- `index.html` - メインHTMLファイル
- `docs/app.min.js` - アプリケーション（Cartogramライブラリ + メイン、minify・バンドル版）
- `docs/style.min.css` - スタイルシート（minify版）
- `docs/data/japan.topojson` - 日本地図データ（docs 配下に含めるようになりました）
- `docs/data/theme.csv` - サンプルデータ
- `assets/d3-legend.min.js` - D3凡例プラグイン

**アップロード不要（CDN経由で読み込み）:**
- Bootstrap CSS/JS
- D3.js
- d3-scale-chromatic
- TopoJSON

**ディレクトリ構成:**
```
public_html/
├── index.html
├── docs/
│   ├── app.min.js
│   ├── style.min.css
│   ├── index.html
│   ├── CNAME
│   └── data/
│       ├── japan.topojson
│       └── theme.csv
├── assets/
│   └── d3-legend.min.js
└── data/
    ├── japan.topojson
    └── theme.csv
```

**GitHub Pagesの場合:**

リポジトリのルートまたは`gh-pages`ブランチに上記のファイル構成でpushすれば、自動的に公開されます。

### 開発フロー

1. `assets/main.js`, `assets/topogram.js`, `assets/style.css`を編集
2. `npm run build`を実行してバンドル・minify版を生成
   - `docs/app.min.js` (topogram.js + main.js を連結・minify)
   - `docs/style.min.css` (style.css をminify)
3. 生成された`docs/`ファイルをコミット＆プッシュ
4. リモートサーバにアップロード

### ビルド出力ファイル

ローカルビルド時に`docs/`に生成されるファイル：
- `docs/app.min.js` - バンドルされたアプリケーション（本番用）
- `docs/style.min.css` - Minify済みスタイルシート（本番用）
