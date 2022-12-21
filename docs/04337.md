# TypeScript:功能编程技术

> 原文：<https://dev.to/oieduardorabelo/typescript-tecnicas-de-programacao-funcional-2jod>

功能编程(FP)周围有很多广告，很多合法的孩子都这样做，但不是银子弹。与其他编程范式/风格一样，功能性编程也有自己的优缺点，一个范式可能更喜欢另一个范式。如果您是 TypeScript / JavaScript 开发人员，并且想探索功能编程，则不必担心，您不需要学习 Haskell 或 Clojure 等面向功能编程的语言，因为 JavaScript 和 TypeScript 也可以帮助您完成此任务，本出版物是专为您编写的。

如果您要在 [Java](https://dev.to/deepu105/functional-programming-in-java-a-primer-13nb) 或 [Golang](https://dev.to/deepu105/7-easy-functional-programming-techniques-in-go-3idp) 中查找功能编程，请查看其他系列帖子。

我不会详细阐述所有功能编程概念。相反，我将重点介绍您可以在 TypeScript 中执行的与功能编程概念一致的操作。我也不会讨论一般功能编程的利弊。

请记住，尽管本出版物是关于 TypeScript 的，但在 JavaScript 中也可以很容易地做到这一点，因为 TypeScript 只是键入的 JavaScript 的一个超级集合。

* * *

# 什么是功能编程？

Conforme 维基百科:

> 功能编程是一种编程范式——一种构建计算机程序结构和元素的风格——它将计算视为数学函数的评估，并防止状态和数据的变化。

因此，在功能编程中，有两个非常重要的规则:

*   **无数据突变:**是指数据对象创建后不得更改。
*   **无隐含状态:**应避免隐藏/隐含状态。在功能调度中，状态不会被删除，而是变为可见和显式

这意味着:

*   **无副作用:**功能或操作不得改变其功能范围以外的任何状态。也就是说，函数只能向调用方返回一个值，并且不能影响任何外部状态。这意味着程序更容易理解。
*   **仅纯函数:**功能代码是幂零的。函数只能根据传递的参数返回值，不能影响(副作用)或取决于全局状态。对于相同的参数，此类函数始终生成相同的结果。

除此之外，下面的功能编程概念可以应用于 TypeScript，下面我们将讨论这些项目。

*   [高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)
*   [关闭](https://en.wikipedia.org/wiki/Closure_(computer_programming))
*   [阿谀奉承](https://en.wikipedia.org/wiki/Currying)
*   上诉
*   [懒惰评估](https://en.wikipedia.org/wiki/Evaluation_strategy)
*   [Transparência Referencial](https://en.wikipedia.org/wiki/Referential_transparency)

使用功能编程并不意味着全部或全部，您始终可以使用功能编程概念来补充 TypeScript 中面向对象的概念。无论您使用何种范式或语言，都可以尽可能利用功能编程的优势。这正是我们将要看到的。

* * *

# type script 中的功能编程

TypeScript 不是纯功能语言，而是提供了许多与功能语言一致的概念；因此，让我们看看如何在 TypeScript 中应用上述一些功能编程概念。

## 一等及上级职能

第一类函数(作为第一类公民的函数)意味着您可以将函数分配给变量，将一个函数作为参数传递给另一个函数，或者从另一个函数返回一个函数。TypeScript 支援此功能，因此可让您轻松撰写概念，例如 closure、currying 和高阶函数。

仅当一个函数具有一个或多个作为参数的函数或返回另一个函数时，才能将该函数视为顶级函数。

在 TypeScript 中，这相当容易做到

```
type mapFn = (it: string) => number;

// A função de ordem superior usa uma matriz e uma função como argumentos
function mapForEach(arr: string[], fn: mapFn): number[] {
    const newArray: number[] = [];
    arr.forEach(it => {
        // Estamos executando o método passado
        newArray.push(fn(it));
    });
    return newArray;
}

const list = ["Orange", "Apple", "Banana", "Grape"];

// estamos passando a matriz e uma função como argumentos para o método mapForEach.
const out = mapForEach(list, (it: string): number => it.length);

console.log(out); // [6, 5, 6, 5] 
```

但是在 JavaScript / TypeScript 中，我们也可以使用内置的功能方法(如映射、缩小等)这样做。

```
const list = ["Orange", "Apple", "Banana", "Grape"];

// estamos passando uma função como argumentos para o método map interno.
const out = list.map(it => it.length);

console.log(out); // [6, 5, 6, 5] 
```

也可以在 TypeScript
中进行闭合和转换

```
// esta é uma função de ordem superior que retorna uma função
function add(x: number): (y: number) => number {
    // Uma função é retornada aqui como closure
    // a variável x é obtida do escopo externo deste método e memorizada no closure
    return (y: number): number => x + y;
}

// estamos aplicando o método add para criar mais variações
var add10 = add(10);
var add20 = add(20);
var add30 = add(30);

console.log(add10(5)); // 15
console.log(add20(5)); // 25
console.log(add30(5)); // 35 
```

TypeScript/JavaScript 中有许多宣告性的高阶原生函数，如 map、 **reduce** 、 **forEach** 、 **filter** 等。还有许多库提供了在 TypeScript/JavaScript 中使用的功能接口。

# 纯函数

如前所述，纯函数只能根据过去的参数返回值，不能影响或依赖全局状态。您可以在 TypeScript 中轻松地执行此操作。

这相当简单，请看下面这是一个纯函数。它将始终返回给定输入的相同输出，其行为是高度可预测的。如果需要，我们可以安全地缓存该方法。

```
function sum(a: number, b: number): number {
    return a + b;
} 
```

如果在此函数中添加额外的一行，则行为将变得不可预测，因为它现在具有影响外部状态的副作用。

```
const holder = {};

function sum(a: number, b: number): number {
    let c = a + b;
    holder[`${a}+${b}`] = c;
    return c;
} 
```

因此，尽量保持其功能纯洁简单。使用诸如和[【typescript】](https://github.com/typescript-eslint/typescript-eslint)等工具，可以应用它们。

# 上诉

功能性编程倾向于使用而不是循环。让我们来看一个计算数字因子的示例。

论传统的迭代方法:

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

您可以使用以下功能来执行此操作，这在功能计划中是有利的。

```
const factorial = (num: number): number =>
    num == 0 ? 1 : num * factorial3(num - 1);

console.log(factorial(20)); // 2432902008176640000 
```

递归方法的缺点是，与迭代方法相比，它通常速度较慢(我们寻求的优势是代码的简单性和可读性)，并且可能会导致堆栈溢出错误(“t0”【stack overflow】，因为所有函数调用都必须保存为帧为避免这种情况，特别是经常进行上诉时，优先采用尾上诉(*尾上诉*。在排队资源中，递归调用是函数执行的最后一项操作，因此，编译器不需要保存函数栈帧。大多数编译器可以像优化迭代代码一样优化队列资源代码，从而避免性能瓶颈。尾调用优化是 ECMAScript 规范的一部分，但遗憾的是，大多数 JavaScript 引擎仍不支持尾调用优化。

现在，通过使用尾部函数，可以编写与下面相同的函数，但根据机制的不同，这可能无法优化，虽然有替代解决方案，但在基准方面还是有较好的表现。

```
const factorialTailRec = (num: number): number => factorial(1, num);

const factorial = (accumulator: number, val: number): number =>
    val == 1 ? accumulator : factorial(accumulator * val, val - 1);

console.log(factorialTailRec(20)); // 2432902008176640000 
```

在编写 TypeScript 代码时，请考虑使用函数以提高可读性和稳定性，但如果性能非常重要或迭代次数很多，请使用标准循环。

# 懒评

宽松评价或非严格评价(“T0”非严格评价“”)是将表达式的评价推迟到需要时进行的过程。一般来说，TypeScript 会做严格/事先的评估，但对于诸如 **& &** 、 **||** 和**等操作来说呢？:**作出缓慢的评价。我们可以利用短路技术、高阶函数、闭式和记忆技术进行懒惰的评价(*【休闲评价】*。

请参见下面的示例，其中 TypeScript 会提前评估所有内容。

```
afunction add(x: number): number {
    // isso é impresso, pois as funções são avaliadas primeiro
    console.log("executing add");
    return x + x;
}

function multiply(x: number): number {
    // isso é impresso, pois as funções são avaliadas primeiro
    console.log("executing multiply");
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

这将产生下面的输出，我们可以看到这两个函数始终都在运行

```
executing add
executing multiply
8
executing add
executing multiply
16 
```

我们可以用高阶函数把它改写成懒惰的评价版本:

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

// Agora é uma função de ordem superior, portanto, a avaliação das funções é adiada em if-else
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

```
executing add
8
executing multiply
16 
```

或者是记忆

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

这将生成以下结果，我们可以看到，对于相同的值，这些函数只运行过一次:

```
executing add
executing multiply
8
16 
```

请注意，仅当存储技术的功能是纯的且在引用上透明时，存储技术才起作用。

还有其他方法可以做这样懒惰的评价。在 TypeScript 中进行懒惰的评估有时可能不值得代码的复杂性，但如果所涉及的函数在处理方面过于繁重，则值得懒惰地进行评估。

# Sistema de Tipos

TypeScript 具有强大的类型系统，也具有很强的类型推断能力。尽管基本 JavaScript 本身的键入方式很弱，但 TypeScript 与支持的 IDE 一起可以填补这一空白。

# transparência Referencial

Da 维基百科:

> 功能程序没有赋值语句，即功能程序中变量的值在定义后永远不会更改。这消除了产生副作用的可能性，因为任何变量都可以在任何运行时替换为其实际值。因此，功能程序在参照上是透明的。

不幸的是，严格限制 JavaScript 中的数据移动的方法并不多，但是，使用纯函数并明确避免使用我们前面看到的其他概念进行数据移动和重新分配，这是可以实现的。默认情况下，JavaScript 通过值传递原始变量**，通过引用**传递对象**，因此我们必须小心不要在函数内更改数据。诸如[不变的图书馆。js](https://github.com/immutable-js/immutable-js) 也可以考虑。尽量使用 **const** 以避免重新分配。**

例如，下面会产生错误:

```
const list = ["Apple", "Orange", "Banana", "Grape"];

list = ["Earth", "Saturn"]; 
```

但是，当变量保留对其他对象的引用时，这将不起作用，例如，下面的切换将独立于关键字 **const** 起作用。

```
const list = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // vai mudar a lista
list.push("Saturn"); // vai mudar a lista 
```

**const**关键字允许所引用变量的内部状态发生变化，因此，从功能编程的角度来看，**const**关键字仅对原始常量和避免重新赋值非常有用。

但是，使用 TypeScript，我们可以使用特殊类型的映射来使对象成为只读对象，从而避免在编译时捕获的意外数据转换和错误。感谢[@立体助推器](https://dev.to/stereobooster)和 [@juliang](https://dev.to/juliang) 给我展示。阅读[我关于此处映射类型和条件类型的帖子](https://dev.to/deepu105/react-components-done-right-with-typescript-mapped-and-conditional-types-420l)以了解更多信息。

```
const list: Readonly<string[]> = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // causará erro de compilação 
```

欧

```
const list: ReadonlyArray<string> = ["Apple", "Orange", "Banana", "Grape"];

list.push("Earth"); // causará erro de compilação 
```

其他技术正在使用‘t0’object . freeze’或‘T2’map、**reduce**、 **filter** 等内部方法，因为它们不会修改数据。我们也可以用[这个 ESlint 插件](https://github.com/jfmengels/eslint-plugin-fp)来限制突变。

# 数据结构

使用功能编程技术时，建议使用具有功能实现的数据类型，如、**、**和**[。](https://en.wikipedia.org/wiki/Purely_functional_data_structure)**

 **为了保存数据，**映射**优于功能编程中的阵列或散列集。

* * *

# 总结

这只是尝试在 TypeScript 中应用某些功能编程技术的用户的简介。在 TypeScript 和 ECMAScript 的基础上还可以做更多的工作，这应该更容易。如前所述，功能编程不是超级解决方案，而是为更易于理解、可持续和可测试的代码提供了许多有用的技术。它可以与强制和面向对象的编程风格完美共存。事实上，我们都应该尽可能利用一切。

* * *

我希望你觉得这有用。如果您有任何疑问或认为我错过了什么，请将提示添加为注释。

如果您喜欢这篇文章，请留下喜欢的或评论。

你可以在[推特](https://twitter.com/deepu105)和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上跟我来。

* * *

# 学分

*   [面向所有人的简易函数式编程技术](https://dev.to/deepu105/easy-functional-programming-techniques-in-typescript-for-everyone-1bl2)、escrito origination por[Deepu K Sasidharan](https://twitter.com/deepu105)**