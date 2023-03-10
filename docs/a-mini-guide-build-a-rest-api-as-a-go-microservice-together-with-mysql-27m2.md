# 迷你指南——与 MySQL 一起构建一个 REST API as a Go 微服务

> 原文：<https://dev.to/johanlejdung/a-mini-guide-build-a-rest-api-as-a-go-microservice-together-with-mysql-27m2>

我最近发现自己正在编写和部署许多 Go 微服务，既包括我在 Storytel 的日常工作，也包括我的副业项目[wiser](https://wiseer.io)。在这个小教程中，我将使用 MySQL 数据库创建一个简单的 REST-API。完整项目的代码将在文章末尾链接。

如果你还没有这样做，我推荐参加[巡回赛](https://tour.golang.org/)作为这篇文章的补充。

让我们开始吧！

* * *

# 设置 API

首先要做的事情之一是选择路由包。路由是连接 URL 和可执行函数的东西。对于*路由*，mux 包为我提供了很好的服务，但是也有其他的选择，在性能上没有真正的区别。如[http outer](https://github.com/julienschmidt/httprouter)和 [chi](https://github.com/go-chi/chi) 。我将在本指南中使用 mux。

为了简单起见，我们将创建一个输出消息的单一端点。