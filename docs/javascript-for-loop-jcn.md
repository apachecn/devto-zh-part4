# Javascript for() {}循环-简化

> 原文：<https://dev.to/dillionmegida/javascript-for-loop-jcn>

`for loop`创建一个循环，只要给定的条件保持为真，就执行执行。

你可以在这里找到其他的循环方法。
我选择解释 for 循环，因为它很好地描述了循环操作是如何执行的。尽管 for 循环有复杂的实现。

### `for loop`

语法是

```
for(initialization; condition; final-expression) {
   // statement
} 
```

Enter fullscreen mode Exit fullscreen mode

参数由一个`semi-colon`分隔。
`initialization`是在循环开始前计算的表达式或变量。通常，它被用作计数器变量。
`condition`是一个表达式，在每次迭代之前进行计算。只有当条件保持为真时，才会进行迭代，即表达式的计算结果为真。
`final-expression`是下一次迭代之前执行的最后一个表达式。
只要条件保持为真，就会对`statements`进行评估。

一个简单的典型例子是:

```
function example() {
    let a = 3;
    let sum = 0;
    for(let i = 0; i < a; i++) {
        sum += 1;
    }
    return sum;
}
example();
//expected output - 3 
```

Enter fullscreen mode Exit fullscreen mode

### 我们来分析一下这个程序，好吗？😃

我将 for 循环放在一个函数中，这样代码只有在函数被调用时才被执行——`example`函数。

我们的程序(或者更确切地说，这个函数)只是将变量`a`设置为 5，将`sum`设置为 0。它还包含一个 for 循环操作。
`for...loop`操作`initializes`将变量`i`置为 0，声明 a `condition`仅在`i`变量小于`a`变量时执行循环语句，声明 a `final-expression`在每次操作后，将`i`变量加 1 ( `i++`)，并留下一条将总和加 1 的语句待执行。

当`i`小于`a`时，总和继续增加 1。

第一次迭代

变量`i`被初始化为 0。
(`i`比`a`少吗？)
(是的就是它😊，`0`小于`3`，因此，条件返回`true` )
`sum += 1`隐含`sum = sum + 1`。`sum`故为`= 0 + 1`。
`sum = 1`
根据`final-expression`，变量`i`增加 1。`i`现在变成了`0 + 1 = 1`

*第二次迭代*

(`i`现在是 1)
(是的就是它😊，`1`小于`3`，因此，条件返回`true`)
`sum+=1`-`sum = 2`
-`i++`-`i = 2`

*第三次迭代*

(`i`现在是 2)
(是的就是它😊，`2`小于`3`，因此，条件返回`true`)
`sum+=1`-`sum = 3`
-`i++`-`i = 3`

*第四次迭代*

(`i`现在是 3)
(不，不是☹️，`3`不小于`3`，而是等于 3，因此，条件返回`false` )
(结果，我们的循环不再执行任何操作，我们的最终表达式也不再执行)

然后，我们的函数返回`sum`的值，即 3。

也可以在许多其他场景中实现。
例如，遍历数组，遍历对象，当条件为真时执行一个函数。

#### 让我们尝试循环遍历一个数组🤗

我们的目标是将数组的元素记录到控制台。

```
let x = ['Dillion', 45, 'Megida'];
console.log(x);
// expected output - ["Dillion", 45, "Megida"]

let currentElement;
// But we need the elements instead 🤔
for(let arrIndex = 0; arrIndex < x.length; arrIndex++) {
    currentElement = x[arrIndex];
    console.log(currentElement);
}

// At console, we have
// Dillion
// 45
// Megida 
```

Enter fullscreen mode Exit fullscreen mode

### 分析🙂

我们将数组附加到变量`x`上。
我们可以看到`x`登录到控制台的结果。
但是，我们的目标是获得单个元素。
所以我们决定循环遍历数组，进一步执行一些语句。

声明了一个`currentElement`变量，它将决定我们所在数组的当前元素。

众所周知，数组的每个元素都有一个索引，索引从第一个元素所拥有的 0 开始。

声明了一个`arrIndex`,并将其初始化为 0。
给出的条件是`arrIndex`应该总是小于我们数组的长度，因为最后一个元素的索引是`length of array - 1`。
并且在每次迭代结束时给出最终表达式，将`arrIndex`变量增加 1。

第一次迭代

`arrIndex`初始化为 0。
(是`arrIndex` < `x.length`？)
( yes it is，0 为< 3，条件返回 true)
`currentElement`对数组当前索引处的元素求值，为`Dillion`。
`Dillion`记录到控制台。
`arrIndex`递增 1。`arrIndex`现在是 1。

*第二次迭代*
`arrIndex`现在是 1。
(是`arrIndex` < `x.length`？)
( yes it is，1 为< 3，条件返回 true)
`currentElement`现为`45`
`45`登录控制台。
`arrIndex`现在是 2。

*第三次迭代*
`arrIndex`现在是 2。
(是`arrIndex` < `x.length`？)
(是是，2 是< 3，条件返回真)
`currentElement`现在是`Megida`
`Megida`登录控制台。
`arrIndex`现在是 3。

*第四次迭代*
`arrIndex`现在是 3。
(是`arrIndex` < `x.length`？)
(No it is not，3 is = 3，条件返回 false)
`currentElement`不求值。
控制台没有记录任何内容。
`arrIndex`是从未被评价过的。

现在你有了它，所有的元素都记录到控制台🎉

如我所说，for 循环还有许多其他实现。

我希望你对循环有了更好的理解。

对陈述有用的文章-

感谢阅读。😀

请评论您的评论