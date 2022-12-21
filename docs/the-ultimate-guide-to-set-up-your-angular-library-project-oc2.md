# 设置 Angular 库项目的最终指南

> 原文：<https://dev.to/kreuzerk/the-ultimate-guide-to-set-up-your-angular-library-project-oc2>

#### 使用更漂亮的& Husky 自动格式化代码，测试覆盖报告，使用 Travis CI 为您的 Angular 库部署 showcase 和全自动发布

[![](img/fda3fa9860ae14093964967b79ee991d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tnI1p-Fu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApIDAs7gTgnIE6fZ9_QT0gQ.png)

Angular 是一个非常棒的框架，我们都喜欢它😍这是一个完整的工具包，帮助我们建立惊人的东西。

更棒的是。Angular 也包含了一个很棒的社区。一个不断成长并推动 Angular 前进的社区。

因此，框架周围有一个庞大的生态系统也就不足为奇了。从有用的服务到功能齐全的组件库，Angular 生态系统提供了这一切。

所以没有什么是不可能的。你有一个很酷的棱角图书馆的想法吗？然后继续，参与，实现你的库，并与世界分享！🌎

### 你将学到什么👨‍🎓

最新的 Angular 版本已经很好地支持了库的创建。然而，开发和测试只是创建 Angular 库的一部分。另一部分是运送艺术品。🚢

交付软件需要一些步骤；我们需要运行测试，构建我们的工件，发布它，用发布资产更新项目，并记录发布。

一个软件工程师我们倾向于自动化重复的手工步骤。这就是我们要做的。自动化 Angular 库的发布不是一件容易的事情。尤其是，如果您不仅想要自动化发布，而且想要自动化发布资产的处理。

这篇博文旨在为你提供一个完全自动化和完整的 Angular 库设置的终极指南。它涵盖了许多主题，因此最好分多次阅读。

所以，事不宜迟，让我们重温一下☕，然后直接投入进去吧！