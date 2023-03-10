# 以太坊:一个安全的分散通用交易分类账

> 原文：<https://dev.to/crisgarner/ethereum-a-secure-decentralised-generalised-transaction-ledger-41im>

## TL；速度三角形定位法(dead reckoning)

这是以太坊黄皮书的总结，包含了我个人的想法。你可以在这里阅读全文:[https://ethereum.github.io/yellowpaper/paper.pdf](https://ethereum.github.io/yellowpaper/paper.pdf)

* * *

首先，以太坊黄皮书技术性很强，只有在你想从头开始构建以太坊客户端的情况下才有用。如果你只想从概念或可用性的角度了解更多关于以太坊的知识，我建议你阅读白皮书:[https://github.com/ethereum/wiki/wiki/White-Paper](https://github.com/ethereum/wiki/wiki/White-Paper)

比特币的诞生只有一个目的，那就是以可信和分散的方式在同行之间进行价值交易。相反，以太坊的主要目标是促进个人之间的任何交易，否则在某些情况下，由于低效的法律制度、地理位置或腐败，这些人将无法相互信任。

以太坊的区块链在很大程度上受比特币的启发，它使用工作证明作为减少垃圾邮件的媒介。主要区别在于，它有一个保存信息的全局状态，通常以太坊被称为“世界计算机”，块由一系列交易组成，这些交易包含有关发送者、接收者、随机数(以防止双重交易)、发送量和气体的信息。

有两种类型的帐户，用户和智能合同(在现实程序中)，主要区别是智能合同没有私人和公共密钥，当它们被创建时，有与它们的执行相关的数据。智能合约可以由用户或其他智能合约执行，为了执行它们需要支付名为 **gas** 的执行费用，Gas 是一种保护机制，用于减少错误或恶意行为者的无限资源消耗，想要使用智能合约的行为者需要支付具有每块限制的 Gas，不允许无限循环和网络失速。因为 Gas 的实现，据说以太坊实现了一个准完全图灵机。

以太坊从一开始就被设计成区块链的赌注证明，但是技术还没有准备好，为了促进向赌注证明的迁移，它实现了一个名为难度炸弹的内部机制，这将增加挖掘的难度。

就像比特币一样，以太坊被设计成尽可能多的人可以访问，其理念是采矿将是从电到以太的简单交换。比特币有一个被称为 ASICs 的瘟疫，它使用专门的硬件来挖掘，以防止 ASICs 以太坊实现了一种不同的赌注证明算法，名为 Ethash contrast。

总之，我可以说比特币有更好的存储价值的结构，但对于去中心化应用和金融的实现，以太坊是最好的解决方案。