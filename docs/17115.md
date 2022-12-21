# Reduce 承诺 Node.js 中的内存使用

> 原文：<https://dev.to/andyghiuta/reduce-promises-memory-usage-in-node-js-1a3i>

我最近不得不修复一个 Node.js lambda 函数，它在完成处理之前被 AWS 突然停止，达到了该函数的最大内存。在裸机服务器上，您可以再添加一个 RAM 模块，并期待最好的结果。在无服务器环境中，存在一些限制。特别是在 AWS 中，您最多可以给 lambda 函数 3,008 MB。你会认为这已经足够了...你就错了，就像我们一样。

这个函数并不特别复杂。它必须解析一个 CSV，并为每条记录做一系列事情:验证，从 DynamoDB 中读取一些内容，然后根据一些数据对每条记录进行一到两次写入。

复杂的是，它必须等到所有的行都被处理并返回一个结果:当整个过程完成时，有多少行被成功处理，有多少行出错，哪个出错(验证)。

更复杂的是，在某个时候，有人想要处理一个 70k 的记录文件。3,000 MB 似乎还不够。

# 提出解决方案

## 1。不要做无服务器

当然，我们首先想到的是把它移出 lambda。在 AWS 中，这可以通过 ECS(弹性容器服务)来完成。可以工作，但是会增加一个需要维护和了解的服务。

## 2。分割 CSV

可能，但容易出错。多少才算太少，我们如何确保做到这一点？CSV 由第三方上传。最同步的夜晚。可能是自动的。丑。

## 3。尝试改进代码

这可能很耗时，但如果证明有效，这很容易成为最佳的解决方案。

# 实施方案 3

代码已经过时了，构建在节点 v6 上，带有众所周知的回调地狱，用著名的 [async](https://caolan.github.io/async/v3/) 库来管理。

### 第 0 步:重构

任务:

*   使用较新版本的节点
*   重新思考逻辑

直到最近 AWS 才支持 6 和 8.10，所以我们选择了 8，它带来了对承诺和原生 async/await 的支持，以消除一些回调问题。

最初的实现有一个相当大的问题:每个记录都是单独处理的，尽管它包含一些与其他记录重复的数据。所以从 DynamoDB 中有重复的读取。很多都是。

更好的解决方案是按照通用标准对记录进行分组，然后并行处理这些组，并对每个组的所有记录进行并行处理。`Promise`和`async` / `await` FTW！生成的代码更小，更容易理解，从数据库中读取的数据减少了 90%...仍然达到了内存极限。

这里是我为测试这个而设置的演示 repo 的结果(处理 100 组，每组 1000 条记录):

```
$  node index.js
Memory used before processing all records: 9.17 MB
Memory used after processing all records: 92.79 MB
Process time: 3352.570ms 
```

Enter fullscreen mode Exit fullscreen mode

### 第一步

在挖掘出什么可以吃掉所有多汁的 RAM 之后，事实证明 Promise 并不是特别容易记忆。[蓝鸟](http://bluebirdjs.com/docs/getting-started.html)被[建议](https://github.com/nodejs/node/issues/6673)，那我们试试吧。

需要更改:

```
$  npm i bluebird 
```

Enter fullscreen mode Exit fullscreen mode

```
const Promise = require('bluebird'); 
```

Enter fullscreen mode Exit fullscreen mode

很容易解决。失忆了。下降了大约 30%。但是对于大文件，该功能仍然超时。不太好。

下面是测试输出:

```
$  node index.js
Memory used before processing all records: 9.3 MB
Memory used after processing all records: 67.32 MB
Process time: 3169.421ms 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步

[原来](https://stackoverflow.com/questions/46654265/promise-all-consumes-all-my-ram)等待所有的承诺继续意味着我们已经将所有的承诺储存在记忆中。猜猜看...
因此，我们需要减少并行处理的请求数量。蓝鸟再次出手相救，带着[无极. map](http://bluebirdjs.com/docs/api/promise.map.html) 。使用该功能的`concurency`选项，我们可以设置在给定时间应该处理多少个并发项目。

以及最终的测试输出:

```
$  node index.js
Memory used before processing all records: 9.29 MB
Memory used after processing all records: 17.34 MB
Process time: 30132.855ms 
```

Enter fullscreen mode Exit fullscreen mode

更好的是，使用这种方法，内存峰值是稳定的。它不会随着要处理的项目数量的增加而增加，因为在处理完每一批记录之后，GC 就会发挥作用。当然，这确实增加了处理整个集合的总时间，但是对于这个特殊的场景，我们只对不消耗所有内存感兴趣。

真实世界的代码使用大约 400 MB 的内存，在大约 30 秒内处理 10k 条记录。我们认为这是可以接受的。

检查 GitHub 存储库中的提交，以遵循上面的步骤:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[承诺-履行](https://github.com/andyghiuta/promise-performance)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 承诺 Node.js (v8.10.0)中的性能改进

该脚本试图模拟处理记录矩阵。例如:

```
const records = [[1, 2], [3, 4]];
```

Enter fullscreen mode Exit fullscreen mode

要知道所有记录何时被处理，我们需要知道每一行何时被处理，以及所有行何时被处理。

# 丰富

## 步骤 0(无改进)

其思想是用一个承诺来处理每条记录，并且对于每一行，`await Promise.all`行，仅在行中的所有记录都处理完之后才返回，然后对于整个集合，`await Promise.all`为行返回承诺。

### 观察

内存使用率很高。脚本使用大约 99.8MB，并且不会在处理完每一行后释放内存。相当有趣...

## 第一步

看起来蓝鸟可以帮忙: [nodejs/node#6673](https://github.com/nodejs/node/issues/6673)

需要的更改:

```
$ npm i bluebird
```

Enter fullscreen mode Exit fullscreen mode

```
const Promise = require('bluebird');
```

Enter fullscreen mode Exit fullscreen mode

### 观察

内存使用率下降…

</article>

[View on GitHub](https://github.com/andyghiuta/promise-performance)