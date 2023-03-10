# 构建从 0 到英雄的 WTF util

> 原文：<https://dev.to/victoravelar/building-a-wtf-util-from-0-to-hero-4fda>

卡斯帕·卡米尔·鲁宾在 Unsplash 上的照片

# 第 1 集——他妈的为什么(简介)

不久前，我发现了一个惊人的面向开发人员的工具，可以监控、检查和浏览很多东西，它使用模块操作，有一个模块我一直觉得非常有用，叫做新闻。

这个模块可以让你看到最新的 [HackerNews](https://news.ycombinator.com/) 文章列表，如果你觉得它足够有趣，你可以点击它，它会在你的默认浏览器上打开相关的文章，这对我来说很神奇，因为无论如何我每天都在查看网站。

基本上是这种日常事务让我思考，我经常查看其他网站以了解科技界的最新消息，这里是我灵光一现的地方，尽管**如果这个东西还没有出来，让我们创建它。**

## 走近

首先，我喜欢分享我知道的一些事情，即使这不是最好的方法，我也会分享，然后当有人说嘿，这也可以这样做时，我学到了一些新的东西，这是一个你应该尝试的黄金循环，所以对我来说，这就像一个教程系列，讲述如何从最初的项目目录开始，直到准备好工具供 wtfutil 用户使用。

敬请关注。

干杯🍺

### 资源

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ wtfutil ](https://github.com/wtfutil) / [ wtf](https://github.com/wtfutil/wtf)

### 您终端的个人信息仪表板

<article class="markdown-body entry-content container-lg" itemprop="text">

[![WTF](img/f4e5d49000450440fbac639f7d8e1aab.png "WTF")](https://github.com/wtfutil/wtimg/logo_transparent.png?raw=true)

[![Go Report Card](img/85d397b883670edbc61ef7403315e79e.png)](https://goreportcard.com/report/github.com/wtfutil/wtf)[![Twitter](img/f3a430a66e01907979ffe1dbfd8481aa.png)](https://twitter.com/wtfutil)[![DeepSource](img/cdbba9023a13bbd70026ef90b1620e59.png "DeepSource")](https://deepsource.io/gh/wtfutil/wtf/?ref=repository-badge%7D)

WTF(又名“wtfutil”)是您终端的个人信息仪表板，提供对您非常重要但不常用的统计和数据的一目了然的访问。

世界上成千上万的开发者和技术人员使用 WTF，它是免费和开源的。为了支持 WTF 的继续使用和发展，请考虑通过 [GitHub 赞助商](https://github.com/sponsors/senorprogrammer)赞助 WTF。

### 你是贡献者还是赞助者？

厉害！[看这里](https://wtfutil.com/sponsors/exit_message/)了解如何改变退出消息，WTF 退出时显示的消息，为你做一些特别的事情。

## 主办单位

[![Airbrake](img/3853116ed01e8edd3c014a08e3e0d1be.png "Airbrake")](https://airbrake.io/?utm_medium=sponsor&utm_source=WTFutill&utm_content=airbrake-home-page&utm_campaign=2021-sponsorships)

* * *

*   [安装](https://github.com/wtfutil/wtf#installation)
    *   [通过自制软件安装](https://github.com/wtfutil/wtf#installing-via-homebrew)
    *   [通过 MacPorts 安装](https://github.com/wtfutil/wtf#installing-via-macports)
    *   [安装二进制文件](https://github.com/wtfutil/wtf#installing-a-binary)
    *   [从源安装](https://github.com/wtfutil/wtf#installing-from-source)
    *   [通过 Docker 运行](https://github.com/wtfutil/wtf#running-via-docker)
*   [通信](https://github.com/wtfutil/wtf#communication)
    *   [GitHub 讨论](https://github.com/wtfutil/wtf#github-discussions)
    *   [推特](https://github.com/wtfutil/wtf#twitter)
*   [文档](https://github.com/wtfutil/wtf#documentation)
*   [模块](https://github.com/wtfutil/wtf#modules)
*   [修复漏洞或增加功能](https://github.com/wtfutil/wtf#getting-bugs-fixed-or-features-added)
*   [贡献源代码](https://github.com/wtfutil/wtf#contributing-to-the-source-code)
    *   [添加依赖关系](https://github.com/wtfutil/wtf#adding-dependencies)
*   [为文件做出贡献](https://github.com/wtfutil/wtf#contributing-to-the-documentation)
*   [贡献者](https://github.com/wtfutil/wtf#contributors)
*   [致谢](https://github.com/wtfutil/wtf#acknowledgments)

[![](img/3cb80f4b834a353c5eb420b1b1298c06.png "screenshot")](https://github.com/wtfutil/wtimg/screenshot.jpg)

## 装置

### 通过自制软件安装

自制的最简单方法:

```
brew install wtfutil
wtfutil
```

Enter fullscreen mode Exit fullscreen mode

这个版本有时会滞后一点，因为配方…

</article>

[View on GitHub](https://github.com/wtfutil/wtf)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[devto-API-go](https://github.com/VictorAvelar/devto-api-go)

### 开发到 API 的 go 客户端

<article class="markdown-body entry-content container-lg" itemprop="text">

# 开发应用编程接口

开发到 API 的 go 客户端

[![License: MIT](img/db85796de36d003cae919db6f133f455.png)](https://opensource.org/licenses/MIT)

#### 特拉维斯·CI

[![Build Status](img/2f1b58bd484d692482b6dfbc5f4cf54d.png)](https://travis-ci.org/VictorAvelar/devto-api-go)

#### 仔细或彻底检查

[![Scrutinizer Build Status](img/dd26fac85af7836c178831bb4174749a.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/build-status/master)[![Scrutinizer Code Quality](img/4e95ffe53e71af50f0cf446342069fdf.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)[![Code Coverage](img/4b24d605b035f3712c4061b10d4c59ed.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)

#### Go 生态系统

[![Go Report Card](img/9b9b43938b0abe1ad6b7bf883b9ce923.png) ](https://goreportcard.com/report/github.com/VictorAvelar/devto-api-go) [ ![GoDoc](img/8074e2e7c6159296ccb342c7f3aea036.png)](https://godoc.org/github.com/VictorAvelar/devto-api-go)

## 路标

*   基本客户端和配置
*   文章资源
*   CLI 实用程序
*   认证资源
*   扩展测试套件
*   提供代码示例

## 放弃

这个库不是一个官方的开发者 API 库，而是对开发者社区的友好贡献

</article>

[View on GitHub](https://github.com/VictorAvelar/devto-api-go)