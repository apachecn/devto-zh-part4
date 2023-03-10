# 使用苹果快捷方式将 Twilio 机器人集成到移动工作流程中

> 原文：<https://dev.to/matthewvielkind/integrating-twilio-bots-into-mobile-workflows-with-apple-shortcuts-1d3a>

构建消息机器人让我能够个性化我的移动体验。在没有满足我个人需求的现有服务的情况下，我已经根据我的个人需求定制了机器人，就像这个我称为 [Twilio Fit](https://www.twilio.com/blog/build-sms-exercise-training-bot-python-zappa-aws-twilio-sms) 的机器人，它设计了一个不会变得无聊的日常锻炼。机器人是一种以移动优先的方式部署定制功能的好方法，而不必设计完整的应用程序，但交互仅限于机器人部署到的各种渠道。将消息机器人整合到更大的移动体验中是一个挑战。当我偶然发现苹果快捷方式时，这一切都改变了，这使我的 Twilio 机器人达到了一个全新的水平。

## 发现苹果快捷方式。

[苹果快捷方式](https://apps.apple.com/us/app/shortcuts/id915249334)是苹果公司提供的一款应用，可以让你在 iPhone 上设置工作流程。您可以设计由各种操作触发的自定义工作流，将输入和输出传递给任意数量的不同任务。在应用程序中，有一个完整的快捷方式画廊，允许你为早上的通勤做准备，通过短信分享你的当前位置，跟踪你今天喝了多少咖啡因，或者在许多许多其他人中帮助你处理那些棘手的吉他和弦。在回顾了你可以在苹果快捷方式中采取的所有不同行动后，我想，如果我把它连接到我的一个机器人上会发生什么？让我们来了解一下！

## 朱婷和苹果的捷径是爱情。

我经常使用的一个机器人是我的短信购物清单机器人，它可以将食谱截图转换成更有用的购物清单。对于这个机器人，一个食谱的截图被发送到一个 Twilio 短信机器人，机器人以笔记的形式返回一个配料列表，使购物更容易。如果没有快捷方式，这一过程的很多部分将是手动的，在 iMessage 中找到机器人的号码，选择要发送的图片，打开笔记并粘贴返回的列表。有了快捷方式，这些步骤中的一些可以自动化，使机器人更容易访问。让我们演练一下快捷方式中的工作流程，让你了解 Twilio 和快捷方式如何改善你的机器人的移动体验。以下是工作流程的前 3 个步骤。

[![enter image description here](img/97842ab3465550695157073bbf84b974.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OhiJXSw2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/3X0TsqOMXCBurOJiG_2tcYuPWczkzdvVdzLFt8WyogqEzl5K3XMDgbRP3WBDM2AgBnTky39n3q-u) 
在这个用例中，假设最后一个屏幕截图是具有配方的那个。或者，您可以提示用户选择他们想要发送的屏幕截图。有许多不同的方式可以配置它！接下来，**发送消息**部分获取选中的屏幕截图，并将其作为彩信发送到 Twilio SMS 短信服务，我已将其保存为联系人 twilio-sms-shopping-list。许多机器人都有某种唤醒词来开始对话。在**发送消息**步骤中，你可以将唤醒词传递给你的短信机器人，以快速开始与机器人的对话。当**发送消息**被触发时，一个新的消息窗口打开，您的图像已经填充在消息中。你所要做的就是点击发送！

[![enter image description here](img/5ced74428e6a8f4eedbe168bf32ae203.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q3QwKPV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/QsVxAXINoEviRf9Oc1SPpsZC4Vf3voWse3rx-Z88CYSZtBiZ7_00VWxDxpak0TN8OW3Jn1N0tszX) 
此时截图已经发送给 bot 进行处理。提取文本和应用 NLP 模型需要一点时间，所以**等待返回**步骤暂停剩余的过程，直到有输出继续。**等待返回**动作允许用户离开快捷应用程序，并在用户返回时继续。几秒钟后，Twilio 机器人将返回食谱配料。当配料被返回时，用户可以打开消息并复制配料。

[![enter image description here](img/0fec64da2a9c8c2e3c400ca25c69aeed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ajHcHhBw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/CbyDRBuShpdeCPJ95Tg1-vqVoFUCCUMr1BV0b_JELA_Opb-XelQmuHrTFL3xaLSsLVahV3PMuIwS) 
复制配料后，当用户再次打开快捷方式应用程序时，该过程将继续。最后几个步骤将把复制到剪贴板上的配料，在**便签**应用程序中创建一个新的便签，其中包含从消息中复制的配料列表。

[![enter image description here](img/d46eb236598c339f9a2942909828aca5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FXq6d-3I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/TYO-kflUEv6cBduT75Cl_7vWQR0Kl7t9iGDQREYggux-10CQiun6hZdutvA49hOd3CSYkVct2Cye) 
当完成时，刚刚创建的便笺就会被打开。就像这样，你准备好去杂货店购物，并列出你实际上可以使用的清单！再也不会忘记配料了！

[![enter image description here](img/6c7de6d239ef695c728df27bbf5c989e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hm798Mwr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/aM7-uykTv6Zi0-1JnWcFIG-3CQoLKIxbV7Y3pZBqB6YMiOwtCrCpBkuiPrGW_gDAAq7A2fgpShSL) 
最棒的是，一旦设置好快捷方式，只需点击主屏幕上的一个按钮就可以访问它！你甚至可以为你的机器人设计自定义图标，使它们更加个性化。现在，只需在手机的主屏幕上点击一个按钮，就可以访问我所有的机器人了！

[![enter image description here](img/bd9de623d3aff724f15edea7daa2f359.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kPtPwlZY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/lG-kRnKSnmFpZaA_Y0ni4XaW51SNYhxBdPoy4m7lWqTMfIPee4lwwZHNQ18Qvcnw1aw2sbW49_rm)

## 包装完毕

将 Twilio 与快捷方式结合起来会产生一种新的动力，使你的机器人更容易访问。凭借 Twilio 平台的灵活性和集成的不同服务快捷方式的数量，有这么多不同的选项来测试您的机器人与您的移动体验的不同集成。

现在，您已经了解了如何将 Twilio 和 Apple 快捷方式结合使用，下面是几个其他教程来帮助您入门:

*   Twilio Fit 打造自己的私人教练机器人。
*   制作一个机器人，将你最喜欢的食谱截图转换成有组织的购物清单。

如果你是 Twilio 的新手，你可以免费花 10 美元使用这个[推荐链接](//www.twilio.com/referral/J5x4pK)来开始！

快乐大厦！