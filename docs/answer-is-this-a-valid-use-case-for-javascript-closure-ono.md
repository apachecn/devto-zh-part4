# 回答:这是 javascript 闭包的有效用例吗？

> 原文：<https://dev.to/harittweets/answer-is-this-a-valid-use-case-for-javascript-closure-ono>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:这是 javascript 闭包的有效用例吗？](https://stackoverflow.com/questions/11496790/is-this-a-valid-use-case-for-javascript-closure/57779674#57779674)

Sep 3 '19[![](img/e3f0373ec76330150a340eacd410b600.png)0![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/11496790/is-this-a-valid-use-case-for-javascript-closure/57779674#57779674) </header>

我试图提出一个信息隐藏/封装的实际例子，这是我目前为止的尝试

```
'use strict';

const bank = (accountHolderName, initialDeposit) => {
    let balance = initialDeposit;
    const add = (amount) => {
        balance += amount;
    }

    let subtract = (amount) => {
        balance
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/11496790/is-this-a-valid-use-case-for-javascript-closure/57779674#57779674)</button>