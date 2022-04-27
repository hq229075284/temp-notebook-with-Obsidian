
## 构造函数
```javascript
new Proxy(
  {},
  {
    get(target, propKey) {
      return target[propKey];
    },
  }
);
```

## get

**第三个参数`receiver`代表原始的读操作所在的那个对象**

```javascript
const target = Object.defineProperties({}, {
  foo: {
    value: 123,
    writable: false,
    configurable: false
  },
});

const handler = {
  get(target, propKey) {
    return 'abc';
  }
};

const proxy = new Proxy(target, handler);

proxy.foo// Uncaught TypeError: 'get' on proxy: property 'foo' is a read-only and non-configurable data property on the proxy target but the proxy did not return its actual value (expected '123' but got 'abc')
```

## set
**第四个参数`receiver`，指的是原始的操作行为所在的那个对象**

```javascript
const obj = {};
Object.defineProperty(obj, 'foo', {
  value: 'bar',
  writable: false
});

const handler = {
  set: function(obj, prop, value, receiver) {
    obj[prop] = 'baz';
    return true;
  }
};

const proxy = new Proxy(obj, handler);
proxy.foo = 'baz';
proxy.foo // "bar"
```

>[Return value](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/Proxy/set#return_value "Permalink to Return value")
>
>The `set()` method should return a boolean value.
>
> + Return `true` to indicate that assignment succeeded.
> + If the `set()` method returns `false`, and the assignment happened in strict-mode code, a [`TypeError`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError) will be thrown.
> 
 
## apply
`apply`方法拦截函数的调用、`call`和`apply`操作。

`apply`方法可以接受三个参数，分别是目标对象、目标对象的上下文对象（`this`）和目标对象的参数数组。


## has
`has()`方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时，这个方法会生效。典型的操作就是`in`运算符。

## construct()
`construct()`方法用于拦截`new`命令

`construct()`方法中的`this`指向的是`handler`


