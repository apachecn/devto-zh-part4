# BIP 32 手表钱包

> 原文：<https://dev.to/wagslane/bip-32-watch-only-wallets-cme>

在我看来，比特币改进提案 32 是我们拥有的最重要的 bip 之一。(感谢[彼得·维尔](https://twitter.com/pwuille?lang=en)！)BIP 32 给了我们层次确定性钱包，也就是从一粒种子创造出一个密钥树的能力。

在比特币的早期，每次用户想要收到新的硬币时，他们的钱包都会随机生成一个新的比特币私钥，以及相关的公钥和地址。例如(并且不要试图使用这些):

私钥:**l57 hxx ktcrgjtymvbhaxnsk wzdzrey6d6 aqqrm 39**

地址(公钥哈希):**1 P3 azkwvv 1 jdjahebqby 6873 xwjvymzdrn**

每个用户的钱包软件都包含公钥和私钥对的列表。如果用户的计算机损坏或被盗，所有的私钥都会丢失。用户必须相当精通技术，知道如何导出和加密他们的密钥以安全保存，但即使如此，每次他们收到新地址的硬币，他们都需要重新备份新的私钥。

## 高清钱包来了

根据 BIP 32，新的钱包称为高清钱包(分层确定性，而不是高清晰度)，使备份过程更容易和更安全。

HD 钱包不是为每笔交易生成随机私钥，而是生成一个[种子](https://bitcoin.org/en/glossary/hd-wallet-seed)，用于创建主私钥。这个主私钥可以用于为每个事务生成更多的“子”私钥。因为每次使用相同的模式来确定子密钥，所以用户不需要备份他们的所有私钥，只需要备份一个种子:

**000102030405060708090 a 0 b 0 c 0d 0 e 0 f**

然而，由于大多数钱包也采用了 [BIP 39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki) 你可能更熟悉 12 或 24 个字的种子编码(同样不要使用这个例子):

**泡泡猫跑快乐树鸟雪花人橙黄妈妈**

现在，在一张纸上或者像 Qvault T1 这样的密码管理器中，用户可以备份他们的种子短语，并且知道所有将被存储在他们钱包中的比特币都是可恢复的。

## 只看高清钱包

[![](img/68450e6144c7b061783ddb264f9b9f7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0mJ8RS8L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/07/icon95-20-512.png)

只有手表的高清钱包和普通的高清钱包是一样的，除了它不能消费硬币，只能储存硬币。仅手表钱包非常适合那些希望钱包可以轻松接收新硬币，但又不想定期从钱包中消费的用户，类似于储蓄账户。

种子(和相关的私钥)被离线或高度加密存储，而[扩展公钥](https://www.google.com/search?q=bitcoin+xpub&oq=bitcoin+xpub&aqs=chrome.0.0j69i60j0l4.2127j0j7&sourceid=chrome&ie=UTF-8)被用于在线仅观看钱包。

一个全手表专用的钱包比一次又一次地使用同一个地址要好得多，也安全得多，因为使用新地址增强了隐私。

## Qvault BIP 32 计划

我们目前正在对 Qvault 进行更新，允许用户在保险库中生成比特币种子和扩展公钥。下面是一个高安全性的例子，说明如何创建一个完全数字化的(因此容易恢复和复制)储蓄钱包:

*   [在计算机上下载并安装 q vault](https://qvault.io)(q vault 可以离线使用，从源代码构建以获得最大的安全性)。
*   下载并安装一个只能看手表的钱包(我们推荐 [electrum](https://bitcoinelectrum.com/creating-a-watch-only-wallet/) 或 [samourai 的哨兵](https://samouraiwallet.com/sentinel)

<figure>[![](img/03dd01c52760d2baacc07b5b1ea4b780.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XgkCexAe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/07/electrum-logo-.png) 

<figcaption>驻极体</figcaption>

</figure>

<figure>[![](img/4201a3c103732219bcc95198e65a02ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3l6fgx8N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/07/sentinel.png) 

<figcaption>萨摩赖哨兵</figcaption>

</figure>

*   使用 Qvault 生成新种子及其关联的 XPUB(旧地址)、YPUB 或 ZPUB (segwit 地址)。
*   将 XPUB、YPUB 或 ZPUB 导入您的手表专用钱包。
*   确保你的加密。 *qvault* 文件备份在某个安全的地方，最好是在多个存储设备上或者 qvault 的云存储上。

现在，您有能力安全地接收比特币，您的种子仅存储在加密的保险库文件中，只有您有能力打开该文件。当你最终想花掉你的手表钱包里的硬币时，你只需将种子导入一个普通的比特币钱包。

感谢您的阅读，祝您好运，并保持安全，成为您自己的银行！