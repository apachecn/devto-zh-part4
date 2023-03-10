# 使用 Symfony (API 上下文)增强您的功能测试

> 原文：<https://dev.to/juyn/enhanced-your-functional-tests-with-symfony-api-context-nfg>

我和我的团队有很多微服务要开发和维护，而且，由于这种架构成功的关键是性能，这是我们在编码时主要关心的问题。

不幸的是，除了 Profiler 之外，Symfony 没有提供一种简单的方法来监控性能指标。

TL；博士:

[![You shall not pass !](img/7c1e34c3d2ca92a76b3975d35e8c553c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j44qCh95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/RwD8eyx.gif)

我们希望能够对我们的表现进行自动检查:

*   数据库查询次数
*   执行时间(不仅仅是响应时间，它包含了太多的变量)
*   使用的内存

## 如何做到快？

这不是本文的主题，但我将在 Symfony 环境中快速提醒一下快速 API 的一些要求:

*   尽可能减少数据库查询
*   不要注入太多服务
*   使用尽可能少的内存
*   禁用/删除所有未使用的额外 Symfony 软件包
*   如果可能的话，不要使用监听器
*   ...

## 但是，你是怎么检查的呢？

Symfony 提供了一个非常方便的工具来检查前面的要点。
它允许您:

*   知道数据库查询的次数
*   了解你的应用使用了多少内存
*   有关于表演的信息

很神奇，对吧？

当我编码或做代码审查时，这是我检查的第一件事。当您有一个获取表中所有行的途径时，很容易通过一个一对一的关系获得大量的查询，而这不应该用于生产！

## 功能测试呢？

嗯，你的代码在你的机器上是可以的，你有正确的查询次数，你知道你的路线不会消耗太多的内存，而且它足够快。

但我是一个偏执的开发者，我不信任代码，甚至是我的代码。这就是我写单元测试、功能测试和集成测试的原因。这些测试都不允许我断言我的路由具有少于 5 个数据库查询，并且执行时间(如果与响应时间不同的话)足够快。

我看了看，没有找到任何简单的方法。我想做，但是太贵了，对我来说有点吃不消。但是，嘿，我是个开发者，所以让我们自己来做吧，那会很有趣的！

首先，我想在哪里访问这些数据？

### 在单元测试期间？

不，我不想在这些测试中做数据库调用，而且，我不喜欢启动内核

### 集成测试期间？

这可能会成功，但目前我们在提交前不会经过 CI，所以只有在投入生产前才会触发 CI

### 功能测试期间？

是啊！那太好了。

我们使用 Postman(及其 CLI 工具“newman”)来实现这一点。我喜欢这个工具，因为它提供了一种测试你正在进行工作的方法，当你完成时，你只需用 Javascript 编写测试。

我们习惯于在提交之前，在代码审查期间(这是它的一部分)，当然，在任何生产推进之前运行邮差测试。

## 访问数据

好的，我知道当我想访问这些指标时，我仍然需要想办法。Postman 无法访问分析器，因为它需要二次 HTTP 调用，并对 HTTP 响应进行爬网。所以我们必须想出另一种方法。

为什么不在响应头中加入这些指标呢？

*   邮递员可以阅读它们并对其进行一些测试
*   我们可以轻松地访问它们，甚至从 Chrome 开发工具
*   挺性感的，承认吧！

我们决定我们想要:

*   路由器调用的控制器和方法的名称
*   有数量的数据库查询
*   使用的内存
*   执行时间

```
Execution-Controller → App\Controller\MyController -> myMethod
Execution-Doctrine-Queries → 8
Execution-Max-Memory → 4194304 bytes
Execution-Time → 206 ms 
```

## 它是如何工作的？

嗯，真的很简单。

*   当使用`onKernelController`事件触发任何控制器时，秒表启动，然后在 KernelResponse 上停止。这个秒表给了我们执行时间和最大内存
*   我们使用 DoctrineDataCollector(就像探查器一样)来统计查询的数量

添加到页眉，就这样！

用 Postman 或者你正在使用的任何工具写一些测试，你可以断言你的路线使用的内存少于 2mb，需要的数据库查询少于 5 次！

[![You shall not pass !](img/89e2d49f5d041a777b77d3cf7e19cac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_RM_ygI0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/3o7btNa0RUYa5E7iiQ/giphy.gif)

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【实验室表面】](https://github.com/laboutiqueofficielle) / [【单眼捆绑】](https://github.com/laboutiqueofficielle/MonitHeaderBundle)

### 在响应的标头中添加监控数据

<article class="markdown-body entry-content p-5" itemprop="text">

# MonitHeaderBundle

这个 Symfony 3 包在响应的头上增加了一些额外的信息

## 装置

简单的把这一行加到 AppKernel.php

```
 public function registerBundles()
 {
 $bundles = array_merge($this->servicesBundles($this->getEnvironment(), $this), [
 [...]
 new Lbo\Bundle\MonitHeaderBundle\MonitHeaderBundle(),
```

## 使用

然后，检查你的邮件头，例如，邮递员

```
Execution-Controller →App\Controller\MyController -> myMethod
Execution-Doctrine-Queries →8
Execution-Max-Memory →4194304 bytes
Execution-Time →206 ms 
```

</article>

[View on GitHub](https://github.com/laboutiqueofficielle/MonitHeaderBundle)