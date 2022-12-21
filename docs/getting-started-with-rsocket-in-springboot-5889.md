# 春靴火箭入门

> 原文：<https://dev.to/petros0/getting-started-with-rsocket-in-springboot-5889>

# 简介

RSocket 是一种二进制协议，用于 TCP、WebSockets 和 Aeron 等字节流传输。

它通过在单个连接上传递异步消息来实现以下对称交互模型:

*   请求/响应(1 个流)
*   请求/流(有限的多流)
*   一劳永逸(无响应)
*   事件订阅(无限多流)

它支持会话恢复，允许跨不同的传输连接恢复长期流。当网络连接频繁断开、切换和重新连接时，这对于 mobile⬄server 通信尤其有用。

在我们的教程中，我们将使用 Java 编程语言实现 RSocket。

# 为什么要跳崖

虽然我可以用一个简单的 Java 应用程序简单地实现 RSocket，但我选择了 Springboot，因为它是 JVM 语言生态系统上的一个大项目。Springboot 还没有 RSocket 的稳定版本，但这不应该阻止我们对它进行实验。

# 结构

我们的项目将由两个子项目组成。发出请求的**消费者**和向**消费者**提供数据的**生产者**。

# 入门

如果你想轻松地开始一个 Springboot 项目，我建议总是使用 [Spring Initializr](https://start.spring.io/) 。

## 配置梯度

首先，我们必须配置我们的 build.gradle 并包含 Springboot 的 rsocket-starter-dependency: