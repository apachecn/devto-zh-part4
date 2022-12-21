# 设计连续构建系统:用 Sanic 处理 Webhooks

> 原文：<https://dev.to/tryexceptpass/designing-continuous-build-systems-handling-webhooks-with-sanic-4120>

在介绍了如何[设计构建管道](https://dev.to/tryexceptpass/designing-continuous-build-systems-6d9)和[在连续构建系列中定义构建指令](https://dev.to/tryexceptpass/designing-continuous-build-systems-parsing-the-specification-2b83)之后，是时候看看如何从代码库中处理事件了。

随着互联网标准的发展，HTTP 协议变得越来越流行。它更容易路由，更容易实施，甚至更可靠。这种无处不在的特性使得在公共互联网上运行的应用程序更容易相互通信。结果，webhooks 的想法变成了一种“基于 http 的事件”机制。

有了 GitHub 作为存储库管理平台，我们就有了使用他们的 webhook 系统在互联网上交流用户操作并进入我们的构建管道的优势。

[读下去...](https://tryexceptpass.org/article/continuous-builds-webhooks/)