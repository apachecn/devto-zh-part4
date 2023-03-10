# Salesforce 的 5 种地鼠开源方法

> 原文：<https://dev.to/salesforceeng/5-ways-gophers-get-it-done-with-open-source-at-salesforce-4heg>

本文由 Josh Simmons 撰写，最初发表于 engineering.salesforce.com。

Go 编程语言，或 Golang，是一种小巧、干净、快速、高效且固执己见的编程语言。作为一种高性能语言，它适用于运营和基础架构中的应用程序…这恰好是您在 Salesforce 中可以找到大量它的地方！在 Heroku 和 Einstein Analytics 的背后也有很多东西可以发现。

这里是我们使用(并参与)的 5 个用 Go 编写的开源项目:

1.  [Kubernetes](https://kubernetes.io/) 和 [Knative](https://knative.dev/) 。好的，我们实际上在谈论 6 个项目，但是这两个项目是密切相关的！我们使用 Kubernetes (K8s)进行容器编排，并使用 Knative 来抽象出使用 K8s 的许多复杂性。
2.  [码头工人](https://github.com/docker)。集装箱，集装箱，集装箱！我们当然用 Docker。根据我们谈论的 Salesforce 的哪一部分，我们要么正在转向以微服务为主的架构，要么服务一直是这样(对于 always 的某些价值)。无论哪种方式，容器都非常适合这种架构。
3.  [公开人口普查](https://opencensus.io/)。我们管理严格，要遵守服务级别协议(SLA ),因此了解我们的服务执行情况非常重要。OpenCensus 是一组用于收集应用程序指标和分布式跟踪的轻量级库，它让我们知道什么时候事情没有按预期进行。
4.  [小数](https://github.com/ericlagergren/decimal)。这个库实现了任意精度的十进制浮点数。简单地说，这非常有用。
5.  [咆哮位图](https://roaringbitmap.org/)。就存储和计算而言，处理图像的成本可能很高。咆哮位图是一个库，你可以在我们的很多依赖图中找到。

想要更多吗？在 GitHub 上查看 Salesforce 在 Go 中构建的[开源项目。](https://github.com/salesforce?utf8=%E2%9C%93&q=&type=&language=go)