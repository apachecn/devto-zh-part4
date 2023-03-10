# JavaScript 集非常棒！

> 原文：<https://dev.to/jacobmgevans/javascript-sets-are-excellent-1bh7>

# JavaScript 中的集合有🔥

*   在 [MDN Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 中，它声明“Set 对象允许您存储任何类型的唯一值，无论是原始值还是对象引用。”

因此，集合的明显用例是删除重复项！他们做得非常简单明了。我个人喜欢我为横幅创建的示例。一个**数组文字**内的 spread `...`和`new Set()`的组合消耗来自函数的输入，这意味着它将接受任何可以被 spread 的 iterable，对其进行“重复数据删除”,并将这些值返回到一个数组中！超级爽！！在一个小包装里有很多东西。

# 准类型检查/搜索&搜索&删除

*   JavaScript 中的集合有一些数组没有的东西，也缺少一些数组有的东西...例如，集合没有索引！“但我记得你说过它是可重复的？”我做了，这并不意味着它有一个索引😆

*   好的，那么所有可迭代的共享的最有用的东西是什么呢？我想知道它“有”😆something，with Objects `.hasOwnProperty()`将检查某物是否存在，但不检查类型是否匹配...
    [![const object = { cat:  raw `meow` endraw , 1:  raw `hundred` endraw  }<br>
    console.log(object.hasOwnProperty( raw `cat` endraw ))<br>
    console.log(object.hasOwnProperty( raw `1` endraw ))](img/bb5277541d56c166f29a287196cf367c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vkcYH_vp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ewz2kxtn0n8ch4g6e5q.png)

*   因此...不仅来自集合的`.has()`检查的工作方式与来自`.hasOwnProperty()`的稍有不同，而且它的工作方式也与来自数组的`.includes()`稍有不同，但是两个`.has()` & `.includes()`都将返回假的**类型**与正在搜索的内容不匹配。
    [![const mySet = new Set([1, 2, 3, 4])<br>
    console.log(mySet.has( raw `2` endraw )) // false<br>
    console.log(mySet.has(2)) // true](img/035b0930b1597be39803bd9d6f9d0609.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2P4xpdt4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tc1eo32ih8gydrb8grt6.png)

*   集合内置的另一个超级有用的东西是`.delete()`,在数组中没有对应的东西，你可以知道数组的索引，或者搜索并得到元素的索引，然后找出如何移除它。

    *   Set `.delete()`相比之下是什么样子的？![const mySet = new Set([1, 2, 1223, 431242, 342345245235, 2123123, 12313])
        console.log(mySet.delete(2)) // true
        console.log(mySet.delete(342345245235)) // true
        console.log(mySet.delete( raw `431242` endraw )) // false
        console.log(mySet); // Set { 1, 1223, 431242, 2123123, 12313 }](img/420b2d94459cfc9c78585d7ce06b0727.png)

这也是一个很好的集上阅读资源！
[https://medium . com/front-end-weekly/es6-set-vs-array-what-and-when-EFC 055655 E1 a](https://medium.com/front-end-weekly/es6-set-vs-array-what-and-when-efc055655e1a)

🚧🚧🚧🚧
更多精彩内容包括使用案例和示例，以及解释😎
🚧🚧🚧🚧