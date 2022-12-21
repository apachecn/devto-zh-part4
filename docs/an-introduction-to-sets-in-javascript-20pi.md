# JavaScript 中的集合介绍

> 原文：<https://dev.to/lelouchb/an-introduction-to-sets-in-javascript-20pi>

#### 使用集合来处理数据集中的唯一元素

<figure>[![](img/28eb89d771cd39103e791316c49c9ace.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_GywGCSt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2JwaGxljIRs94WsH1MKhww.jpeg) 

<figcaption>照片由[乔舒亚·科尔曼](https://unsplash.com/@joshstyle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上[下](https://unsplash.com/search/photos/sets?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

集合是 ES6 中包含的一种新的对象类型，它允许创建唯一值的集合。集合中的值可以是简单的原语，如字符串和整数，也可以是更复杂的对象类型，如对象文字或数组。把集合想象成一个没有重复元素的数组。

在许多情况下，我们有一个包含许多元素的数组，我们希望将这些元素转换成一个唯一的数组。虽然它可以通过数组上的暴力、检查元素、使用循环、不同的回调方法等来完成。，最好的方法是使用 set()。

语法:

```
new Set([_iterable_]); 
```

Enter fullscreen mode Exit fullscreen mode

我们先来看一个例子