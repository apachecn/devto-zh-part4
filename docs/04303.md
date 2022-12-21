# Java 客户端库-简介

> 原文：<https://dev.to/radixdlt/java-client-library-introduction-3i4b>

# Java 客户端库

使用 Java 客户端库构建 Radix 网络的开发人员指南

# 简介

用于与 [Radix](https://www.radixdlt.com/) 分布式账本交互的 Java 客户端库。

> 注意:这个库和网络本身目前处于测试开发阶段。请在 [GitHub 问题跟踪器](https://github.com/radixdlt/radixdlt-java/issues)中报告任何问题。

# 特性

-连接到 Betanet 测试网络
-测试网络的免费交易
-公钥身份创建
-令牌创建(ERC-777 风格)
-消息发送
-基于 RXJava 2 的
-利用 WebSockets 上的 JSON-RPC

# [度](#gradle)

包括以下梯度相关性:

```
repositories {
    maven { url 'https://jitpack.io' }
} 
```

```
dependencies {
    implementation 'com.radixdlt:radixdlt-java:dbfd5064e5'
} 
```

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询