# 在我最近的项目之后，我学到了一些关于代码的经验

> 原文：<https://dev.to/stereobooster/lessons-learned-about-code-after-my-recent-project-1ng8>

为[操作可视化](https://github.com/contiamo/operational-visualizations)编写代码后的经验教训(和其他想法)。

## 过早优化

> 真正的问题是程序员在错误的地方和错误的时间花了太多的时间担心效率；过早的优化是编程中所有罪恶(或者至少是大部分罪恶)的根源。
> 
> 唐纳德·克努特

这是一个众所周知的想法。我听说过很多次了。我已经引用它无数次了。然而，我设法增加了很多复杂性，因为我担心它会没有性能。

我想这是任何程序员的一个盲点，无论你多么努力想让事情变得简单，你还是会让事情变得过于复杂。似乎解决这个问题的唯一办法就是把你的代码交给其他开发者，让他们使用，而不告诉他们如何使用。当你看到人们在努力使用它时，你会立即发现所有有问题的地方。

看到这个 [PR](https://github.com/contiamo/operational-visualizations/pull/96/files) 。

## 静态类型

我喜欢静态类型的跳棋。我试图遵循让非法国家失去代表性的原则。

**但是** TypeScript 不是最先进的类型检查器，因为它试图与 JS 语义兼容，这意味着它无法做一些事情，例如，没有判别式的不相交联合(又名标记)。

有时追求“使非法状态不可表示”会使您编写不可维护的代码。通常，这些复杂的结构会产生不可读的错误消息。

参见[静态对比动态检查。](https://github.com/contiamo/operational-visualizations/blob/master/docs/adr/0006-static-vs-dynamic-check.md)

## 工具

在 monorepo 中集成 TypeScript 和 StoryBook 花了相当长的时间[,从那以后它就一直在崩溃。目前，类型错误不会出现在故事书的覆盖图中，所以当出现错误时，你不知道，需要切换到终端来查看是否有错误消息。](https://github.com/stereobooster/typescript-monorepo)

这有点烦人，但我喜欢它给我带来的好处:

*   多亏了 TypeScript 的静态类型检查
*   感谢 StoreyBook 和 Chromatic 的截图测试

花了一些时间才弄清楚如何使用 lerna 实现自动化部署。起初，我们试图使用 commitizen 和 commitlint，但这是一个非常令人讨厌的体验:你试图提交一些东西，它问你 5 个问题，最后提交失败。这并不是对这些工具的批评，它们对我们的团队根本不起作用。

所以我们拒绝了 commitlint，相反，我们同意使用常规格式的公关名称和挤压公关，这样我们可以有一个干净的主分支没有烦恼。

感谢 lerna，我们自动从 master 向 canary channel 发布 npm 包。我们使用 GitHub 工作流运行 lerna。配置 GitHub 工作流程是一件痛苦的事情。

使用 Netlify 绝对是一种享受——PR 预览非常有用。特拉维斯没有说话——一个老朋友，这是我非常喜欢的(然而`"$TRAVIS_BRANCH" == "master"`的概念非常混乱)。

我们使用`react-scripts`进行 eslint 配置。它工作得很好，除了没有办法使它在 CI 中的警告失败。`lint-staged`和`prettier`好用。

这是数量惊人的工具。我开始怀疑🤔如果我们过度设计它，或者其中一些工具应该内置在标准环境中。

## PS

这是一次很好的学习经历。还有很多事情要做。