# NLog vs log4net

> 原文：<https://dev.to/thomasardal/nlog-vs-log4net-21mb>

在选择日志框架时， [NLog](https://nlog-project.org/) 和 [log4net](https://logging.apache.org/log4net/) 是两个非常受欢迎的选择。NET 应用程序。在这篇文章中，你将会了解到两种框架及其区别。对于那些不知道其中一个或两个框架的人来说，让我们先介绍一下每个框架。

## log4net

[![log4net](img/a7485322c7f036339b4de2277e67d1ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--81JYHZuo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.elmah.io/conteimg/2019/04/log4net.png)

log4net 可能是这个领域最古老的日志框架，从那时起就存在了。NET 被引入。log4net 最初是由 Neoworks Limited 在 2001 年开发的一个内部 Apache log4j 端口。该项目很快转移到 Sourceforge(2001 年的 GitHub ),并在 Apache 许可下发布。从那时起，log4net 就成了。NET world 为应用程序添加日志记录。

log4net 使用了 appenders 的概念，其中日志消息可以被路由到不同的数据存储。这些年来，已经实现了很多附加器，比如登录到文件系统、SQL Server、HTTP 端点，甚至 NoSQL 数据库，但是作为非结构化文本消息，log4net 和 NoSQL 并不完全齐头并进。

## [战](#nlog)

[![NLog](img/51ba4b24b77eccbbe52450d65ddf2f49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oOOIDe_P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.elmah.io/conteimg/2019/04/nlog-banner-1.png)

虽然 log4net 很快成为默认选择，但替代方案开始出现。可能 log4net 统治地位的第一个真正的竞争对手是 NLog。NLog 最初是由 Jarek Kowalski 开发的，有近 100 人提出请求，NLog 是一个很好的选择。从 2006 年开始，log4net 几乎停滞不前，而 NLog 却一直在发展。虽然 Jarek 似乎在谷歌开始时就停止了工作，但社区似乎在逐步发展，新的版本仍在不断涌现。

与 log4net 一样，NLog 包含多个日志目标，并且能够将消息记录到各种数据存储中。

## 配置

所有的日志框架都需要某种配置，以便框架知道记录什么以及记录到哪里。

### log4net

log4net 是通过你的 app.config 文件或者专用的 log4net.config:
配置的

```
<log4net>
  <appender name="FileAppender" type="log4net.Appender.FileAppender">
    <file value="log-file.txt" />
    <appendToFile value="true" />
    <layout type="log4net.Layout.PatternLayout">
      <conversionPattern value="%date %level %message%newline" />
    </layout>
  </appender>

  <root>
    <level value="ALL" />
    <appender-ref ref="FileAppender" />
  </root>
</log4net> 
```

### [战](#nlog)

NLog 可以通过 XML 和 C#进行配置。大多数人使用 XML，因为它从一开始就存在，而且基于 C#的 API 不如像 Serilog 这样的新框架好。

```
<nlog>
  <targets>
    <target xsi:type="File" name="file" fileName="log-file.log"
            layout="${longdate} ${level} ${message}" />
  </targets>

  <rules>
    <logger name="*" minlevel="Trace" writeTo="file" />
  </rules>
</nlog> 
```

## 记录消息

在记录来自应用程序的消息时，log4net 和 NLog 的工作方式非常相似。

### log4net

日志消息是使用 log4net API 编写的:

```
var log = LogManager.GetLogger(typeof(Bar));
log.Debug("Hello World"); 
```

通过使用一系列不同的上下文选项，可以将额外的日志信息附加到日志消息中:

```
log4net.GlobalContext.Properties["Application"] = "MyCoolApp";
using(log4net.ThreadContext.Stacks["NDC"].Push("context"))
{
    log.Debug("Hello World");
} 
```

虽然您可以使用 log4net 将信息直接嵌入到日志消息中，但是不支持语义日志。

### [战](#nlog)

NLog 提供了一个类似于 log4net 的 API，用于将消息记录到一组已配置的目标:

```
var logger = NLog.LogManager.GetCurrentClassLogger();
logger.Debug("Hello World"); 
```

也可以附加附加属性:

```
var msg = new LogEventInfo(LogLevel.Debug, "", "Hello World");
msg.Properties.Add("Application", "MyCoolApp");
logger.Debug(msg); 
```

NLog 的最新版本支持语义日志记录。要为日志消息添加语义，请使用花括号语法:

```
logger.Info("Order {orderid} created for {user}", 42, "Kenny"); 
```

## 结论

这篇文章的目的不是选出一个获胜者。这两种日志记录框架各有利弊。

### log4net

**优势**

*   被广泛采用
*   网上有很多文档和资源

**缺点**

*   不使用新的可能性。网
*   无结构化日志记录

### [战](#nlog)

**优势**

*   结构化日志记录
*   更现代的 API
*   更好地支持 NoSQL 数据库

**缺点**

*   没有 log4net 那么多资源

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

这篇文章最初出现在 elmah.io 的博客上，时间是[https://blog.elmah.io/nlog-vs-log4net/](https://blog.elmah.io/nlog-vs-log4net/)