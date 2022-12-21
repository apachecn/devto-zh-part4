# 你对编写第三方服务或集成的测试有什么看法？

> 原文：<https://dev.to/david_ojeda/what-s-your-take-on-writing-tests-for-third-party-services-or-integrations-1bco>

假设您有一个使用 Stripe 的应用程序。

Stripe 为您提供了一个开发环境——自己的 API 密钥、自己的仪表板、自己的数据，所有这些都与您的 Stripe 的实时数据相分离。

你会编写与这个开发环境交互的测试吗？或者你会模仿响应或类似的东西吗？

我工作的一个应用程序有许多与测试/开发/沙盒环境通信的集成测试，它们有时会因为服务错误而失败，而不是我们自己的错误。现在我们在讨论我们是否应该嘲笑这些回答。