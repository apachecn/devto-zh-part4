# 用于测试智能合同的 Live Stellar 钱包

> 原文：<https://dev.to/edezekiel/live-stellar-wallet-for-testing-smart-contracts-1ei2>

[![](img/4c857e73c9391d9dab45339fcc747ecc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uB4D4Mqq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuNcG8cuQQI9zraR7tf5mdg.jpeg)

我非常高兴地宣布我的开源 Stellar testnet 钱包的 [Live](https://stellar-wallet.netlify.com/account) 版本。这是 [Github](https://github.com/edezekiel/stellar-wallet) 上的代码。

这是为恒星爱好者和任何人工作的恒星智能合同。下面，我将(1)解释这个工具为什么有用，(2)讨论一些关于 Stellar smart contracts 的背景信息，(3)简要回答我在开发过程中遇到的一些最具挑战性的问题，(4)简要说明如何使用这个工具，以及(5)列出一些我认为有用的资源。

**免责声明** : *我把它作为一个教育实验来构建，这个程序还没有经过彻底的测试。*

### 1。为什么要开发这个一流的工具？

我在区块链一家名为 [web3devs](https://web3devs.com/) 的开发公司工作。我们最著名的工作可能是以太坊智能合约，但我们也与恒星智能合约合作！

尽管 Stellar.org 解释了带时间锁的多签名托管账户的概念，并提供了创建多签名账户的示例代码，但是他们并没有展示如何实现时间锁和恢复方法。**在这篇文章中，我发布了那段代码和它如何工作的现场演示。**

### 2。背景资料

大多数应用程序通过 Horizon 与 Stellar 网络交互，Horizon 是一个 RESTful HTTP API 服务器。参见[Stellar.org](https://www.stellar.org/developers/guides/get-started/)。您使用 SDK 与 Horizon 进行交互。我用的是 Javascript SDK。

以太坊和恒星智能合约有显著区别。首先，以太坊智能合约是用 Solidity 编写的，这是一种图灵完全语言。

相比之下，恒星智能合约只能完成有限的一组任务。你可以使用 JavaScript 这样的通用编程语言，或者与 Horizon 交互，Horizon 是 Stellar Core 和想要访问 Stellar 网络的应用程序之间的接口。

### 3。有害的错误和棘手的问题

#### 为什么我的交易一直失败(从 Horizon 得到 400 响应)？

*   您没有设置基本费用:

```
const baseFee = await server.fetchBaseFee();

const transaction = new StellarSdk.TransactionBuilder(account, {
 fee: baseFee }) 
```

*   您在建立交易之前没有加载帐户:

```
const account = await server.loadAccount(sourceKeys.publicKey());

const transaction = new StellarSdk.TransactionBuilder(account, { fee: baseFee }) 
```

#### 为什么我无法向 Horizon 提交解锁 XDR？

您可能试图过早提交 XDR。解锁 XDR 只能在锁定期结束后*提交。这有点令人困惑，因为您必须在*锁定期结束之前创建解锁事务*。*

解决方案是签署解锁事务，并将该事务公开保存在某个地方。仅在锁定期结束后向 Horizon 提交解锁 xdr。

```
try {
 // Save as an XDR string
 const transactionXDR = transaction
 .toEnvelope()
 .toXDR()
 .toString("base64");
 console.log("FN: unlock", "Success! Results:", transactionXDR);
 return transactionXDR; 
```

#### 如何设置时间界限？

找出设置时间界限的语法并不容易。以下是最终对我有效的方法:

```
// The unlock date (D+T) is the first date that the unlock transaction can be
 // submitted. If Transaction 3 (this transaction) is submitted before the
 // unlock date, the transaction will not be valid.

const transaction = new StellarSdk.TransactionBuilder(escrowAccount, {
 fee: baseFee,
 timebounds: {
 minTime: (
 Math.floor(Date.now() / 1000) + parseInt(unlockTx.unlockDate)
 ).toString(),
 // The maximum time is set to 0, to denote that the transaction does not have
 // an expiration date.
 maxTime: (0).toString()
 },
 sequence: (parseInt(escrowAccount.sequence) + 1).toString()
 }) 
```

### 4。说明

为了使用这个工具，你需要创建至少两个 testnet 帐户。您可以通过点击“创建重要帐户”按钮在工具中创建新帐户。只需注意标题中弹出的公钥和私钥。您也可以使用这些预制的测试帐户:

#### 卖方/目的地

公开金钥:gchvxwblcpwrmaf 7 qqnkpb 22 JX 6 vytmx 2 jq 5 xavmem 6 PD 5 hrhf 42 ezc 秘密金钥:sai 2 uo47 oba le 4 yauvpz3 qn 3 ltype 5 cxp 3 megqr 64 owvslt 6 m7q 2 fk

#### 采购员

公开金钥:gdeju 3 bk apmdp 3 saxe 5 C5 T2 ie 6363 zszmr 7 ss 3 yztoqjz 3 l24 y jsz 4g 秘密金钥:sdcs 7 ucinwvfunhrzruu 5 wq 7 zaifxqned 4 ibq 2 U6 和 45W72U22ST

**注意:**这些是测试账户，所以不涉及真金白银。你不应该在公开的 stellar 服务器上发布一个账户的密钥。

您可以将一个帐户想象为“卖方”(目的地)，将另一个帐户想象为“买方”。买方创建托管账户，并将卖方(目的地)添加为签名人。当锁定期到期时，卖家可以使用 XDR 来“解锁”托管账户中的资金。

### 5。资源:

以下是一些我认为有用的资源:

*   恒星实验室:我经常打开这个标签来测试各种操作、交易等。
*   米歇尔·穆尔德斯文章:帮助你更熟悉账户签署者的概念。
*   Sylvain Faucherand 文章:介绍了一个基本的托管账户，但没有说明如何对其进行时间锁定。
*   [罗伯特·德斯特文章:](https://hackernoon.com/i-just-wrote-a-stellar-smart-contract-pt-2-lets-dive-a-little-deeper-a8dae19b9d0a)如果你还在为账户签名挠头。