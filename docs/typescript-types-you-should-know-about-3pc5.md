# 您应该了解的类型脚本类型

> 原文：<https://dev.to/xpbytes/typescript-types-you-should-know-about-3pc5>

在我使用 TypeScript 的日常工作中，我在大多数(如果不是所有的话)项目中使用了许多实用程序类型和标准
类型。本文包含以下主题:

*   在`type-fest`中键入
*   其他类型(自定义、内置或`utility-types`)
*   常见模式
    *   包边型防护装置
    *   `const`数组和并集类型
    *   自定义错误
    *   设置`this`

[![A photo of a lot open books, nicely aligned, taken at FIKA Cafe, Toronto, Canada](img/26fd19ddf25d2b6194ae345b25b60eff.png "Photo by Patrick Tomasso (https://unsplash.com/@impatrickt) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--UDJeCbii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p9wzwyyiaocpv0r6czhz.jpg)

## [`type-fest`T4】](https://github.com/sindresorhus/type-fest)

基本类型脚本类型的集合。

这个`npm`包包含了相当多的(尚未)内置的。我有时使用这个包(并从那里导入),有时将它们复制到我的项目中的一个环境声明文件中。

### `SafeOmit<T, K>` [🌐](https://github.com/sindresorhus/type-fest/blob/master/source/)

从没有特定键的对象类型创建类型。

用例是一个比内置`Omit`更安全的(r)版本，它不对照`T`检查
键`K`，而是对照`any`检查它们。

```
export type SafeOmit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>

type ExecutionOptions = {
  debug: boolean;
  dry?: boolean;
  tag: 'default' | 'name';
}

type ExecutionFlags = SafeOmit<ExecutionOptions, 'tag'>
// => {
//  debug: boolean;
//  dry?: boolean | undefined;
// } 
```

### `ReadonlyDeep<T>` [🌐](https://github.com/sindresorhus/type-fest/blob/master/source/readonly-deep.d.ts)

递归地将`object` s、`Map` s、`Set` s 和`Array` s 及其所有属性/元素转换成不可变的结构。

我的用例主要是在导入 JSON，或者处理抽象语法树的时候。这些需要是完全不可变的(直到它们被克隆),这就强制了这一点。内置的`Readonly<T>`只浅功。

```
import { ReadonlyDeep } from 'type-fest'
import dataJson = require('./data.json')

const data: ReadonlyDeep<typeof dataJson> = dataJson
data.property.value.push('bar')
//=> error TS2339: Property 'push' does not exist on type 'readonly string[]' 
```

### `RequireAtLeastOnce<T, K>` [🌐](https://github.com/sindresorhus/type-fest/blob/master/source/require-at-least-one.d.ts)

创建至少需要一个给定属性的类型。其余的
属性保持不变。

我的用例主要是当我必须确保一个已知的接口方法存在时(通常是 api、服务、转换/转换样式对象)，但是类型的其余部分由总是可用的属性和成员组成。

```
import { RequireAtLeastOne } from 'type-fest'

type Responder = {
  text?: () => string;
  json?: () => string;
  secure?: boolean;
}

const responder: RequireAtLeastOne<Responder, 'text' | 'json'> = {
  json: () => '{"message": "ok"}',
  secure: true
} 
```

### `Merge<A, B>` [🌐](https://github.com/sindresorhus/type-fest/blob/master/source/merge.d.ts)

将两种类型合并成一种新类型。第二种类型的键覆盖第一种类型的键。

我的用例主要是当我想使用`Object.assign`而不是使用析构/扩展来构建我的合并对象时。在下面的例子中，您可以看到默认的`Object.assign`产生了一个不正确的类型。

```
type Stringy = {
  bar: string,
  foo: string
}

type NotStri = {
  foo: number
  other: boolean
}

const stringy: Stringy = { bar: 'bar', foo: 'foo' }
const notstri: NotStri = { foo: 42, other: true }

const result1 = Object.assign(stringy, notstri)
//       infers Object.assign<Stringy, NotStri>
result1.foo
// => string & number

export type Merge<T, V> = Omit<T, Extract<keyof T, keyof V>> & V;
const result2: Merge<Stringy, NotStri> = Object.assign(stringy, notstri)

result2.foo
// => number

const result3 = { ...stringy, ...notstri }
// => number 
```

### `Mutable<T>` [🌐](https://github.com/sindresorhus/type-fest/blob/master/source/mutable.d.ts)

将具有`readonly`属性的对象转换为可变对象。
之逆`Readonly<T>`。

我个人很少使用这个*，因为我倾向于`Object.freeze`那些“真正”`Readonly`的变量。正如`Required<T>`是`Partial<T>`的逆，`Mutable<T>`是`Readonly<T>`的逆。* 

```
import {Mutable} from 'type-fest';

type Foo = {
    readonly a: number;
    readonly b: string;
};

const mutableFoo: Mutable<Foo> = { a: 1, b: '2' };
mutableFoo.a = 3; 
```

## 其他类型

### `WithFoo<T>`

每当我有一些数据`T`并修改它，使它有更多的数据，我通常使用一个包装类型，这样就很容易在进行中用*组合*类型。

```
interface MyType {
  bar: 'string';
}

type WithFoo<T> = T & { foo: number }

const data: MyType[] = [{ bar: 'first' }, { bar: 'second' }]
const dataWithFoo: WithFoo<MyType>[] = data.map((item, index) => ({ ...item, foo: index }))

// The inverse uses SafeOmit
type WithoutFoo<T> = Omit<T, 'foo'> 
```

### 至少一个

有时我想确保一个数组至少有一个项目。有些类型库实际上定义了一个*整体*而不仅仅是这个简单的别名，但是这超出了本文的范围。

```
type AtLeastOne<T> = [T, ...T[]] 
```

### `PromiseType<T>` [🌐](https://github.com/piotrwitek/utility-types#promisetypet)

其中一个更有趣的解包器。这给出了一个`Promise<T>`类型的内部类型`T`。当某事将解开类型，或者你想要在承诺的上下文之外工作或者构建一个新的承诺类型时有用(例如`Promise<WithLabel<PromiseType<Original>>>`)。

```
import { PromiseType } from 'utility-types';

type Response = PromiseType<Promise<string>>;
// => string 
```

### `ReturnType<T>`(内置)

获取函数类型的返回类型。

这是我使用的所有类型中最强大的推断类型之一。不是
一遍又一遍地复制类型期望，如果我知道一个函数保证调用(或期望调用)一个函数`foo`，并且我返回结果，我给它返回类型`ReturnType<typeof foo>`，它将返回类型从`foo`的函数声明转发到当前函数。

```
type T10 = ReturnType<() => string>
// => string
type T11 = ReturnType<(s: string) => void>
// => void

function foo(): Promise<number> { return Promise.resolve(42) }
type FooResult = ReturnType<typeof foo>
// => Promise<number> 
```

### `InstanceType<T>`(内置)

获取构造函数类型的实例类型。

如果我有一个构造函数类型(一个可构造的类型)，我就使用这个，但是我需要使用这个构造函数的`ReturnType<T>`。或多或少是`ConstructorType<T>`的逆。

```
class C {}

type T20 = InstanceType<typeof C>;
// => C 
```

### `ConstructorType<T>`

匹配一个 [`class`构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)

当我有一个类型(`T`)并且我创建了一个生成这些类型的工厂时，或者当我需要构造器*类型*，给定一个实例类型时，我使用这个。或多或少是`InstanceType<T>`的逆。

```
export type ConstructorType<T> = new(...arguments_: any[]) => T 
```

## 常见图案

### 超载式警卫

我经常使用自定义字体保护，以便轻松缩小非常宽的字体。宽类型的问题是，在检查是否存在或缩小范围之前，您只能访问*交叉点*。

有时，您希望检查的不仅仅是一个宽类型，并且不希望 typeguard 在它不匹配某个缩小谓词时分配 *never* 。请参见下面的示例。

```
import { AST_NODE_TYPES, TSESTree } from "@typescript-eslint/typescript-estree";

// Aliases for these types, so they are easy to access
type Node = TSESTree.Node
type BinaryExpression = TSESTree.BinaryExpression

// Store all the possible values for the operator property
type BinaryOperator = BinaryExpression['operator']

// Define a special type that narrows the operator
type BinaryExpressionWithOperator<T extends BinaryOperator> = BinaryExpression & { operator: T }

// Generic overload that doesn't test for operator
export function isBinaryExpression(node: Node): node is BinaryExpression
// Special overload that only matches if the opertor matches
export function isBinaryExpression<T extends BinaryOperator>(node: Node, operator: T): node is BinaryExpressionWithOperator<T>

// Implementation that allows both arguments
export function isBinaryExpression(node: Node, operator?: string): node is BinaryExpression {
  return node.type === AST_NODE_TYPES.BinaryExpression && (
    operator === undefined
    || node.operator === operator
  )
}

const generic: Node = {
  type: 'BinaryExpression',
  operator: '+',
  /*...*/
} as Node
// => TSESTree.ArrayExpression
//    | TSESTree.ArrayPattern
//    | TSESTree.ArrowFunctionExpression
//    | TSESTree.AssignmentExpression
//    | TSESTree.AssignmentPattern
//    | TSESTree.AwaitExpression
//    | ... 150 more ...
//    | TSESTree.YieldExpression

if (isBinaryExpression(generic)) {
  // typeof generic is now
  // => { type: 'BinaryExpression', operator: BinaryOperator, left: ..., }
} else {
  // typeof generic is now anything except for
  // ~> { type: 'BinaryExpression' }
}

if (isBinaryExpression(generic, '+')) {
  // typeof generic is now
  // => { type: 'BinaryExpression', operator: '+', left: ..., }
} else {
  // typeof generic is still Node
} 
```

### `const`阵列和`OneOf<const Array>`

通常情况下，您需要允许一组不同的值。自从 TypeScript 3.4
以来，没有必要使用帮助函数进行奇怪的转换。

下面的例子在`A`中有一组选项，并定义了联合类型`OneOfA`，它是`A`的选项之一。

```
export type OneOf<T extends ReadonlyArray<any>> = T[number]

const A = ['foo', 'bar', 'baz'] as const
type OneOfA = OneOf<typeof A>
// => 'foo' | 'bar' | 'baz'

function indexOf(key: OneOfA): number {
  return A.indexOf(key)
  // never returns -1
} 
```

### 自定义错误

根据 TypeScript 2.1，内置[的编译很奇怪](https://github.com/Microsoft/TypeScript-wiki/blob/master/Breaking-Changes.md#extending-built-ins-like-error-array-and-map-may-no-longer-work)。如果不需要支持 IE10 或更低版本，下面的模式很好:

```
class EarlyFinalization extends Error {
  constructor() {
    super('Early finalization')
    // Doesn't work on IE10-
    Object.setPrototypeOf(this, EarlyFinalization.prototype);

    // Adds proper stacktrace
    Error.captureStackTrace(this, this.constructor)
  }
} 
```

### 设定`this`

(至少)有*两种*方式告诉 TypeScript 一个函数的当前上下文`this`值是多少。第一个是给你的函数添加一个参数`this`:

```
interface Traverser {
  break(): void
}

function walker(this: Traverser, root: Node) {
  this.break()
  // no error
} 
```

如果你在一个`class`或类似函数的范围之外声明函数，但是你知道`this`值将被绑定到什么，这将是非常有用的。

第二种方法实际上允许你在函数
的之外定义它

```
interface HelperContext {
  logError: (error: string) => void;
}

const helperFunctions: { [name: string]: (() => void) } & ThisType<HelperContext> = {
  hello: function() {
      this.logError("Error: Something went wrong!");
      // TypeScript successfully recognizes that "logError" is a part of "this".

      this.update();
      // TS2339: Property 'update' does not exist on HelperContext.
  }
} 
```

如果您正在绑定一组函数，这将非常有帮助。

## 结论

TypeScript 有很多宝石💎在用户世界更是如此。确保在使用`as unknown as X`或`: any`之前，检查内置类型、`type-fest`和您自己的代码片段集合。很多时候，做事情真的有一个合适的方法。

[![A photo of yellow and gray, cube shaped houses, at Rotterdam, The Netherlands](img/eff3263d38104dc51bb84c092e160367.png "Photo by Nicole Baster (https://unsplash.com/@nicolebaster) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--wGggZipw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/96restcss1m07ljvl67q.jpg)**