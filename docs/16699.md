# 网上研讨会公告。与邦迪和 WAMP 一起实现多语言微服务架构。

> 原文：<https://dev.to/erlangsolutions/webinar-announcement-implementing-a-polyglot-microservices-architecture-with-bondy-and-wamp-4d10>

Bondy，是一个用 Erlang 编写的分布式微服务和物联网应用的开源网络平台。它实现了开放 Web 应用程序消息传递协议(WAMP)，这是一个开放的标准 TCP 和 WebSocket 协议，统一了两种应用程序消息传递模式:RPC 和 PubSub。

在我们的下一次网络研讨会中，Alejandro Ramallo 将解释 WAMP 是如何工作的，以及它是如何在 Bondy 中实施的。他将展示 Bondy 如何用于将已生产两年的物联网平台的组件与成千上万的联网车辆和家庭设备互联。

网络研讨会将于 7 月 10 日英国夏令时 17:30 举行。录音将在接下来的几周内发送给所有注册人。在[https://www2.erlang-solutions.com/webinarardev](https://www2.erlang-solutions.com/webinarardev)注册

### 3 大要点

-Bondy 是 WAMP 路由器 Erlang 中的一个开源实现。它支持超过 12 种编程语言，并支持多种数据编码格式。
-Bondy 使用 Partisan 进行集群和节点间通信，并使用最终一致性模型在整个集群中复制其状态，即使在网络分区或集群节点崩溃时也保持可用。
-Bondy 超越了 WAMP 实现，嵌入了基于角色的访问控制系统和 REST API 网关，并部分实现了 OAuth2 流，将 REST 操作转换为 WAMP RPC 和 PubSub 消息，反之亦然。