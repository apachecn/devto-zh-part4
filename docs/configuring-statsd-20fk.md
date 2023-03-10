# 配置 StatsD

> 原文：<https://dev.to/victorcosta/configuring-statsd-20fk>

## 目标

了解如何使用 statsD 生成指标。

* * *

## 什么是 StatsD

[Statsd 对其 git 回购协议的](https://github.com/statsd/statsd)描述非常清楚，我们就用它吧:

> 在 Node.js 平台上运行的网络守护程序，它侦听通过 UDP 或 TCP 发送的统计信息，如计数器和计时器，并将聚合发送到一个或多个可插拔后端服务(如 Graphite)。

* * *

## 何时使用，何时不使用 StatsD

您不应该期望 statsD 生成的指标是 100%精确的，也不应该从中提取业务指标(例如:有多少客户购买了产品，或者有多少用户注册了您的简讯)，因为数据是通过`UDP`发送的(更多信息请见下文)。使用 statsD 指标来识别服务中的异常情况(例如:过多的错误状态；方法需要很长时间才能完成；等等)。

> 通过用户数据报协议(UDP)发送的包不能保证送达。更多关于这个[维基百科页面](https://en.wikipedia.org/wiki/User_Datagram_Protocol)。

* * *

## 我们当前的场景和目标与 statsd

假设我们有一个应用程序，没有太多用户访问它，平均每分钟有 10 次登录操作。如果一分钟内有超过 30 次不成功的登录操作，我们将有一个 CloudWatch 警报来通知我们，这样我们就可以了解发生了什么。应用程序将使用 CloudWatch 代理向 CloudWatch 发出带有 statsD 的事件。

* * *

## 本地运行 StatsD

首先确保你已经安装并运行 docker。然后运行以下命令:

```
docker run --rm\
 --name graphite\
 -p 80:80\
 -p 81:81\
 -p 2003-2004:2003-2004\
 -p 2023-2024:2023-2024\
 -p 8125:8125/udp\
 -p 8126:8126\
 hopsoft/graphite-statsd 
```

您已经可以导航到 [http://localhost:81](http://localhost:81) 并看到一个来自 [Graphite](https://graphiteapp.org) 的空图。

## 我们的应用

[我的这个演示应用程序](https://github.com/augustovictor/loud-watch-agent-statsd-collectd)(关于如何运行的说明可以在资源库的`README.MD`中找到。

下面是重要的代码部分。为简单起见，我将所有内容保存在同一个文件中: