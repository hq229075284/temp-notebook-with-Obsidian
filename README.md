# postcss-px2var

[PostCSS] plugin postcss-px2var.

[PostCSS]: https://github.com/postcss/postcss

1. with config: { varName:'unit' }
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
  width:calc(10px * var(--unit));
}
```

2. with config: { varName:'unit',selectorBlacklist:[/abc/] }
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
  width:calc(10px * var(--unit));
}

.abc {
  width:1.1px;
}
```

3. with config: { varName:'unit',selectorBlacklist:[/abc/],fallback:false }
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
  width:calc(10px * var(--unit));
}

.abc {
  width:1.1px;
}
```

## Usage

**Step 1:** Install plugin:

```sh
npm install --save-dev postcss postcss-px2var
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
+   require('postcss-px2var')({varName:'unit'}),
    require('autoprefixer')
  ]
}
```
or
```diff
module.exports = {
  plugins: [
+   require('postcss-px2var')({varName:'unit',includes:[/regexp/]}),
    require('autoprefixer')
  ]
}
```
or
```diff
module.exports = {
  plugins: [
+   require('postcss-px2var')({varName:'unit',includes:[/regexp/],fallback:false}),
    require('autoprefixer')
  ]
}
```

[official docs]: https://github.com/postcss/postcss#usage
