
## eslint  plugin路径解析方式

eslint配置文件中插件路径解析方式和eslint extends的配置文件中的插件路径解析方式一致，都是相对于主配置文件(入口配置文件)

**Notes:**

1.  Plugins are resolved relative to the config file. In other words, ESLint loads the plugin as a user would obtain by running `require('eslint-plugin-pluginname')` in the config file.
2.  Plugins in the base configuration (loaded by `extends` setting) are relative to the derived config file. For example, if `./.eslintrc` has `extends: ["foo"]` and the `eslint-config-foo` has `plugins: ["bar"]`, ESLint finds the `eslint-plugin-bar` from `./node_modules/` (rather than `./node_modules/eslint-config-foo/node_modules/`) or ancestor directories. Thus every plugin in the config file and base configurations is resolved uniquely.
> [reference](https://eslint.org/docs/latest/use/configure/plugins#configure-plugins)


## [extraFileExtensions](https://typescript-eslint.io/architecture/parser/#configuration)

