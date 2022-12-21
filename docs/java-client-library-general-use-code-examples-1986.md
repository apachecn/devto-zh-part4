# Java 客户端库-通用代码示例

> 原文：<https://dev.to/radixdlt/java-client-library-general-use-code-examples-1986>

# 简介

让我们回顾一些关于如何处理一般任务的代码示例:

- [初始化一个宇宙](https://docs.radixdlt.com/radixdlt-java/examples/code-examples/general-use#initializing-a-universe)
- [初始化应用 API](https://docs.radixdlt.com/radixdlt-java/examples/code-examples/general-use#initializing-the-dapp-api)

> 提示:如果您不熟悉我们的 Java 库，我们建议您从我们的入门指南开始。

# 初始化宇宙

一个宇宙是一个基数分布式分类帐的实例，它是由一个起源原子和一组形成一个网络的不允许的动态节点定义的。

## 可用网络

| 网络 | 描述 |
| --- | --- |
| LOCALHOST_SINGLENODE | 本地承载的单节点连接。 |
| 单节点 Betanet 连接。 | 单节点 Betanet 连接。 |

可以使用预定义的配置来引导至 LOCALHOST_SINGLENODE 网络:

> BootstrapConfig config = Bootstrap.LOCALHOST_SINGLENODE;

# 初始化应用程序 API

Radix 应用程序 API 是一个客户端 API，它公开了高级抽象以使 DApp 创建更容易。

要初始化 API，请执行以下操作:

> radixaapplicationapi = radixaapplicationapi . create(Bootstrap。贝塔网，身份)；

要持续同步和提取您帐户上的网络分类帐:

> 一次性 d = API . pull()；

要停止同步:

> d .处置()；

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询