# 使用 AWS Amplify 控制台部署 React

> 原文：<https://dev.to/dannyaziz97/deploying-react-with-aws-amplify-console-3dka>

### TLDR:

*   如果您使用过其他 CI/CD 工具，那么设置 AWS Amplify 控制台/部署就非常熟悉了
*   它不仅构建您的应用程序，还部署到托管的 CloudFront 发行版
*   如果您来自 CircleCI Intro: AWS Amplify 是 AWS 的新产品，那么构建配置可能会让您感到困惑。它由两部分组成，一个是用于创建应用程序的框架，该框架将您的应用程序与 AWS 服务连接起来，另一个是用于部署任何应用程序的 CI/CD 工具。

今天我们将讨论 AWS Amplify 的部署服务。

在研究 CloudFront 发行版上缓存失效的最佳方式时，我偶然发现了 AWS Amplify。

事实证明，AWS Amplify 在每次部署时都会立即失效！(点击此处了解更多信息)

我将带您通过 Github 设置 AWS Amplify 并部署客户端渲染 React 应用程序。

我们走吧🚀

### 细分步骤:

1.  集成 Github😸
2.  连接回购🔌
3.  设置构建配置🔨
4.  构建配置滴滴
5.  网址重定向↩️完成！✨ 🎉

### 整合 Github😸

耗时:3 分钟

像许多 CI/CD 工具一样，您将 AWS Amplify 与 Github(或其他版本控制工具)连接起来，并在每次提交 repo 时启动构建过程。

[![](img/71975b4739f6d797d8fcd02f19cf2511.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4KAe1ktx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4zmc43s6p9n6yi32znn4.png)

### 连接回购🔌

耗时:1 分钟

一旦你用 AWS Amplify 设置了你的版本控制，你需要选择你的回购并选择一个分支。

### 设置构建配置🔨

耗时:5 - 10 分钟

[![](img/c8d6bbf977a891a3cfe8ce07ae557c4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QXsmPbU8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m9vfjetpwmi8d2g9qunq.png)

这是魔法开始的地方。如果您使用过其他 CI/CD 工具，那么您可能不熟悉这个流程。

AWS Amplify 将尝试自动创建一个构建配置，您可以下载并将其放入 repo 或在控制台上编辑。

如果您有其他 CI 工具的配置文件，AWS Amplify 也可以自动复制这些文件(这是一件好事也是一件坏事)

### [t1【构建配置滴滴】](#build-config-gotchas)

有几个问题导致了我最初的构建失败。我已经经历了痛苦，所以你不必:

*   `sudo`不起作用-这类似于其他 CI/CD 工具
*   自动配置可能会从其他 CI 工具的配置中提取内容，就像我之前说过的，这是一件好事，也是一件坏事。我有很多 CircleCI 构建和部署所需的命令，但 AWS Amplify 不需要——仔细检查自动配置。
*   构建输出目录——警告就在那里，但我忽略了它。--确保将 baseDirectory 更改为您的构建过程输出文件夹(可能是/build)

### URL 重定向↩️

如果你正在运行一个 SPA(创建 React 应用程序等)，那么你将需要设置 URL 重定向。

在控制台内的应用程序项目中，左侧会有一些设置，其中之一是“重定向和重写”

这是 SPA 的基本要求:

[![](img/3d1e442a1c7cedfcfc74260090ec448c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kdTFha0r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lrww6ds4bgjrxww26csa.png)

### 搞定！✨ 🎉

你应该完了！

AWS Amplify 会将你的应用程序部署到 CloudFront，并给你一个 URL。

需要做更多或有问题？查看下面的链接

### 更多阅读:

[欢迎指南](https://docs.aws.amazon.com/amplify/latest/userguide/welcome.html)

[URL 重定向](https://docs.aws.amazon.com/amplify/latest/userguide/redirects.html)

[自定义域名](https://docs.aws.amazon.com/amplify/latest/userguide/custom-domains.html)

* * *

这篇文章最初发表在[https://blog.dannyaziz.com](https://blog.dannyaziz.com)