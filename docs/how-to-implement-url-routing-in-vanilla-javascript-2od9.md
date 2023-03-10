# 如何用普通 JavaScript 实现 URL 路由

> 原文：<https://dev.to/prahladyeri/how-to-implement-url-routing-in-vanilla-javascript-2od9>

在这篇文章中，我将向你展示如何用普通的 JavaScript 实现动态 URL 路由，而不使用任何沉重的框架，如 angular、vue、react、ember 等。不到 30 行代码！

你需要知道的重要属性是`window.location`，更具体地说是我们的目的`window.location.hash`。这个内置的属性基本上告诉我们当前在哪个页面 URL 或路径上(例如:`index.html#something`)。如果没有散列 URL，并且您位于主 URL(例如:`index.html`或`index.html#`)上，则该属性为空。在这种情况下，您可以假设一个默认散列，比如`_index`，以保持一致性:

```
function displayHash() {
  var theHash = window.location.hash;
  if (theHash.length == 0) { theHash = "_index"; }
  var elems = document.querySelectorAll("#caption");
  elems[0].innerText = "Current Hash: " + theHash;
  return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的函数在名为`#caption`的标题元素中显示当前散列。如果你想让它在每次哈希改变时触发(用户导航到你的应用程序的不同页面)，你可以使用`hashchange`窗口事件:

```
window.addEventListener("hashchange", function() {
  console.log("hashchange event");
  displayHash();
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了在用户第一次在浏览器中加载 URL 时显示标题，您也可以在`DOMContentLoaded`事件调用中调用`displayHash()`:

```
window.addEventListener("DOMContentLoaded", function(ev) {
  console.log("DOMContentLoaded event");
  displayHash();
}); 
```

Enter fullscreen mode Exit fullscreen mode

那都是乡亲们！URL 路由很容易实现，根本不需要使用任何笨重的框架。这个例子的演示可以在[prahladyeri.github.io/learn-js/url-routing](https://prahladyeri.github.io/learn-js/url-routing)找到。您可以看到，当您单击各个超链接时，标题标签会发生变化以显示当前的哈希。

完整的源代码可以在我的 github repo 中找到， [prahladyeri/learnjs](https://github.com/prahladyeri/learn-js/blob/master/url-routing/index.html) (不到 30 行；-).

我是通过图里奥·法利亚的 StackOverflow 帖子偶然发现这个解决方案的，这个帖子使用了 jquery，但是我把它改编成了普通的 JavaScript。