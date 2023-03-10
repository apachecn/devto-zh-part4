# 啊哈！理解 Typescript 的类型谓词

> 原文：<https://dev.to/daveturissini/aha-understanding-typescript-s-type-predicates-40ha>

本文将在较高层次上讨论类型谓词。为了理解类型谓词，我们必须首先理解它们在联合类型中是如何使用的。

## 工会类型

在 Typescript 中，变量并不总是局限于单一类型。[联合类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)是一种在单个值上声明多个类型的方法。

```
// value can now be set to a `string`, `boolean`, or `null` value. 
let value: string | boolean | null = ...

interface Cat {
  numberOfLives: number;
}
interface Dog {
  isAGoodBoy: boolean;
}

let animal: Cat | Dog = ... 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用联合类型时，我们必须将可能的类型缩小到当前值的实际类型。[型守卫](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-guards-and-differentiating-types)就是让我们做到这一点的缩小。

## 型卫士

根据官方文件，

> 类型保护是一些执行运行时检查的表达式，它保证类型在某个范围内。

换句话说，当一个字符串也可以是一个数字时，类型守卫保证它是一个字符串。

类型保护与进行特征检测并没有完全不同。主要想法是尝试检测属性、方法或原型，以找出如何处理值。有四种使用类型保护的主要方法:

*   `in`关键字
*   `typeof`关键字
*   `instanceof`关键字
*   带有自定义类型保护的类型谓词

## 类型谓词

虽然您可能熟悉“in”、“typeof”和“instanceof”，但您可能想知道什么是“类型谓词”。类型谓词是一种特殊的返回类型，它通知 Typescript 编译器特定值是什么类型。类型谓词总是附加到接受单个参数并返回布尔值的函数上。类型谓词表示为`argumentName is Type`。

```
interface Cat {
  numberOfLives: number;
}
interface Dog {
  isAGoodBoy: boolean;
}

function isCat(animal: Cat | Dog): animal is Cat {
  return typeof animal.numberOfLives === 'number';
} 
```

Enter fullscreen mode Exit fullscreen mode

对于样本函数，`isCat`，与所有其他类型的保护一样，在运行时执行。由于该函数返回一个布尔值并包含类型谓词`animal is Cat`，如果`isCat`的值为真，Typescript 编译器会将`animal`正确地转换为`Cat`。如果`isCat`评估为假，那么*也将*将`animal`转换为`Dog`。

```
 let animal: Cat | Dog = ...

if (isCat(animal)) {
  // animal successfully cast as a Cat
} else {
  // animal successfully cast as a Dog
} 
```

Enter fullscreen mode Exit fullscreen mode

相当整洁！也许关于自定义类型守卫和类型谓词最好的事情是，我们不仅可以在我们的类型守卫中使用`in`、`instanceof`和`typeof`，而且我们还可以自定义类型检查。只要我们的函数返回一个布尔值，Typescript 就会做正确的事情。