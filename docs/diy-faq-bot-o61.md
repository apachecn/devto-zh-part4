# DIY 常见问题机器人

> 原文：<https://dev.to/twilio/diy-faq-bot-o61>

下面这篇博文将带你了解如何使用微软 Azure [机器人服务](https://azure.microsoft.com/en-us/services/bot-service/?WT.mc_id=devto-blog-chcondon)用最少的编程、 [Twilio](//www.twilio.com/referral/xBBj30) 和一个 Word 文档创建一个简单的聊天机器人。

*如果你正在寻找关于 [Azure Bot 服务](https://azure.microsoft.com/en-us/services/bot-service/?WT.mc_id=devto-blog-chcondon)的快速总结和概述，我建议从我们的[文档](https://azure.microsoft.com/en-us/services/bot-service/?WT.mc_id=devto-blog-chcondon)开始，或者花一两个小时完成一步一步的[学习路径](https://docs.microsoft.com/en-us/learn/paths/create-bots-with-the-azure-bot-service/?WT.mc_id=devto-blog-chcondon)，这样你就对 Azure 提供的各种 Bot 选项有了一些了解。*

这篇文章是一系列文章的第一部分，讲述了如何用 Azure 创建各种不同难度的机器人(这篇文章是最简单的)。

祝你装瓶愉快！🤖

*——克洛伊*

[![](img/9fd57959ff2bd0748732fc93e046f319.png)](https://i.giphy.com/media/3o7TKpndlPDwez5Mac/giphy.gif)

我是一个超级粉丝，喜欢用技术来解决生活中的问题和常见的焦虑来源。如果你过去关注过我的任何内容，你可能会熟悉[我的假男友应用](https://dev.to/azure/an-ambivert-s-guide-to-azure-functions-27b8)，它是我用来把我从尴尬的社交场合中拯救出来的，还有[我的多动症药物提醒](https://dev.to/azure/building-a-diy-adhd-medication-reminder-with-azure-functions-o70)。最近，我一直在处理我的 DMs 周围的许多压力和焦虑。特别是我在 Twitter、Instagram、Facebook Messenger 等网站上获得的**总收入**。我经常失手，因为我在忙着阅读一条信息，承诺以后会回复，还有……向所有被我跟踪的人道歉😬👻。事实是，作为一名[云倡导者](https://developer.microsoft.com/en-us/advocates/)，我的角色是难以置信的社交。我的 DM 经常充满了尝试咖啡会议的计划，来自朋友的迷因，发言询问，寻求职业建议的训练营毕业生，以及偶尔来自一个我从未见过面的陌生人的不受欢迎的“嘿”😑。

[![DMs gif](img/7a25051aee6e1cd549d718bca508e485.png)](https://i.giphy.com/media/1dJtYN1CwujK9dmVCH/giphy.gif)

不幸的是，由于我收到的大量邮件，我经常被所有的噪音淹没。持续不断的消息和通知不仅让我难以集中注意力(尤其是在编程的时候)，而且当我把球丢给别人时，我也感觉很糟糕。因此，我开始尝试 Azure Bot 服务，看看是否有什么方法可以构建一个机器人来帮助我管理和自动化我与人们的交流。

经过快速的头脑风暴，我发现我的 DMs 通常分为以下几类:

*   Azure 相关问题👩🏼‍💻
*   寻求职业建议的新兵训练营毕业生🙋‍♀️
*   口头询问
*   与密友的持续对话👯‍♀️
*   令人毛骨悚然的人(机器人？)我从没遇到过只说“嘿”的🙅🏼‍♀️
*   各种机会(播客、采访、特定 Furby 黑客问题等)。🎧🎙🎭❓

我知道我想构建一个机器人，但我不想让它响应每一条入站消息。例如，如果我有办法自动回答新兵训练营的毕业生们经常问的问题，我可以节省大量的时间。而来自 Azure 用户的消息需要我个人的/详细的回应。此外，我突发奇想，训练我的机器人来帮助我安排和组织演讲机会，以及标记或委派重要的查询/消息。然而，我从未制造过机器人...但是感谢[微软学习](https://docs.microsoft.com/en-us/learn/modules/build-a-faq-chat-bot-with-qna-maker-and-azure-bot-service/?WT.mc_id=devto-blog-chcondon)——我能够在不到一个小时的时间里启动并运行我的第一个机器人！

所以，我用 QnA Maker 制作了我的第一个机器人。如果你不熟悉， [QnA Maker](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview/?WT.mc_id=devto-blog-chcondon) 允许你使用一个简单的 FAQ 列表创建一个机器人。下面，我将介绍如何构建自己的 QnA bot 来帮助自己委派入站消息。如果你是一个小企业主、首席执行官、十几岁的女孩，或者像我一样是一个焦虑的技术工人，害怕在 DMs 上丢球，建立自己的机器人是非常容易的(*说真的，这感觉就像巫术* ✨😳).

[![witchcraft if](img/45f2bb82c1f7b7ea131f4c50e29f34c1.png)](https://i.giphy.com/media/zIwIWQx12YNEI/giphy.gif)

让我们快速回顾一下我们正在做的事情...

**问题:**嘈杂的 DMs +来自未回答的 DMs 的持续焦虑
**解决方案:**构建一个机器人来帮助回复和自动化我在线接收的常见消息
**技术:** QnA bot

下面是我们将如何从[文档](https://docs.microsoft.com/en-us/learn/modules/build-a-faq-chat-bot-with-qna-maker-and-azure-bot-service/1-introduction)中构建 QnA 机器人的视图

[![diagram](img/a5f4a94aa26d80850e025055c0104aec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rsRapc8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xi4ghe8om835irsg6rrm.png)

## 让我们创建一个知识库& QnA 服务

首先，前往 QnA 门户网站([https://www.qnamaker.ai/](https://www.qnamaker.ai/))，用你的 Azure 凭证在右上角登录。点击以创建知识库(【https://www.qnamaker.ai/Create】T2)。

我们的机器人的知识库将是一个可搜索的问题和答案的集合。它将被托管在 Azure 的 QnA 服务中。单击创建 QnA 服务(下面以粉红色突出显示的按钮)。

[![](img/d7175737380e1dae81da584edfc05446.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--073T1JOe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6zria5o47940usc6jsok.png)

## 让我们提供 QnA Maker 服务详情

接下来，我们需要提供 QnA Maker 的一些细节，包括唯一的名称、Azure 订阅、位置等。下图是我的输入。您会注意到，我选择了 F0 作为定价(免费层)，并选择了西海岸(离我当前位置最近)作为所有位置。我还创建了一个名为 ChloBots 的新资源组(您可以随意命名)。对于定价层，我选择了 F(三个索引),这是搜索定价层的自由层。我还为这个演示禁用了 App Insights，但如果您想要监控数据，启用也是一个选项(您可以在此处了解更多信息)。

[![](img/1eedcc7a9ac80eb2fe6fe481c9898ed1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cZ3c2Qh---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ag6uwsdeqsqn047m1oqy.png)

点击页面底部的 **Create** ，大约一分钟后，您就会为该服务创建一个资源。

## 让我们将 QnA Maker 服务连接到知识库

返回到 [QnA Maker 门户网站](https://www.qnamaker.ai/Create)。如果您刷新页面，显示您的帐户信息的下拉菜单将出现在步骤 2 下。选择目录 ID、订阅名称和 QnA 服务(我们刚刚在 Azure 门户中选择/创建的)。

向下滚动到步骤 3，并为知识库提供一个唯一的名称。我将我的命名为“BootcampBotFAQs”。

## 填充知识库

下载我创建的 FAQ 的 Word 文档(显然，你可以创建你自己的 FAQ 集或者编辑这个文件来为你自己的机器人定制它)。在步骤 4 下，将文件添加到 [QnAMaker](https://www.qnamaker.ai/Create) 中。

在步骤 4 中，你还可以选择你的机器人将拥有的“聊天”类型。这将决定你的机器人的个性。提供的选项从“专业”到“热情”，或者根本没有。我会选择“友好”，因为我想确保我的机器人对它遇到的每个人都非常热情友好。😁

如果这一页上的一切看起来都很好，继续点击**创建您的知识库**。在短暂的等待之后，我们将会有一个知识库，并且**编辑**页面将会加载。

### 来测试一下吧！

信不信由你，我们的机器人已经准备好测试(告诉你这是巫术🔮)!在右上角，点击**测试**，你就可以开始测试你的机器人了！继续问它一些常见的问题，看看它的反应。

[![](img/83d49951c7d8841cd399c00c4865678d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i9M4Fyka--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ratlq5048uu37fklxog.gif)

### 发布我们的知识库吧！

一旦你测试了你的机器人，并且对它的答案有信心，我们就可以继续发布我们的知识库了！

转到 QnA Maker 知识页面(与我们一直在测试的页面相同),点击页面顶部的**发布**。您将被带到一个页面，让您知道您的知识库将不再处于测试模式，它将成为您能够使用的端点。如果一切顺利到这一步，你会看到一个**的成功！**包含 URL 的页面。

### 我们来整合吧！

在 [Azure 门户](https://azure.microsoft.com/?WT.mc_id=devto-blog-chcondon)中，点击页面左侧的*创建资源*。在搜索栏输入“bot”，找到**网络应用 Bot** 。我们现在将为我们的机器人创建一个网站，我们还将让它注册到 Azure 机器人服务。这看起来类似于我们之前创建的 QnA Maker 服务，但是有一些小的调整。提供一个名称(这必须是唯一的！)，并选择您的订阅和您之前使用/创建的资源组。使用 S1 定价层，在 **Bot 模板**下选择 **SDK v3** 和 **C#** 作为语言，以及**问答**作为模板。我们也将关闭应用洞察。

一旦**创建**被点击，它将在几分钟内准备好！

当您收到部署成功的通知时，导航到您最近创建的**配置**部分的左侧导航，查看您的**应用程序设置**。

[![](img/9c56daab9899330d87b968e7dff66d5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6iId2bIt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ofo1asgm9aqpv2pwnly.png)

### 让我们将我们的机器人连接到我们的 QnA 服务！

这是我们把它们连接在一起的部分🤖如果您还没有打开它，请创建一个新标签来登录您的 [QnA Maker](https://www.qnamaker.ai/) 帐户，并找到您已发布的 QnA Maker 服务的详细信息。在 Postman 示例中，找到 GUID(这代表知识库 ID)并复制它。我们将粘贴该 GUID，并在我们的**应用程序设置**中为 **QnAKnowledgebaseId** 选择**隐藏值**。

[![](img/356df7d11a453ed0b3cd0004117e3440.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HMs85frr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h59kh2pgtt86j3w18mhd.png)

对**应用设置**中的 **QnAEndpointHostName** 和 **QnAAuthKey** 进行同样的操作。保存这些设置，并在左侧导航的 **Bot 管理**部分下的网络聊天中找到**测试。我们的机器人现在连接到我们的 QnA 制造商服务！🎉通过问一两个问题来测试这个机器人，你的机器人应该已经开始运行并为你服务了。**

恭喜你-你做到了！您现在已经发布了一个 QnA Maker 服务，该服务已在 Azure 上发布。您的网络聊天机器人已经与 QnA Maker 集成，并允许人们以互动的方式与您的机器人聊天。好吧，那是很多...让我们再看看那个图表，好吗？

[![diagram](img/a5f4a94aa26d80850e025055c0104aec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rsRapc8x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xi4ghe8om835irsg6rrm.png)

### 我们来连线一下 Twilio！

现在我们需要把它连接到正确的频道。我已经决定使用 [Twilio](//www.twilio.com/referral/xBBj30) 来做这件事，因为我熟悉他们的 API。如果你不熟悉的话，Twilio 允许软件开发人员以编程方式拨打和接听电话，发送和接收文本消息，并使用其 web 服务 API 执行其他通信功能。前往 Twilio，如果您还没有帐户，请创建一个帐户(🙋‍♀️参考消息:你可以使用代码 CHLOE20 获得一些免费积分，并在这里注册。你可以以各种方式使用 Twilio([假男友应用](https://dev.to/azure/an-ambivert-s-guide-to-azure-functions-27b8)和[多动症提醒](https://dev.to/azure/building-a-diy-adhd-medication-reminder-with-azure-functions-o70)等等)🤳)!

创建 Twilio 帐户后，创建一个新项目。我们需要一些东西来让我们的机器人连接到 Twilio:

*   Twilio 电话号码
*   Twilio 账户 Sid *(作为我们 Twilio 应用的用户名)*
*   一个 Twilio 认证令牌*(充当密码)*

给你的项目取一个唯一的名字(我把我的命名为“BootcampBot”)，然后你就会看到你的仪表板。在左侧导航中，点击**电话号码**部分(标记为**“#”**——找不到？它可能就藏在*后面“……”*您需要扩展的图标！).点击**购买号码**并点击**搜索**按钮。一旦你找到一个符合你需求的号码(区号并不重要，但它**将**需要短信功能！)，点击**购买**(注:电话号码一般每月花费 1-2 美元左右)。

### 我们来创建一个 TwiML App 吧！

现在我们需要[创建一个 TwiML 应用程序](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-twilio/?WT.mc_id=devto-blog-chcondon)，这样我们就可以给它一个请求 URL(在本例中是 https://sms.botframework.com/api/sms[*)来与 Twilio 进行消息传递。在*](https://sms.botframework.com/api/sms) *[Twilio 控制台](//www.twilio.com/console)中，点击进入可编程**消息传递>工具> TwiML 应用**。点击红色➕标志图标，或**创建新的 TwiML 应用**。*

 *使用友好的名称填写 TwiML 应用程序表单，并在**消息传递**下添加**[【https://sms.botframework.com/api/sms】](https://sms.botframework.com/api/sms)**作为**消息传递请求 URL** (HTTP POST)。

最后，导航回 Azure 门户，选择**频道**(在 **Bot 管理**下)并将 Twilio 凭证添加到 Azure Web 应用 Bot，如下所示:

[![](img/8b0f4ed2fbdf93e9bb9f75c43759a3e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l304Qd7Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ixhr446v8mh28eeuobkd.png)

**注意** : *确保你的 Twilio SID + Auth 令牌的私密性和安全性！*

要测试你的机器人是否一切正常，发一个问题到你的 Twilio 电话号码，然后开始享受吧！你已经成功地用 Azure 构建了一个 QnA Bot，并成功地将其与 Twilio- **连接起来！**🎉

这是我的机器人运行的视频(我已经将机器人添加到我的联系人中，名为“🤖训练营机器人🤖".

[https://www.youtube.com/embed/4l5TXxS7Pi8](https://www.youtube.com/embed/4l5TXxS7Pi8)

是啊！我们的机器人还活着！

[![](img/3029e48f5603720d8df8a906f2aec59d.png)](https://i.giphy.com/media/3o6gDPmm6ZvFVu4yJO/giphy.gif)

如果你已经做到了这一步，祝贺你制作了第一个基本机器人。如你所见， [QnA Maker](https://www.qnamaker.ai/) 是一个非常简单的方法，可以创建一个基本的机器人来管理客户、朋友、家庭成员或对训练营好奇的人的常见问题。你的网站上有 FAQ 页面吗？然后你可以为它造一个机器人！

一如既往，在这里回答任何问题，解决任何错误，或帮助您建立自己的。请在下面发表评论，或者在遇到困难时在 Twitter 上寻求帮助！我是来帮忙的。🤖💖克洛伊*