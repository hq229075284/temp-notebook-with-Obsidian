# postcss-px2var

[PostCSS] plugin postcss-px2var.

[PostCSS]: https://github.com/postcss/postcss

## Option

| 属性              | 类型          | 默认值               | 描述                                 |     |
| ----------------- | ------------- | -------------------- | ------------------------------------ | --- |
| varName           | string        | postcss-px2var-scale | css变量的名称                        |     |
| selectorBlacklist | Array\<RegExp\> | undefined            | 不处理符合此匹配规则的选择器下的样式 |     |
| fallback          | boolean       | true                 | 是否保留原样式属性                   |     |
| includes                  |      Array\<string\>         |           undefined           |                                      |     |

## Example
1. with config: `{ varName:'scale' }`
```css
/* Input example */
.foo {
  width:10px;
}
```

```css
/* Output example */
.foo {
  width:10px;
  width:calc(10px * var(--scale, 1));
}
```

2. with config: `{ varName:'scale',selectorBlacklist:[/abc/] }`
```css
/* Input example */
.foo {
  width:10px;
}

.abc {
  width:1.1px;
}
```

```css
/* Output example */
.foo {
  width:10px;
  width:calc(10px * var(--scale, 1));
}

.abc {
  width:1.1px;
}
```

3. with config: `{ varName:'scale',selectorBlacklist:[/abc/],fallback:false }`
```css
/* Input example */
.foo {
  width:10px;
}

.abc {
  width:1.1px;
}
```

```css
/* Output example */
.foo {
  width:calc(10px * var(--scale, 1));
}

.abc {
  width:1.1px;
}
```

## Usage

**Step 1:** Install plugin:

```sh
npm install --save-dev postcss @dc/postcss-px2var
```

**Step 2:** Check you project for existed PostCSS config: `postcss.config.js`
in the project root, `"postcss"` section in `package.json`
or `postcss` in bundle config.

If you do not use PostCSS, add it according to [official docs]
and set this plugin in settings.

**Step 3:** Add the plugin to plugins list:

```diff
module.exports = {
  plugins: [
+   require('postcss-px2var')({varName:'scale'}),
    require('autoprefixer')
  ]
}
```
or
```diff
module.exports = {
  plugins: [
+   require('postcss-px2var')({varName:'scale',includes:[/regexp/]}),
    require('autoprefixer')
  ]
}
```
or
```diff
module.exports = {
  plugins: [
+   require('postcss-px2var')({varName:'scale',includes:[/regexp/],fallback:false}),
    require('autoprefixer')
  ]
}
```

[official docs]: https://github.com/postcss/postcss#usage
