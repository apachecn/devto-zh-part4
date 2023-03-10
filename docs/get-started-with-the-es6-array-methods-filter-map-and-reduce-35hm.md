# 开始使用 ES6 阵列方法。filter()，。map()和。减少()

> 原文：<https://dev.to/themarcba/get-started-with-the-es6-array-methods-filter-map-and-reduce-35hm>

ES6 有许多新功能。其中之一是一些非常方便的`Array`函数。最受欢迎的日常任务是`.filter()`、`.map`、`.reduce()`。在这篇文章中，我将首先用“老”的方式，然后用这些新的功能的例子来覆盖每一个。

这篇文章假设你知道箭头函数。如果你不知道它们是什么，不要担心。阅读我的关于数组函数的[博客文章的第一章，你应该很容易理解这里发生了什么。](https://developer.blog/es6-arrow-functions)

## 他们都有什么共同之处

这三个函数都是在 ES6 (ECMAScript 2015)中引入的，并且是应用于`Array` 的**函数。**

同样，它们都是所谓的高阶函数。这意味着它们是一个将函数作为结果返回的函数，或者**将其他函数作为参数**。在这种情况下，是后者。理解这一点对本课非常重要。

在内部，这些函数**运行一个 forEach 循环**，然后以某种方式将传递的函数应用于它正在迭代的数据。

## [T1。过滤器()](#filter)

对我来说最容易理解的是。它确实做到了它所说的——它接受现有的数组并返回数组的一部分，可能跳过一些值。或者换句话说:把我们不想要的物品拿出来。

有以下一系列年龄的人。

```
const people = [
    { name: 'John', age: 32},
    { name: 'Isabelle', age: 18},
    { name: 'Steve', age: 17},
    { name: 'Winston', age: 10},
    { name: 'Agnes', age: 82}
] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只想让成年人回来，或者换句话说，过滤掉未成年人。让我们假设在这个练习中，我们生活在一个 18 岁或以上意味着你成年的国家。

如果我们不用`.filter()`函数来写这个语句，它会是这样的:

```
let adults = []
people.forEach(person => {
    if(person.age >= 18) {
        adults.push(person)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

用 ES6 的`.filter()`功能如果看起来会是这样:

```
const adults = people.filter(person => person.age >= 18)

// Will outout the objects for John, Isabelle and Agnes...
console.log(adults) 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分析一下...

*   作为参数，我们传递一个函数
*   这个函数(对于这个例子)有一个参数(这里:`person`)
*   该参数引用 filter()的 forEach 中正在迭代的当前元素。
*   我们传递的**函数返回一个布尔值**作为值。该布尔值指示当前元素是否被过滤掉(`true` =留在数组中，`false` =被跳过)
*   如果这个人的年龄大于或等于 18 岁，我们就有这个布尔运算的条件。

抬起头来！为了让这篇博文更简单，我就不解释*能*通过的所有参数了。关于可选参数的更多细节，请查看 [MDN docu on filter()](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 。

## [T1。地图()](#map)

`.map()`也返回一个数组结果。但是这次我们不会过滤任何东西，而是用从原始数组计算出来的数据填充新数组。

让我们假设这个数组中的温度是摄氏度:

```
const celsiusArray = [ 21.5, 33.5, -7.1, 12.6 ] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要把它转换成同一个数组，但是用华氏温度，我们*可以*这样传统地做:

```
let fahrenheitArray = []
celsiusArray.forEach(celsius => {
    fahrenheitArray.push((celsius * 9/5) + 32)
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想用`.map()`写同样的东西，它应该是这样的:

```
const fahrenheitArray = celsiusArray.map(celsius => (celsius * 9/5) + 32) 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，与`.filter()`示例不同，当我们使用`.map()`函数时，原始数组和输出数组的数组大小总是相同的。

我们来分析一下这里发生了什么...

*   作为参数，我们传递一个函数
*   这个函数(对于这个例子)有一个参数(这里:`celsius`)
*   该参数引用 filter()的 forEach 中正在迭代的当前元素。
*   我们传递的**函数返回我们想要的任何类型的值**
*   该值将被插入到新数组中。

抬起头来！为了让这篇博文更简单，我就不解释*能*通过的所有参数了。有关可选参数的更多详情，请查看 [MDN docu on map()](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 。

## [T1。减少()](#reduce)

这个函数是三个函数中唯一不返回数组的函数。相反，它返回一个值。

这意味着，这些函数使用来自数组的信息并从中计算出一些东西。它计算什么完全取决于我们给它的函数。

让我们假设在餐馆消费的这一系列产品:

```
const foodOrder = [
    { name: 'Pizza Diavola', price: 15.9 },
    { name: 'Sparkling Water', price: 2.9 },
    { name: 'Tiramisu', price: 6.5 }
] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想计算食物的总数，并在顾客的发票上显示总数，包括 17%的增值税，如果我们不知道。reduce()函数:

```
let sum = 0
foodOrder.forEach(food => {
    sum += food.price
}) 
```

Enter fullscreen mode Exit fullscreen mode

使用`.reduce()`，它将看起来像这样:

```
const total = foodOrder.reduce(( accumulator, food ) => accumulator + food.price, 0); 
```

Enter fullscreen mode Exit fullscreen mode

> 注意，除非像其他两个函数一样，`.reduce()`不返回数组，而是返回单个值。

那么在上面的例子中会发生什么呢？

*   `.reduce()`遍历我们的数组，用每个食物项目调用我们的函数
*   第一项`accumulator`是到目前为止循环计算的当前“总计”值
*   `food`是当前已经迭代过的项目
*   我们传递的函数的**返回值**是**无论我们想做什么**，使用`accumulator`和`currentValue`(在这种情况下，将两者相加)
*   只有一个小问题:在大多数情况下，这种计算必须有一个初始值。幸运的是，ES6 为我们提供了一个可选参数(在`.reduce()`函数中是第二个)

抬起头来！为了让这篇博文更简单，我就不解释*能*通过的所有参数了。关于可选参数的更多细节，请查看 reduce() 上的 [MDN docu。](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)