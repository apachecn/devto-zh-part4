# 检测 Rails 应用程序

> 原文：<https://dev.to/punnie/instrumenting-a-rails-application-5a1k>

2019 年 7 月 18 日，我在里斯本 Le Wagon】做了一个关于如何使用 Prometheus 检测 Rails 应用的演讲。

自从我的上一次演讲以来，我越来越喜欢简单的现场编码演示，以传达实用的知识以及事情往往比看起来容易的意识。这一次，我从一个已经存在的应用程序中收集了一些系统和定制的指标，放入一个现有的 Prometheus 服务器中。

[带有最终设置的应用程序在这里](https://github.com/punnie/rails-prom)可用，作为 docker compose 捆绑包，可以轻松运行以检查结果并做进一步的实验。自述文件中的说明应该足够清楚，可以为您提供一个工作示例，但是如果不是这样，您可以随时在存储库中打开一个问题。

黑客快乐！