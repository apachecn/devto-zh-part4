# 在 messenger 应用程序架构中使用 RabbitMQ

> 原文：<https://dev.to/smartym/how-to-use-rabbitmq-for-building-a-messenger-app-architecture-19ma>

移动信使最近变得非常流行。目前，有很多移动通讯工具，如 Viber、WhatsApp、Skype、Telegram，它们各有利弊。

在当今竞争激烈的市场中，应用程序必须具有完美的设计，易于扩展，并且能够在必要时支持繁重的负载。

更重要的是，它必须提供高安全级别，以便用户可以确信他们的个人数据在安全的人手中。为了确保安全，你可以集成[端到端加密](https://smartym.pro/blog/mobile-app-security-using-end2end-encryption-for-protecting-mobile-messengers/)，这已经被许多著名的信使使用。

构建这样的功能是一个真正的挑战，因为它不仅需要大量的开发和测试时间以及高水平的开发人员资格，还需要与可靠的消息传递平台集成。

在我们的例子中，最好的方法是使用开源消息代理 [RabbitMQ](https://github.com/rabbitmq) ,它支持不同的消息传递协议，并拥有大量的客户端库，允许将其与流行的移动平台(iOS、Android)集成。

这种解决方案让我们更加关注架构和业务逻辑的开发，并确保消息传递的稳定工作和易于扩展。

**RabbitMQ 能够轻松实现以下特性:**

*   群聊
*   消息传递检查
*   离线时接收来自用户的消息
*   在所有客户端应用程序上同时接收消息(iOS、Android)
*   防止发送不必要的消息
*   在与 RabbitMQ 断开连接的情况下，例如在互联网中断期间，优化消息传递

同时，软件工程过程总是有一些挑战。例如，在制作移动解决方案时，我们的团队必须使用 [AMQP 协议](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol)实现 RabbitMQ 与服务器端的通信以及与 Android 应用程序的通信。

一开始，我们计划使用 AMQP Objective-C 包装器为 iOS 创建 librabbitmq-c 库。但事实证明它有许多潜在的问题——这个问题通过用 [MQTT](https://github.com/jmesnil/MQTTKit) 替换 AMQP 协议得到了解决。应该注意的是，我们在 Android 上的客户端库没有这样的困难。

此外，我们还面临着正确的消息传递配置的挑战，这需要正确选择服务质量(QoS)。因此，我们创建了一个可靠的系统，该系统支持繁重的负载以及由于用户群和用户活动峰值的扩大而增加的负载。

在消息应用程序架构中使用 RabbitMQ 有助于在支持多用户对话和多个客户端打开的情况下轻松解决消息传递问题。

因此，RabbitMQ 消息代理是 messenger 开发和实现许多其他基于物联网的任务的有效解决方案。它完全解决了与应用程序的通信问题，这使得工程师可以专注于实现 app 业务逻辑。

此外，了解更多关于使用 [RabbitMQ 创建信使](https://smartym.pro/blog/using-rabbitmq-message-broker-for-messenger-development-some-clues-for-developers/)的信息，并在其[教程](https://www.rabbitmq.com/getstarted.html)中了解如何开始使用 RabbitMQ。