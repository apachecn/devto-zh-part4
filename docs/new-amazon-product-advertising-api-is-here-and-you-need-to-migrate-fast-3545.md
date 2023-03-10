# 新的亚马逊产品广告 API 就在这里，您需要快速迁移！

> 原文：<https://dev.to/skatkov/new-amazon-product-advertising-api-is-here-and-you-need-to-migrate-fast-3545>

*原载于[fbacatalog.com/blog](https://fbacatalog.com/blog/new-product-adveritising-api.html)T3】*

亚马逊低调地推出了新产品广告 API 版本 5。我真的很高兴听到亚马逊忽略了很长一段时间的 API 的所有变化。

我通常会监控大量的亚马逊支持表单，以获得类似的更新。但这一次不同了——我在谷歌上根本找不到任何公告。但是在我关注的的一个[开源库中偶然发现了这条新闻。](https://github.com/hakanensari/vacuum/issues/77)

在详细说明实际发生了什么变化之前，我想强调一个事实，即**10 月 31 日，当前 API 将关闭**，所有未迁移的 API 都将断开连接。

## 变化

*   已弃用所有与购物车功能相关的内容
*   对 API 的请求现在需要[进行不同的签名](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)
*   现在，在 HTTPS 邮报/RPC(JSON)的支持下，API 变得更加安全
*   Response now 比较轻，而且是 JSON 格式的(yaaay！)
*   更快的响应时间(翻倍！)
*   一系列新的数据点，如以首选货币显示的*价格、多语言支持、prime exclusive、免费送货资格、浏览节点的销售排名*
*   还有一个[新的草稿栏，可以用来玩新的 API](https://webservices.amazon.com/paapi5/scratchpad/index.html)

对于所有的本质细节，并行的请求和响应比较查看[他们的文档](https://webservices.amazon.com/paapi5/documentation/migration-guide/whats-new-in-paapi5.html)。

这个 API 附带了对 Java、PHP、Python 和 JavaScript SDK 的官方支持。叶芝，看不到 Ruby 的支持(开始哭泣...)

## 要做的第一件事——迁移到新键

亚马逊产品广告 API 的当前用户拥有与 AWS 关联的账户。亚马逊希望所有这些人做的第一件事是[从 AWS](https://webservices.amazon.com/paapi5/documentation/migrating-your-product-advertising-api-account-from-your-aws-account.html) 中分离关联账户。

这里有一个合理的警告:
*当您从 AWS 迁移您的产品广告 API 帐户时，您的旧凭证在 3 天内保持有效。3 天后，您的旧 AWS 凭证将停止使用产品广告 API。*

因此，我建议立即这样做——一旦你发放了新的钥匙，你将有 3 天的时间实际开始使用它们。**新凭证适用于 API 版本 4 和 5，因此不需要修改代码**。

## 移动到新的 API

正如亚马逊声称的那样，更新可能需要长达 4 周的时间。

“我们发现，从 PA API 4 升级到 PA API 5，对典型应用程序进行更改需要 3 或 4 周的时间。测试和部署的时间取决于您的具体安装。”

考虑到 PA API 团队没有多少建议，我建议现在就开始行动。

如果你依赖于任何与 Cart 相关的功能，你应该完全放弃这个解决方案，转而使用 [Cart 表单](https://webservices.amazon.com/paapi5/documentation/add-to-cart-form.html)。这实际上是我几个月前做的迁移的第一步，体验棒极了——它实际上减少了维护负担，如此重要的操作的复杂性降低了(换句话说，*它只是工作*)。

只有在这之后，您才能集成到 PA-API 5.0。如果你没有使用任何官方支持的 SDK 的代码，你最好的选择是偷偷看一下 T2 的 Java 代码，然后使用你自己的实现。

祝你好运！

## 终注

纯粹从文档来判断——这些改进中的大部分都是许多人所期望的。PA API 4 的许多奇怪的部分已经被删除或得到了显著的改进。尤其是转向 JSON 和 HTTPS 邮报/RPC(JSON)是一个非常受欢迎的变化。

但在沟通方面还有很多需要改进的地方:

*   一些关于改变的适当的提醒对每个人都有好处——论坛，会员帐号或任何东西...求你了。
*   文档只有一个版本,并且从未更新后记

我真的希望 Amazon 能够推迟 PA API v4 被弃用的最后期限。似乎我问的每个人都没有意识到这个变化，每个人都和我一样惊讶。