# 一个方法链可以叫做 LINQ 吗？

> 原文：<https://dev.to/peledzohar/can-a-method-chain-be-called-linq-2l6e>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [一个方法链能叫做 LINQ 吗？](https://stackoverflow.com/questions/46070567/can-a-method-chain-be-called-linq)

Sep 6 '17 Comments: 4 Answers: 1[![](img/e3f0373ec76330150a340eacd410b600.png)10![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/46070567/can-a-method-chain-be-called-linq) </header>

把这段代码片段叫做 LINQ(语言集成**查询**)对吗？

```
var lstMyStrings = new List<string>();
lstMyStrings.Where(aX => string.IsNullOrEmpty(aX))
            .Skip(3)
            .ToList(); 
```

我很困惑，因为 **`System.Linq`对于这个代码是强制的**。
然而，当我看到这样的问题和答案:[。NET LINQ 查询语法 vs 方法链](https://stackoverflow.com/questions/3776577/net-linq-query-syntax-vs-method-chain) …

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/46070567/can-a-method-chain-be-called-linq)</button>