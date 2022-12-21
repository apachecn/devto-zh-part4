# JavaScript:“下面的值刚刚被评估了”，是什么意思？

> 原文：<https://dev.to/harittweets/javascript-value-below-was-evaluated-just-now-what-does-it-mean-1pfc>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [“下面的值刚刚被评估了”，什么意思？](https://stackoverflow.com/questions/57843677/value-below-was-evaluated-just-now-what-does-it-mean)

Sep 8 '19 Comments: 3 Answers: 2[![](img/e3f0373ec76330150a340eacd410b600.png)-1![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/57843677/value-below-was-evaluated-just-now-what-does-it-mean) </header>

伙计们，我试着列出当`new`关键字被用来创建一个实例时，在幕后发生了什么。

下面是我的代码的样子

```
function F() {}
let f1 = new F()
f1.__proto__ 
```

当我理解到目前为止是，当`new`被使用，一个新的…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57843677/value-below-was-evaluated-just-now-what-does-it-mean)</button>