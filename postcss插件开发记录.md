# postcss插件开发

## postcss8之前的版本

### 插件开发模板
```javascript
let postcss = require('postcss')

module.exports = postcss.plugin('PLUGIN_NAME', (opts = { }) => {
  // Work with options here
  return (root, result) => {
    // Transform CSS AST here
    root.walkDecls(decl => { /* do something */ })
  }
})
```

[插件开发参考](https://dockyard.com/blog/2018/02/01/writing-your-first-postcss-plugin)

## postcss8

### 插件开发模板

```javascript
/**
 * @type {import('postcss').PluginCreator}
 */
module.exports = (opts = {}) => {
  // Work with options here

  return {
    postcssPlugin: 'pluginName',<- 用于判断是否基于postcss8
    /*
    Root (root, postcss) {
      // Transform CSS AST here
    }
    */

    Declaration (decl, postcss) {
      // The faster way to find Declaration node
    }

    /*
    Declaration: {
      color: (decl, postcss) {
        // The fastest way find Declaration node if you know property name
      }
    }
    */
  }
}

module.exports.postcss = true <- 用于判断是否基于postcss8
```

[插件开发导览1](https://github.com/postcss/postcss/blob/main/docs/writing-a-plugin.md)

[插件开发导览2](https://github.com/postcss/postcss/blob/main/docs/guidelines/plugin.md)


## 在线工具
[AST](https://astexplorer.net/)