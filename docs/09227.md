# 部署到生产中:红灯的需要

> 原文：<https://dev.to/gdcohen/deploying-into-production-the-need-for-a-red-light-1nmd>

[![Zebrium is a red light that stops badly behaved builds from impacting production](img/0b12d3d802a5b2c8884f7de38c85bcad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_z88_T10--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.zebrium.com/hs-fs/hubfs/Stop%2520graphic2.png%3Fwidth%3D2027%26name%3DStop%2520graphic2.png) 
**凝视深渊**

随着规模和复杂性的增长，部署前测试的回报越来越少。一个测试作者无法想象会产生灾难的巧合的组合爆炸。我们必须承认，部署到生产环境中是唯一确定的测试。

**拥抱未知**

现在怎么办？虚无主义的挽歌是无益的。如果我们不能避免它，那么我们必须拥抱它...或者至少，做好准备。如果我们愿意假设未知的问题可能会在生产中出现，我们该如何准备呢？

**限制曝光**

根据我们的堆栈，我们可能能够使用特性标志进行部署。特性标志让我们将生产用户组织到 A/B/X 中，并为我们提供了一种非常简单的方法来尝试新部署的代码并关闭它。或者，我们可以通过节点或 pod 进入 A/B/X，并拥有一个自动回滚过程。

**观察破损:一个“红灯”**

我们仍然需要一种方法来判断 A 坏了:某种意义上的“红灯”。仪表在这里起了作用，但是我们希望我们的红灯尽可能的通用。正如测试人员不能测试所有的未知，开发人员也不能测试所有的未知。人们很容易认为，随着新故障模式的发现，我们可以对它们进行检测，这就足够了。

不幸的是，随着我们代码库的规模越来越大，未知的表面积也越来越大。我们永远也不会仅仅依靠手工努力和反应式的改进。

利用你所拥有的

我们想利用所有可用的线索和提示来点亮红灯。让我们看看一些最近的 RCA 可能提供了什么方法来考虑检测未装备的问题。

**检测停止发生的事件**

Stripe 最近由于数据库错误以及配置更改([https://stripe.com/rcas/2019-07-10](https://stripe.com/rcas/2019-07-10))而出现了一次中断。经过深思熟虑的 RCA 显示，有一段时间没有发现问题，因为 DB 节点响应为启动，但已经停止发送它们的复制度量。

在这里，通过将这些更新视为一系列停止发生的大致周期性事件，人们可能会更早地注意到这个问题。这突出了能够辨别出一些有规律的事情停止发生的重要性。

**不维护正则表达式(并使用度量标准)**

Cloudflare 最近因为一个糟糕的正则表达式([https://blog.cloudflare.com/cloudflare-outage](https://blog.cloudflare.com/cloudflare-outage))而发生了一次宕机。他们正在测试一个基于正则表达式的规则来扫描 JavaScript，查看该规则的误报率。正则表达式导致了过多的回溯(我猜的)并钉住了很多 CPU。

这个例子突出了创建、监管、管理和维护正则表达式的困难(同样的困难使许多人无法充分利用他们的日志和事件流)。它还表明，监控指标显然很重要。

**使用日志**

Honeycomb 最近因缺少二进制文件而停机([https://www . honeycomb . io/blog/incident-review-you-cant-deploy-binaries-that-dont-exist/](https://www.honeycomb.io/blog/incident-review-you-cant-deploy-binaries-that-dont-exist/))。长话短说，buildevents 工具退化了，所以尽管有构建错误，也没有以非零代码退出。

在这里，我认为注意到构建日志中出现的新的/异常罕见的构建事件本身可能会亮起红灯，也许是在部署之前。但是如果没有自动构造事件和构建事件类型字典(就像 Zebrium 提供的)，就不可能可靠地做到这一点。

**我们的路**

[Zebrium 的](//www.zebrium.com)使命是成为生产部署的最佳“红灯”。我们承认并接受工具的重要性，但我们坚持认为，要完成这项任务，需要对记录的事件和附带指标进行自动结构化的理解。很明显，需要多种数据源，以及对这些数据中的模式进行更高级别的学习和机器解释。

如果你有兴趣尝试我们的技术，你可以点击这里免费开始。

经作者许可后发布:Larry Lancaster，Zebrium 首席技术官。