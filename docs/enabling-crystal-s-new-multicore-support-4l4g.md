# 启用 Crystal 的新多核支持

> 原文：<https://dev.to/jgaskins/enabling-crystal-s-new-multicore-support-4l4g>

Crystal 是一种静态类型的、面向对象的编程语言，它的语法深受 Ruby 的启发，概念也受到很多语言的启发，包括 Ruby、Go、Rust、Swift 等等。

## 并发

Crystal 的一个优点是它的并发模型。不是管理线程，而是使用`spawn`方法:
旋转[纤程](https://crystal-lang.org/reference/guides/concurrency.html)

```
spawn do
  puts "This runs when the program’s main fiber is sleeping or waiting on I/O"
end 
```

Enter fullscreen mode Exit fullscreen mode

这是强大的。线程需要大量的手工管理——如果你创建了它们，你必须自己去收集它们——但是纤程是由垃圾收集器来管理的，所以如果你愿意的话，你可以一发不可收拾。

纤维的重量也轻得多。你可以用它们来剥离后台工作，这样你就可以同时发送 50 个网络请求*而不是一次一个*。你可以用线程做到这一点，但是`pthread_create`和`pthread_join`(通常支持线程的`libc`函数)是*昂贵的*系统调用，所以你真的不应该这样做。

## 并发！=并行度

纤程的缺点是所有水晶纤程都在主线程上执行。这允许您执行大量 I/O 相关的工作(HTTP 请求、数据库查询、从磁盘读取文件等)，但是 CPU 相关的工作(序列化/反序列化 JSON、为报告处理数据等)仅限于单个 CPU 内核。Ruby、Python 和 JavaScript 都有同样的限制。

然而昨天水晶团队[发布了 0.31.0](https://crystal-lang.org/2019/09/23/crystal-0.31.0-released.html) 版本，自带多核支持！这使得我们不仅可以像以前一样进行*并发*工作，还可以进行真正的*并行*工作——我们可以将我们的工作负载分散到我们机器拥有的尽可能多的 CPU 内核上。例如，这是一个使 32 芯数字海洋液滴饱和的单晶过程:

[![32-core server running  raw `htop` endraw  with all cores at 100%](img/efc68a77ca49e4695415c783a37c333b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kLRs9EEd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://aws1.discourse-cdn.com/standard10/uploads/crystal_lang/optimized/1X/baec43d0b09fd5c6d630af0e659230ae53a95c9d_2_1380x386.png)

这个应用程序的代码很简单:

```
32.times { spawn { loop {} } }
sleep # Keep the main thread from exiting 
```

Enter fullscreen mode Exit fullscreen mode

## 启用并行

Crystal 的多核支持仍在预览中，所以默认是关闭的。您可以使用`-D preview_mt`编译器标志:
来启用它

```
crystal build -Dpreview_mt my_app.cr 
```

Enter fullscreen mode Exit fullscreen mode

或者您可以直接运行它而不创建构建工件:

```
crystal run -Dpreview_mt my_app.cr 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，最好的部分是并行性的级别(可以并行执行的纤程的数量)是在应用程序引导期间，在您自己的应用程序代码实际运行之前，通过设置`CRYSTAL_WORKERS`环境变量来设置的。这意味着你可以调整你的应用程序将尝试使用的 CPU 资源数量——你可以在应用程序*启动*时而不是在构建过程中这样做。因此，如果您在同一台 16 核服务器上运行两个不同的 Crystal 应用程序，它们不会都试图使用所有 16 核。您可以为其中一个分配 4 个内核，为另一个分配 12 个内核:

```
CRYSTAL_WORKERS=4 first_app
CRYSTAL_WORKERS=12 second_app 
```

Enter fullscreen mode Exit fullscreen mode

## 它是如何工作的？

并行性是在应用程序启动时配置的，也就是说，在开始执行应用程序代码之前，连接所有需要的部分。这种并行性是通过静态线程池实现的。当你的应用程序运行时，没有线程被启动或停止，所有的线程都是在你的第一行应用程序代码执行时创建的！

这个池中的每个线程都有自己的纤程调度器。它基本上是在单线程模式下做什么，只是跨更多的线程。这意味着纤程目前只在最初分配给它的线程中运行。不过，这不一定是创建它的线程。例如，如果线程 1 调用`spawn`，那么新的纤程可能被分配给线程 4，并且它将在线程 4 上生存，直到它死亡。

Crystal 团队已经讨论了实现“纤程窃取”(基本上，如果线程 1 无事可做，而线程 2 有很多纤程，线程 1 可能会将这些纤程中的一部分分散到工作中)，但我感觉这还很遥远。

## 这个能做什么？

任何可以从并发工作中获益的东西都将被自动并行化。例如，web 应用程序经常使用来自 Crystal standard 库的`HTTP::Server`——要么直接使用，要么通过诸如 [Amber](https://amberframework.org) 或 [Lucky](https://www.luckyframework.org) 之类的框架使用。这个类在其自己的纤程中分离出每个请求处理程序。随着`preview_mt`标志的启用，这种情况现在会在 CPU 内核间传播！

像 Sidekiq 这样的后台作业(是的， [Sidekiq 已经被它的作者移植到 Crystal](https://github.com/mperham/sidekiq.cr) 上)在它自己的纤程中执行每个作业。您可以使用 [RabbitMQ 客户端](https://github.com/cloudamqp/amqp-client.cr)并为每个传入的消息生成一个纤程。

或者你甚至可以将原本并行进行的工作分开。假设您有下面的代码，它遍历一个数组并连续处理每个数组:

```
results = array.map { |value| process(value) } 
```

Enter fullscreen mode Exit fullscreen mode

这段代码非常简单，但是如果需要很长时间来处理，那么将各个部分分散到所有 CPU 内核中可能是值得的。为了实现这一点，我们可以将它转换为一个[生产者/消费者](https://en.wikipedia.org/wiki/Producer%E2%80%93consumer_problem)设置，生产者旋转一个纤维，在其中计算结果并通过`Channel`传递给消费者接收:

```
channel = Channel(MyValue).new(array.size)

array.each do |value|
  spawn { channel.send process(value) }
end

results = Array.new(array.size) { channel.receive } 
```

Enter fullscreen mode Exit fullscreen mode

队列通常是生产者/消费者问题的解决方案，我们使用一个`Channel`作为队列。它们内置于标准库中(也在 Crystal 本身中使用),因此我们可以依赖它们而无需安装额外的依赖项。

## 告诫

不幸的是，多线程可能有些库还不是线程安全的。没关系，解决这些问题通常是围绕状态变化包装互斥体，使它们原子化(就像处理数据库事务一样)，因此这是为开源库做贡献的绝佳机会。

如果你不确定如何在你的应用中实现这一点，你可以通过 [Crystal Gitter channel](https://gitter.im/crystal-lang/crystal) 或者在 [Crystal forums](https://forum.crystal-lang.org/) 或者 [subreddit](https://www.reddit.com/r/crystal_programming/) 上发帖。这个社区非常有帮助，我们都对 Crystal 生态系统中的多核支持感到非常兴奋，所以如果您有任何问题，请随时提问！