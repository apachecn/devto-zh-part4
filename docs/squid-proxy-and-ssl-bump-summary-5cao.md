# Squid 代理和 SSL 碰撞，摘要

> 原文：<https://dev.to/suntong/squid-proxy-and-ssl-bump-summary-5cao>

我的 [squid 缓存服务器](http://www.squid-cache.org/)系列的事实收集博客的第一部分到此结束。再说一遍，他们中的大多数都是从网上收集的，并在这里重新发布，并归功于原来的网址/作者。

这个系列的目的是从地球的每个角落收集所有有价值的文章，这样我们就可以有一个 T2 的一站式位置来阅读它们，最重要的是，以一种逻辑顺序，这种顺序更适合那些没有这方面知识的人，便于他们了解细节。这里有一个启用 SSL 的 Squid 代理的总结，摘自 [alpinelinux wiki](https://wiki.alpinelinux.org/wiki/Setting_up_Explicit_Squid_Proxy#SSL_interception_or_SSL_bumping) :

> 官方的 squid 文档似乎更喜欢用术语 *SSL 拦截*来表示透明 squid 部署，用 *SSL 碰撞*来表示显式代理部署。尽管如此，两种环境都使用`/etc/squid/squid.conf`中的 [ssl_bump 配置指令](http://www.squid-cache.org/Doc/config/ssl_bump/)(以及其他一些指令)进行配置。在一般术语中， *SSL 拦截*通常用于描述这两种部署，这里也将使用这个术语。当然，我们在这里处理的是*显式转发代理配置*(在那篇 wiki 文章中)。

好了，目前为止所有的转发都是关于正常# linux #服务器下的`squid`。我也在其他平台上找到了一些文章，我只打算把它们包含在这个总结中，而不是再次转贴:

*   [为 **AlpineLinux** 设立鱿鱼代理](https://wiki.alpinelinux.org/wiki/Setting_up_Explicit_Squid_Proxy)，其拥有与`deb`和`rpm`完全不同的&包装系统。
*   [使用 Windows Squid 设置 HTTPS 检测](https://cloudbuilder.io/documentation/Windows-SQUID-SSLBUMP/)。当然，即使是 **Windows** 也可以享受到 Squid 代理和 SSL 碰撞的好处。

此外，所有转帖的一个共同主题是，如果你已经注意到了，当谈到[安装和配置](https://comtechies.com/free-proxy-server-setup-squid-proxy.html)时，细节被省略了。这是有充分理由的。

从下一篇文章开始，我们将开始一段关于 Squid 代理的不同旅程——[Squid 缓存服务器](http://www.squid-cache.org/)系列事实收集博客的*第二部分*。敬请期待...