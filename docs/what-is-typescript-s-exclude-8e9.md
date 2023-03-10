# 什么是 TypeScript 的 Exclude？

> 原文：<https://dev.to/josnun/what-is-typescript-s-exclude-8e9>

*原贴于[JosNun.com](https://josnun.com/posts/what-is-typescripts-exclude)T3】*

我实话实说，在这里。我*喜欢* TypeScript，但是一些关于它们的实用方法的文档并不是很好。谢天谢地，他们有例子，但有时，即使那些也没什么帮助。对我来说，这是一个总是有一种模糊气氛的实用方法，但是最近我做了一些研究，我发现写一些类型会干净得多。

那么 exclude 是干什么的呢？嗯，[医生是这么说的](https://www.typescriptlang.org/docs/handbook/utility-types.html#excludetu):

> `Exclude<T, U>`:通过从 T 中排除所有可赋给 u 的属性来构造类型。

不太好描述，但至少它给我们指出了正确的方向。在我看来，`Exclude`的类型定义要有用得多。

```
type Exclude<T, U> = T extends U ? never : T; 
```

一种简单的说法是，如果`TypeA`是`TypeB`的一个子类型，它将被移除。这里有一个例子:

```
interface Widget {
  doesStuff: boolean;
}

type Doodad = number | string | symbol | object | Widget;

type PropertyAccessor = Exclude<Doodad, object>; // string | number | symbol
type ExcludeWidget = Exclude<Doodad, Widget>; // string | number | symbol | object;

const string: PropertyAccessor = 'key';
const number: PropertyAccessor = 3;
const symbol: PropertyAccessor = Symbol();

const obj: PropertyAccessor = {}; // Type '{}' is not assignable to type 'symbol'
const widget: PropertyAccessor = {
  doesStuff: true,
}; // Type '{ doesStuff: boolean; }' is not assignable to type 'string | number | symbol'

const obj2: ExcludeWidget = {};
const widget2: PropertyAccessor = {
  doesStuff: true,
} as Widget; 
```

如示例所示，`Exclude`，帮助器使我们能够从类型联合中移除类型。现在，你可能会惊讶地发现`Widget`接口没有包含在`PropertyAccessor`类型中。这是因为接口扩展了对象。如果接口被声明为类型而不是接口，也会发生同样的情况。然而，另一方面，如果我们从`Doodad`中排除`Widget`而不是`object`，那么`obj2`和`widget2`都将完全有效。被允许是有道理的，因为它的类型在联盟中。`widget`不过呢？！我们明确排除了那种类型！

当`Widget`被排除后，`object`仍然是`ExcludeWidget`的有效类型。`Widget` s 是`object`的子类型，但是，既然 object 有效，那么`Widgets`也有效；

经过我的研究和修改，我发现这个名字`Exclude`非常适合这个实用程序的功能。希望现在您对`Exclude`的工作方式有了更好的理解，并且您将能够利用它来编写更干净、更简单的类型，而不会在您排除的类型仍然完全有效时感到沮丧；).