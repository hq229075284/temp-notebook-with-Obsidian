```vue
<slot v-if="a" v-bind="params">
<slot v-if="b">
```

```vue
<slot v-bind="a.b">
```

**slot + v-bind + v-if => 编译后的代码运行时，会将含key的字面量对象和v-bind对象混合后，传递给子节点**

[![pFCAzyn.png](https://s11.ax1x.com/2024/01/11/pFCAzyn.png)](https://imgse.com/i/pFCAzyn)
