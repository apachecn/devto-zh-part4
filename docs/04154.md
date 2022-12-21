# 理解 JavaScript 中的 reduce

> 原文：<https://dev.to/savagepixie/understanding-reduce-in-javascript-3jkh>

当我刚开始学习 JavaScript 时，我很难掌握`reduce()`。我听说过其他人在开始时也有困难。所以我决定写一篇关于它如何工作的概述，希望它能帮助其他人。

## 它有什么作用

`reduce()`是一种可以用于任何数组的方法。它遍历每个元素，并根据操作返回一个结果。这些操作依赖于一个名为`reducer`的参数，它是我们为`reduce()`方法提供的一个[回调函数](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)。

## ‘a’到底是什么？

真正让我对`reduce()`感到困惑的是`a`。但是什么是`a`？如果你在网上寻找使用`reduce()`的代码示例，你很可能会找到这样的东西。

```
const arr = [ 1, 2, 8, 14 ]
const sum = arr.reduce((a, b) => a + b) 
```

我会看着这样的东西告诉自己，*好吧，我知道`a`和`b`应该是数组中的元素，但是 JavaScript 到底是怎么知道哪个是哪个的呢？它们加起来的结果是什么？*这是一个简单的例子。然后你会看到像这样的东西。

```
const arr = [["potatoes", 3], ["tomatoes", 85], ["onions", 27]]
const result = arr.reduce((a, b) => {
  a[b[0]] = b[1]
  return a
}, {}) 
```

这太疯狂了。最后一个参数`{}`到底是什么？这到底是在干什么？

好吧，让我们看看这个`a`是什么意思(或者那个位置的任何其他参数名，就此而言，不必是`a`)。)`reduce()`的语法如下:

```
arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initial value]) 
```

我们的回调函数需要两到四个参数。

*   `accumulator`这就是我们之前例子中的`a`。这是一个变量，其中`reduce()`存储数组上每次迭代的结果。
*   这是我们用来称呼数组中每个元素的名字。
*   `index``currentValue`的指标(可选。)
*   我们正在迭代的整个数组(可选。)

对我来说，启发性的时刻是意识到`a`是`reduce()`存储先前迭代信息的地方。所以当我们向它添加东西的时候，我们要保证它的安全，直到执行结束。

## 让我们分析第一个例子

```
const arr = [ 1, 2, 8, 14 ]
const sum = arr.reduce((a, b) => a + b) 
```

在这里，我们的回调函数将当前元素与累积值相加，**成为新的累积值**。因为没有初始值作为参数传递，所以它将使用第一个元素，并跳过它。因此`reduce()`将执行以下操作:

1.  `a` = 1，`b` = 2
    *   1 + 2 = 3
    *   `a`变成了 3
2.  `a` = 3，`b` = 8
    *   3 + 8 = 11
    *   `a`变成了 11
3.  `a` = 11，`b` = 14
    *   11 + 14 = 25
    *   `a`变成了 25
4.  `reduce()`返回 25，指定为`sum`的值。

## 我们来看第二个例子

```
const arr = [["potatoes", 3], ["tomatoes", 85], ["onions", 27]]
const result = arr.reduce((a, b) => {
  a[b[0]] = b[1]
  return a
}, {}) 
```

这个有点复杂，因为操作并不简单，因为我们将`a`初始化为一个空对象。我们的 reducer 函数接受一个包含多组`key`和`value`的二维数组，并将它转换成一个对象。让我们更详细地看看发生了什么

*   `a`是一个空对象。
*   在对象中创建一个新的属性，并将深层数组中第二个索引的值赋给它。
*   函数返回`a`；无论我们的 reducer 函数返回什么，都将成为新的累积值。

1.  `a` = {}
2.  `a.potatoes`被创建并被赋值为 3；`a` = {土豆:3 }
3.  `a.tomatoes`被创建并赋值为 85；`a` = {土豆:3，西红柿:85 }
4.  `a.onions`被创建并被赋值为 27；`a` = {土豆:3，西红柿:85，洋葱:27 }

## 结论

`reduce()`是一个强大的方法，可用于以多种方式转换数组中的数据。刚开始可能会有点混乱。但对我来说，诀窍是理解回调函数每次运行的结果将存储在它接受的第一个参数中，这个小的`a`或`accumulator`。