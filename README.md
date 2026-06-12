# starter-widget

**npm install 不要・ビルド不要**のゼロ設定スターターです。  
luno の **embed スクリプト**（`embed/luno-xxxxxxxx.js`）で HTML だけブログを公開できます。

ドキュメント原稿: [`docs/site/07-embed.md`](../../docs/site/07-embed.md)（公開 URL: `/docs/embed`）

## セットアップ（3ステップ）

### 1. LUNO 側（フォームセット + ウィジェット）

詳細: [`luno/SETUP.md`](./luno/SETUP.md)

| 状況 | やること |
|------|----------|
| **既にフォームがある**（お客様の声等） | 公開記事があることを確認 → **サイトに埋込 → ウィジェット** で embed を作成・公開 |
| **これから作る** | **フォームを作成** → テンプレート **「ブログ」** → 記事を公開 → ウィジェットを作成 |
| **`blueprint.json`** | 管理画面に JSON アップロードはない。テンプレート「ブログ」が同等 |

SDK の `LUNO_FORM_SET_SLUG` は **不要** です。ウィジェット ID と公開 API キーを使います。

### 2. 認証情報を設定する

**モノレポ開発（推奨）** — 5 スターター共通の `starters/.env` を使います。

```bash
cp starters/.env.example starters/.env
# LUNO_API_URL, LUNO_PUBLIC_API_KEY, LUNO_WIDGET_ID を記入
pnpm dev:starter-widget
```

HTML ソースは書き換えず、サーバーが `YOUR_*` プレースホルダを env から注入します。

**単体利用 / 本番デプロイ** — 各 HTML の以下を **3 箇所** 置き換えます。

| プレースホルダ | 取得場所 |
|----------------|----------|
| `YOUR_PUBLIC_API_KEY` | 設定 → **公開 API キー**（`luno_pub_…`） |
| `YOUR_API_DOMAIN` | API のオリジン（例: `https://api.example.com`、末尾スラッシュなし） |
| `YOUR_WIDGET_ID` | ウィジェット公開後の id（例: `luno-abcdef12`） |

```html
data-api-key="YOUR_PUBLIC_API_KEY"
data-api-url="https://YOUR_API_DOMAIN"
data-widget-id="YOUR_WIDGET_ID"
```

```html
<script src="https://YOUR_API_DOMAIN/public/v1/embed/YOUR_WIDGET_ID.js?api_key=YOUR_PUBLIC_API_KEY" async></script>
```

管理画面の **ウィジェット編集 → 埋め込みタグ** に同形式の完成スニペットがあります。

### 3. HTML ファイルをホスティングする

任意の静的ホスティングに置くだけで完成です（Cloudflare Pages・GitHub Pages 等）。

**モノレポ:** `pnpm dev:starter-widget`（`starters/.env` 参照）

**単体:**

```bash
python3 -m http.server 8080
```

## ファイル構成

| ファイル | 用途 |
|---|---|
| `home.html` | トップページ（最新記事プレビュー） |
| `index.html` | 記事一覧（トップバーフィルター + カードグリッド） |
| `index-sidebar.html` | 記事一覧（サイドバーフィルター） |
| `article.html` | 記事詳細（URL の `?entry=スラッグ` を自動読み取り） |

## ウィジェット種別

テーマ・件数・表示項目は **管理画面のウィジェット設定** が正です（embed JS が HTML に注入します）。

### 一覧 — `#luno-list`

```html
<div id="luno-list"
     data-api-key="..."
     data-api-url="https://YOUR_API_DOMAIN"
     data-widget-id="YOUR_WIDGET_ID">
</div>
```

### 最新記事 — `data-luno-top`

```html
<div data-luno-top
     data-api-key="..."
     data-api-url="https://YOUR_API_DOMAIN"
     data-widget-id="YOUR_WIDGET_ID"
     data-count="5"
     data-heading="最新記事"
     data-link-pattern="article.html?entry={slug}"
     data-see-more-url="index.html">
</div>
```

### 記事詳細 — `#luno-article`

```html
<div id="luno-article"
     data-api-key="..."
     data-api-url="https://YOUR_API_DOMAIN"
     data-widget-id="YOUR_WIDGET_ID"
     data-show-back="true"
     data-back-label="← ブログ一覧へ">
</div>
```

### フィルター — `data-luno-filter`

`data-target` に **同じ widget id** を指定します。

```html
<div data-luno-filter
     data-api-key="..."
     data-api-url="https://YOUR_API_DOMAIN"
     data-type="search"
     data-target="YOUR_WIDGET_ID">
</div>
```

| `data-type` | 機能 |
|---|---|
| `search` | キーワード検索 |
| `category` | カテゴリ絞り込み |
| `monthly` | 月別絞り込み |
| `tags` | タグ絞り込み |

## GitHub 公開

`github.com/luno-cms/starter-widget` への mirror: [`docs/starter-mirror.md`](../../docs/starter-mirror.md)

## 関連

- [`starters/README.md`](../README.md)
- [`examples/README.md`](../../examples/README.md)
