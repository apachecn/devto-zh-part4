# TypeScript 中内置实用工具类型的综合列表

> 原文：<https://dev.to/miloszpp/comprehensive-list-of-built-in-utility-types-in-typescript-c8p>

TypeScript 文档的高级类型部分提到了一些非常有用的内置类型，作为条件类型和映射类型的例子。

我很惊讶地得知，有更多这样的类型，其中一些似乎没有记录。本文包含所有此类类型的列表。

这个列表是基于我在 github 的[上的`es5.d.ts`中找到的内容。](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts)

## 类型列表

### `Partial`

`Partial<T>`返回与`T`属性相同的类型，但它们都是可选的。这在启用`strictNullChecks`标志时非常有用。

只在一个层次上起作用——它不影响嵌套的对象。

`Partial`的一个常见用例是当你需要输入一个函数，让你覆盖一些对象属性的默认值。

```
const defaultSettings: Settings = { /* ... */ };

function getSettings(custom: Partial<Settings>): Settings {
  return { ...defaultSettings, ...custom };
} 
```

**更新:**

但是，这种技术不是 100%类型安全的。正如 [AngularBeginner](https://www.reddit.com/r/typescript/comments/b2xftx/comprehensive_list_of_builtin_utility_types_in/eiw15mm?utm_source=share&utm_medium=web2x) 所指出的，如果`custom`有一个属性已经被显式地设置为`undefined`，那么结果也将导致这个属性未被定义。因此，它的类型(`Settings`)将是谎言。

一个更加类型安全的版本`getSettings`应该是这样的:

```
function getSettings(custom: Partial<Settings>): Partial<Settings> {
  return { ...defaultSettings, ...custom };
} 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1404)。

### `Required`

`Required<T>`从`T`的属性中删除可选性。同样，如果你启用了`strictNullChecks`，你很可能需要它(你应该这样做😉).

与`Required`类似，`Partial`仅在顶层工作。

这个例子在某种程度上与前一个对称。这里，我们接受一个具有一些可选属性的对象。然后，当属性不存在时，我们应用默认值。结果是一个没有可选属性的对象- `Required<Settings>`。

```
function applySettings(settings: Settings) {
  const actualSettings: Required<Settings> = {
    width: settings.width || 100,
    height: settings.height || 200,
    title: settings.title || '',
  }
  // do something...
} 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1411)。

### `Readonly`

这个你可能听说过。`Readonly<T>`返回与`T`属性相同的类型，但都是`readonly`。它对函数式编程非常有用，因为它让你在编译时确保不变性。一个明显的例子是将其用于 Redux 状态。

同样，`Readonly`不会影响嵌套的对象。

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1418)。

### `Pick`

`Pick`允许您创建一个仅具有另一个类型的选定属性的类型。

一个例子是让调用者只重写一些默认属性的特定子集。

```
function updateSize(overrides: Pick<Settings, 'width' | 'height'>) {
  return { ...defaultSettings, ...overrides};
} 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1425)。

### `Record`

`Record`允许您用属于特定类型的关键字定义一个字典。

JavaScript 对象可以非常自然地用作字典。但是，在 TypeScript 中，您通常使用预定义了键集的接口来处理对象。你可以这样写来解决这个问题:

```
interface Options {
  [key: string]: string;
} 
```

`Record`让你以更简洁的方式做到这一点:`type Options = Record<string, string>`。

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1432)。

### `Exclude`

当你从可能值的集合的角度来看类型时，这很有意义。例如，`number` type 可以看作是包含所有数字的集合。`A | B`被称为并集，因为它的可能值集是`A`的可能值和`B`的可能值之和。

`Exclude<T, U>`返回一个类型，该类型的一组值与类型`T`的一组值相同，但删除了所有的`U`值。它有点像子结构，但是定义在集合上。

使用`Exclude`的一个很好的例子就是定义`Omit`。`Omit`接受一个类型和它的键，返回一个没有这个键的类型。

```
interface Settings {
  width: number;
  height: number;
  title: string;
}

type SizeSettings = Omit<Settings, 'title'>;

// type SizeSettings = {
//   width: number;
//   height: number;
// } 
```

`Omit<T, K>`可以通过从`T`中选择除`K`以外的所有按键来定义。首先，我们将`Exclude`从`T`的组合键中取出`K`。接下来，我们将使用这组键从`T`到`Pick`。

```
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>; 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1439)。

### `Extract`

`Extract<T, U>`返回`T`中包含的可分配给`U`的类型。你可以说它返回了`T`和`U`的一个*公共部分*。然而，类型不必完全相同——来自`T`的类型可以分配给`U`就足够了。

例如，您可以使用`Extract`从联合类型
中过滤出函数类型

```
type Functions = Extract<string | number | (() => void), Function>;  // () => void 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1444)。

### `NonNullable`

`NonNullable<T>`从`T`的可能值集中删除`null`和`undefined`。

它在处理`strictNullChecks`和可选属性和参数时非常有用。它对已经不可为空的类型没有影响。

```
type Foo = NonNullable<string | null | undefined>; // string 
```

你可以在我的[上一篇文章](https://codewithstyle.info/Deep-property-access-in-TypeScript/)中找到一个很好的`NonNullable`用法的例子。

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1449)。

### `Parameters`

这个有用的类型返回给定函数的参数类型的元组。

```
function fetchData(id: number, filter: string) {
}

type FetchDataParams = Parameters<typeof fetchData>; // [number, string]

type IdType = FetchDataParams[0]; // number 
```

一个有趣的用法是键入包装函数而不必重复参数列表。

```
function fetchDataLogged(...params: Parameters<typeof fetchData>) {
  console.log('calling fetchData');
  fetchData(...params);
} 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1454)。

### `ConstructorParameters`

`ConstructorParameters`与`Parameters`完全相同，但使用构造函数。

```
class Foo {
  constructor(a: string, b: number) {}
}

type FooConstructorParams = ConstructorParameters<typeof Foo>; 
```

一个警告是你必须记住类名前面的`typeof`。

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1459)。

### `ReturnType`

这个名字是不言自明的——它返回一个由给定函数返回的类型。我发现这种类型真的很有用。

一个例子是在 Redux 中定义动作创建者和减少者。缩减器接受状态对象和动作对象。您可以使用动作创建器的`ReturnType`来键入动作对象。

```
function fetchDataSuccess(data: string[]) {
  return {
    type: 'fetchDataSuccess',
    payload: data
  }
}

function reduceFetchDataSuccess(state: State, { payload }: ReturnType<typeof fetchDataSuccess>) {
} 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1464)。

### `InstanceType`

很有意思。你可以说是赠送给`typeof`操作员的。

它接受构造函数的类型，并返回该函数的实例类型。

在 TypeScript 中，`class C`定义了两件事:

*   用于创建类`C`的新实例的构造函数`C`
*   用于类`C`的对象的接口——实例类型的

 *`typeof C`返回构造函数的类型。

`InstanceType<typeof C>`接受构造函数并返回该函数产生的实例的类型:`C`。

```
class C {
}

type CInstance = InstanceType<typeof C>;  // C 
```

[见实施](https://github.com/Microsoft/TypeScript/blob/4ff71ecb98ccbd882feb1738b0c6f1cc93c2ea66/src/lib/es5.d.ts#L1469)。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的课程！

[高级类型脚本主类](https://www.educative.io/courses/advanced-typescript-masterclass)*