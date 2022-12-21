# 使用 React、Redux 和 React-Router 进行服务器端渲染

> 原文：<https://dev.to/itnext/server-side-rendering-with-react-redux-and-react-router-2en7>

### **TL；博士**

最近有一场关于服务器端渲染的大讨论。大家都说很难实现和维护。我用 **3 种不同的方法**创建了一个 **React** News web 应用程序，以便能够了解彼此之间的性能和实现差异。我想让这个应用程序尽可能接近真实世界的用例。我用过； **React，React-Router，Redux，Node.js，**和 **Express** 。您可以通过下面的链接查看每个应用程序；

*   ***单页应用***[**【bit.ly/spa-react】**](http://bit.ly/spa-react)
*   ***【服务器端渲染(通用)***[**【bit.ly/ssr-react】**](http://bit.ly/ssr-react)
*   ***服务器端渲染用***[**【bit.ly/ssr-cached】**](http://bit.ly/ssr-cached)

### 为什么？

我在网上看到了很多关于服务器端渲染的讨论，尤其是在 Twitter 上。我知道 React 有很多通用的渲染框架，但我想自己从头开始构建它，不需要任何黑盒解决方案。但首先最好理解什么是服务器端和客户端渲染。

[![](img/e1f22a93f05f5b913dc7122c69a5c9f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9_6-QlU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2400/1%2A0AIrrQHAhke2mny9VfDmdw.png)

### Javascript 革命

浏览器比 5-10 年前更加强大。我们开始用客户端 JavaScript 创建完整的网站和网络应用。我们开始把这种方法叫做**“单页应用”**。这导致我们创建更多的交互式实时更新的 web 应用程序。

但是这种方法有一个问题，我们最初的 HTML 不再包含与该 URL 相关的所有数据。