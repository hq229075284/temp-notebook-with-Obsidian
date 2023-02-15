## enum、never、new


## interface、type


## global、module


## type merge


## union、intersection、

```typescript
// union
type Size = "small" | "medium" | "large"

// intersection
type coord = { x: number } & { y: number }

// 
interface A1<T>{
	p1: T
	p2: number
}

type A2<T> = { x: T } & { y: 1 }
```

## type compatibility

```typescript
// 函数参数兼容
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error

// 赋值后y仍然是原来的类型，y的类型可以保证x执行时不会出错，即x是y的缺省参数的版本
// 换句话说，就是函数参数类型兼容的情况下，参数多的函数可以包容参数少的函数


// 函数返回值兼容
let x = () => ({ name: "Alice" });
let y = () => ({ name: "Alice", location: "Seattle" });

x = y; // OK
y = x; // Error

// 类型系统要求source函数的返回类型要是target函数的返回类型的扩展（子类型），即类型安全


```