# vector——用 Rust 编写的高性能日志和度量路由器

> 原文：<https://dev.to/timber/vector-a-high-performance-logs-metrics-router-written-in-rust-1b75>

大家好，我们刚刚发布了 [**Vector**](https://github.com/timberio/vector) ，一个 Rust 内置的开源日志和度量收集器。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [廷伯里奥](https://github.com/timberio) / [矢](https://github.com/timberio/vector)

### 一种用于构建可观测管道的轻量级超快速路由器

<article class="markdown-body entry-content container-lg" itemprop="text">

**[组件](https://vector.dev/components/)[指南](https://vector.dev/guides/)[文档](https://vector.dev/docs/)[博客](https://vector.dev/blog/)[下载 v0.10.0](https://vector.dev/releases/latest/download/)**

* * *

[![Vector](img/643f911682c1b100df0c997963249f1c.png)](https://camo.githubusercontent.com/31fc55c082673f28461c6296f3b9194586ee7ce6/68747470733a2f2f766563746f722e6465762f696d672f726561646d655f6469616772616d2e737667)

## 什么是向量？

Vector 是一个轻量级、超快速、[开源的](https://github.com/timberio/vector)工具，用于构建可观测性管道。与 Logstash 和 friends 相比，Vector [将吞吐量提高了约 10 倍，同时显著减少了 CPU 和内存的使用](https://vector.dev/#performance)。

### 原则

*   **可靠性第一。** -内置[锈](https://www.rust-lang.org/)，Vector 的首要设计目标是可靠性。
*   **一个工具。所有数据。** -一个简单的工具获得你的[日志](https://vector.dev/docs/about/data-model/log/)、[度量](https://vector.dev/docs/about/data-model/metric/)，以及从 A 到 b 的轨迹(即将推出)
*   **单一责任。** - Vector 是一个*数据路由器*，它没有计划成为一个分布式处理框架。

### 谁应该使用 Vector？

*   你*应该*用 Vector 代替 Logstash，Fluent*，Telegraf，Beats，或者类似的工具。
*   你*应该*使用 Vector 作为[守护](https://vector.dev/docs/setup/deployment/strategies/#daemon)或者[边车](https://vector.dev/docs/setup/deployment/strategies/#sidecar)。
*   你*应该*使用 Vector 作为可观察性数据的 Kafka 消费者/生产者。
*   你*应该*在资源受限的环境(比如设备)中使用 Vector。
*   你*不应该*使用…

</article>

[View on GitHub](https://github.com/timberio/vector)

基本上，Vector 与您的应用程序一起收集有用的数据(日志和指标)，并将其转发给您选择的服务。例如:弹性搜索、S3、云观察日志等等。这将您的应用与任何特定的供应商分离，为您提供控制、数据所有权和灵活性。更不用说它的速度[要快得多](https://docs.vector.dev/performance):)

文档和网站提供了更深入的内容:

*   github:[https://github.com/timberio/vector](https://github.com/timberio/vector)
*   文件:[https://docs.vector.dev/](https://docs.vector.dev/)
*   网址: [https://vector.dev](https://vector.dev)

让我们知道你的想法！