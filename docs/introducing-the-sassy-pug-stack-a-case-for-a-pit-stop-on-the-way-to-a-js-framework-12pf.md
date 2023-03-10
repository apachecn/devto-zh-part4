# 介绍 Sassy Pug Stack(这是通向 JS 框架的中途停留的一个案例)

> 原文：<https://dev.to/schusterbraun/introducing-the-sassy-pug-stack-a-case-for-a-pit-stop-on-the-way-to-a-js-framework-12pf>

[![Sassy Pug stack](img/cfed12f1de2fdfc598998f3488ae604f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AYj_k3vy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o90f6znxtm3fbywbprpt.jpeg) 
首先，我想说这是我个人的观点，我并不想羞辱任何人或任何特定的技术。我的目标是为刚刚起步的 web 开发人员提供一个可能的垫脚石。这里有一个 [Github 回购](https://github.com/axecopfire/Sassy_Pug_Stack)来帮助开始

刚进入这个领域的前端开发人员在网上收到大量消息，告诉他们需要学习三大框架之一。我想向他们推荐 Sassy Pug Stack 作为学习框架的垫脚石。这个堆栈是(Pugjs、SASS 和 Gulpjs)

这个堆栈使用了一些工具，这些工具可以自动完成一些新开发人员可能会觉得乏味的任务。很快:

*   PugJS: HTML 模板(你有一个 5 页的网站。你厌倦了写导航条吗？)
*   SASS: CSS 预处理器(你嵌套了 3 个元素 3 深，现在你要继续写它们的父元素？预先处理那个！)
*   Gulp:任务运行程序(如果你要做上面的事情，手动运行这些任务会让你很烦。让我们学会让生活变得更简单)

我特别推荐这些工具有几个原因。首先，如果你对 HTML/CSS 有一个基本的了解，那么 Pug 和 Sass 语法的入门门槛真的很低。在 Codepen 上玩一玩，做一些很酷的东西真的很容易。下一个级别的关键是真正让这些在本地工作，这需要一个任务执行者。我推荐 GulpJs，因为这是我曾经使用过的，并且对我的目的有效。

这些工具将使您作为开发人员更有效率，但此时真正的价值是它们将教会您一些概念，如模板、加载库、预处理以及如何进行构建过程。如果有足够的时间与这些技术一起工作，它们将向您介绍三大框架解决的棘手问题。

对于一个刚进入前端开发领域的新开发人员来说，选择什么样的工具适合什么样的项目规模是一件非常困难的事情。逐步提升你的技能真的很重要。我知道在我学习了普通 HTML、CSS 和 JS 的基础知识之后，我对学习三大 JS 框架之一 React/Angular/Vue 的前景感到非常震惊。如果没有一个精神向导来指导你的愿景探索，你可能会觉得不可能逐步学习这些框架所必需的东西。

我提倡的一些免费资源形式的精神指南是伟大的[开发者路线图](https://github.com/kamranahmedse/developer-roadmap)和[前端大师手册](https://frontendmasters.com/books/front-end-handbook/2019/)。作为一名年轻的开发人员，让我不知所措的事情是，从我的项目中没有库集成到最初的几个库来改善我的工作流程。三大框架都有命令行工具，让您能够创建样板文件。试图跳到这些解决方案之一的诱惑之歌非常吸引人。但是很快我就发现自己在 hello world 应用程序之后挣扎着试图整合东西。当看着就业市场时，你看到的都是框架开发工作。但是我觉得更强大的途径是构建一个集成项目，我使用 Sassy Pug Stack 构建了自己的框架。

为了迈出这一步并学习 React，我认为您应该熟悉一些 JS 术语/概念:

*   进口/出口
*   班
*   构造器
*   函数论元
*   `this`的概念(你也许可以通过发送胖箭头函数来获得)
*   熟悉 CLI 和加载外部库

在那之后，你仍然需要学习特定的词汇和做事的方式。当我不知道上面的列表时，我感到不知所措。我发现自己不知道什么被破坏了，或者我是否写错了 React 或者这些新的 JS 特性。

总之，Sassy Pug Stack 向开发人员介绍了如何构建他们自己的开发环境，为他们提供了整合他们想要的任何库的工具，是对 web 开发的一个很好的介绍。