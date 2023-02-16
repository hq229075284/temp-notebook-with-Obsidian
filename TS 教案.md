## enum、never、new


## interface、type

### 拓展方式不同
```typescript
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}

type Bear = Animal & { 
  honey: boolean 
}
```

### 是否多次声明类型

```typescript
// OK, participate declaration merging. 参与类型合并
interface A {
  title: string
}  
interface A {
  ts: TypeScriptAPI
}

// Error, Duplicate identifier. 重复定义
type B = {
  title: string
}  
type B = {
  ts: TypeScriptAPI
}
```

### 声明限制

interface仅用于声明对象类型（object, function, newable function），interface不支持声明基本数据类型（rename）
```
interface Obj{
  x:string
}

type numberAlias = number
```

## global、module

```
// global.ts
interface Obj{
  x:string
}

// customer.ts
var a:Obj
a.x // 类型为string

// -------------------------------------------------------

// module.ts
interface Obj{
  x:string
}
export {}

// customer.ts
var a:Obj // Cannot find name 'Obj'
```


## type merge


## union、intersection、type parameters

```typescript
// union
type Size = "small" | "medium" | "large"

// intersection
type coord = { x: number } & { y: number }

// type parameters
interface A1<T>{
	p1: T
	p2: number
}

type A2<T> = { x: T } & { y: 1 }
```

## type compatibility

### 一般

类型结构相同即可

### 函数

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

### Class

class实例的对象成员相同即可，包括成员类型也要相同（publish、protect、private）,等号左侧class类型中存在private或protect成员时，等号右侧class类型中也要存在对应类型的成员，且这些成员来自于同一个源(class)

```typescript
class Private1{
  private a=1
}

class Private2{
  private a=2
}

var p1:Private1
var p2:Private2
p1=p2 // Types have separate declarations of a private property 'a'

// ---------------------------------------------------

class Protect1{
  protected b=2
}

class Protect2{
  protected b=3
}

var p3:Protect1
var p4:Protect2
p3=p4 // Property 'b' is protected but type 'Protect2' is not a class derived from 'Protect1'.

// ---------------------------------------------------

class Base{
  private a=2
  protected b=3
}

class Derived1 extends Base{
  public c=3
}

class Derived2 extends Base{
  public c=4
}

var d1:Derived1
var d2:Derived2
d1=d2 // OK
```

### 特殊类型

[`Any`,`unknown`,`object`,`void`,`undefined`,`null`, and`never`assignability](https://www.typescriptlang.org/docs/handbook/type-compatibility.html#any-unknown-object-void-undefined-null-and-never-assignability)


## conditional type

```typescript
type target<T> = T extends Number ? Number : String 
```