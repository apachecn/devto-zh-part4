# 什么是 API 网关？

> 原文：<https://dev.to/educostadev/what-is-api-gateway-4jd>

API gateway 就像一堵墙，前面有一扇大门和守卫。每个到达这个门的人都有一个问题要问门另一边的人。他们知道知道答案的人的名字，但不知道答案在哪里。
于是门卫告知:

> 说出你的问题和你要找的人的名字，我会给你送过去。有时这个名字是一个昵称，我需要问不止一个人才能得到你的问题的答案。但是你不需要担心。”

尽管这个守卫负责一些安全方面的工作，但他们并不像在防火墙上工作的守卫那样专业。这个小比喻让您对 API 网关是什么以及如何工作有了一个大致的了解。这个故事的技术方面是，API gateway 是一个接口层，它位于 API 的前面，在将请求重定向到内部服务之前处理所有传入的请求。这种行为被称为反向代理。它们的功能如下:

*   将传入流量路由到适当的服务。
*   限制各种 API 的公开
*   过滤来自网络、手机等不同规则的传入流量
*   实施安全机制来验证和记录服务使用情况

使用 API 网关有以下好处:

*   **关注点分离:**允许服务消费者和微服务提供者之间的分离。
*   **编排:**允许将多个服务调用编排成一个 API 调用。
*   **Monitor** :提供监控 API 调用的能力

除了这些好处之外，API gateway 还引入了更多的故障点，并增加了需要管理的项目。利弊需要仔细分析。您可以使用[云原生](https://educosta.dev/blog/what-is-cloud-native/)技术自行实现 [API 网关模式](https://microservices.io/patterns/apigateway.html) / [API 组合模式](https://microservices.io/patterns/data/api-composition.html)，或者使用云提供商的 [API 网关。另请参见](https://aws.amazon.com/pt/api-gateway/) [API 网关技术](https://landscape.cncf.io)，您可以在您的项目中应用这些技术，这些技术由 [Cloud Native Foundation](https://www.cncf.io/) 提供支持。

这篇文章最初发表在我的博客上。如果你有任何疑问或问题，欢迎在这里发表评论或通过 Twitter [@educostadev](http://twitter.com/educostadev) 联系我。