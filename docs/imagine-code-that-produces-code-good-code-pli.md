# 代码创建，试试吧！

> 原文：<https://dev.to/chris_bertrand/imagine-code-that-produces-code-good-code-pli>

我认为我们应该考虑选择**写代码**，它为我们写代码！我来解释一下原因。

我们热爱编码，我们试图写尽可能多的代码。LoC 是衡量你是否优秀的最终尺度。😊但是我们中有多少人编写代码来为我们创造代码呢？

当然，我们喜欢使用工具/框架等来帮助我们的写作，但是仅限于使用这些工具。

我最近一直在看[故事书](https://storybook.js.org/)来帮助我们的 UI 库自动创建一个演示应用程序。目前，这是一项手动任务，创建页面并实现所需的组件和变化。

有人告诉我，这并不困难，也不需要很长时间。我的第一个想法是，那么我们为什么没有自动化呢？

<figure>[![Related image](img/63285c414aa83212d3c132e533c8d8e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YkLpJVKd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.imgur.com/cPw6cAK.gif) 

<figcaption>但是我也会削铅笔</figcaption>

</figure>

自动化似乎已经被测试吞噬了。开发人员不再把它作为他们武库中的一个工具。我们很乐意使用 AngularCLI 或替代产品！**为什么不创建脚手架**？每个代码库都有一种风格，每个公司都以自己的方式做事，所以他们应该创建工具。让我们结束风格指南和冗长的操作方法的概念。

[![Image result for automation gif](img/d63bfd7867ce55689a1bc70dc40c411d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_RATvLqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A1hT23VteSYhRbOaUtCcuEg.gif)

一个组件有一个模板和一些输入。你可以创建一个简单的配置文件，可以是 JSON，YAML，JavaScript，任何你选择的语言，可以解析这些输入并动态地创建这些页面。

## 我们为什么要自己做这件事？

<figure>[![Image result for code monkey gif](img/a85052d05e6e8082b955dcdfb30b5262.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gPwsA-nE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/KpACNEh8jXK2Q/giphy.gif) 

<figcaption>任何人都能做到这一点！</figcaption>

</figure>

这种繁重的工作不会给我们带来任何满足感，但是为我们编写一个工具来完成这项工作，打包、设计和部署它将会非常棒。也许这是 web 开发人员和软件工程师的本质区别？我不知道，我一直以为这些称呼是可以互换的，难道不是这样吗？是不是 JavaScript 还没这么想过？

是的， [Storybook](https://storybook.js.org/) 给了我们这个功能，它似乎在这方面也做得很好。我只是担心，我们期望这些天为我们建造的一切。我们似乎忘记了，我们可以按照自己的意愿建造它们。轻便、可定制，专门针对我们的使用情形。即使我们不这样做，它至少值得考虑不是吗？

我想这是前几周 Bens 的帖子的后续。

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 投资构建定制工具

### 本哈尔彭 6 月 16 日 191 分钟阅读

#productivity](/ben/invest-in-building-custom-tools-3430)

目前有多少人这样做？这种方法的主要优点和缺点是什么？你会再做一次吗？