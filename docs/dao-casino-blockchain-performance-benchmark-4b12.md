# 道贝特(前——道。赌场)区块链性能基准

> 原文：<https://dev.to/daobet/dao-casino-blockchain-performance-benchmark-4b12>

加入 DAOBet 电报组的讨论:

*   [国际社会【EN】](https://t.me/daobet)
*   [验证者群聊](https://t.me/daobet_validators)
*   [游戏开发者群聊](https://t.me/daobet_developers)

加入我们的社交媒体，了解所有最新消息: [Twitter](https://twitter.com/daobet_org/) 、[脸书](https://facebook.com/DAObet.org/)和 [LinkedIn](https://linkedin.com/company/daobet/) 。

自从推出我们的 TestNet 以来，我们一直在进行复杂的测试，以确保我们的区块链项目继续展示最高标准的可靠性和性能。

今天，我们想分享我们最近的一次负载测试的积极结果——在 TestNet 中执行。

当前的 TestNet 设置包括 21 个产生块的验证器节点，外加 6 个额外的全节点。运行节点的虚拟机硬件配置如下: **4 个 vCPU，8Gb RAM。**

我们使用定制的测试实用程序来生成随机事务，并将它们分布在全节点中。这模拟了验证器节点不可公开访问的真实场景。

在基准测试期间，我们能够在该测试环境中记录以下指标:

每秒事务数: **1000**
平均块终止延迟-块之间的秒数被标记为**最后不可逆的** (LIB 图表)-即成为最终: **1.1** 秒(偶尔峰值不超过 2 秒)
节点平均 CPU 利用率: **49%**

[![](img/431aa989db3846877325acb56757fa61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h5NA-6gr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbokj2r4ktp41rzmc256.png)

显而易见，由于我们突破性的 RANDPA consensus 算法具有超高速终结性，我们能够实现**更大的事务吞吐量**，同时仍然实现**最小终结延迟**。

请注意，虽然我们试图创建尽可能逼真的场景，但我们当前的结果并不完全反映真实世界的情况，因为我们的 TestNet 机器是在中级硬件上配置的。

请随时关注我们的进展，因为我们将继续发布我们的基准测试结果，作为我们未来发展进度和改进的里程碑。

加入 DAOBet 电报组的讨论:

*   [国际社会【EN】](https://t.me/daobet)
*   [验证者群聊](https://t.me/daobet_validators)
*   [游戏开发者群聊](https://t.me/daobet_developers)

加入我们的社交媒体，了解所有最新消息: [Twitter](https://twitter.com/daobet_org/) 、[脸书](https://facebook.com/DAObet.org/)和 [LinkedIn](https://linkedin.com/company/daobet/) 。