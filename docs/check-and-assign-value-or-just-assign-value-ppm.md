# 检查并赋值还是只赋值？

> 原文：<https://dev.to/yuzviko/check-and-assign-value-or-just-assign-value-ppm>

有时，如果集合中的一个项目满足某个条件，就需要设置一些标志。它通常在某种循环中完成，就像这样:

```
let hasEdibleBananas = false;

bananas.forEach(banana => {

  // ... do some banana related stuff here

  if (banana.isYellow) {
      hasEdibleBananas = true;
  } 
})

```

这段代码可以工作，但是我们会多次将值重新赋值给 *hasEdibleBananas* ，以防有不止一个黄色香蕉。
我们可以添加另一个条件，在给它赋值之前检查 *hasEdibleBananas* 是否已经设置:

```
let hasEdibleBananas = false;

bananas.forEach(banana => {

  // ... do some banana related stuff here

  if (banana.isYellow && !hasEdibleBananas) {
      hasEdibleBananas = true;
  } 
})

```

我的问题是，在设置 *hasEdibleBananas* 标志之前，是否真的有必要检查它是否被设置。
据我所知，在第二种情况下，应该没有任何可察觉的性能优势——当我们执行额外的检查时，我也不认为代码在这两种情况下有更多或更少的可读性。事实上，有些人可能会说，添加另一个从功能角度看没有任何区别的条件会给代码增加不必要的噪音，从而降低代码的可读性。

你怎么想呢?