# 台湾和香港需要我们的帮助

> 原文：<https://dev.to/xenoxdev/taiwan-hong-kong-need-our-help-3le4>

大家好。希望你们都过得好。

今天，我想谈一件严肃的事情。这是我们所有人都关心的事情，但它经常被忽视，因为我们中的许多人选择不关心。

我想谈谈开发人员群体中缺乏政治意识的问题。

在过去的几个场合中，这个问题困扰了我好几次，但是这次一个朋友引起了我的注意，我不得不写这篇文章。

[![yoren image](img/6968f1b5ef0ce29b71aec8fdacdd1547.png)](/yoren)

## [张友仁](/yoren)

约仁是我二月份去台湾旅行时认识的一个朋友。她是一名台湾开发人员和 Wordpress 专家，她是希望保护台湾民众权利和身份安全的声音社区的一员。

## 那么发生了什么？

这一切都是从 WooCommerce 上的某个公关产生的时候开始的。

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)  #24237 添加港、台为中国各州；修正澳门名称 #24425](https://github.com/woocommerce/woocommerce/pull/24425) 

[![nobodxbodon avatar](img/106a8a5ba448968c5f2d73fdc09171bd.png)](https://github.com/nobodxbodon) **[nobodxbodon](https://github.com/nobodxbodon)** posted on [<time datetime="2019-08-18T22:16:32Z">Aug 18, 2019</time>](https://github.com/woocommerce/woocommerce/pull/24425)

### 所有投稿:

*   [ ]您是否遵循了 [WooCommerce 投稿指南](https://github.com/woocommerce/woocommerce/blob/master/.github/CONTRIBUTING.md)？
*   [x]你的代码遵循了编码标准吗？
*   [x]您是否已经检查过，以确保没有其他针对相同更新/变更的打开的[拉取请求](../../pulls)？

### 在此提出变更拉动请求:

关闭#24237。实际上它已经关闭了，但是正如在[评论](https://github.com/woocommerce/woocommerce/issues/24237#issuecomment-516976282)中提到的，中国国家清单中似乎缺少了几个项目。根据 [ISO 3166](https://www.iso.org/obp/ui/#iso:code:3166:MO) 的说法，澳门的拼写似乎有一个错别字。

### 如何测试这种拉动请求的变化:

### 其他信息:

*   [ ]您是否解释了您所做的更改，以及为什么您希望我们包含这些更改？
*   [ ]您是否为您的更改编写了新的测试(如果适用)？
*   [ ]您是否成功地在本地运行了包含您的更改的测试？

### 变更日志条目

> 输入此拉取请求的所有更改的摘要。如果被接受，这将出现在变更日志中。

[View on GitHub](https://github.com/woocommerce/woocommerce/pull/24425)

快速浏览一下，我们大多数人可能不会认为公关有什么问题。毕竟，dev 只是试图遵循 ISO 3166 准则，对吗？在讨论中，你会看到他们反复提出这一点。

你可以在下面的帖子中看到 Yoren 和她的台湾开发伙伴们反对它，他们已经清楚地阐明了为什么他们认为接受 PR 会有问题。

但问题远不止于此。我们检查了 PR 提交者的个人资料，发现了一个名为 One-China 的存储库。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ nobodxbodon ](https://github.com/nobodxbodon) / [一个中国](https://github.com/nobodxbodon/one-China)

### Open source projects that contain contents inconsistent with the "one China" principle

<article class="markdown-body entry-content container-lg" itemprop="text">

### The following open source projects are inconsistent with the "one China" principle, so please use them carefully.

下面的开源项目有不符合“一个中国”政策的内容，请谨慎使用。

**Note: for the time being, I will only negotiate** the items that clearly indicate that the data comes from [T1】 ISO 3166 【T2].

### 交涉中交流

| project | 相关发行/公关 | Brief description of problems |
| --- | --- | --- |
| [woocommerce](https://github.com/woocommerce/woocommerce) | [添加香港、台湾为中国州；固定澳门名称](https://github.com/woocommerce/woocommerce/pull/24425) | Hong Kong |
| [mle doze/国家](https://github.com/mledoze/countries) | [请删除台湾的“中国省”](https://github.com/mledoze/countries/issues/4) | platform |
| [巴斯蒂安/bStats](https://github.com/Bastian/bStats) | [在 ISO 3166 数据中使用简称](https://github.com/Bastian/bStats/pull/99) | platform |
| [自动/wp-calypso](https://github.com/Automattic/wp-calypso) | [统计数据:台湾应该被列为中国的一个省，还是作为一个单独的国家(现状)](https://github.com/Automattic/wp-calypso/issues/35896) | platform |

### 尚未交涉尚未开始通信

| project | Brief description of problems |
| --- | --- |
| [fzaninotto/Faker](https://github.com/fzaninotto/Faker) | Chinese language pack is correct. Taiwan is in the list of countries with other language packs, and [Hong Kong and Taiwan](https://github.com/fzaninotto/Faker/blob/fb218ada627f7c750c6e0e41cae486d2db48e911/src/Faker/Provider/ja_JP/Address.php#L15) is in the list of countries with Japanese. |
| [joke2k/faker](https://github.com/joke2k/faker) | be the same as the above |

### To be confirmed

| project | potential problem | 相关发行/公关 |
| --- | --- | --- |
| [Drupal](https://www.drupal.org) | platform | [从 ISO-3166-1 国家数据切换到 CLDR unicode 数据](https://www.drupal.org/project/drupal/issues/1938892) |
| [DiUS/java-faker](https://github.com/DiUS/java-faker) | platform | [固定 TW 地址](https://github.com/DiUS/java-faker/pull/429) |
| 人的本质 | platform | 台湾实际上是一个“<国>，而不是“<国>” |

</article>

[View on GitHub](https://github.com/nobodxbodon/one-China)

显然，这个人一直在向许多不“遵守”一个中国政策的开源项目提交拉请求，并试图实现这种改变，这种改变将损害生活在台湾等有争议领土上的人们的身份。

## 为什么这个有问题？

这是在不折不扣地遵循 ISO 3166 标准的幌子下进行的。那是通常的论点或类似的东西。但事实是 **ISO 3166 并不是一个中立的标准**。事实上，远非如此！它深受联合国的影响，联合国不承认像台湾这样的许多国家是主权领土，尽管它们有自己独立的政府、法律体系、护照和货币。

很难说服许多开发伙伴理解像 ISO 3166 这样被广泛采用的标准实际上是有争议的。我们大多数人倾向于简单的解决方法，因为了解这些争端背后的历史需要很大的耐心。

那👆🏼由于许多台湾开发人员分别联系 WooCommerce 和 Automattic 团队的努力，这个特殊问题幸运地得到了解决，他们最终改用了 CLDR 标准。但是像这样的事件对台湾人来说是非常敏感的，因为每次这样的事情发生，都会威胁到他们的身份。如果我们中有更多的人有政治意识，他们就不会每次都跳出来独自反击。对一些人来说，这似乎是一件微不足道的事情，但一遍又一遍地为自己的身份而斗争真的会令人厌倦，即使过去有很多解决这类问题的先例。

例如， [Drupal 从 CLDR 转到 ISO 3166](https://www.drupal.org/project/drupal/issues/1938892)，因为他们意识到“联合国各方的利益与软件开发者(尤其是开源开发者)的利益不一致，因此，这不是一个好的国家/地区数据来源。”

## 我们能帮上什么忙？

[台湾希望被称为台湾；台湾人希望被称为台湾人。](http://harvardpolitics.com/world/taiwaneseidentity/)我觉得应该尊重他们的意愿，接受他们独特的身份。

然而，这不仅仅关系到台湾。它关系到每一个为自己在世界上的地位而奋斗的人。

你们中的一些人可能已经知道了香港正在发生的抗议活动，因为它已经被国际媒体广泛报道。但你也知道伊朗、叙利亚、克里米亚等地正在发生的问题吗？最近 GitHub 不得不屏蔽这些国家的用户。由于这些国家在美国出口禁令名单上，GitHub 作为一家美国公司，必须遵守美国贸易法。

你能想象当这些国家的开发人员突然发现他们所有的工作都泡汤了时，他们会有多么困惑、震惊和心碎吗？GitHub 最终确实恢复了他们账户的一些功能，比如免费公开回购，但这只是在一片喧嚣之后才发生的。标签#GithubForEveryone 和下面链接的库获得了很多支持，GitHub 最终恢复了这些国家用户账户的部分功能。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[1995 年帕姆](https://github.com/1995parham)/[github-do-not-ban-us](https://github.com/1995parham/github-do-not-ban-us)

### GitHub 不会禁止我们进入🇮🇷开源世界

<article class="markdown-body entry-content container-lg" itemprop="text">

# 给 GitHub 的消息

[![GitSpo Mentions](img/5a5bbf68814fbe993cc0f13d73b959c0.png) ](https://gitspo.com/mentions/1995parham/github-do-not-ban-us) [ ![Drone (cloud)](img/6cabf215073b4a3c233d59c57cf6b24a.png)](https://cloud.drone.io/1995parham/github-do-not-ban-us)

英语|[简体中文](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./README-CN.md) | [【西班牙语】](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./README-ES.md)||[【意大利语】](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./README-IT.md) | [【欧安组织】](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./README-RU.md)|| >。日本語||[繁體中文](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./README-TW.md)

**注意！我们非常感谢你的支持。这个库总是显示人们都在一起对抗世界各地不幸发生的事情，由于 GitHub 修改了以前的一些限制，我们决定减少这个库的活动。当然，这并不意味着我们同意制裁和 GitHub，我们将很快发布一个结论和更多的解释，请等待这场运动将被终止**8 月 1 日星期四 23:59(德黑兰时间+4:30)** 。这次之后我们不会合并任何新的公关。再次感谢您的支持和好意，请等待我们的最终结论**

[![alt text](img/4aaaaa4334821f1b15f1aa86ab6a5a3e.png)](https://raw.githubusercontent.com/1995parham/github-do-not-ban-us/master/./message.png)

~~**提示:**可以用~~ …

</article>

[View on GitHub](https://github.com/1995parham/github-do-not-ban-us)

如果人们不站出来支持，那么多人会无缘无故地丢掉工作。Slack 也发生了类似的事情，他们后来[出面道歉](https://slackhq.com/an-apology-and-an-update)。**如果这还不是政治意识的有力证明，我不知道什么才是。**

### 还有一件事

这不仅仅是一个政治问题。也是用户体验问题。作为开发人员，我们有责任尊重我们的用户，并确保我们构建的东西确实对用户有益，对吗？

以上面的 WooCommerce 公关为例。如果评论者在没有认真思考这个问题和倾听台湾开发者的声音的情况下就接受了它，这将会损害 WooCommerce，不是吗？因为没有一个头脑正常的台湾人会选择中国作为他们的国家，然后选择台湾作为他们的省份。这与他们的身份以及他们如何看待自己的国家相冲突。对他们来说，台湾显然是一个拥有民选政府的主权国家。

所以开发者们很聪明，他们实际上倾听了人们的意见，认识到这是一个“有争议的”案例，并基于用户体验而不是不住在台湾的人们的意识形态向前推进。毕竟，从各种电子商务网站向台湾/香港订购商品的人实际上是生活在那里的人，所以关注他们的用户行为是有意义的。

世界上有很多宣传机器，如果我们不小心，很容易被他们的观点所左右。这是很自然的，然后，认为我们在我们的项目中犯了一个错误，并希望尽快修复它。这就是为什么我们需要根据用户体验做出明智的决定。

似乎有很多人试图以这种方式利用其他人的政治无知，从而操纵他们。这就是为什么我想强烈敦促你们所有人意识到这一点，并开始变得更有政治意识。

我强烈建议您从观看这些视频开始:

[https://www.youtube.com/embed/6_RdnVtfZPY](https://www.youtube.com/embed/6_RdnVtfZPY)

[https://www.youtube.com/embed/KQTtwh2GRME](https://www.youtube.com/embed/KQTtwh2GRME)

## 接下来是什么？

我认为这是一个非常重要的问题。让我们互相教育，提高对政治问题的认识，这对我们开发者来说是很重要的。

你知道有这样的例子或政治问题以某种方式影响了开发者吗？或者甚至是开发者联合起来影响政治问题的故事？请分享自己的经历和趣闻。

* * *

这个问题有几个原因，其中之一是开源项目成为攻击目标。对我来说，开源在我心中有一个特殊的位置。事实上对于 XenoX 团队的所有人来说。所以没人能乱动开源，至少在我看来不行。😤

[![Nick Fury - Avengers Initiative](img/7210712ebb8a392b1d9a2efd539cbbe1.png)](https://i.giphy.com/media/WbNqQbrnAGr5e/giphy.gif)

如果你对开源有着同样的热爱，并且想用它来造福世界，我们的大门永远为你敞开！不管你是新手还是老手，都欢迎加入 partayy！加入 XenoX 大军，帮助我们改变世界，一步一个脚印。🔥只需按照这里的说明[操作](https://github.com/sarthology/XenoXMultiverse)就可以了。

如果你在商业项目上需要帮助，有一个很棒的团队随时准备投入行动。😉欢迎[联系我们](http://bit.ly/sknxct)。