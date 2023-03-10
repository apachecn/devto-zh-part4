# Osgood 性能提升

> 原文：<https://dev.to/tlhunter/osgood-performance-gains-n59>

我们最近对新的 JavaScript 平台 [Osgood](https://github.com/IntrinsicLabs/osgood) 的请求/响应路径进行了一些优化，试图让它运行得更快。Osgood 是一个运行 JavaScript HTTP 服务器的安全、快速、简单的平台，它以二进制形式发布，可以像 Node.js 一样在您的服务器上运行。

## 优化

其中一些改进相当简单，适用于大多数代码库:

*   删除[不必要的](https://github.com/IntrinsicLabs/osgood/commit/2a839bb2e1d580e8858f30e6e73f1bcf55ccd5f6) [工作](https://github.com/IntrinsicLabs/osgood/commit/8fd4ab1317cfe66de1453c32c1b5f99d3f2ba022)(在我们的例子中，当返回一个字符串响应时)
*   [懒洋洋地构造](https://github.com/IntrinsicLabs/osgood/commit/7788a98f15fd513ab775ac26378292d9b2454a44)复杂的类实例(如`Headers`)
*   [传递引用](https://github.com/IntrinsicLabs/osgood/commit/5d6d2471510f0012557282ab6a0301ec83a14256)而不是执行表格查找

其他优化取决于 V8 如何优化和运行 JavaScript，在其他情况下不一定会更快:

*   用[私有类字段](https://github.com/IntrinsicLabs/osgood/commit/67037b4a3f048a5d4ec3781ec6575227f0ab0c2b)替换`defineProperty()`调用
    *   虽然[私有符号](https://github.com/IntrinsicLabs/osgood/commit/5afdc5be1a2cd751cbe8656ac6da7fb847e26d93)被证明甚至更快
*   实例化[相似形状的对象](https://github.com/IntrinsicLabs/osgood/commit/c5b5053f8a17361d8da3c032ea53a615eaf5f0ab)时使用`class`

这些优化中有许多是你不一定想在应用程序代码中进行的。然而，由于 Osgood 是一个运行应用程序代码的大受众平台，因此尽可能进行优化并使大量应用程序受益是有意义的。

## 结果

使用`wrk`基准测试工具，当运行一个简单的“Hello，World！”时，我们看到了**3.0 倍**的改进——以每秒请求数(r/s)衡量具有 *10 个并发请求* : `osgood@0.1.0`的基准测试运行在 **25，261 转/秒**，而`osgood@0.2.1`运行在 **77，450 转/秒**！(作为参考，`node@12.7.0`也运行服务器端 JavaScript，运行在**31159 r/s**。)

[![Osgood Performance Gains](img/0dc1da7e22aae7ef8bf2c1c5fb6f67e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QyKZk1eL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gutm6rontu4i0cmcet83.png)

如您所见，随着并发性的增加，Osgood 运行得更快了。我们从一开始就考虑到并发性来构建 Osgood，所以这些结果并不令人惊讶。在幕后，奥斯古德正在利用托基奥。从 [Tokio 主页](https://tokio.rs/):

> 用 Tokio 构建的应用是现成的并发应用。Tokio 提供了一个多线程、窃取工作的任务调度器，针对异步网络工作负载进行了优化。

下面是来自这些基准测试的一些原始数据，它们也显示了响应时间标准偏差是如何在一个数量级上变得更平稳的:

```
$ wrk -d 60 -c 10 http://localhost:3000/hello # osgood 0.1.0
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.26ms    9.91ms 123.57ms   92.77%
    Req/Sec    12.69k     2.91k   16.98k    73.83%
Requests/sec:  25261.70

$ wrk -d 60 -c 10 http://localhost:3000/hello # osgood 0.2.1
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   140.86us  219.40us  15.27ms   97.41%
    Req/Sec    38.92k     2.30k   45.89k    71.38%
Requests/sec:  77449.91

$ wrk -d 60 -c 10 http://localhost:3000/hello # node v12.7.0
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   321.69us   96.95us  11.08ms   98.41%
    Req/Sec    15.66k     1.18k   17.50k    76.54%
Requests/sec:  31159.16

$ wrk --version
wrk 4.0.0 [epoll] 
```

用于这些基准测试的代码是[，可从这里](https://github.com/IntrinsicLabs/osgood/tree/master/benchmarks)获得。

## 接下来是什么

我们对已经取得的性能提升非常满意。也就是说，我们有更多的计划让它变得更快。我们计划实现的一个这样的特性是可选的[自动缩放工人](https://github.com/IntrinsicLabs/osgood/pull/12)(这个特性比`osgood@v0.1.0`版本提高了**2.5 倍**)。

虽然`osgood@0.2.1`的*平均*延迟不到 Node.js 的一半，但是*最大*仍然更高。这意味着仍有一些空间来优化垃圾收集并获得更一致的结果。

像往常一样，[补丁是受欢迎的](https://github.com/IntrinsicLabs/osgood)，如果你看到有助于性能的地方，我们很乐意从你那里得到一个公关！

想得到这个更快版本的奥斯古德吗？访问[版本](https://github.com/IntrinsicLabs/osgood/releases)页面并下载最新版本！