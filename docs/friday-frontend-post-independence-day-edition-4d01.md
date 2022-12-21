# 星期五前端:后独立日版

> 原文：<https://dev.to/kball/friday-frontend-post-independence-day-edition-4d01>

希望你度过了愉快的一周！祝在美国的你们 7 月 4 日快乐。本周我们为您准备了一系列精彩内容；CSS 和 JavaScript 方面的一些伟大的架构作品。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 自定义级联中的属性](https://www.smashingmagazine.com/2019/07/css-custom-properties-cascade/)

深入探究 CSS 自定义属性，以及它们如何使您能够利用继承和级联，同时仍然获得大量隔离。最近，我在 JS 世界的时间比在 CSS 世界的时间要长，但是像这样的帖子让我非常兴奋，能够使用我们现在拥有的新工具回到样式和 CSS 架构中。

##### [多个网站的 CSS 架构](https://medium.com/@elad/css-architecture-for-multiple-websites-ad696c9d334)

这是一个关于如何考虑样式和分层设计 CSS 的很好的例子。这种分层方法也是考虑应用程序架构和设计的一种很好的方式——有些东西确实是共享的和全局的，而另一些则是特定于某个特定组件的。

##### [CSS:焦点-内](https://davidwalsh.name/css-focus-within)

简短、直截了当、范围严格的文章，讨论了`:focus-within`属性以及如何使用它来提高站点或应用程序的可访问性。如果你正在使用`:hover`来暴露信息或可操作的元素，并且还没有应用`:focus-within`，你一定要阅读这篇文章。

##### [CSS 速记与手写——用哪个](https://www.hongkiat.com/blog/css-shorthand-longhand-notations/)

CSS 属性中速记和手写之间的一些差异，当你使用速记时你放弃了什么，以及它们各自的优缺点。

#### JavaScript

##### [2019 年 JavaScript 的成本](https://v8.dev/blog/cost-of-javascript-2019)

Addy Osmani 关于 JavaScript 在 2019 年对网络和性能的影响以及你可以做些什么来影响它的更新。有趣的是，要注意的头号事情已经从几年前的传统智慧中改变了。据 too Osmani 称，解析和编译时间不再是大问题。在 2019 年，处理脚本的主要成本现在是下载和 CPU 执行时间。

##### [十大构件戒律](https://dev.to/selbekk/the-10-component-commandments-2a7f)

基于组件的架构是当前前端开发的大趋势之一，我喜欢看到这样的文章关注如何创建可用的、高质量的组件。本文中的一些“戒律”是 React 特有的，而其他的则更普遍适用。向所有在现代 JS 框架中工作的人推荐这个。

##### [2019 年棱角分明的状态](https://blog.bitsrc.io/the-state-of-angular-in-2019-b5fb7783a1c6)

对 JavaScript 框架生态系统中“以企业为中心”的参与者的有趣观察。肯定有一些项目让我感到惊讶(考虑到总体上缺乏关注，听到他们看到 50%的年同比增长有点令人震惊)。很多好东西，尤其是如果你已经在使用 TypeScript 并且对包含电池的框架感兴趣的话。

##### [node . js 中的设计模式:实用指南](https://dev.to/bnevilleoneill/design-patterns-in-node-js-a-practical-guide-2c19-temp-slug-9263040)

虽然标题是 Node.js，但这些实际上只是 JavaScript 设计模式。我也在客户端代码中使用了其中的每一个。它们非常值得了解。

##### [RxJS:简单介绍](https://medium.com/@rossbulat/rxjs-a-simple-introduction-32fb48f52a67)

RxJS 已经被 Angular 社区接受为 Angular 中的核心事件处理模式之一，但是它实际上适用于全球。我真的很喜欢心智模型，尽管这个库的 API 表面积相当大，很难获取。这篇文章很好地介绍了它。

#### 其他牛气

##### [HTML 能做到吗？](https://dev.to/ananyaneogi/html-can-do-that-c0n)

这太棒了。我看过很多关于语义 HTML 对于可访问性和机器解析和分析的好处的文章，但是没有一篇像这样系统地展示 HTML 的能力。这里有一些项目，我发誓你需要 JavaScript 来做，但是没有！就 HTML！

##### [向 React 应用添加 WebAssembly 组件](https://blog.scottlogic.com/2019/06/14/add-webassembly-to-react-app.html)

将 WebAssembly(在本例中是从 AssemblyScript 生成的)集成到 web 应用程序中是一个非常简单快捷的例子。我认为这种“多语言”的语言方法将成为未来大型应用程序的重要组成部分。用 JavaScript 编写 Glue & UI 代码，从用 C++或 Rust 等语言编写的应用程序中导入性能密集型和/或专用代码作为 WASM。

##### [无障碍树如何通知辅助技术](https://hacks.mozilla.org/2019/06/how-accessibility-trees-inform-assistive-tech/)

很好地了解了什么是可访问性树，不同的 HTML、CSS 和 JavaScript 如何影响它们，以及如何在 Firefox 中查看由代码生成的可访问性树。

##### [布局不稳定 API](https://web.dev/layout-instability-api/)

我敢肯定，我不是唯一一个对我阅读时内容的跳跃感到非常恼火的人——我也从来没有故意给我的用户创造这种体验。但是有时候你不知道你的代码在“野外”是如何运行的，你也不能完全和每个用户保持一致。布局不稳定性 API 旨在解决这个问题。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】