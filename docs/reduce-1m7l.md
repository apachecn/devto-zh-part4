# 减少

> 原文：<https://dev.to/critjen/reduce-1m7l>

这周我决定写关于 reduce 的文章。Reduce 是 JavaScript 中一个内置的数组函数。当我们谈论不同的用于数组转换的内置函数时，比如 map 或 filter reduce 是其中最通用的。这是我们阵列转换的瑞士军刀！

[![](img/3755eb961987ab3a441622ff8de75c46.png)](https://i.giphy.com/media/EwivVqmC7zln2/source.gif)

那么它是做什么的呢？

它通过从左到右执行为数组中的每个值提供的回调函数，将数组缩减为单个值。如果这真的令人困惑，也许可以看看我以前关于高阶函数的博文。如果只是有一点混乱，那么让我们继续努力澄清这一点。

一个非常简单的例子是，如果我有一个数字数组，我想得到总和。

```
const nums = [2, 3, 4, 5] 
```

我完全可以用一个循环来完成。

```
let sum = 0
for(let i = 0; i < num.length; i++) {
   sum += nums[i]
}
//sum = 14 
```

我们可以用 reduce 完成同样的事情。

```
const sum = nums.reduce(function(sum, num){
   return sum + num
}, 0)

//sum = 14 
```

逻辑非常简单，但是语法值得分解。reduce 的语法是:

```
array.reduce(function(total, currentValue, currentIndex, arr), initialValue) 
```

所以在上面的例子中，我们对数字数组调用 reduce，并传入两个参数。首先我们传入回调函数(因为 reduce 接受一个函数作为参数，所以它是一个高阶函数)。

```
function(sum, num){
   return sum + num
} 
```

我把它写成了内联的，但是你也可以把这个函数存储在一个变量中，然后把它传入。出于这个例子的目的，我们只需要传入四个潜在参数中的两个，即 sum(当前总数)和 num(当前值)。作为 reduce 的第二个参数，我传入了初始值 0。所以在 reduce 做任何事情之前，它会将 sum 设置为你传入的初始值，在这个例子中是 0。

接下来，reduce 将从左到右遍历数组中的每个元素。因此，对于我们的第一次迭代，它从零索引或最左边的值(2)开始。它用这个 num 和我们的初始 sum 执行回调函数。

```
function(0, 2){
   return 0 + 2
} 
```

这会返回 2。现在返回值将作为我们下一次迭代的总和被传入。

Reduce 然后在数组中向右移动一个索引，新的 num 或 currentValue 为 3。

```
function(2, 3){
   return 2 + 3
} 
```

我们返回一个值 5，这个值在下一次迭代中作为和被传入。这将继续下去，直到遍历整个数组并返回总计 14！所以如上所述，它只返回一个值。

当数组包含更复杂的项目时，也可以使用 reduce。因此，如果您有一个表示您办公室当月甜甜圈订单的对象数组。
[![](img/b0006d37899b01bf8b85960594ded12c.png)](https://i.giphy.com/media/bd4td7PlhYY9i/source.gif)T4】

```
 const orders = [
   { amount: 5, topping: "sprinkles" },
   { amount: 12, topping: "oreos" },
   { amount: 10, topping: "glaze" },
   { amount: 30, topping: "icing" }
] 
```

我们不在乎配料，我们只需要知道我们订了多少甜甜圈。我们可以再次使用 reduce 来找到数字

```
orders.reduce((total, order) => {
   return total + order.amount
}, 0) 
```

我在这里使用了一个箭头函数，只是为了给你一个大概的概念。我们也可以去掉花括号和单词“return ”, JavaScript 就会知道自动返回值。

```
order.reduce((total, order) => total + order.amount, 0) 
```

希望这能让你对 reduce 的工作原理有所了解！它并没有展示 reduce 有多强大和多用途，但是首先理解它是如何工作的是很重要的。希望我能在将来介绍一些更复杂的实现！