# SASS ディレクトリ構造

このドキュメントは、プロジェクトのSASS構成を他プロジェクトで再利用するためのテンプレート・リファレンスです。

## ディレクトリ構造

```
sass/
├── style.scss              # エントリーポイント（メインコンパイル対象）
├── abstracts/
│   ├── _index.scss
│   ├── _variables.scss
│   ├── _functions.scss
│   └── _mixins.scss
├── base/
│   ├── _reset.scss
│   └── _base.scss
├── layout/
│   ├── _header.scss
│   ├── _footer.scss
│   ├── _wrapper.scss
│   └── _kv.scss
├── components/
│   ├── _buttons.scss
│   ├── _fadein.scss
│   └── （その他コンポーネント）
├── pages/
│   ├── _top.scss
│   ├── _company.scss
│   └── （ページ別）
└── utility/
    ├── _lineBreak.scss
    ├── _margin.scss
    └── _padding.scss
```

---

## 最小構成（新規プロジェクト用）

以下のファイルのみ作成すればコンパイル可能です。

### 1. style.scss

```scss
@charset 'utf-8';

@use "abstracts" as *;
@use "base/reset";
@use "base/base";
@use "utility/lineBreak";

// layout, components, pages は必要に応じて追加
```

### 2. abstracts/_index.scss

```scss
@forward 'variables';
@forward 'functions';
@forward 'mixins';
```

### 3. abstracts/_variables.scss

```scss
// Colors
$color-black: #191919;
$color-white: #ffffff;

// Typography
$font-family-jp: 'Noto Serif JP', sans-serif;
$font-size-base: 16px;

// Line Heights
$line-height-tight: 1.5;
$line-height-base: 1.75;

// ブレイクポイント
$breakpoints: (
  'xs': 530px,
  'sm': 670px,
  'md': 768px,
  'lg': 1024px,
  'xl': 1178px,
  'xxl': 1200px
);
```

### 4. abstracts/_functions.scss

```scss
// カスタム関数をここに定義（未使用の場合は空でOK）
```

### 5. abstracts/_mixins.scss

```scss
@use "sass:map";
@use "variables" as *;

@mixin mq($key) {
  @media screen and (min-width: map.get($breakpoints, $key)) {
    @content;
  }
}

@mixin hover-opacity {
  transition: opacity 0.3s ease;
  &:hover {
    opacity: 0.7;
  }
}
```

### 6. base/_reset.scss

```scss
*,
*::before,
*::after {
  box-sizing: border-box;
}

html {
  -moz-text-size-adjust: none;
  -webkit-text-size-adjust: none;
  text-size-adjust: none;
}

body, h1, h2, h3, h4, p, figure, blockquote, dl, dd {
  margin: 0;
}

ul[role='list'],
ol[role='list'] {
  list-style: none;
}

a {
  text-decoration: none;
}

img, picture {
  max-width: 100%;
  display: block;
}

ul, li {
  list-style: none;
  padding-left: 0;
  margin: 0;
}
```

### 7. base/_base.scss

```scss
@use '../abstracts' as *;

html, body {
  height: 100%;
  scroll-behavior: smooth;
}

body {
  font-family: $font-family-jp;
  line-height: $line-height-base;
  color: $color-black;
}

a {
  @include hover-opacity;
  color: $color-black;
}
```

### 8. utility/_lineBreak.scss

```scss
@use "../abstracts" as *;

// SP/PC で <br> の表示を切り替えるユーティリティ
.u-br-sp {
  display: inline;
}

.u-br-pc {
  display: none;
}

@include mq(md) {
  .u-br-sp {
    display: none;
  }
  .u-br-pc {
    display: inline;
  }
}
```

---

## 使用方法

### 変数・ミックスインの読み込み

各パートialsでは次のように読み込む：

```scss
@use '../abstracts' as *;

.myClass {
  color: $color-navy;
  @include mq(md) {
    font-size: $font-size-lg;
  }
}
```

### メディアクエリ（mq）

```scss
.element {
  font-size: 14px;
  @include mq(md) {
    font-size: 16px;
  }
  @include mq(lg) {
    font-size: 18px;
  }
}
```

### 改行制御ユーティリティ（u-br-sp / u-br-pc）

テキストは 1 つのまま、SP と PC で改行位置だけを変えたい場合に使用します。

HTML では、改行したい位置にクラス付きの `<br>` を挿入します。

```html
<p class="hero__lead">
  年会費5万円で、クラブの未来を共に築くビジネス<br class="u-br-sp" />
  パートナーへ。スポンサーシップとは異なる新しい<br class="u-br-pc" />
  応援のカタチが、いま始まります。
</p>
```

- **SP（デフォルト）**: `.u-br-sp` が有効／`.u-br-pc` は非表示
- **PC（`mq(md)` 以上）**: `.u-br-sp` が非表示／`.u-br-pc` が有効

これにより、テキストを複製せずに、レイアウトに合わせて柔軟に改行位置を調整できます。

### コンパイル

```bash
# Dart Sass
sass sass/style.scss sass/style.css

# 監視モード
sass sass/style.scss sass/style.css --watch
```

Live Sass Compiler（VS Code拡張）を使用する場合は、`style.scss` を開いて「Watch Sass」を実行。

---

## 命名規則

- **ファイル**: `_` プレフィックス（パーシャル）
- **変数**: `$` + キャメルケース（例: `$color-navy`）
- **ミックスイン**: ケバブケース（例: `hover-opacity`）
- **クラス**: BEM 推奨（例: `.block__element--modifier`）

---

## 注意事項

- Dart Sass 3.0 対応: `map-get` は `map.get`、`darken()` は変数または `color.adjust` を使用
- `abstracts` の `@forward` 順序: variables → functions → mixins（依存関係に注意）
