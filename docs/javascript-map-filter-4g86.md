# JavaScript -映射和过滤

> 原文：<https://dev.to/mthacker09/javascript-map-filter-4g86>

我必须先说我喜欢。地图，。过滤器，还有。Javascript 中的 reduce 方法。然而，在相当长的一段时间里，直到现在，我偶尔会翻回 MDN 或 W3 来查找一些东西。这就是为什么我决定我的下一个帖子将是一个演练，让我更好地记住这些神奇的工具。

* * *

### ***。*地图()方法**

JavaScript Map 方法遍历数组中的每个元素，并返回一个包含结果的新数组。`.map()`和`.forEach()`方法似乎具有相同的功能；关键的区别是`.map()`能够返回一个新的一个新的数组。

**语法**

`.map()`方法的教科书语法是`array.map(callbackFunction(currentValue, index, arr), thisValue)`。

让我们单独考虑每个元素:

*   *数组* -任何 JavaScript 数组，即`let myArr = [1,2,3,4,5]`
*   *map()* -在 *myArr* 上被调用的方法
*   *callbackFunction* -一个函数，可以是 name、anonymous 或 arrow，它将遍历 *myArr* 中的每一项
*   *索引* - ( **可选**)正在处理的数组中元素的索引
*   *arr* - ( **可选**)正在调用 map 方法的数组
*   *thisValue* - ( **可选**)值，用于执行回调时的`this`(我们将在后面的文章中讨论`this`)

正如我所说的，上面的语法是“教科书”下面是利用`.map()`方法的三种简单方法。

[![](img/52a14e25e3c2f51742f51e6b1c861e3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IHKam6vP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cp0c15zovhapyiyq13zk.png)

*   利用匿名功能
*   `var c`利用一个命名函数
*   `var d`利用箭头功能

`.map()`方法不会改变原始数组。在上面的代码中，`a`的计算结果仍然是`[1,2,3,4,5]`。如果您没有使用新数组或者没有从回调函数返回任何值，那么使用`.forEach`可能是更好的选择。

* * *

### ***。*滤镜()方法**

与`.map()`一样，`.filter()`方法根据传递给原始数组中每一项的标准返回一个新数组。`Filter`也使用了回调函数。对我来说，处理`.filter()`中回调函数如何工作的最好方法是，回调函数是检查数组中每一项的标准。如果该项的计算结果为 true，则该项被“过滤”到新数组中。

**语法**

`.filter()`方法的语法与`.map()`相同。

`array.filter(callbackFunction(currentValue, index, arr), thisValue)`。

*   *数组* -任何 JavaScript 数组，即`let myArr = [1,2,3,4,5]`
*   *filter()* -在 *myArr* 上被调用的方法
*   *callbackFunction* -一个函数，可以是 name、anonymous 或 arrow，它将遍历 *myArr* 中的每一项
*   *索引* - ( **可选**)正在处理的数组中元素的索引
*   *arr* - ( **可选**)正在调用 map 方法的数组
*   *thisValue* - ( **可选**)值，用于执行回调时的`this`(我们将在后面的文章中讨论`this`)

同样，下面是利用`.filter()`方法的不同示例:

[![](img/f3bd200574c1a66250d1b1e73145976d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5g28-uMI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vnynomdt58aygriz35l7.png)

需要注意的一点是，正如我所说的，`filter`返回一个新的数组，即使原始数组中没有满足条件的元素。如果没有项目通过，则返回一个空数组。

下面是一个在现实生活中使用`Filter`的例子:

[![](img/a1dda3e5c9efaef216a3e199560fa8c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R_CUA8Tb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6n9qtq3cmcvlwlq67n9p.png)

这里列出了一个城市数组(尽管这是一个短数组，但这个概念可以推广到更大的获取请求)。接下来是一个函数，它将一个数组和搜索条件作为参数，并基于`search`参数返回一个过滤后的数组。在本例中，`cities`数组被传递，并被要求返回数组中包含字母“a”的所有城市。

* * *

当谈到`map`和`filter`时，还有另一种典型的配对方法，那就是`reduce`。然而,`reduce`方法有很多，我认为它应该有自己的位置。

至此，我希望有人会觉得这篇教程很有帮助，就像我一样。我只张贴了很短的时间，但是我已经发现我自己回到我的关于析构的帖子来保持它在我脑海中的新鲜。

一如既往，编码快乐！