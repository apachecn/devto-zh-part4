# 合并 node.js 中的多个对象

> 原文：<https://dev.to/malith1994124/merge-multiple-objects-in-node-js-21be>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [在 node.js](https://stackoverflow.com/questions/58056310/merge-multiple-objects-with-different-format-in-node-js) 中合并多个不同格式的对象

Sep 23 '19 Comments: 2 Answers: 2[![](img/e3f0373ec76330150a340eacd410b600.png)1![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/58056310/merge-multiple-objects-with-different-format-in-node-js) </header>

我创造了下面的物体，

```
products = [
    {id:1, name: a},
    {id:2, name: b},
]

prices = [
    {id:1, month:1, price:1},
    {id:1, month:2, price:1},
    {id:2, month:1, price:1},
    {id:2, month:2, price:1},
] 
```

所以我想要产量作为产品价格，

```
product_prices = [
    {id:1, name: a, prices: [{id:1, month:1, price:1},{id:1, month:2, price:1}]}, 
    {id:2,
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/58056310/merge-multiple-objects-with-different-format-in-node-js)</button>