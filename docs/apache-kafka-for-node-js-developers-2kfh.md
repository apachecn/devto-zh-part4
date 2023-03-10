# 面向 Node.js 开发人员的 Apache Kafka

> 原文：<https://dev.to/ganeshmani/apache-kafka-for-node-js-developers-2kfh>

在本文中，我们将看到如何用 node.js 实现 apache kafka

首先，kafka 是一个实现发布/订阅模式的流媒体平台。

此外，如果你想了解卡夫卡是如何工作的，你可以阅读这个系列文章

[阿帕奇卡夫卡系列——第一部](https://cloudnweb.dev/2019/04/how-apache-kafka-works-kafka-getting-started-part-1/)

[卡夫卡系列的建筑——第二部](https://cloudnweb.dev/2019/04/kafka-getting-started-kafka-series-part-2/)

[卡夫卡系列的核心概念——第三部](https://cloudnweb.dev/2019/05/heres-what-makes-apache-kafka-so-fast-kafka-series-part-3/)

### 卡夫卡和 Node.js

另一方面，你可以想到为什么作为 node.js 开发者，我需要了解 Kafka。这是有原因和必要的。

主要是我们处于 **[微服务](https://cloudnweb.dev/2019/04/10-resources-to-make-you-better-at-microservices-in-2019/)** 的发展时代。大多数公司更喜欢使用微服务来构建他们的应用程序。

如果您是 Node.js 开发人员，您可能也处于需要构建一个 node . js 的情况。如果你对微服务做一些研究，你会发现很多参考 Kafka 和微服务的资源。

原因是，微服务的许多用例是 **[事件源](https://www.confluent.io/blog/event-sourcing-cqrs-stream-processing-apache-kafka-whats-connection/)**

### 为事件采购实施 kafka

这是一个流行的用例，其中一个微服务通过 Kafka 与另一个微服务对话。

[![](img/9d0c9b39d4f06aa5c7f5ada439a20586.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IqRrqd4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/08/event_sourcing.png)

主要地，我们将看到如何使用 Kafka 和 Node.js 实现上面的用例

*   用户服务——该服务处理所有用户业务逻辑，如用户创建、更新和删除。
*   post Service——该服务为用户处理帖子的所有操作。

当我们删除一个用户时，我们需要删除与该用户相关的所有帖子。

当客户端进行 API 调用时，网关将请求发送给相应的微服务。

如果特定的服务与其他服务有一些数据依赖，它将使用 Kafka( **Event Sourcing** )将数据发送给其他服务。

其他服务订阅 Kafka 主题并获取数据来进行业务运营。

### 演示

[![](img/15de4c90a51c0003c5c98a646da9db3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xnk4oKDr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/08/Screenshot-2019-08-11-at-4.08.29-PM-1024x530.png)

完整的源代码可以在[这里](https://github.com/ganeshmani/node-eventsourcing)找到

### 摘要

Apache Kafka 可用于许多情况，如事件源、数据流和消息队列。作为后端开发人员，了解不同的可能性来决定解决问题的最佳方案总是好的。

参考资料:

[event sourcing-microservice . io](https://microservices.io/patterns/data/event-sourcing.html)

[活动采购，CQRS -阿帕奇卡夫卡](https://www.confluent.io/blog/event-sourcing-cqrs-stream-processing-apache-kafka-whats-connection/)