# 发布一个现代 JavaScript(或类型脚本)库

> 原文：<https://dev.to/4nduril/publish-a-modern-javascript-or-typescript-library-3hc3>

您是否曾经一起编写了一些库代码，然后想以 NPM 包的形式发布，但是意识到您不知道当前有什么技术可以做到这一点？

你有没有想过“我应该使用 Webpack 还是 Rollup？”，“ES 模块呢？”，“实际上，其他的包格式呢？”，“如何随编译后的代码一起发布类型？”诸如此类？

完美！你找对地方了。在这一系列文章中，我将尝试回答所有这些问题。具有这些工具和愿望的大多数可能组合的示例配置。

### 技术基础

这是本教程测试的工具集及其各自的版本范围:

*   现在是 2018 年
*   网络包> = 4
*   巴别塔> = 7.4
*   打字稿> = 3
*   累计> = 1
*   React >= 16.8(针对其他库(如 Vue 或 Angular)的代码应该也是如此)

接下来的部分甚至大部分内容也适用于这些工具的旧版本。但我不会保证或测试它。

### 创作

发布库之前要做的第一件事显然是写一个。假设我们已经这样做了。其实就是[这个](https://github.com/4nduril/library-starter/tree/init)。它由几个源文件和模块组成。我们提供了我们想要的功能，使用了我们最喜欢的现代 JavaScript(或 TypeScript)特性，并用我们最喜欢的编辑器设置精心制作了它。

现在怎么办？我们想在本教程中实现什么？

1.  Transpile 现代语言的特点，使每一个浏览器的最后两个版本之一，可以理解我们的代码。
2.  避免复制编译阶段的助手，以保持库尽可能小。
3.  通过林挺和测试确保代码质量。
4.  将我们的模块打包成一个可消费、可安装的文件。
5.  提供 ES 模块使库树可摇动。
6.  如果我们用 TypeScript 编写我们的库，请提供类型。
7.  改善与其他开发人员的协作(来自我们的团队，或者，如果是开源库，来自公众)。

哇哦。那是一大堆事情。让我们看看我们是否能按时到达。

请注意，这些步骤中的一些可以用不同的工具来完成，或者根据用 TypeScript 或 JavaScript 编写的代码而有所不同。我们会涵盖所有这些。嗯，可能不是所有的，但我会尝试涵盖最常见的组合。

本系列的章节不仅会展示我认为你应该使用的配置，还会解释其背后的原因和工作原理。如果你对这些背景不感兴趣，在每个帖子的顶部会有一个链接，直接链接到要执行的配置和步骤。

### 出发！

我们将从上面列表中的第一点开始。随着新文章的到来，我会将它们作为链接添加到这里，并且当它们使用的工具获得新功能或改变 API 时，我也会尝试保持已完成的文章更新。如果你发现什么不再是真的，请给我一个提示。

1.  [Transpile 现代语言特征——带巴别塔](https://dev.to/4nduril/transpile-modern-language-features-with-babel-4fcp)。
2.  [用 TypeScript 编译器编译现代语言特性](https://dev.to/4nduril/compiling-modern-language-features-with-the-typescript-compiler-36m3)。
3.  建立你的图书馆:第一部分
4.  [检查类型并发出类型声明](https://dev.to/4nduril/check-types-and-emit-type-declarations-1i29)
5.  [如何捆绑您的库，为什么](https://dev.to/4nduril/how-to-bundle-your-library-and-why-1gao)
6.  [将您的库与 Webpack 捆绑在一起](https://dev.to/4nduril/bundling-your-library-with-webpack-12ig)

哦，最后一件事:我会在整个系列中使用`npm`,因为我喜欢它。如果你更喜欢`yarn`，只需交换命令。