## 示例

更新到vue 3.4之后：
[![pFCky4g.png](https://s11.ax1x.com/2024/01/11/pFCky4g.png)](https://imgse.com/i/pFCky4g)

> 原因：https://github.com/vuejs/core/pull/7958


## 解决方法

1. 在入口文件中显示的引入`vue/jsx`，参考：[jsx.d.ts](https://github.com/vuejs/core/blob/main/packages/vue/jsx.d.ts)
```javascript
import 'vue/jsx'
```

2. 在`tsconfig.json`的`compilerOptions.types`中指定加载jsx类型声明文件，参考：[typeRoots](https://www.typescriptlang.org/tsconfig/#typeRoots)、[types](https://www.typescriptlang.org/tsconfig/#types)
```json
{
  "compilerOptions": {
    "typeRoots": ["./node_modules/@types","./node_modules/vue"],
    "types": ["jsx"]
  }
}
```

3. 设置`tsconfig.json`的`compilerOptions.jsxImportSource`为`vue`，使用`vue/jsx-runtime`，参考：[jsxImportSource](https://www.typescriptlang.org/tsconfig/#jsxImportSource)
```json
{
  "compilerOptions": {
    "jsxImportSource": "vue"
  }
}
```

4. 在每个tsx文件或script部分的顶部添加`/** @jsxImportSource vue */`
```vue
<script lang="tsx" setup>
/** @jsxImportSource vue */
import {
  watch, onMounted, ref, computed,
} from 'vue'

...

</script>
```

