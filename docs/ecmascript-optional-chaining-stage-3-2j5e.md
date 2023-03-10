# ECMASCRIPT:可选链接|阶段 3

> 原文：<https://dev.to/numtostr/ecmascript-optional-chaining-stage-3-2j5e>

可选链接处于阶段 3🎉🎉。这个是我最喜欢的。很快就会有打字稿了。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ tc39 ](https://github.com/tc39) / [提议-可选-链接](https://github.com/tc39/proposal-optional-chaining)

<article class="markdown-body entry-content container-lg" itemprop="text">

# JavaScript 的可选链接

## 状态

流程第 4 阶段的 ECMAScript 提案。

## 作者

*   克劳德·帕奇(“t0”)
*   加布里埃尔·伊森伯格( [github](https://github.com/gisenberg) ， [twitter](https://twitter.com/the_gisenberg) )
*   丹尼尔·罗森瓦塞尔( [github](https://github.com/DanielRosenwasser) ， [twitter](https://twitter.com/drosenwasser) )
*   达斯汀·萨维里( [github](https://github.com/dustinsavery) ， [twitter](https://twitter.com/dustinsavery) )

## 概述和动机

当在树状结构中查找属性值时，通常需要检查中间节点是否存在:

```
var street = user.address && user.address.street;
```

此外，许多 API 要么返回一个对象，要么返回 null/undefined，只有当结果不为 null 时，才需要从结果中提取属性:

```
var fooInput = myForm.querySelector('input[name=foo]')
var fooValue = fooInput ? fooInput.value : undefined
```

可选的链接操作符允许开发人员处理许多这样的情况，而无需重复和/或在临时变量中分配中间结果:

```
var street = user.
```

…</article>

[View on GitHub](https://github.com/tc39/proposal-optional-chaining)