# 一个 CI/CD Hello World 管道:可能吗？

> 原文：<https://dev.to/ai101gh/a-ci-cd-hello-world-pipeline-is-it-possible-9dj>

我还没有使用完整的 CI/CD 管道的经验，所以我想我应该构建一个[“你好，世界！”](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program)一个。我意识到 CI/CD 是一个复杂的主题，它需要我花时间去学习和建立，但是我想尽我最大的努力使它保持简单...差不多吧。

可能有许多技术组合可以使用，但我想使用以下标准来缩小范围:

*   工具应该免费供个人使用——最好是开源的或者有社区版本。
*   工具应该在我的 Ubuntu 18.04 机器上运行。
*   工具应能在 Windows 和 Mac 上运行。

现在我是一个非常自信的 Bash 脚本编写者，我想我可以用 Bash 作为我的 rinky-dink(？！！)pipeline 但是我不想，因为我想玩得开心，想学到所有的东西！

所以我用谷歌搜索了一下，拼凑出了下面这条线:

*   用于源代码/配置控制的 Git 和 GitHub。
*   用于问题跟踪的 GitHub。
*   詹金斯运行构建和测试。
*   Docker 容器来运行 2 个 hello-world-ish 微服务中的每一个。
*   Kubernetes 将协调容器部署，让用户能够持续访问微服务。
*   我可能不得不使用 Virtual Box 来模拟多节点架构。

我意识到 Azure Devops 有一个看起来很棒的免费层。我知道 GitHub 正在开发一个 [CI/CD 管道](https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/)，里面有免费的(我想是)自主运行者。但我觉得更像是一个挑战。但是，如果事情失控，我保留改变主意的权利！

这就是大致的计划。请随时告诉我我缺少什么，我在哪里过度工具化，或者你会在评论中做什么不同。