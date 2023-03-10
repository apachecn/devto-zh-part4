# Swift 项目的 Github 行动 CI

> 原文：<https://dev.to/artemnovichkov/github-actions-ci-for-swift-projects-36kp>

Github [引入了基于动作的](https://github.blog/2019-08-08-github-actions-now-supports-ci-cd) CI 和 CD。它允许您在任何平台上自动分析、构建、测试和部署您的项目，包括 Linux、macOS 和 Windows。

我对这个消息感到兴奋，迫不及待地想尝试一下。我的想法是自动化 Swift 项目的构建和单元测试。有两种类型的行动，在开始之前我们应该知道它们的区别。

## 码头集装箱 vs JS

最初，所有操作都基于 Docker，并对工作流使用 HCL 语法。基于 Docker 的动作只能在 Linux 虚拟环境下运行。随着 CI/CD 发布，Github 发布了基于 JavaScript 的操作和新的 YAML 工作流语法。

对于 iOS 项目，您只能使用 JS 动作，因为它们应该在 macOS 上运行。此外，使用 JavaScript 动作简化了动作代码，并且比 Docker 容器动作执行得更快。

## 灯光！相机！Github 行动！

作为一个例子，我将使用[纹理样式](https://github.com/rosberry/texstyle)。这是一个格式化属性字符串的框架。先从一个完整的配置开始，一步步考察: