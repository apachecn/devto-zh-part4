# 当地奖励(LocationCore)和 NEM

> 原文：<https://dev.to/danvoyce/locally-reward-locationcore-nem-17pb>

这是我们写的第一篇讨论本地奖励发展的博客，讲述了我们在 NEM 发展的经历。

如果你不知道，NEM 是一个区块链，它有自己的本土货币 XEM，它被设计成这样一种方式，用户可以通过使用 API 与它交互(大多数情况下，稍后会有更多的介绍…)，这意味着你可以使用你熟悉的工具和环境，而不是晦涩难懂和经常未经测试的语言(例如 Solidity)。

NEM 与以太坊有些不同。例如，根据我的经验，NEM 被设计为一个开发平台，它目前不支持特定的智能合约，而是将功能和基础设施留给开发人员，这非常适合您的业务运行速度，因为您可以使用 NEM 这样的平台将您的部分业务迁移到区块链，而不会有太多麻烦。

## 那么本地奖励到底在发展什么呢？

简而言之——**所有的事情**。这是很棒的几个月，就我个人而言，我喜欢一个团队处于纯粹的创造空间，我们有一个很棒的团队来开发这个产品。

然而，产品并不仅仅是本地奖励最初发布的有形应用程序。我们聘请了区块链总监、数据科学家、平台开发者、更多的 app 开发者和 QA 工程师。本地奖励是一个更大的产品的一部分，所有部分都需要协同工作。

因此，对于这些更新，我将重点关注我在开发方面的一些经验，而将移动术语留给我的其他开发人员撰写的文章。

## 可信 NIS 服务器、移动应用和私钥

最近我们不得不克服的最大障碍是关于本地奖励的支付方法。我们从一开始就决定，本地奖励应用程序将生成一个与 NEM 的 NanoWallet 兼容的钱包，这对我们来说既是一个战略决策，也是一条捷径。对于这个本地奖励测试版应用程序，我们决定向技术和非技术用户推出它，因此使用 NanoWallet 生成工作钱包的决定比让应用程序处理私钥更可取，从而避免了使用密钥在 NanoWallet 上设置工作钱包的复杂过程。这也意味着用户将可以立即获得他们的资金和所有的马赛克，马赛克只有在拥有足够的 XEM 支付交易费用的情况下才能转让，所以这也解决了这个问题。

这样做的额外好处是，因为我们将发起区块链交易的责任从设备上转移，我们现在可以将该帐户视为我们系统中的“仅接收”参与者，这意味着我们可以使用 NIS 来签署和广播这些交易，而不是试图在本地签署该交易并遇到“费用”问题。

(NB: NEM2 / Catapult 将有一个很棒的功能，请求者可以选择谁支付费用——我们的最终目标是在 Catapult 公共链准备就绪时使用该功能！)

这再次在其他方面引入了复杂性，因为主要的本地奖励帐户现在将启动交易，以将马赛克转移给用户，应用程序需要与某些东西交谈，让我们知道支付用户多少钱以及何时支付！到目前为止，我们已经设法避免了任何基础设施或数据流的集中化，但由于移动应用程序无法运行本地 NIS 节点，在这种情况下没有太多选择——所以从技术角度来看，我们如何在不增加可以水平扩展以处理未来流量的服务器集群的情况下处理这一问题？

## 输入λ

对于那些不知道的人来说，无服务器编程是下一件大事(显然？)，就我个人而言，我还没有爱上它，但它为特定的用例提供了一些很好的解决方案，在这种情况下，它是“我们如何才能在不花费大量服务器的情况下生产一个无限可扩展的 API？”

### λ函数

注意:我不是一个 Javascript 程序员，但是自从这篇文章写出来之后，我已经做了很多 Node 的工作，我知道下面的回调有多糟糕...

下面的函数基本上允许我们建立交易，并根据用户发送给我们的数据的数量和类型来奖励用户。这将按预定的时间间隔运行(具体时间尚未确定)。

```
exports.handler = function (event, context, callback) {

    // Sanity check the data
    if (sanityCheck(event)) {

        var nem = require("nem-sdk").default;
        var crypto = require('crypto');

        var endpoint = nem.model.objects.create("endpoint")('http://ournode', nem.model.nodes.defaultPort);
        var common = nem.model.objects.create("common")("", "privatekeyfromenv");
        var mosaicDefinitionMetaDataPair = nem.model.objects.get("mosaicDefinitionMetaDataPair");
        var transferTransaction = nem.model.objects.create("transferTransaction")(event.address, 0, "Test " + Date.now());
        var mosaicAttachment = nem.model.objects.create("mosaicAttachment")("LOCALLY Reward", "lcc", event.amount * 100);

        transferTransaction.mosaics.push(mosaicAttachment);

        // Need mosaic definition of LOCALLY Reward:lcc to calculate fees, so we get it from network.
        nem.com.requests.namespace.mosaicDefinitions(endpoint, mosaicAttachment.mosaicId.namespaceId).then(function (res) {

            var neededDefinition = nem.utils.helpers.searchMosaicDefinitionArray(res.data, ["lcc"]);
            var fullMosaicName = nem.utils.format.mosaicIdToName(mosaicAttachment.mosaicId);

            if (undefined === neededDefinition[fullMosaicName]) return console.error("Mosaic not found !");

            mosaicDefinitionMetaDataPair[fullMosaicName] = {};
            mosaicDefinitionMetaDataPair[fullMosaicName].mosaicDefinition = neededDefinition[fullMosaicName];

            // The difficulty with fee calculation is that it needs the current supply, many libraries out there are incorrectly coded to use
            // the initial supply
            nem.com.requests.mosaic.supply(endpoint, nem.utils.format.mosaicIdToName(mosaicAttachment.mosaicId)).then(function (res) {

                mosaicDefinitionMetaDataPair[nem.utils.format.mosaicIdToName(mosaicAttachment.mosaicId)].supply = res.supply;

                nem.com.requests.mosaic.supply(endpoint, fullMosaicName).then(function (res) {

                    mosaicDefinitionMetaDataPair[fullMosaicName].supply = res.supply;

                    var transactionEntity = nem.model.transactions.prepare("mosaicTransferTransaction")(common, transferTransaction, mosaicDefinitionMetaDataPair, nem.model.network.data.testnet.id);

                    // Transaction is broadcast here
                    nem.model.transactions.send(common, transactionEntity, endpoint).then(function (res) {
                        response = {
                            statusCode: 200,
                            body: JSON.stringify(res)
                        };
                        callback(null, response);
                    });

                }, function (err) {
                    console.error(err);
                });
            }, function (err) {
                console.error(err);
            });
        }, function (err) {
            console.error(err);
        });
    }    
}; 
```

对 Lambda 函数的访问将通过 Amazon Cognito 用户池来处理。

## 用户如何获得奖励，接受哪些数据？

[本地奖励在收集点奖励用户](https://locally.io/products/reward/)，在这种情况下，设备将缓存准备发送到本地奖励的数据，应用程序将在预定的时间间隔内这样做(我们正在考虑测试版每天都有，但可能每周一次，当它是活的)。

一旦数据准备好发送，就会发生几件事:

设备维护了(从设备、脸书等)收集的数据的滚动哈希，该哈希在截止点“完成”，因此用户可以将区块链交易的哈希与他们之前发送的数据块进行比较，以确保他们已经支付。

事务以这个散列作为消息来启动

用户可以通过比较哈希来查看和验证他们的数据是否已经付费。

<colgroup><col> <col></colgroup> 
| ![](img/0a046a7d9077b25b8d025c7740c132fe.png) | 首席技术官丹尼尔·沃伊斯Daniel 负责本地的技术战略、架构和开发。他把澳大利亚墨尔本作为自己的家，是一名卓有成就的技术领导者，在定义和开发数字路线图、高科技系统和框架以及软件平台方面拥有丰富的经验。 |