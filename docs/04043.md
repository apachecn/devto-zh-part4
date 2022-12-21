# 实施 ES6(或使用 ES6 简化问题解决)

> 原文：<https://dev.to/sid_maddy/es6-in-action-or-using-es6-to-ease-problem-solving-17pd>

你好！

在本文中，我将介绍 ECMAScript 2015 (ES6)中引入的一些功能(我知道有点晚了！😅)借助一道练习题。

# 问题陈述

[max counters-codi lity](https://app.codility.com/programmers/lessons/4-counting_elements/max_counters)

应用所有交替运算后计算计数器的值:计数器加 1；将所有计数器的值设置为当前最大值。

对问题不感兴趣？[跳到](#es6-features)对 ES6 特性的解释。

## **剧透预警！**

下面是针对上述问题的*一个*解决方案。我非常鼓励你在阅读更多内容之前自己解决问题。

# 解

```
/**
 * @param {number} N Number of counters
 * @param {number[]} A Operations to be performed on the counters
 * @returns {number[]} Counters after operations
 */
function solution(N, A) {
    // Generators
    const arrSplits = (function* () {
        // Map
        let split = new Map();
        // for...of
        for (const x of A) {
            if (x === N + 1) {
                yield split;
                split = new Map();
            } else {
                split.set(x, (split.get(x) || 0) + 1);
            }
        }
        return split;
    })();

    let max = 0;
    // Object destructuring assignment
    let { value: split, done } = arrSplits.next();
    while (!done) {
        // Spread operator
        max += split.size ? Math.max(...split.values()) : 0;
        ({ value: split, done } = arrSplits.next());
    }

    // Array.from
    const counters = Array.from({ length: N }, () => max);
    // Array destructuring assignment
    for (const [x, c] of split) {
        counters[x - 1] += c;
    }
    return counters;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解的解释

时间复杂度:`O(N + M)`
T5】空间复杂度:`O(N + K)`；`K` =在`A`中不同元素的数量

### 算法

1.  定义一个生成器，将操作`A`的输入数组分割成由`N + 1`元素分隔的子数组。

    换句话说，每当遇到`N + 1`元素时，这个生成器函数将`yield`一个包含该子数组中所有元素的出现的`Map`。

2.  声明两个变量——`max`,用于存储运行的最大值，以及`split`,用于存储当前的子数组。

3.  迭代生成的子数组，并将`max`计算为子数组中出现次数的最大值(`split`)。

    注意，我们迭代操作数组*只有*直到最后一个`maxCounter`操作。

4.  用初始化为计算出的`max`的所有值构建`counters`数组。

5.  A 中的剩余操作存储在最后一个子数组(`split`)中。

    执行这些操作，就像我们天真地解决这个问题时会做的那样。

### 举例

让我们以样本测试用例为例。

`solution(5, [3, 4, 4, 6, 1, 4, 4]) // => [3, 2, 2, 4, 2]`

第一个`Map` `yield` ed 是`Map { 3 => 1, 4 => 2 }`
T5 在这一点上，`max === 2`。

在索引`3`处只有一个`N + 1`元素，这意味着生成器被耗尽。

在这一点上，`max === 2`和`split`就是`Map { 1 => 1, 4 => 2 }`。

### 逻辑

嗯，这就是*如何*。让我们来谈谈*为什么是*。

阅读完问题陈述后，您可能注意到的第一件事是，执行`maxCounter`操作实际上是*重置*计数器，唯一的区别是计数器的值。

开始时，计数器为`[0, 0, 0, 0, 0]`。

在`3`点`maxCounter`操作后，计数器变为`[2, 2, 2, 2, 2]`。

如前所述，我们通过仅跟踪运行的最大值(`max`)和正在迭代的子数组中的计数器(`split`)来利用这种行为。

然后，这只是一个处理边缘案件的问题，瞧！我们已经解决了问题！

# ES6 特性

下面是上述解决方案中使用的 ES6 功能的摘要。

## 发电机

生成器是由生成器函数(使用 function*语法定义)返回的对象。这些对象的特殊之处在于它们既是迭代器又是可迭代的。

从迭代协议的 [MDN 页面，](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

> **iterable** 协议允许 JavaScript 对象定义或定制它们的迭代行为。
> 
> **迭代器**协议定义了产生一系列值(有限或无限)的标准方法，并且当所有的值都生成后，可能会返回一个值。

这意味着，因为生成器是可迭代的，所以它可以被传递给任何可以接受或期望可迭代的 API、函数或语法。这些包括但不限于`Set([iterable])`、`Array.from()`和`for...of`循环。

同样，因为它也是一个迭代器。它可以用来生成有限或无限序列。这对于一次对序列中的一个元素或一组元素进行操作的流算法特别有用。

参考:[函数* - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)

## 地图

s 是哈希表/映射的 JS 版本。在`Map` s 之前，`Object` s 被用于同样的目的。

MDN 在`Object` s 和`Map` s 之间有出色的[对比。

让`Map`比`Object`更受欢迎的关键区别在于能够毫不费力地进行迭代。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map#Objects_and_maps_compared)

使用`Object` s，您必须显式地获得一个`Object`的键，它将包含原型链中的任何自定义属性。

对于`Map` s，这只是一个直接迭代的问题，因为它符合上面提到的可迭代协议。

还有 [`WeakMap` s](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 可以用来节省内存，如果保留在别处不再被引用的键(也称为垃圾收集)是不必要的。

[`Set` s](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 和 [`WeakSet` s](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet) 是使用散列表实现的其他兄弟对象。

参考: [Map - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)

## 为...关于

`for...of`语句创建了一个遍历可迭代对象的循环

还有就是[为...在语句](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)中，其行为稍有不同。

> 森林...in 语句以任意顺序迭代对象的可枚举属性。

这意味着，如果您使用`for (const x in iterable)`，您将最终迭代`iterable`的属性以及在其原型链上定义的任何自定义属性。

> 森林...of 语句迭代 iterable 对象定义要迭代的值。

很简单。如果您使用`for (const x of iterable)`，您将只迭代那些`iterable`的迭代器允许您迭代的值。

参考:[为...of - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)

## 销毁分配

析构赋值允许你将对象内部的值解包到不同的变量中。

我认为，这个想法来自于基于约束的编程和函数式编程语言(如 Haskell)中的模式匹配语法。

这个 MDN 页面(链接如下)提供了大量编写良好的示例。我最喜欢的用例是，您可以使用它来模拟类似 GraphQL 的选择性，只从您想要的对象中获取信息。

```
const user = {
    givenName: 'Siddhesh',
    familyName: 'Mhadnak',
    age: '22',
    subscriptions: [{
        name: 'netflix',
        paid: true
    }]
};

const {
    givenName: firstName, // rename
    age, // implicit
    subscriptions: [{
        name: subscriptionName, // rename
    }]
} = user;

console.info(firstName); // => "Siddhesh"
console.info(age) // => 22
console.info(subscriptionName); // => "netflix" 
```

Enter fullscreen mode Exit fullscreen mode

引用:[析构赋值- JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

## 传播语法

Spread 及其兄弟 rest 语法可分别用于扩展和压缩 iterable。

当我们有一个 iterable 并且我们想把它传递给一个只接受不同参数的函数时，比如`Math.max`、`Math.min`等等，这是很有用的。

在展开语法之前，惯用的方法是使用`f.apply(null, args)`。但是，使用 spread 语法，它就像`f(...args)`一样简单。

当使用 spread 语法复制一个深层对象时，需要注意的一件重要事情是，spread 只深入一级。

```
const c = { a: { b: 1 } };
const d = { ...c };
d.a.b = 2;
console.info(c.a.b); // => 2 
```

Enter fullscreen mode Exit fullscreen mode

参考:[传播语法- JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

## 来源于

`Array.from()`方法从类似数组或可迭代的对象创建一个新的浅拷贝的`Array`实例。

正如您对命名构造函数的期望，它本质上是从传递的 iterable 构造一个数组。

但是，你说的阵列式是什么意思？类数组对象是指具有`length`属性的对象。

```
const arr = Array.from({ 0: "Hello", 1: "World", length: 2 });
console.info(arr.join(", ")); // => "Hello, World" 
```

Enter fullscreen mode Exit fullscreen mode

当我们想要构造一个已知长度的`Array`并且想要使用一些逻辑用值预先填充它时，这可能是有用的。

```
Array.from({ length: 5 }, (v, i) => i + 1); // => [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

参考:[array . from()-JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)

感谢阅读😊！