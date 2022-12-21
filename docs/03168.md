# 请求映射

> 原文：<https://dev.to/adactio/request-mapping-hk1>

*这篇文章最初发表于[我的网站](https://adactio.com/journal/15797)。*

[请求地图生成器](http://requestmap.webperf.tools/)是一个非常棒的工具。它由[西蒙·赫恩](https://simonhearne.com/)制作，使用[网页测试](https://www.webpagetest.org/) API。

你弹出一个 URL，它获取页面，并在一个漂亮的交互式圆圈图中映射出所有后续请求，显示第三方脚本自己生成了多少请求。我发现这是一种非常有效的方式，向对瀑布图不感兴趣的人展示第三方脚本的影响。

我在想…如果这能被内置到浏览器中不是很好吗？

在我们的开发者工具中已经有了一个“网络”标签。该选项卡的目的是显示收到的请求。浏览器已经有了所有它需要的信息来制作一个请求图，就像[请求图生成器](http://requestmap.webperf.tools/)所做的那样。

在 Firefox 中，在“网络”标签的左下角有一个小时钟图标。单击它会显示请求的饼图视图。这很有用，但是如果有选项也能看到[请求地图生成器](http://requestmap.webperf.tools/)显示的连接圆圈，我会很高兴。

只是一个想法。

*这篇文章最初发表于[我的网站](https://adactio.com/journal/15797)。*