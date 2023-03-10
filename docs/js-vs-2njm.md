# JS == vs ===

> 原文：<https://dev.to/kiarathedev/js-vs-2njm>

这篇文章需要关于数据类型的先验知识。

Javascript 给出了两个看起来非常相似的测试等式的选项。我们一起来看看`==`和`===`的区别。

[![confused](img/f9845f8c754931fdf7b8f3a61311d55e.png)](https://i.giphy.com/media/4JVTF9zR9BicshFAb7/giphy.gif)

### 三重等于/ ==

使用三重等于测试严格相等，这意味着类型和 T2 值必须相同。

下面是一些返回 **true** 的例子:

`10 === 10
// true : both Integers and values match`

`true === true
// true : both Booleans and values match`

`'hello world' === 'hello world'
// true : both Strings and values match`

下面是一些返回 **false** 的例子:

`100 === '100'
// false : comparison of an Integer and a String`

`'red' === 'blue'
// false : both Strings with different values`

`false === 0
// false : both different types and different value`

如果我们想在只有价值重要的地方测试松散的平等会怎么样？然后我们可以使用`==`

### 双等号/ ==

使用 Double Equals 测试松散等式。这里只有**值**必须相同。Javascript 通过完成类型强制使这成为可能，类型强制实际上将我们的值转换成类似的类型。让我们回到前面返回**假**的一个例子。

`100 == '100'
// true : comparison of an Integer and a String`

`false == 0
// true`

`false == 0`的例子可能会令人困惑。这个例子返回`true`，因为 Javascript 中的 0 被描述为 **falsy** 。

### 总之

当测试相等时，最好使用三重相等。测试类型和值确保真正的相等测试。

这篇文章提供了关于这个话题的信息，并详细描述了 **falsy** 的意思。