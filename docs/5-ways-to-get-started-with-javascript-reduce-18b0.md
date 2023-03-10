# 开始使用 JavaScript 的 5 种简单方法。减少()

> 原文：<https://dev.to/jasterix/5-ways-to-get-started-with-javascript-reduce-18b0>

作为一名全新的开发人员，我最喜欢的 JavaScript 工具是。map()和 for 循环。但是最近，我下定决心要理解更复杂的方法，比如. reduce。

在 [CSX](https://csx.codesmith.io/units/callbacks) 中完成回调和高阶函数的挑战是对 reduce 的完美介绍，这对我来说是一个很大的挑战。但是 reduce 是一个强大的方法，绝对值得一试。

第一个。我接触到的 reduce()方法是 via[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)
`arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])`
[W3Schools definition](https://www.w3schools.com/jsref/jsref_reduce.asp)要好一些，但也好不了多少。

#### 那么克服这种恐吓因素的最好方法是什么呢？

1)从基本的例子开始

*   大多数新手都熟悉使用。reduce()来聚合数组中的元素

```
const theOG = (array) => {
  return array.reduce((acc, el) => {
    return acc + el
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

2)删除任何易混淆的术语——累加器？什么回调？

*   我只使用 x 和 y 玩了简单的 reduce 应用程序。
*   但是使用总计和当前或总计和项目可能更有意义
*   这个语法看起来不眼熟吗？

```
return array.reduce((x, y) =>  x + y % 2)
return array.reduce((x, y) =>  x - y) 
```

Enter fullscreen mode Exit fullscreen mode

3) console.log 很多

*   如果你不确定 reduce 在做什么，可以查看 console.log，看看它与你的期望是否相符。

```
const reduces = (array) => {
return array.reduce((acc, num) => {
  console.log(acc, num)
  return acc + num
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

4)添加其他参数和 console.log】

```
array.reduce((acc, num, index, array) => {
    console.log(num, index) 
```

Enter fullscreen mode Exit fullscreen mode

5)练习+重复

*   挑战自己解决不同的问题。reduce()而不是更明显的循环或方法。
*   反复解决同样的问题，直到它们成为第二天性
*   对我来说，CSX 挑战赛在这方面做得很好，但你也可以编造自己的挑战

和每个话题一样，FreeCodeCamp 有一篇简单明了的文章,你可以在遇到困难时参考。

你也可以在这里查看我的减少挑战