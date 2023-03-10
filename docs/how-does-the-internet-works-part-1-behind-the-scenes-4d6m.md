# 互联网是如何工作的？第 1 部分:浏览网站

> 原文：<https://dev.to/ahmedsomaa/how-does-the-internet-works-part-1-behind-the-scenes-4d6m>

谁一天中至少不用一次互联网？几乎所有人，对吗？你首先打开你的浏览器(Firefox，chrome，safari...等等。)，写下你最喜欢的网站的**网址** (DEV，Google...等等。)在浏览器的地址栏中，你点击 go，网站就会显示出来，但你有没有想过这是怎么发生的？一旦你给了浏览器网址，它就会显示出来，这背后发生了什么？

理解互联网是如何工作的非常重要。许多人通常浏览互联网工作机制背后的概念的表面，而没有完全理解一切是如何一起工作的。没有这些基础，考虑性能、优化站点等等就变得非常困难。因此，我决定与你分享我所知道的，我决定把它作为一个系列来发表，这样这篇文章就不会占用你太多的时间。

### 第一部分:浏览网站

#### (一)找出该域的 IP 地址

[![Alt Text](img/a53e0ddf1d31fb6230fa92273b4bedff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3y3wClVi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lh66syyh8zu9d9fathnm.png) 
当你输入一个网址(Google.com...例如)并点击 go 按钮，浏览器首先需要知道您想要访问的域名的 **IP 地址**，以便导航到该域名。因此，浏览器会检查其缓存，该缓存保存了 **DNS** 记录(2-30 分钟)。如果不存在，浏览器会向你的互联网路由器发送一个请求，询问它“这个 Google.com 家伙是谁？”您的路由器通过检查自己的缓存中的 **DNS** 记录来做同样的事情。如果它也不存在，它会向您的 **ISP** 发送相同的请求，询问相同的问题*“您知道这个 google.com 吗？”*您的 **ISP** 再次向其 **DNS** 缓存发送相同的请求。 **DNS** 回复 ISP: *“我不知道 google.com 是谁，但我有他的地址，这里是 127 . 217 . 7 . 23”*。 **ISP** 将 **IP 地址**返回给路由器，路由器再将其发送回你的浏览器。

#### 【B】导航到网站

[![Alt Text](img/f64a162020f0e632c61118b339b5a2e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--df7WUR3N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htfpa6huhiyb5vv4kurl.png) 
一旦浏览器接收到 **IP 地址**，它就向该地址发送 **HTTP 请求**，请求在浏览器和网站的 web 服务器之间建立连接。Web 服务器处理请求，并通过发送网站的 HTML、CSS 和 JavaScript 来回复浏览器，浏览器会呈现这些内容，以便您可以查看网站的页面。

封面图片图标许可: [icons8](https://icons8.com/) 免费许可。