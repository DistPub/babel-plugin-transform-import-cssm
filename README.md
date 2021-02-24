# babel-plugin-transform-import-cssm

> Based on [`CSS Modules V1`](https://github.com/WICG/webcomponents/blob/gh-pages/proposals/css-modules-v1-explainer.md) [caniuse](https://caniuse.com/?search=adoptedStyleSheets)

Transform import `.cssm` statement to auto assign styles to `document.adoptedStyleSheets`.

## Install

Using npm:

```sh
npm install --save-dev babel-plugin-transform-import-cssm
```

## What is `.cssm` type?

A `.cssm` file is a general `text/css` type file that contains `exported` tag name.

>`exported` must is the last css rule.

You can use [`postcss-modules`](https://github.com/css-modules/postcss-modules) to generate it.

For example:

### postcss-modules In

```css
/* styles.css */
:global .page {
  padding: 20px;
}

.title {
  composes: title from "./mixins.css";
  color: green;
}

.article {
  font-size: 16px;
}

/* mixins.css */
.title {
  color: black;
  font-size: 40px;
}

.title:hover {
  color: red;
}
```
### postcss-modules Out

```css
/* styles.cssm */
._title_116zl_1 {
  color: black;
  font-size: 40px;
}

._title_116zl_1:hover {
  color: red;
}

.page {
  padding: 20px;
}

._title_xkpkl_5 {
  color: green;
}

._article_xkpkl_10 {
  font-size: 16px;
}

exported {
  --json: {"title": "_title_xkpkl_5 _title_116zl_1", "article": "_article_xkpkl_10"}
}
```

## babel-plugin-transform-import-cssm In

```js
import styles from './styles.cssm';
element.innerHTML = '<div class="' + styles.title + '">';
```

## babel-plugin-transform-import-cssm Out

```js
import _styles from './styles.cssm';
document.adoptedStyleSheets = [...document.adoptedStyleSheets, _styles];
const styles = JSON.parse(_styles.cssRules[_styles.cssRules.length - 1].style.getPropertyValue('--json'));
element.innerHTML = '<div class="' + styles.title + '">';
```