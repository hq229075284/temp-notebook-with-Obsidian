# popState

[demo](https://stackblitz.com/edit/web-platform-svhxhs?file=a.html,index.html,script.js,c.html)

| 描述                                | 触发popstate事件 |
| ----------------------------------- | ---------------- |
| window.location.hash改变            | 是               |
| window.history.pushState改变路径    | 否               |
| window.history.replaceState改变路径 | 否               |
| 用户导航行为                        | 是               |
| history.back和history.forward       | 是               |

`window.history.pushState`和`window.history.replaceState`改变hash时不会触发`hashchange`

事件触发，`popstate`优先于`hashchange`



**Note that just calling `history.pushState()` or `history.replaceState()` won't trigger a `popstate` event. **
