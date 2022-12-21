# 答案:对计数器进行排序(并提取最常见的值)

> 原文：<https://dev.to/pancy/answer-sorting-a-counter-and-extracting-the-most-common-values-23bm>

<header>

# ![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [ 答案回复:对一个计数器进行排序(并提取最常见的值)](https://stackoverflow.com/questions/25636291/sorting-a-counter-and-extracting-the-most-common-values/57799517#57799517)

Sep 5 '19[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)0![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/25636291/sorting-a-counter-and-extracting-the-most-common-values/57799517#57799517) </header>

更新一下这个问题，我用的是 Julia v1.2.1，没有`select!` afaik。

下面是对我使用的@Toivo 实现的一个小修改:

```
using DataStructures

most_common(c::Accumulator) = most_common(c, length(c))
most_common(c::Accumulator, k) = sort(collect(c), by=kv->kv[2], rev=true)[1:k] 
```

这将返回一个带有`k`成员的`::Array{Pair{String,Int}},1`,类似于 Python `Counter.most_common(k)`,它…

<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/25636291/sorting-a-counter-and-extracting-the-most-common-values/57799517#57799517)</button>