# 了解类型脚本泛型

> 原文：<https://dev.to/bgschiller/understanding-typescript-generics-52j6>

让我们考虑一个简单的日志函数

```
function log(x) {
  console.log(x);
  return x;
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现它比单独使用`console.log`更有用，因为我可以在大型链中使用它来查看发生了什么。

```
const commandLineArgs = args
  .map(validate)
  .map(log) // logs values without modifying them
  .filter(differentThanDefault)
  .map(log)
  .reduce(collect, {}); 
```

Enter fullscreen mode Exit fullscreen mode

但是它的类型标志是什么呢？目前，该函数接受一个`any`参数并返回一个`any`值。我们确实希望它能为任何可能的值工作，但是通过返回一个`any`，我们干扰了 TypeScript 跟踪值的类型的能力。

```
const nums: number[] = [5, 6, 7, 8];
const squares = nums.map(x => x * x); // inferred as number[]
const loggedSquares = log(squares); // inferred as any 
```

Enter fullscreen mode Exit fullscreen mode

这是一个问题，因为如果 TypeScript 认为我们的值是类型`any`而不是`number[]`，当我们出错时它不会捕捉到:

```
// Error: Operator '+' cannot be applied to types number[] and 5
const plusFive = squares + 5;

// no complaint from TS
const loggedPlusFive = loggedSquares + 5; 
```

Enter fullscreen mode Exit fullscreen mode

我们真正想说的不是“`log`接受任意类型的 arg，返回任意类型的值”而是“`log`接受某类型的 arg，返回某类型的值*”。泛型函数为我们提供了一种方法。让我们使用泛型重写我们的`log`函数。* 

```
function log<T>(x: T): T {
  console.log(x);
  return x;
} 
```

Enter fullscreen mode Exit fullscreen mode

`<T>`语法引入了所谓的“类型变量”或“类型参数”。就像函数参数是稍后确定的值的替身一样，类型变量是稍后确定的*类型*的替身。给类型命名就是我们如何指定“`log`接受某种类型的变量`T`并返回相同类型的变量`T`”。

### 类型参数推断

在极少数情况下，您可能需要在类型参数中指定想要的具体类型。大多数情况下，TypeScript 会为您解决问题。使用我们之前的`log`函数:

```
const x: number = 5;

log<number>(x); // you're allowed to specify what type you want
log(x); // TS knows x is a number, so it figures out that log<T> should be log<number> 
```

Enter fullscreen mode Exit fullscreen mode

### 输入其他语言的参数

有时，了解相同的概念在其他语言中的表现会很有帮助。Python 是另一种最近在其基础上加入了类型系统的语言。在 python 中，我们需要在使用一个值之前将其声明为类型参数。

```
from typing import Sequence, TypeVar

T = TypeVar('T')  # Declare the type variable 
def first(lst: Sequence[T]) -> T:
  return lst[0] 
```

Enter fullscreen mode Exit fullscreen mode

如果没有那个`T = TypeVar('T')`声明，python 会去寻找一个附近的或者导入的类型，字面上叫做`T`。很可能，当它没有找到一个具有`NameError`名字的类型时，它会失败。更糟糕的是，也许*是*的一种类型，叫做`T`，我们无意中编写了一个只对该类型的值起作用的函数。通过将它声明为`TypeVar`，我们告诉类型检查器:“实际上并没有一个类型叫做`T`。相反，`T`是稍后决定的类型的占位符。

在我看来，TypeScript 的`<T>`是一个更好的语法，但它服务于同样的目的。

### 多个类型参数

一些函数的类型定义有两个或更多的类型变量。`map`是一个常见的例子:它接受某种类型的数组(第一个类型参数)，从第一个类型到另一个类型的函数，并返回第二个类型的数组。不使用名字甚至很难写出来！让我们用名字再试一次:

> `map`接受某种类型`T`的数组，一个从`T`到另一种类型`R`的函数，并产生一个`R`的数组。

通过练习，打字稿的语法将变得比英语更容易阅读。下面是地图的样子:

```
function map<T, R>(lst: T[], mapper: (t: T) => R): R[] 
```

Enter fullscreen mode Exit fullscreen mode

再一次，在
旁边有描述

```
function map
  <T, R>( // for some types T and R
    lst: T[], // lst is an array of T
    mapper: (t: T) => R // mapper is a function from T to R
  ): R[] // The return value is an array of R 
```

Enter fullscreen mode Exit fullscreen mode

### 参考文献

*   [泛型上的类型脚本文档](https://www.typescriptlang.org/docs/handbook/generics.html)*