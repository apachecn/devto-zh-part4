# Web assembly 和 Blazor:现状

> 原文：<https://dev.to/samueleresca/web-assembly-and-blazor-state-of-art-3nk2>

*原贴于[https://samueleresca.net](https://samueleresca.net)T3】*

*封面图片由[科拉多泽妮](http://www.corradozeni.it/)T3】*

我在 2017 年第一次看到了 Blazor，以及更普遍的 Web 组装技术。同年，我在下面的博客中写了这个话题。网芯。两年后，Blazor 即将发布它的第一个正式版本，它不再是实验性的，它正在成为。网络生态系统。下面的文章给出了 Blazor 框架的一些快速更新。

## Blazor 如何工作？

首先，让我们看看 Blazor 的背后是什么，以及它是如何使用新的 Web 程序集工作的。以下模式显示了 Blazor 的基础:

[![web assembly blazor](img/a152cdf8d27665ac15f6aeb35fcbdb75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tDb3tid0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/samueleresca.net/wp-content/uploads/2019/05/Screenshot-2019-05-27-at-22.47.24.png%3Ffit%3D720%252C476%26ssl%3D1)

[Web assembly](https://webassembly.org/) 位于金字塔的底部，它定义了一种二进制标准格式，允许将字节代码运行到浏览器中。此外，其中一个

Web 程序集是一个标准，没有链接到。NET 生态系统，但它已经迈出了第一步。NET 引入客户端开发。

Blazor 背后的另一个核心角色是 Mono 框架。Mono 是一个. NET 运行时，它是。由微软和社区维护的。Mono 是为可移植性而设计的，因此它被编译成 web 汇编，从下面的 PR 开始:[https://github.com/mono/mono/pull/5924](https://github.com/mono/mono/pull/5924)

最后，最上面一层是 Blazor。Blazor 是一个 UI 框架，它定义了 UI 的启动过程，并且实现了允许组件一起通信的基础设施。从...开始。NET Core 3.0，Blazor 将作为框架的一部分推出。

## Blazor 应用概述

可以使用以下指令创建新的 Blazor 模板:

```
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview5-19227-01
```

```
dotnet new blazor -n 
```

第一个命令使用的 3.0.0- `preview5-199227-01`版安装 Blazor 模板包。网芯。第二个命令使用`web_app_name`在当前文件夹中创建一个新的基础项目。

生成的项目和文件系统将与此类似: