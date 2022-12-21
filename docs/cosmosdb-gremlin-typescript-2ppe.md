# CosmosDB + Gremlin + TypeScript = :|

> 原文：<https://dev.to/dnoberon/cosmosdb-gremlin-typescript-2ppe>

在与 Gremlin 和 CosmosDB 合作仅两周之后，我正在写这篇文章。我写的东西可能是完全错误的。我真诚地希望如此，因为如果我错过了什么，以及在这种体验可能恢复之前我对 Azure 的一点点善意，我的工作会容易得多。

本文假设您对 TypeScript 有一定的了解，并对 Gremlin 和 CosmosDB 有一定的了解。我不会停下来解释 TypeScript 的好处或者 Gremlin 是什么以及它是如何工作的，但是我已经包含了到相关资源的链接。如果你觉得生疏了，可以用下面的文章复习一下。

*   【Gremlin 入门
*   [5 分钟内打字完毕](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
*   [CosmosDB Node.js 快速入门(Gremlin)](https://docs.microsoft.com/en-us/azure/cosmos-db/create-graph-nodejs)

谈正事。

# 玩家

*   **[Gremlin](https://tinkerpop.apache.org/gremlin.html)** -图遍历机和语言(你如何与一些图数据库沟通)
*   **[CosmosDB](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)**——微软 Azure 的多模型数据库，具体来说就是它的图形数据库产品
*   **[打字稿](https://www.typescriptlang.org/)**——JavaScript 的神智清醒的弟弟
*   **[Gremlin-JavaScript](http://tinkerpop.apache.org/docs/current/reference/#gremlin-javascript)**-Apache 的托管 Gremlin-JavaScript 实现

# TypeScript 和 Gremlin-JavaScript

在开始使用 CosmosDB 之前，我们需要纠正 Gremlin-JavaScript 包的 TypeScript 类型声明。不要跳过这一节，否则您将了解到当类型声明与它们的对应功能不匹配时会发生什么。

[@types/gremlin](https://www.npmjs.com/package/@types/gremlin) 包包含不正确的声明，目前是不完整的。我正在为官方计划做贡献，但这是一个缓慢的过程。同时，最好的选择是使用[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)来扩充和修正当前的类型声明。这是我更正的声明文件的一部分