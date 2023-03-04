## enum、never、new

### enum

```typescript
function getFirst() {
    return 1
}

enum A {
    one = getFirst(),
    three = 3,
    six = 6,
    twentyFour = 24,
}

// 可以实现双向映射
A['one'] // 1
A[24] // twentyFour
```
### never
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#the-never-type
```typescript
// never在这里表示函数抛出异常或执行终止
function fail(msg: string): never {
	throw new Error(msg);
}

// never在这里表示无法到达
function F(p: string | number) {
  if (typeof p === 'string') {
    p
  } else if (typeof p === 'number') {
    p
  } else {
    p // type 为 never
  }
}
```

### new
```typescript
type InstanceStruction = {
  x: number
}

var Ctor:new ()=>InstanceStruction
var instance=new Ctor()
instance.x // number

// ------------------------------------------

type InstanceStruction = {
  x: number
}
interface newConstructor {
  new(p: string): InstanceStruction
}

var ClassFn: newConstructor
var instance = new ClassFn('1')
instance.x // number

// ------------------------------------------

class A{
  x:number
  constructor(p:string){
    this.x=1
  }
}
var a:typeof A

interface Ctor{
  new (p:string):{x:number}
}
var b:Ctor

a=b // OK, 近似于typeof A === Ctor

// ------------------------------------------

class Food{}
function fn1(arg:Food){}
function fn2(arg:typeof Food){}

fn1(new Food) // OK
fn2(Food) // OK
```

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
```typescript
interface Obj{
  x:string
}

type numberAlias = number
```

## global、module

```typescript
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
### interface merge
```typescript
interface Box {
	height: number;
	width: number;
}
interface Box {
	scale: number;
}
let box: Box = { height: 5, width: 6, scale: 10 };

// ---------------------------------------------------

interface Cloner {
	clone(animal: Animal): Animal;
}

interface Cloner {
	clone(animal: 'a dog'): Dog;
}

interface Cloner {
	clone(animal: Dog): Dog;
	clone(animal: Cat): Cat;
}
// 👆 seem as 👇
interface Cloner {
	// 此group，参数类型为字符串字面量时，优先级高
	clone(animal: 'a dog'): Dog;
	
	// 此group优先级较高
	// group内部保持原优先级
	clone(animal: Dog): Dog;
	clone(animal: Cat): Cat;
	
	// 此group优先级低
	clone(animal: Animal): Animal;
}
```
### namespace merge
```typescript

// namespace间合并
namespace Animal {
	let haveMuscles = true;
	export function animalsHaveMuscles() {
		return haveMuscles;
	}
}

namespace Animal {
	export function doAnimalsHaveMuscles() {
		return haveMuscles; // Error, because haveMuscles is not accessible here
	}
}

// namespace给class添加静态属性，也可以给函数添加静态成员
class A {
	label: typeof A.B;
}

namespace A {
	export function B() {
		
	}
}

// namespace给enum添加额外属性
enum Color {
  red = 1,
}

namespace Color {
  export function mixColor(colorName: string) {
  }
}

```

### module augmentation
```typescript
// 定义模块外添加的属性
// observable.ts
export class Observable<T> {
  // ... implementation left as an exercise for the reader ...
}

// map.ts
import { Observable } from "./observable";
declare module "./observable" {
  interface Observable<T> {
    map<U>(f: (x: T) => U): Observable<U>; // 仅定义了类型，没有具体实现
  }
}
// 编写具体实现
Observable.prototype.map = function (f) {
  // ... another exercise for the reader
};
```
## union、intersection、generic

```typescript
// union
type Size = "small" | "medium" | "large"

// intersection
type coord = { x: number } & { y: number }

// generic
interface A1<T>{
	p1: T
	p2: number
}

type A2<T> = { x: T } & { y: 1 }

// 受约束的函数泛型
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
	return obj[key];
}
```


## type Inference
https://stackoverflow.com/a/70320446
https://stackoverflow.com/a/64653179


## type compatibility

### 一般

类型结构相似且source类型能满足target类型的要求

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

## Narrowing

```typescript

// typeof 收缩类型范围
function TypeGuard(param: string[] | string | number | null) {
  if (typeof param === 'object') {
    console.log(param);
  } else if (typeof param === 'string') {
    console.log(param);
  } else {
    console.log(param);
  }
}

// if、&&、||、! 收缩类型范围
function Truthiness(param: string | null) {
  // 空字符串或null
  if (param) {
    console.log(param);
  } else {
    console.log(param);
  }
}

// ===、!==、==、!= 收缩类型范围
function Equality(x: string | number, y: string | boolean) {
  if (x === y) {
    console.log(x, y);
  } else if (x === 0) {
    console.log(x);
  } else if (y === false) {
    console.log(y);
  }
}

// in 收缩类型范围
type Fish = { animal: boolean; swim: boolean };
type Bird = { animal: boolean; fly: boolean };
function inGuard(pet: Fish | Bird) {
  if ('animal' in pet) {
    console.log(pet);
  }
  if ('swim' in pet) {
    console.log(pet);
  }
  if ('fly' in pet) {
    console.log(pet);
  }
}

// instanceof 收缩类型范围
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x);
  } else {
    console.log(x);
  }
}

// 分析流程 收缩类型范围
function padLeft(padding: number | string, input: string) {
  if (typeof padding === 'number') {
    return ' '.repeat(padding) + input;
  }
  return padding + input;
}

// user-defined type guard 自定义收缩类型范围
type Fish = { animal: boolean; swim: boolean };
type Bird = { animal: boolean; fly: boolean };
function isFish(pet: Fish | Bird): pet is Fish {
  return 'swim' in pet;
}

function action(pet: Fish | Bird) {
  if (isFish(pet)) {
    pet.swim;
  } else {
    pet.fly;
  }
}

// 类型定义与收缩的思考
interface Shape {
  kind: 'circle' | 'square';
  radius?: number;
  sideLength?: number;
}
const thing: Shape = { kind: 'circle', radius: 1 };
if (thing.kind === 'circle') {
  2 * Math.PI * thing.radius;
}

////////////// --------VS----------------------

interface Circle {
  kind: 'circle';
  radius: number;
}
interface Square {
  kind: 'square';
  sideLength: number;
}
type Shape = Circle | Square;
const thing: Shape = { kind: 'circle', radius: 1 };
if (thing.kind === 'circle') {
  2 * Math.PI * thing.radius;
}
```

## Module Resolution
https://www.typescriptlang.org/docs/handbook/module-resolution.html#how-typescript-resolves-modules

```json
{

  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "moduleResolution": "Node",
    "paths": {
      "@/*":["./src/*"]
    }
  }
}
```