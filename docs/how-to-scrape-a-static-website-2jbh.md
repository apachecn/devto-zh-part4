# 如何刮一个静态网站

> 原文：<https://dev.to/robghchen/how-to-scrape-a-static-website-2jbh>

一个非常快速的教程

[![](img/b6ced83cc5ab69b0d9c6ae586df76ff2.png)](https://medium.com/@robghchen/how-to-scrape-a-static-website-846bd9c6ed28?source=rss-1a2b6738cba4------2)

先决条件:本教程将需要 [React.js](http://reactjs.org/) 的知识。

假设您想从网站前端获取数据，因为没有可用的 API。您检查页面并看到数据在 HTML 中可用，那么您如何收集这些信息用于您的应用程序呢？这相当简单，我们将安装两个库，并编写不到 50 行代码来演示网站的抓取。为了使本教程简单，我们将使用[https://pokedex.org/](https://pokedex.org/)作为我们的例子。

1)在终端:

```
create-react-app scraping-demo
cd scraping-demo
npm i request-promise
npm i cheerio 
```

Enter fullscreen mode Exit fullscreen mode

2)我们将从使用请求-承诺开始，将来自[https://pokedex.org/](https://pokedex.org/)的 HTML 放入控制台日志。

在 App.js 中: