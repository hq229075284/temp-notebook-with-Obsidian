## [Modules](https://www.typescriptlang.org/docs/handbook/modules.html)

> In TypeScript, just as in ECMAScript 2015, any file containing a top-level `import` or `export` is considered a module. Conversely, a file without any top-level `import` or `export` declarations is treated as a script whose contents are available in the global scope (and therefore to modules as well).

### [`export =`and`import = require()`](https://www.typescriptlang.org/docs/handbook/modules.html#export--and-import--require)

> When exporting a module using `export =`, TypeScript-specific `import module = require("module")` must be used to import the module.

### [Optional Module Loading and Other Advanced Loading Scenarios](https://www.typescriptlang.org/docs/handbook/modules.html#optional-module-loading-and-other-advanced-loading-scenarios)

>  If a module identifier is only ever used as part of a type annotations and never as an expression, then no `require` call is emitted for that module.


### [Code Generation for Modules](https://www.typescriptlang.org/docs/handbook/modules.html#code-generation-for-modules)

| ![zOkJd1.md.jpg](https://s1.ax1x.com/2022/12/20/zOkJd1.md.jpg) | ![zOkGZR.md.jpg](https://s1.ax1x.com/2022/12/20/zOkGZR.md.jpg) |
| ------------------------------------------------- | ------------------------------------------------- |


### [Merging ambient class and interface declaration](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-6.html#merging-ambient-class-and-interface-declaration)

```typescript
class Foo {
  public x: number=1;
}

interface Foo {
  y: string;
  fn():void;
}

function bar(foo: Foo) {
  foo.x = 1; // OK, declared in the class Foo
  foo.y = "1"; // OK, declared in the interface Foo
  foo.fn()// ERROR, declared in the interface Foo, but not immpal
}

const foo=new Foo()

bar(foo)
```

### type guard

>Reference:
>[`this`-based type guards](https://www.typescriptlang.org/docs/handbook/2/classes.html#this-based-type-guards)
>[User-defined type guard functions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-6.html#user-defined-type-guard-functions)
>[Using type predicates](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#using-type-predicates)
>[Assertion Functions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#assertion-functions)


