# 区块链以太坊开发指南从 A 到 Z

> 原文：<https://dev.to/weg2g/blockchain-ethereum-development-guide-from-a-to-z-22fb>

以太坊是一个通用的区块链，适合通过高级脚本描述业务逻辑，也称为智能合同。以太坊被设计为一个更广阔的视野，作为一个试图结合区块链力量的分散式或世界计算机，作为一个信任机器，具有图灵完全契约引擎。虽然以太坊借用了许多最初由比特币引入的思想，但两者之间存在许多分歧。Solidity 是一种面向对象的高级语言，用于实现智能合同。智能合约是在以太坊状态下管理账户行为的程序。Solidity 受到了 [C++](https://weg2g.com/application/touchstonewords/article-what-is-c-object-oriented-programming-language-and-how-and-where-to-use-it.php) 、 [Python](https://weg2g.com/application/touchstonewords/article-what-is-python-programming-language-and-how-and-where-to-use-it.php) 和 [JavaScript](https://weg2g.com/application/touchstonewords/article-what-is-javascript-programming-language-and-why-it-is-very-popular.php) 的影响，旨在针对以太坊虚拟机(EVM)。Solidity 是静态类型的，支持继承、库和复杂的用户定义类型等特性。有了 Solidity，你可以为投票、众筹、盲目拍卖和多重签名钱包等用途创建合同。
以太坊虚拟机和智能合约是以太坊的关键元素，并构成其主要吸引力。在以太坊中，智能合约代表一段用高级语言(Solidity，LLL，Viper)编写的代码，并作为字节码存储在区块链中，以便在每个节点中，一旦被调用，就可以在基于堆栈的虚拟机(以太坊虚拟机)中可靠地运行。与智能合约功能的交互通过区块链网络上的事务发生，它们的有效负载在以太坊虚拟机中执行，共享的区块链状态相应地更新。

对于不熟悉区块链技术的人阅读[从比特币看区块链技术的历史和演变](https://myhsts.org/tutorial-history-and-evolution-of-blockchain-technology-from-bitcoin.php)文章强烈推荐。此外，如果您希望学习和练习 Hyperledger 区块链开发，请访问[全面的 Hyperledger 培训教程](https://myhsts.org/tutorial-comprehensive-blockchain-hyperledger-developer-guide-for-all-professional-programmers.php)页面，以获得我们的 Hyperledger 教程文章的概要。

我们已经写了两组食谱来详细探讨以太坊和坚固性编码。第一组包括以下九个教程:

*   [用 DApps 和以太坊 VM 开发以太坊区块链简介](https://myhsts.org/tutorial-learn-about-ethereum-blockchain-development-with-dapps-and-ethereum-vm.php)
*   [用以太坊和 Solidity 编程语言构建拍卖 DApp](https://myhsts.org/tutorial-learn-how-to-build-auction-dapp-with-ethereum-and-solidity-programming-language.php)
*   [通过 Remix IDE 使用以太坊区块链应用](https://myhsts.org/tutorial-learn-how-to-work-with-ethereum-blockchain-applications-through-remix-ide.php)
*   [为以太坊拍卖 DApp](https://myhsts.org/tutorial-learn-how-to-build-bidding-form-in-web3js-for-ethereum-auction-dapp.php) 在 Web3js 中建立投标表格
*   [与 web3js API 和 JSON 合作构建以太坊区块链应用](https://developer.ibm.com/recipes/tutorials/working-with-web3js-api-and-json-to-build-ethereum-blockchain-applications/)
*   [管理以太坊智能合约的部署环境](https://dev.to/weg2g/deployment-environments-for-managing-ethereum-smart-contracts-16ha)
*   [通过 Golang 与 Geth 一起使用以太坊专用网络](https://myhsts.org/tutorial-learn-how-to-work-with-ethereum-private-network-with-golang-with-geth.php)
*   [使用 Solidity 编译器编译和部署以太坊契约](https://myhsts.org/tutorial-learn-how-to-compile-and-deploy-ethereum-contracts-using-solidity-compiler.php)
*   [运行以太坊拍卖 DApp 和坚固性提示](https://myhsts.org/tutorial-learn-how-to-run-ethereum-auction-dapp-with-some-solidity-tips.php)

总之，您将学习如何设置和配置以太坊，以及如何使用 Solidity 编码语言开发区块链应用程序。我们探索其基本组件，如智能合同和 Web3。JS API 通过拍卖分散应用程序(DApp)一步一步。在第二组中，我们将讨论以太坊区块链开发和可靠性中更高级的主题，同时一步一步地构建一个托尼·DApp 游戏。特别是，我们讨论松露和毛毛雨。例如，我们向您展示了 Truffle 这样的工具如何成为构建、测试、调试和部署 DApps 的助手。简而言之，我们将讨论四个主要话题:

*   探索松露套房
*   学习 Solidity 的高级特性
*   合同测试和调试
*   使用毛毛雨构建用户界面

第二套包括如下 8 种配方:

*   [安装 Truffle 并设置 Ganache，为 Tontine DApp 游戏编译以太坊智能合约](https://myhsts.org/tutorial-learn-how-to-install-truffle-and-setup-ganache-for-compiling-ethereum-smart-contracts-for-tontine-dapp-game.php)
*   [运行 Tontine 以太坊 DApp 游戏合同](https://myhsts.org/tutorial-learn-how-to-run-tontine-ethereum-dapp-game-contract.php)
*   [设计 Tontine 以太坊 DApp 游戏界面](https://myhsts.org/tutorial-learn-how-to-design-tontine-ethereum-dapp-game-interfaces.php)
*   [通过托尼·DApp 游戏契约以太坊和实体之间的互动](https://myhsts.org/tutorial-learn-about-blockchain-contract-interactions-between-ethereum-and-solidity-via-tontine-dapp-game.php)
*   [在托尼·DApp 游戏中进行块菌单元测试](https://developer.ibm.com/recipes/tutorials/work-with-ethereum-solidity-and-truffle-unit-testing-in-tontine-dapp-game/)
*   [在托尼·DApp 游戏中调试松露和以太坊混音](https://dev.to/weg2g/debugging-with-truffle-and-ethereum-remix-in-tontine-dapp-game-20mi)
*   [用毛毛雨为 Tontine DApp 游戏构建前端应用](https://myhsts.org/tutorial-learn-how-to-build-frontend-blockchain-application-for-Ethereum-dapp-game-with-drizzle.php)
*   [跑步和玩 Tontine 以太坊 DApp 游戏](https://myhsts.org/tutorial-learn-how-to-run-and-play-tontine-ethereum-dapp-game.php)

作为一个提醒，理解和完成第一组食谱之前，需要工作的第二个。

这里还有一些实践教程，涵盖以太坊区块链开发中的高级主题:[使用以太坊和 Quorum 构建企业区块链即服务应用](https://blockchain.dcwebmakers.com/blockchain-as-a-service-apps-using-ethereum-and-quorum.html)，[在以太坊区块链应用中利用分布式存储 IPFS 和 Swarm 的力量](https://blockchain.dcwebmakers.com/work-with-distributed-storage-ipfs-and-swarm-in-ethereum.html)，以及[区块链开发人员指南——如何通过区块链 Oracles 使用 Java 和 Web3J API 构建以太坊金融应用](https://myhsts.org/ethereum-financial-app-with-java-and-web3j-api-through-blockchain-oracles.html)

**关于作者**
本文由马特·赞德([高中技术服务](https://myhsts.org)、[哈希流](https://hashflow.us)和[编码训练营](https://coding-bootcamps.com)的创始人)与 DC Web Makers 的顾问布莱恩·吴合作撰写。