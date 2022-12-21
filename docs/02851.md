# 探索 Blazor，或 C#和。没有插件的浏览器中的

> 原文：<https://dev.to/dotnet/exploring-blazor-or-c-and-net-in-the-browser-without-plug-ins-5d1d>

已经有很多传言了。网络社区🔥布拉索。然而，与许多炒作的技术不同，Blazor 似乎赢得了关注。它之所以赢得这个奖项，部分是因为它提供了一个解决方案，给了 C#开发人员他们想要的东西，但这是以一种依赖于开放 web 标准的方式实现的。在本次会议中，我将深入探讨 Blazor 是什么，它是如何工作的，以及如何使用它来构建 web 应用程序。

[https://www.youtube.com/embed/BjCgD7sW8FA](https://www.youtube.com/embed/BjCgD7sW8FA)

点击此处获取所有演示的完整源代码:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[JeremyLikness](https://github.com/JeremyLikness)/[blazor-wasm](https://github.com/JeremyLikness/blazor-wasm)

### Blazor 和 WebAssembly 示例(Blazor 演示的一部分)

<article class="markdown-body entry-content container-lg" itemprop="text">

# Blazor 和 WebAssembly

[![Build Status](img/3a521602ba4b2e6e066b29ccf2fd9e7e.png)](https://jeremylikness.visualstudio.com/blazor-wasm/_build/latest?definitionId=9&branchName=master)

[![Release Status](img/76d574317772c923fb1cce1af40527f0.png)](https://camo.githubusercontent.com/3e8bb545a7df85a934274f60837d36d1a2c7a9e0/68747470733a2f2f6a6572656d796c696b6e6573732e7673726d2e76697375616c73747564696f2e636f6d2f5f617069732f7075626c69632f52656c656173652f62616467652f39323462313063332d346663642d343765622d386230332d3335653430663034653836322f312f31)

[![Free Azure Account](img/d9a3cf269ef7f44a874d4d23a7716180.png)](https://jlik.me/gmi) 获得你的[免费 Azure 账户](https://jlik.me/gmi)

这个库包含了关于使用 C#和。NET 在浏览器中使用 WebAssembly 与 Blazor。

<g-emoji class="g-emoji" alias="arrow_forward" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/25b6.png">[Blazor 入门](https://jlik.me/flj)</g-emoji>

👋🏻[Blazor 简介/概述](https://jlik.me/flk)

<g-emoji class="g-emoji" alias="hocho" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f52a.png">🔪</g-emoji> [剃须刀组件介绍](https://jlik.me/fll)

> 这个存储库是使用免费的 Azure 管道不断构建和部署的。如果你对它是如何设置和配置为自动构建并部署到低成本 Azure 存储静态网站感兴趣，请阅读[使用 Azure 管道从 GitHub 部署 WebAssembly 到 Azure 存储静态网站](https://jlik.me/fzh)。

## 介绍会；展示会

<g-emoji class="g-emoji" alias="cinema" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a6.png">🎦</g-emoji>你可以在这里下载相关的 PowerPoint 演示文稿[。](https://jlik.me/fn3)

要了解 Blazor 与 Angular 等其他 SPA 框架的比较，请阅读: [Angular 与 Blazor](https://blog.jeremylikness.com/blog/2019-01-03_from-angular-to-blazor-the-health-app/) 。

## 民众

本节包含执行每个演示的分步说明。

### 先决条件

要使演示正常工作，应该安装以下组件:

*   [演示文稿](https://emscripten.org/docs/getting_started/downloads.html)用于`asm.js`和 WebAssembly 演示
*   [http-service (node.js)](https://www.npmjs.com/package/http-server) 服务于…

</article>

[View on GitHub](https://github.com/JeremyLikness/blazor-wasm)

十多年前，Silverlight 的第一个版本发布，并迅速成为 C#开发人员编写客户端 web 应用程序的灵丹妙药。免费智能手机插件和不断发展的 HTML5 标准的结合导致了它的快速消亡。从它的灰烬中诞生了一个新的框架，完成了它不能完成的事情:跑步。NET，在每一个现代的桌面和手机浏览器，没有插件。了解 Blazor，这是一个用于构建单页应用程序(SPA)的框架，它使用 WebAssembly 的强大功能来实现。NET 应用程序到您的浏览器。

享受演示！

开始使用 Blazor