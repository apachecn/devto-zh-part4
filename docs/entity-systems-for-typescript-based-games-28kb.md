# 基于打字稿的游戏的实体系统

> 原文：<https://dev.to/rhanarion/entity-systems-for-typescript-based-games-28kb>

# 实体——基于文字游戏的系统

这篇文章也可以在我的博客上看到[，如果你感兴趣的话，我计划在这里写更多关于用 typescript 开发游戏的内容:)。](https://rhazn.com/posts/entity-systems-for-typescript-based-games/)

在我最新的游戏项目 [Frozzen](https://frozzen-client.appspot.com/) 中，我想探索一个用 Angular 构建的外部 UI 如何为基于浏览器的游戏工作。因为 Angular 是用 Typescript 编写的，这意味着理想情况下游戏也应该使用相同的。

我过去曾使用 Artemis ODB 作为一个基于 Java 的游戏的框架，并且非常喜欢它。实体系统被大量的文章介绍得更好(例如关于 [T=Machine](http://t-machine.org/index.php/2007/09/03/entity-systems-are-the-future-of-mmog-development-part-1/) 的经典文章),但是我觉得它们特别适合 Javascript/Typescript 开发。

如果严格地将逻辑分离成系统，将数据分离成组件，那么有一种非常自然的方式来序列化组件，即 JSON。整个级别可以表示为用于设置组件的 JSON 数据数组。这就是为什么我更喜欢像 artemis 这样非常基本但严格的实现，而不是 PhaserJS 这样的类似框架。

我从 artemists 开始开发，artemis 的一个 Typescript 端口，由 darkoverlordofdata 开发。不幸的是，代码有点过时，不使用导入/导出，并且不能直接导入新的 Typescript 版本(因为它扩展了内置数组)。

在 darkoverlordofdata 的允许下，我只对代码的 Typescript 部分进行了快速更新，添加了导入/导出支持，并为较新的 Typescript 版本修复了构建。[你可以在这里找到更新版本](https://github.com/rhazn/artemis-ts)。如果你正在寻找这个框架的一个实例，你可以在这里玩一个 Frozzen [的例子。](https://frozzen-client.appspot.com/)

# 关于我

我是一个全栈开发者和数字产品爱好者，我可以自由工作，并总是在寻找下一个令人兴奋的项目:)。

你可以通过电子邮件(pheltweg@gmail.com 或者推特 https://twitter.com/rhanarion T2 联系我。