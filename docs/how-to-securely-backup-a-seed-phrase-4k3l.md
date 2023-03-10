# 如何安全地备份种子短语

> 原文：<https://dev.to/wagslane/how-to-securely-backup-a-seed-phrase-4k3l>

许多比特币和加密货币领域的新来者都对持有自己的私钥感兴趣。众所周知，[不是你的钥匙也不是你的硬币](https://www.youtube.com/watch?v=dnC5mFaIW3Q)。

## 危险

[![](img/c1163adae833c642d279f77b05b3b210.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dgAUY2rR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/08/photo-1555699875-5773b06e8ee2-1024x768.jpeg)

为了消费比特币，用户必须能够访问他们的[钱包](https://bitcoin.org/en/choose-your-wallet)或与钱包一起创建的[种子短语](https://en.bitcoin.it/wiki/Seed_phrase)。同样，攻击者只需要接触到这些东西中的一个就可以窃取硬币。

加密种子实质上是加密钱包的主密钥，通常为 12 或 24 个字的形式。如果钱包丢失，种子可用于重新生成钱包中的每个私钥。

关于如何选择一个好钱包的文章很多。不管钱包，总会有一个种子短语需要安全备份。为此，我们将讨论用户拥有的 som 选项。

## 选项

像选择钱包一样，选择存储种子短语的方法略微取决于用户的技术能力和个人需求。以下是一些不同的选项及其利弊:

*   纸
    *   赞成的意见
    *   不能被数字黑客入侵
    *   自由的
    *   骗局
    *   容易毁坏(被狗吃掉、烧掉、浸湿等)
    *   容易丢失
    *   可以从身体上被偷走
    *   随着存储更多种子短语的需求的增加(不同的钱包有多个种子短语)，漏洞也会增加
*   金属——蚀刻成钢，或类似 [CryptoSteel](https://cryptosteel.com/?gclid=EAIaIQobChMIhfOt-MSr5AIVEKrsCh3ubwXpEAAYAiAAEgLqy_D_BwE) 的工具
    *   赞成的意见
    *   难以摧毁
    *   不能被数字黑客入侵
    *   骗局
    *   潜在昂贵
    *   难以设置
    *   容易丢失，很难复制
    *   可以从身体上被偷走
    *   随着存储更多种子短语的需求增加，脆弱性和成本也增加。(不同钱包的多个种子短语)
*   q vault–数字密码管理器
    *   赞成的意见
    *   不易丢失–加密保管库易于复制并存储在多个设备上。
    *   难以销毁–包含 vault 文件的每个设备都需要销毁
    *   难以窃取-攻击者需要保险库文件、主密码和 Qvault 密钥卡(如果适用)
    *   可以离线使用，以确保黑客几乎不可能
    *   Qvault 卡可用于添加额外的加密密钥和主密码恢复选项。
    *   免费和开源
    *   骗局
    *   易受恶意软件攻击–如果在连接到互联网并包含特定恶意软件的设备上打开了保管库，则种子有可能被破坏。

对于如何存储种子短语，每个人和组织都有不同的偏好。影响这一决定的因素包括存储的密码量、所有者的技术能力以及对不便的容忍度。

我们构建 Qvault 是为了提供一个免费的开源选项，吸引许多典型用户。Qvault 提供了一个用户友好且易于使用的选项，在安全性、可恢复性和易用性之间取得了很好的平衡。也就是说，Qvault 仍处于测试阶段，并且每天都在改进。让我们知道我们能做些什么来使它变得更好！

莱恩·瓦格纳

下载 q vault:[https://q vault . io](https://qvault.io)

星我们的 Github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

帖子[如何安全备份种子短语](https://qvault.io/2019/08/30/how-to-securely-backup-a-seed-phrase/)最早出现在 [Qvault](https://qvault.io) 上。