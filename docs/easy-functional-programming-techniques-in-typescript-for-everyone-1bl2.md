# 面向所有人的简单函数式编程技术

> 原文：<https://dev.to/deepu105/easy-functional-programming-techniques-in-typescript-for-everyone-1bl2>

*最初发布于[deepu . tech](https://deepu.tech/functional-programming-in-typescript/)T3。*

围绕函数式编程(FP)有很多炒作，很多酷孩子都在做，但它不是银弹。像其他编程范例/风格一样，函数式编程也有其优点和缺点，人们可能更喜欢一种范例而不是另一种。如果您是一名 TypeScript/JavaScript 开发人员，并且想尝试函数式编程，请不要担心，您不必学习面向函数式编程的语言，如 Haskell 或 Clojure，因为 JavaScript 和 TypeScript 已经涵盖了您，这篇文章就是为您准备的。

如果你正在寻找 Java 或 Golang 的函数式编程，请查看本系列的其他文章。

我不会深入所有函数式编程概念的细节，相反，我会把重点放在你可以在 TypeScript 中做的符合函数式编程概念的事情上。我也不会讨论函数式编程的利弊。

请记住，虽然这篇文章是关于 TypeScript 的，但是你也可以很容易地在 JavaScript 中做同样的事情，因为 TypeScript 只是 JavaScript 的一个类型化超集。

* * *

## 什么是函数式编程？

As per Wikipedia,

> 函数式编程是一种编程范式——一种构建计算机程序的结构和元素的风格——将计算视为数学函数的评估，并避免改变状态和可变数据。

因此，在函数式编程中，有两条非常重要的规则

*   **无数据突变**:表示一个数据对象在创建后不应该被改变。
*   **无隐式状态**:应避免隐藏/隐式状态。在函数式编程中，状态并没有被消除，相反，它变得可见和显式

这意味着:

*   **无副作用**:一个功能或操作不应该改变其功能范围之外的任何状态。也就是说，一个函数应该只返回一个值给调用者，而不应该影响任何外部状态。这意味着程序更容易理解。
*   **纯函数唯一**:函数代码是幂等的。函数应该只根据传递的参数返回值，不应该影响(副作用)或依赖全局状态。这样的函数对于相同的参数总是产生相同的结果。

除此之外，下面还有一些函数式编程的概念可以应用到 TypeScript 中，我们将会进一步讨论这些概念。

*   [高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)
*   [关闭](https://en.wikipedia.org/wiki/Closure_(computer_programming))
*   [阿谀奉承](https://en.wikipedia.org/wiki/Currying)
*   [递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))
*   [懒惰评估](https://en.wikipedia.org/wiki/Evaluation_strategy)
*   [参照透明度](https://en.wikipedia.org/wiki/Referential_transparency)

使用函数式编程并不意味着要么全部要么什么都没有，你总是可以使用函数式编程概念来补充 TypeScript 中面向对象的概念。无论您使用哪种范式或语言，只要有可能，就可以利用函数式编程的好处。这正是我们将要看到的。

* * *

## TypeScript 中的函数式编程

TypeScript 不是一种纯粹的函数式语言，而是提供了许多与函数式语言一致的概念，所以让我们看看如何在 TypeScript 中应用上面的一些函数式编程概念。

### 一阶和高阶函数

一等函数(作为一等公民的函数)意味着你可以将函数赋给变量，将一个函数作为参数传递给另一个函数，或者从另一个函数返回一个函数。TypeScript 支持这一点，因此使得闭包、currying 和高阶函数等概念更容易编写。

只有当一个函数接受一个或多个函数作为参数，或者返回另一个函数作为结果时，它才可以被认为是高阶函数。

在 TypeScript 中，这很容易做到

```
type mapFn = (it: string) => number;

// The higher-order-function takes an array and a function as arguments
function mapForEach(arr: string[], fn: mapFn): number[] {
    const newArray: number[] = [];
    arr.forEach(it => {
        // We are executing the method passed
        newArray.push(fn(it));
    });
    return newArray;
}

const list = ["Orange", "Apple", "Banana", "Grape"];

// we are passing the array and a function as arguments to mapForEach method.
const out = mapForEach(list, (it: string): number => it.length);

console.log(out); // [6, 5, 6, 5] 
```

Enter fullscreen mode Exit fullscreen mode

但是在 JavaScript/TypeScript 中，我们也可以使用内置的函数方法，如 map、reduce 等，简单地做到这一点。

```
const list = ["Orange", "Apple", "Banana", "Grape"];

// we are passing a function as arguments to the built-in map method.
const out = list.map(it => it.length);

console.log(out); // [6, 5, 6, 5] 
```

Enter fullscreen mode Exit fullscreen mode

在 TypeScript 中闭包和 currying 也是可能的

```
// this is a higher-order-function that returns a function
function add(x: number): (y: number) => number {
    // A function is returned here as closure
    // variable x is obtained from the outer scope of this method and memorized in the closure
    return (y: number): number => x + y;
}

// we are currying the add method to create more variations
var add10 = add(10);
var add20 = add(20);
var add30 = add(30);

console.log(add10(5)); // 15
console.log(add20(5)); // 25
console.log(add30(5)); // 35 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript/JavaScript 中也有许多内置的声明性高阶函数，如`map`、`reduce`、`forEach`、`filter`等。还有许多库提供了在 TypeScript/JavaScript 中使用的函数接口。

### 纯函数

正如我们已经看到的，一个纯函数应该只根据传递的参数返回值，而不应该影响或依赖全局状态。在 TypeScript 中很容易做到这一点。

这相当简单，就拿下面这个纯函数来说吧。对于给定的输入，它总是返回相同的输出，并且它的行为是高度可预测的。如果需要，我们可以安全地缓存该方法。

```
function sum(a: number, b: number): number {
    return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在这个函数中添加额外的一行，行为将变得不可预测，因为它现在具有影响外部状态的副作用。

```
const holder = {};

function sum(a: number, b: number): number {
    let c = a + b;
    holder[`${a}+${b}`] = c;
    return c;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以尽量保持你的函数简单明了。使用像 [ESLint](https://eslint.org/) 和 [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint) 这样的工具是有可能实现的。

### 递归

函数式编程更喜欢递归而不是循环。让我们看一个计算一个数的阶乘的例子。

在传统的迭代方法中:

```
function factorial(num: number): number {
    let result = 1;
    for (; num > 0; num--) {
        result *= num;
    }
    return result;
}

console.log(factorial(20)); // 2432902008176640000 
```

Enter fullscreen mode Exit fullscreen mode

使用下面的递归也可以做到这一点，递归在函数式编程中很受欢迎。

```
const factorial = (num: number): number =>
    num == 0 ? 1 : num * factorial(num - 1);

console.log(factorial(20)); // 2432902008176640000 
```

Enter fullscreen mode Exit fullscreen mode

递归方法的缺点是，与迭代方法相比，它在大多数情况下会更慢(我们的目标是代码的简单性和可读性)，并且可能会导致堆栈溢出错误，因为每个函数调用都需要作为一个帧保存到堆栈中。为了避免这种尾部递归是优选的，特别是当递归进行太多次时。在尾递归中，递归调用是函数执行的最后一件事，因此编译器不需要保存函数堆栈帧。大多数编译器可以像优化迭代代码一样优化尾部递归代码，从而避免性能损失。尾部调用优化是 ECMAScript 规范的一部分，但不幸的是，大多数 JavaScript 引擎还不支持这一点。

现在使用尾部递归，同样的函数可以写成如下，但是根据引擎的不同，这可能不会被优化，尽管有[的变通方法](https://blog.logrocket.com/using-trampolines-to-manage-large-recursive-loops-in-javascript-d8c9db095ae3/)，但是它在基准测试中表现得更好。

```
const factorialTailRec = (num: number): number => factorial(1, num);

const factorial = (accumulator: number, val: number): number =>
    val == 1 ? accumulator : factorial(accumulator * val, val - 1);

console.log(factorialTailRec(20)); // 2432902008176640000 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性和不变性，考虑在编写 TypeScript 代码时使用递归，但是如果性能很重要或者迭代次数很大，则使用标准循环。

### 懒评

惰性求值或非严格求值是将表达式的求值延迟到需要时进行的过程。一般来说，TypeScript 进行严格/急切的求值，但是对于像`&&`、`||`和`?:`这样的操作数，它进行懒惰的求值。我们可以利用短路、高阶函数、闭包和[记忆](https://en.wikipedia.org/wiki/Memoization)技术来进行惰性求值。

举个例子，TypeScript 急切地评估一切。

```
function add(x: number): number {
    console.log("executing add"); // this is printed since the functions are evaluated first
    return x + x;
}

function multiply(x: number): number {
    console.log("executing multiply"); // this is printed since the functions are evaluated first
    return x * x;
}

function addOrMultiply(
    add: boolean,
    onAdd: number,
    onMultiply: number
): number {
    return add ? onAdd : onMultiply;
}

console.log(addOrMultiply(true, add(4), multiply(4))); // 8
console.log(addOrMultiply(false, add(4), multiply(4))); // 16 
```

Enter fullscreen mode Exit fullscreen mode

这将产生下面的输出，我们可以看到两个函数总是被执行

```
executing add
executing multiply
8
executing add
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用高阶函数将它重写为一个延迟评估版本

```
function add(x: number): number {
    console.log("executing add");
    return x + x;
}

function multiply(x: number): number {
    console.log("executing multiply");
    return x * x;
}

type fnType = (t: number) => number;
// This is now a higher-order-function hence evaluation of the functions are delayed in if-else
function addOrMultiply(
    add: boolean,
    onAdd: fnType,
    onMultiply: fnType,
    t: number
): number {
    return add ? onAdd(t) : onMultiply(t);
}
console.log(addOrMultiply(true, add, multiply, 4));
console.log(addOrMultiply(false, add, multiply, 4)); 
```

Enter fullscreen mode Exit fullscreen mode

输出如下，我们可以看到只执行了所需的功能

```
executing add
8
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

或者像这样记忆

```
const cachedAdded = {};
function add(x: number): number {
    if (cachedAdded[x]) {
        return cachedAdded[x];
    }
    console.log("executing add");
    const out = x + x;
    cachedAdded[x] = out;
    return out;
}

const cachedMultiplied = {};
function multiply(x: number): number {
    if (cachedMultiplied[x]) {
        return cachedMultiplied[x];
    }
    console.log("executing multiply");
    const out = x * x;
    cachedMultiplied[x] = out;
    return out;
}

function addOrMultiply(
    add: boolean,
    onAdd: number,
    onMultiply: number
): number {
    return add ? onAdd : onMultiply;
}

console.log(addOrMultiply(true, add(4), multiply(4))); // 8
console.log(addOrMultiply(false, add(4), multiply(4))); // 16 
```

Enter fullscreen mode Exit fullscreen mode

输出如下，我们可以看到，对于相同的值
，函数只执行了一次

```
executing add
executing multiply
8
16 
```

Enter fullscreen mode Exit fullscreen mode

请注意，记忆化技术只有在你的函数是纯的和引用透明的时候才会起作用。

也有其他方式来进行懒惰评估，比如[这个](https://thoughts.travelperk.com/optimizing-js-with-lazy-evaluation-and-memoization-9d0cd8c30cd4)。在 TypeScript 中进行惰性求值有时可能不值得代码复杂，但是如果所讨论的函数在处理方面很繁重，那么惰性求值是绝对值得的。

### 类型系统

TypeScript 有很强的类型系统，也有很强的类型推断能力。虽然底层 JavaScript 本身是弱类型的，但 TypeScript 和兼容的 IDE 可以弥补这一缺陷。

### 参照透明

来自维基百科:

> 函数式程序没有赋值语句，也就是说，函数式程序中的变量值一旦定义就永远不会改变。这消除了任何副作用的可能性，因为任何变量都可以在任何执行点用它的实际值替换。因此，函数式程序在引用上是透明的。

不幸的是，在 JavaScript 中严格限制数据突变的方法并不多，但是通过使用纯函数，通过使用我们前面看到的其他概念明确避免数据突变和重新分配，这是可以实现的。默认情况下，JavaScript 通过值传递原始变量，通过引用传递对象，所以我们需要注意不要改变函数内部的数据。也可以考虑像[不可变 JS](https://github.com/immutable-js/immutable-js) 这样的库。尽可能使用`const`来避免重新分配。

例如，下面将产生一个错误

```
const list = ["Apple", "Orange", "Banana", "Grape"];

list = ["Earth", "Saturn"]; 
```

Enter fullscreen mode Exit fullscreen mode

但是，当变量持有对其他对象的引用时，这将无济于事，例如，below 变异将与关键字`const`无关。

```
const list = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // will mutate the list
list.push("Saturn"); // will mutate the list 
```

Enter fullscreen mode Exit fullscreen mode

`const`关键字允许被引用变量的内部状态变异，因此从函数式编程的角度来看`const`关键字只对原始常量和捕捉重赋值有用。

然而，使用 TypeScript，我们可以使用特殊的映射类型使对象成为只读的，从而避免在编译时被捕获的意外数据突变。感谢 [@stereobooster](https://dev.to/stereobooster) 和 [@juliang](https://dev.to/juliang) 指出。阅读我关于映射和条件类型的帖子[这里](https://deepu.tech/react-done-right-with-typescript)了解更多。

```
const list: Readonly<string[]> = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // will cause compilation error 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
const list: ReadonlyArray<string> = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // will cause compilation error 
```

Enter fullscreen mode Exit fullscreen mode

其他要遵循的技术是使用 [Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) 或内置方法，如 map、reduce、filter 等，因为它们不会改变数据。我们也可以使用[这个 ESlint 插件](https://github.com/jfmengels/eslint-plugin-fp)来限制突变。

### [数据结构](https://en.wikipedia.org/wiki/Purely_functional_data_structure)

当使用函数式编程技术时，鼓励使用具有函数式实现的数据类型，如栈、映射和队列。因此，在函数式编程中，作为数据存储，映射比数组或散列集更好。

* * *

## 结论

这只是对那些试图在 TypeScript 中应用一些函数式编程技术的人的介绍。在 TypeScript 中还可以做更多的事情，随着 ECMAScript 的不断发展，这应该会更加容易。正如我前面说过的，函数式编程不是银弹，但它提供了许多有用的技术，使代码更容易理解、维护和测试。它可以与命令式和面向对象的编程风格完美共存。事实上，我们都应该利用一切最好的东西。

* * *

我希望你觉得这是有用的。如果你有任何问题，或者如果你认为我错过了什么，请添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。