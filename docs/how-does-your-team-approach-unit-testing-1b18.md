# 你的团队如何进行单元测试？

> 原文：<https://dev.to/ddaly/how-does-your-team-approach-unit-testing-1b18>

我为一家大型机构工作，但这是一个相当新的团队。我们的技术栈是基于 JavaScript 的(Node，Typescript，Angular，AWS Lambda)。作为我们团队的核心原则，我们努力在我们工作的任何项目中达到 100%的单元测试覆盖率。

虽然这通常是一个很好的实践，但在为多个项目这样做之后，我可以看到总是必须达到 100%的一些缺点。

例如:

*   必须完全模拟某些东西(数据库、AWS 服务等)...)就是为了打神奇的 100%这个数字。
*   有时添加测试只是为了确保代码被覆盖，而不是真正有用的测试。
*   拥有一个 100%覆盖的策略需要花费大量的时间和精力，但如果不添加 E2E 测试，实际上并不能确保应用程序端到端地工作。

根据经验，我认为一个具有高测试覆盖率的中间地带，但也包括端到端测试，似乎是测试应用程序的更好方法(尽管这也有挑战)。

我很有兴趣在这里听到其他的选择/意见，以及是否有其他的方法来确保测试覆盖率高，而且功能性强。