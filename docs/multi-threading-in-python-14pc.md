# Python 中的多线程

> 原文：<https://dev.to/suryasr007/multi-threading-in-python-14pc>

### Python 中的多线程使用“活动”应用程序

计算机世界中的线程被描述为简单的执行器(执行给定的指令)。这些是轻量级的，用于异步执行任务。

浏览器中不同的标签充当不同的线程

一般来说，在操作系统级别，线程拥有自己的资源，但在抽象级别，它们使用相同的代码、数据和文件。

[![](img/e57d3f14d5784e59cd48e02f60c7ff9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pc3Sm5WR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A1C4e7YzuhSZQcwUf.) 
来源:[https://www.studytonight.com](https://www.studytonight.com/)

**活动应用中的多线程:**

[活动](https://github.com/sayanarijit/activity)是我的好朋友 [Arijit](https://github.com/sayanarijit) 开发的一款应用。它通过 ssh 异步执行批量服务器命令执行和验证。

它是用 python 3.6 开发的。该模块的核心概念是，它使用多线程在所有给定的服务器上并行执行命令，因此它消耗的时间更少，并且以一种有组织和高效的方式执行任务。

为了理解多线程，让我们深入了解一些属于
activity app 的代码，它在所有主机上异步执行 ping 检查。