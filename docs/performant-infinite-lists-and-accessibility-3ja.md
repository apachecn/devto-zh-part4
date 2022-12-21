# 性能无限列表和可访问性。

> 原文：<https://dev.to/anpos231/performant-infinite-lists-and-accessibility-3ja>

我是在 medium 上看的这篇文章:
[https://medium . com/walmartlabs/infinite-scrolling-the-right-way-11b 098 a 08815](https://medium.com/walmartlabs/infinite-scrolling-the-right-way-11b098a08815)

作者说，为了保持事物的性能，我们可以重用现有的`<li>`元素，而不是给`<ul>`添加新的`<li>`。为了营造一种滚动的沉浸感，我们可以在`<ul>`上使用填充。

我马上想到一个问题:由于屏幕阅读器根据`<ul>`中`<li>`的实际数量来公布列表的大小，屏幕阅读器用户将无法知道列表实际有多大。

非屏幕阅读器用户会充满沉浸感，但屏幕阅读器用户不会。