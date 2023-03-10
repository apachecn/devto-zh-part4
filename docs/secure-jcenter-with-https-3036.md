# 通过 HTTPS 保护 JCenter

> 原文：<https://dev.to/jfrog/secure-jcenter-with-https-3036>

[![](img/0dc21ec7214b02e65b0e46a41597ec8a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jc9Gbr26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.jfrog.com/wp-content/uploads/2019/09/06141558/JCenterprotocol-05.png)

你正在使用 Bintray JCenter 查找和共享公共 OSS JVM 语言包吗？如果是这样，我们有一些重要的消息给你，帮助你保持你的构建运行没有中断。

从 2020 年 1 月开始， *JCenter 将只为 HTTPS 提出的请求提供服务。*从那时起，所有使用 HTTP 的请求都将被拒绝，任何使用带有非安全 HTTP 协议的 JCenter URL 的构建都将失败。

TL；博士？尽快使用使用 HTTPS 的 URL 更新您的工具。这是您真正需要做的，以确保您的所有构建将继续顺利运行 JCenter。

为了方便 JCenter 用户的转换，这种变化分两个阶段进行:

*   2019 年 10 月 1 日:对 JCenter 的 HTTP 请求将自动被*重定向*到 HTTPS。
*   2020 年 1 月 13 日:对 JCenter 的 HTTP 请求将被拒绝。只有 HTTPS 会得到支持。

如果你想了解更多，这里有一些你可能会有的问题的答案:

#### JCenter？那是什么？

JCenter 是 JFrog Bintray 平台上的一个中央存储库，用于查找和共享 Maven 格式的流行 JVM 语言包，Maven、Gradle、Ivy、SBT 和其他人使用它来构建 Java、Groovy、Kotlin、Scala 和其他人。JCenter 是 OSS Maven 包最全面的来源，拥有超过 340，000 个公共包。

#### HTTPS 支持新吗？

HTTPS 对 JCenter 的支持并不新鲜。JCenter 从第 0 天起就支持安全 HTTPS，并且它一直是访问 Bintray 上任何存储库的推荐传输协议。对 HTTPS 的支持是 JFrog 对内容完整性承诺的众多组成部分之一。我们也允许使用 HTTP 协议进行访问，但是很快我们就不允许了。

#### 你为什么要逼我改变？

因为我们关心你的安全。HTTP 是一种不加密的传输协议，容易被窃听和篡改。对于 JCenter 用户来说特别重要的是，HTTP 传输支持可以在 JAR 工件上执行的[中间人攻击。作为一种使用双向加密传输的协议，](https://medium.com/bugbountywriteup/want-to-take-over-the-java-ecosystem-all-you-need-is-a-mitm-1fc329d898fb) [HTTPS 提供了基本的保护级别](https://en.wikipedia.org/wiki/HTTPS)。

#### 我已经在用 HTTPS 了。我要做什么呢？

如果你的网址已经在使用 HTTPS，你不需要做任何事情。祝贺你自己的良好安全实践，但是记住， [HTTPS 是不够的](https://blog.bintray.com/2014/08/04/feel-secure-with-ssl-think-again/)！

#### 我正在使用 HTTP，但是我的工具支持重定向。我该怎么办？

你还是应该尽快更新你的网址来使用 HTTPS。

如果你错过了任何一个，在十月的第一次改变之后，你仍然会没事的。10 月 1 日之后，在你的日志中寻找重定向消息，帮助你找到年底前你需要去 HTTPS 的所有地方。1 月 13 日之后，你没有更新的网址的构建将会失败。

#### 我正在使用 HTTP，我的工具不支持重定向。我该怎么办？

更新您的网址，立即使用 HTTPS。如果您不这样做，并且您的工具不支持重定向，那么您的构建将从 10 月份开始失败。

帖子[安全 JCenter 与 HTTPS](https://jfrog.com/blog/secure-jcenter-with-https/) 首先出现在 [JFrog](https://jfrog.com) 上。