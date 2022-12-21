# 我是怎么学会这个的. reduce()；

> 原文：<https://dev.to/prasannavijayan/how-did-i-learn-this-reduce-1ac6>

Javascript 学起来太神奇了。我在飞行中学习一些东西来修复一些错误或尝试{} catch 方式(基本上失败和理解得更好)。其中一个就是`this.reduce();`。

`this.reduce()`获取数组并返回单个值。让我们举一个例子

在进一步理解 reduce 之前，让我们看一下它的论点。Reduce 需要 4 个参数。

1.  总计//！req a + b，它返回初始值或总和值
2.  currentValue //！当前元素的请求值
3.  currentIndex //!opt
4.  arr //！opt 数组

只有数组数量的示例

```
let arr = [1, 2, 3, 4, 5, 6];

let ans = arr.reduce( (a, b) => a + b ); // 21 
```

Enter fullscreen mode Exit fullscreen mode

对象示例

```
let movies = [{ title: 'Cars', part: '1', views: '400' },
              { title: 'Cars', part: '2', views: '300' },
              { title: 'Cars', part: '3', views: '100' },
              { title: 'Planes', part: '1', views: '800' },
              { title: 'Planes', part: '2', views: '500' }];

let total = { cars: 0, planes: 0 };

let totalviewsmovies = movies.reduce( (a, b) => {
    total[b.title.toLowerCase()] += parseInt(b.views, 10);
});

console.log( total ); // { cars: 400, planes: 1300 } 
```

Enter fullscreen mode Exit fullscreen mode

好吧，可能会有个问题？这对你来说有多难？。

回答:直到最近在某个公司面试，我才知道这么多争论的细节以及它是如何运作的。

多亏了他！

让我知道你的想法。