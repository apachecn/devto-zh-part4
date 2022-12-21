# javascript 函数式编程

> 原文：<https://dev.to/delph/functional-programming-with-javascript-1jl>

# 什么是函数式编程？

函数式编程遵循几个核心原则:

*   纯函数，即。

    *   相同的输入总是给出相同的输出
    *   有限的副作用:对函数之外的程序状态的任何改变或突变都被小心控制
*   功能组合:通过组合更多的基本组件/功能来构建复杂的系统

在支持函数式编程的语言中(如 JavaScript、Ruby、Scala 等)，函数是一级对象。这意味着函数可以被赋给一个变量，作为参数传递给另一个函数，或者从另一个函数返回，就像任何其他正常值一样。

## 祈使句 vs 陈述句

函数式编程是声明式编程的一种形式。这意味着你告诉计算机做什么，而不是告诉 T2 如何做。*如何*被抽离)。

这可以通过调用方法或函数来完成。JavaScript 提供了许多处理常见任务的预定义方法，因此我们不需要写出计算机应该如何执行它们。例如，`map`方法可以处理数组迭代的细节，而不是编写一个`for`循环。

这样做的一个优点是，它有助于避免语义错误，如“相差一个错误”。

## 变异与非变异方法

在函数式编程中，函数不应该通过改变代码中的任何变量或对象而产生副作用。(即。函数应该是不可变的)。

在 React 和 Redux 这样的框架/库中，一个核心原则是不应该改变状态。

*   `slice`、`map`、`filter`、`reduce`、`concat`是 JavaScript(即这些方法返回一个新数组)
*   `splice`、`push`、`pop`、`sort`都在突变(即。这些方法改变了原始数组)

提示:`.slice()`是复制整个数组的简单方法。使用 ES6(以及更高版本)，您还可以使用 spread 操作符轻松复制数组或对象。

```
const array = ['a', 'b', 'c'];
const object = {message: 'hello world'};

// ES5
const newArray = array.slice();
const newObject = Object.assign({}, object);

// Spread operator
const newArray2 = [...array];
const newObject2 = {...object}; 
```

提示:要对一个数组排序，将一个空数组连接到正在排序的数组(因为`concat`返回一个新数组)，然后运行 sort 方法。

```
const array = [1 , 4, 5, 3]

const newArr = array.concat([]).sort()

console.log(array) // [1, 4, 5, 3]
console.log(newArr) // [1, 3, 4, 5] 
```

## 逢迎而局部应用

函数的`arity`是它需要的参数个数。`Currying`函数是指将一个多参数的函数转化为一系列单参数的函数(即。从 N 的 arity 到 arity 1 的 N 个函数)

换句话说，它重新构造了一个函数，使其接受一个参数，然后返回另一个接受下一个参数的函数，依此类推。

```
//Un-curried function
const add = (a, b) => a + b

//Curried function
const curriedAdd = a => b => a + b

curriedAdd(1)(2) // Returns 3 
```

Currying 允许函数组合，并让我们能够创建可重用的函数。如果不能一次提供函数的所有参数，我们可以将每个函数调用保存到一个变量中。该变量将保存返回的函数引用，该函数引用在下一个参数可用时接受该参数。

类似地，`partial application`一次将几个参数应用于一个函数，并返回另一个参数较少的函数。