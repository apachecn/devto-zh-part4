# 没有 Web 服务器的普通 JavaScript 路由器

> 原文：<https://dev.to/aminnairi/a-router-without-a-web-server-in-vanilla-javascript-3bmg>

我看到一个关于如何用纯 JavaScript 创建路由器的帖子。因为它不是在谈论散列路由，我决定创建这个帖子来与你分享我的知识。

## 我为什么要这么做？

由于 History API，用 JavaScript 构建路由器现在已经变得很简单了。它在很大程度上是受支持的，并允许你以你想要的方式构建你的路由器，而不依赖于第三方库。在 Vue.js 中，你甚至可以构建自己的自制路由器，并借助 Vue.js 插件进行插拔。事实上，Vue.js 的官方路由器库 Vue Router 依赖于历史 API。但不仅仅是。因为它可以让您随意构建所谓的散列路由器。

## 什么是哈希路由器？

它是一个不依赖于历史 API 的路由器，而是依赖于你的网站的散列 URL。假设你的网页上有一个标题。

```
<h2>About me</h2>
<p>Lorem ipsum dolor sit amet...</p> 
```

Enter fullscreen mode Exit fullscreen mode

你希望你的用户无论何时出现在你页面的最前面，都可以直接跳到这个部分。你可能想为你的标题使用一个`id`属性。

```
<h2 id="about">About me</h2> 
```

Enter fullscreen mode Exit fullscreen mode

并创建一个链接，就像在你的标题中一样，将你的用户重定向到这个部分。

```
<header>
  <a href="#about">About me</a>
</header> 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你点击你的链接，网址应该从

```
http://yoursite.com 
```

Enter fullscreen mode Exit fullscreen mode

至

```
http://yoursite.com#about 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的，对吧？

## 为什么我要使用散列路由器？

基于历史 API 的路由器依赖于页面的来源来工作。如果您尝试打开实现了历史 API 的 HTML 页面，您应该会看到类似这样的内容:

```
Failed to execute 'pushState' on 'History': A history state object with URL 'file:///path/to/index.html' cannot be created in a document with origin 'null' 
```

Enter fullscreen mode Exit fullscreen mode

这是因为，默认情况下，作为文件打开的 HTML 文档的原点设置为`null`。这是一个问题。

相反，基于 hased 的路由器不依赖于此，而是依赖于由 window 对象触发的事件。当我们改变 url 的散列值时，这个事件将被触发。在我们前面的例子中，单击链接将触发这个事件。不需要网络服务器。

## 如何实现散列路由器？

它就像只使用一个事件一样简单。 [`onHashChange`](https://developer.mozilla.org/en-US/docs/Web/API/WindowEventHandlers/onhashchange) 事件。

```
<!DOCTYPE html>
<html>
  <body>
    <a href="#home">Home</a>
    <a href="#about">About</a>
    <script src="script.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
function onRouteChanged() {
  console.log("Hash changed!");
}

window.addEventListener("hashchange", onRouteChanged); 
```

Enter fullscreen mode Exit fullscreen mode

```
Hash changed!
Hash changed! 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/FabulousFloweryQueryplan)。

## 实现路由

我们现在需要获取用户发布的路线。我们可以使用 [`window.location.hash`](https://developer.mozilla.org/en-US/docs/Web/API/Window/location) 属性来获取当前“路线”的值。

```
 function onRouteChanged() {
-   console.log("Hash"); +   console.log(window.location.hash);
  } 
```

Enter fullscreen mode Exit fullscreen mode

```
#home
#about 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/SourLongtermDeal)。

我们现在需要的东西都有了。我们可以开始为路由器实现一个视图渲染器。

```
 <a href="#about">About</a>
+     <a href="#contact">Contact</a>
+     <main id="router-view"></main>
      <script src="script.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

我还加了另一个链接。这将帮助我向你展示我们如何实现一个`404 - page not found`处理器。你会惊讶于这有多简单。

接下来，我们需要给我们的`onRouteChange`处理器增加一点逻辑，这样它就可以像路由器一样呈现我们的路由。

```
 function onRouteChanged() {
-   console.log(window.location.hash); +   const hash = window.location.hash;
+   const routerView = document.getElementById("router-view");
+ 
+   if (!(routerView instanceof HTMLElement)) {
+     throw new ReferenceError("No router view element available for rendering");
+   }
+ 
+   switch (hash) {
+     case "#home":
+       routerView.innerHTML = "<h1>Home page</h1>";
+       break;
+ 
+     case "#about":
+       routerView.innerHTML = "<h1>About page</h1>";
+       break;
+ 
+     default:
+       routerView.innerHTML = "<h1>404 - Page Not Found</h1>";
+       break;
+   }
  } 
```

Enter fullscreen mode Exit fullscreen mode

[网上试试](https://repl.it/repls/HummingParallelPcboard)。

我将散列 URL 存储在一个变量中，这样我就可以使用`switch`语句根据用户发出的路由呈现不同的 HTML 内容。我还存储了 router view 元素，以检查该元素是否确实在文档中(我们永远不知道会发生什么，在这种情况下，最好有一些有说服力的错误消息)。我还需要它来更新路由器的`switch`语句中的`innerHTML`内容。

开关的默认语句将由我们的 contact 链接触发，因为我们没有在开关中为它指定任何处理程序。

就是这样！你有一个非常基本的路由器，可以在任何地方工作，无论它是托管在 web 服务器上，还是作为一个 HTML 页面共享。例如，当你需要向客户展示一个网站的快速原型时，我可以看到一些用例。他所要做的就是在浏览器中打开页面，然后 tada！

## 局限性

当然，这种路由有一个明显的限制，因为我们使用的是 URL 的哈希，并侵入了它的原始用途，将其用作路由器。但是如果我们需要在页面中使用常规的 hrefs，它会简单地中断路由，因为它会触发我们的 hash changed 处理程序。

## 解

我为这个问题找到了一个解决方案，可能不是最好的，但如果你绝对需要使用基于散列的路由，这是值得的，就是使用一个`data-*`属性和一点 JavaScript。

```
<button data-go-to-id="a-little-introduction">to the intro</button>
<!-- later in the document -->
<h2 id="a-little-introduction>A little introduction</h2> 
```

Enter fullscreen mode Exit fullscreen mode

```
"use strict";

document.querySelectorAll("[data-go-to-id]").forEach(function(link) {
  link.addEventListener("click", function() {
    const element = document.getElementById(link.dataset.goToId);

    if (!(element instanceof HTMLElement)) {
      throw new ReferenceError(`Unable to found element with id "${goToId}"`);
    }

    window.scroll({
      top: element.getBoundingClientRect().top,
      left: 0,
      behavior: "smooth"
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

一些设备上的平滑滚动不起作用(我特别想到一些苹果设备)，但我相信这是你能找到的解决这个问题的许多解决方案之一。我的解决方案的缺点是它不能用于像`Hey, look what I found here: http://yoursite.com#home#title-of-article`这样的共享链接。我将此作为读者实现更好解决方案的一个练习。

## 结论

基于散列的路由器是另一种无需重新加载页面就能路由用户的方式。这在创建 GitHub 页面时也很方便，因为我们不必重新考虑基于历史的路由器，也不必在所有路由前加上子目录，如`/github-repo/about`。

如果您不需要使用大量的 href 重定向，并且不想/不能使用历史 API，那么在您的页面中使用路由器是一个很好的解决方案。

我向您展示的是一个基于 hased 的路由器的非常基本的实现。如果你想更进一步，你可以:

*   在一个对象内部实现这个路由器，像`new HashedRouter`一样使 API 更容易使用。特别是像`addRoute`和`start`这样的方法。
*   找到一个比我用来实现页面中的链接更好的解决方案。