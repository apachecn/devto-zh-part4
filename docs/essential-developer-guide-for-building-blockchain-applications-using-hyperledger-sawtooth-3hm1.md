# 使用 Hyperledger 锯齿构建区块链应用的基本开发人员指南

> 原文：<https://dev.to/weg2g/essential-developer-guide-for-building-blockchain-applications-using-hyperledger-sawtooth-3hm1>

Hyperledger 锯齿是一个企业区块链平台，用于构建分布式分类帐应用程序和网络。设计理念的目标是保持分类帐的分布式，并使智能合同安全，特别是对于企业使用。锯齿通过将核心系统从应用领域中分离出来，简化了区块链应用开发。应用程序开发人员可以使用自己选择的语言来指定适合其应用程序的业务规则，而无需了解核心系统的底层设计。

锯齿也是高度模块化的。这种模块化使企业和财团能够做出他们最有能力做出的决策。锯齿的核心设计允许应用程序选择支持其独特业务需求的交易规则、许可和共识算法。

对于不熟悉 Hyperledger 项目的人[Hyperledger 家族和 Hyperledger 区块链生态系统简介](https://weg2g.com/application/touchstonewords/article-intro-to-hyperledger-family-and-hyperledger-blockchain-ecosystem.php)和 [Hyperledger 设计理念和框架架构](https://weg2g.com/application/touchstonewords/article-overview-of-hyperledger-design-philosophy-and-framework-architecture.php)文章强烈推荐。

Hyperledger 锯齿提供的功能如下:

*   **一个真正分布式的 DLT**:Hyperledger 锯齿区块链网络是由验证器节点组成的。该分类帐在所有验证器节点之间共享，并且每个节点具有相同的信息。他们参与管理网络的共识。
*   **耗时证明(PoET)共识及对大规模网络的支持** : Hyperledger 锯齿包含一个新颖的共识算法，PoET。PoET 是一种**拜占庭容错** ( **BFT** )共识算法，支持大规模网络，与工作证明算法相比，计算量最小，资源消耗更高效。PoET 由英特尔发明，利用名为**软件保护扩展** ( **SGX** )的特殊 CPU 指令集来实现中本聪式共识算法的扩展优势。每个节点等待一段随机时间，第一个完成的节点是领导者，并提交下一个块。
*   **快速交易性能** : Hyperledger 锯齿在全球状态下保持最新版本的资产，在每个网络节点上保持区块链的交易。这意味着您可以快速查找状态以执行 CRUD 操作，这提供了快速的事务处理。锯齿要求批量处理事务，并支持事务的并行调度。并行事务执行不仅加速了事务的执行，还正确处理了称为**未用事务输出** ( **UTXO** )的双重花费问题。
*   **支持多种语言**:锯齿支持多种编程语言中的事务家族(安全和智能契约)的实现，包括 Python、Go、Rust、Java 和 JavaScript。
*   **配置私有、公共和联盟区块链网络的能力**:

通过指定允许哪些节点加入验证器网络并参与共识，以及允许哪些客户端提交批处理和事务，可以为 Sawtooth 配置不同的权限来构建私有、联盟或公共网络。

在本文中，我给出了使用 Hyperledger 锯齿构建区块链应用程序的分步指南。要完成本文列出的教程，需要对区块链概念、JavaScript 和 Python 以及 Linux 操作系统的基本技能有很好的了解。

简而言之，我们遵循以下步骤:

*   在 AWS 这样的云服务上安装 Hyperledger 锯齿
*   配置锯齿验证器和 REST API
*   为事务系列设计名称空间和地址
*   实施事务系列
*   构建事务处理器
*   授予锯齿网络权限
*   使用锯齿 REST API 和 SDK 开发客户端应用程序

为了帮助区块链开发人员在 Hyperledger 锯齿开发中站稳脚跟，我编写了一系列实践教程来深入讨论每个主题，如下所示。

**安装** **Hyperledger 锯齿**
按照[安装并配合 Hyperledger 锯齿](https://myhsts.org/tutorial-learn-how-to-install-and-work-with-blockchain-hyperledger-sawtooth.php)的配方工作。

**配置锯齿验证器和 REST API**
遵循[在 AWS](https://myhsts.org/tutorial-learn-how-to-configure-hyperledger-sawtooth-validator-and-rest-api-on-aws.php) 配方上配置 Hyperledger 锯齿验证器和 REST API。

**为交易族设计名称空间和地址，实现交易族**
按照[为超账锯齿交易族设计名称空间和地址](https://developer.ibm.com/recipes/tutorials/designing-namespace-and-address-for-hyperledger-sawtooth-transaction-family/)的配方

**构建一个事务处理器并在锯齿网络上授予权限**
遵循[使用 Python SDK](https://developer.ibm.com/recipes/tutorials/building-transaction-handler-and-processor-for-hyperledger-sawtooth-with-python-sdk/) 方法为 Hyperledger 锯齿构建事务处理程序和处理器

**使用锯齿 REST API 和 SDK**
开发客户端应用，遵循[事务处理器和 Python Egg For Hyperledger 锯齿](https://myhsts.org/tutorial-learn-how-to-build-transaction-processor-as-a-service-and-python-egg-for-hyperledger-sawtooth.php)配方。

**关于作者**
本文由马特·赞德([高中技术服务](https://myhsts.org)的创始人)与 Brian Wu 合作撰写，Brian Wu 是[编码训练营](https://coding-bootcamps.com/)的高级区块链讲师。