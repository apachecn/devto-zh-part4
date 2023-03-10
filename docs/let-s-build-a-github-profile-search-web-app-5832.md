# 让我们构建一个 Github 个人资料搜索 Web 应用程序

> 原文：<https://dev.to/bboyakers/let-s-build-a-github-profile-search-web-app-5832>

[![](img/388a8a88791f7e82d772c0ae7d9564be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zBOrhic3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/40xdw6ag75ksnjwjlkvs.PNG)

作为我的第一篇博文，我想我会很棒，能够尝试并开始发布一系列每周项目，供开发人员构建小型有趣的 web 应用程序来添加到他们的投资组合中。

今天，我想我会很好地开始构建一些相当有趣的东西。使用 Github API 构建 Github 个人资料搜索 Web 应用程序。

我们开始吧！！

1.)首先创建一个这样的文件夹结构。
CSS/styles . CSS
js/scripts . js【index.html】T2
[T5](https://res.cloudinary.com/practicaldev/image/fetch/s--zJWDEs3W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g2z7ko1gzyern6js7bmm.PNG)

如果你真的想学点有趣的东西，试试用终端吧！

触摸 index.html
mkdir CSS&T4【mkdir js】触摸 css/styles.css
触摸 js/scripts.js

“touch”是创建新的空文件的最简单方法,“mkdir”是创建新的空文件夹的最简单方法

2.)设置我们的 HTML
[![](img/9f4b6e7a1c312a3665b7d377c1f28be2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--usYFoa6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/82olac4qdth6vq57pvqy.PNG)

3.)接下来我们需要设置我们的应用程序框架
[![](img/0a7dd053fae6c1bed13361fb626cf0e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dddifC-C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ijr083mo51ps8amniuia.PNG)

我们已经创建了两个主要 div。第一个 div 是我们的输入字段和搜索按钮。第二个 div 是我们显示调用 github api 返回的信息的地方。

我们在那里也有类，因为我们将使用它们来设计应用程序的样式，并使用 JavaScript 中的元素，稍后您将会看到。

构建 JavaScript
在这一节中，我们将构建应用程序的功能。

4.)脚手架

在你构建任何东西之前，在你花时间编码之前，知道你应该写什么总是很好的。问问你自己，“我们想要完成什么？”

任务:
-抓取搜索元素
-抓取将显示数据的 div
-当我们点击我们想要的搜索:
-从文本输入字段中抓取数据
-使用该数据从 Github API 中抓取用户信息
-显示来自 Github API 的响应数据

它应该看起来有点像这样！
[![](img/86cec7f01f182e86c318cfb9fb1268c6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--dv_dU3Y0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ptqu4oaxkr10whp4j7un.PNG)

5.)抓取我们的元素并创建搜索调用
[![](img/d3ff9412f8174c88744d90cb5f3567e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9KJbcjgc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oqjlh3hb83f3xiyavyek.PNG)

如果您对 querySelector()有疑问；这里有一个链接！
[https://www . w3schools . com/jsref/met _ document _ query selector . ASP](https://www.w3schools.com/jsref/met_document_queryselector.asp)
T5】https://www.youtube.com/watch?v=JlgLDfINXvY

如果你想了解 addevent listener()；
[https://www . w3schools . com/jsref/met _ document _ addevent listener . ASP](https://www.w3schools.com/jsref/met_document_addeventlistener.asp)
【https://www.youtube.com/watch?v=F3odgpghXzY】T4

6.)从我们的输入字段获取数据，并设置我们对 Github API
[![](img/6b3a453092ad03a06592263b757711de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xKZZrffM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdih4ywhfi8r3qkf21sg.PNG) 的调用

好了，现在哇。老实说，这里发生了很多事情，你可能想知道。你完全正确，所以让我们来分解一下:)。

当我们单击时，我们需要从输入字段中获取最新的数据。如果我们在文件的全局范围内声明它将
[![](img/e73038e27d5d3ca0e832068b0bd71884.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ljIxLu9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4l76ck626y0gpm7ye0p1.PNG)

其次，让我们看看这个 GET 请求。
[![](img/29af5185c31819656fc6a72a16ee68ef.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--E3ll1akB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nww042t08nmcmjjuqin9.PNG)

什么是 XMLHttpRequest？XMLHttpRequest 是一个内置的浏览器对象，允许用 JavaScript 发出 HTTP 请求。
点击此处了解更多信息。https://javascript.info/xmlhttprequest
T3T5[https://www.youtube.com/watch?v=rjmtYkRK1nM](https://www.youtube.com/watch?v=rjmtYkRK1nM)

仅仅因为浏览器的兼容性，我决定用这个代替 fetch。我认为支持旧的浏览器而不必处理多填充会很棒。看看这里。
T1】https://developer . Mozilla . org/en-US/docs/Web/API/Fetch _ API/Using _ Fetch # Browser _ compatibility

如果你想用 fetch 来代替，请随意！

7.)接下来，我们必须检查并使用我们的响应数据。

您可以通过在我们应该显示数据的地方执行 console.log(response)来检查响应的内容。

现在我们在 HTML
[![](img/ac01b08e2044562f3006bbf3f2ba9323.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iHFuzdWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/163z38vnolakukue3dst.PNG) 中填充数据

8.)现在我们加造型！
[![](img/bafc8a3bd1dfe1ed2814ee79bb07dd4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z0LsOHTA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bqu08f3zfarubkfgzrm2.PNG) 
[![](img/ed44f884666512c59d05d0aec57edcb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7mFtmHpw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhavlcftikqntkbut9m1.PNG)

我们完了！！！！

希望你能确保在这个过程中没有任何错误，并且附上了你的脚本和样式表！

虽然这个应用程序看起来不错，但是你可以做更多的事情来改进这个应用程序。

这里有一个列表:
-* *研究使用 innerHTML 的替代方案，并理解为什么使用它不是最佳实践
-为卡片添加 CSS 动画
-为我们的调用添加错误处理
-为布局使用 CSS 网格或 Flexbox
-将 CSS 转换成 Sass

那会让你忙上一阵子。玩得开心，享受你的一周！