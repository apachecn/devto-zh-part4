# 创建您的第一个 AWS 计费警报

> 原文：<https://dev.to/hzburki/create-your-first-aws-billing-alarm-2h74>

无论你是新手，刚入门还是技术高超的 AWS 忍者，都需要时刻盯着那些讨厌的 AWS 账单。计费是 AWS 中非常重要的一部分，有时也是令人困惑的一部分，如果不加以检查，可能会造成很大的损失。

AWS 提供了一年的免费试用，并且有很多很棒的教程，让年轻的开发者可以很快上手。但是，在开始之前，强烈建议您设置一个计费警报，以避免任何不必要的费用。

> 本文假设您已经创建了一个 AWS 帐户

* * *

## 什么是计费告警？

好问题！计费警报是您可以设置的限制，当您超过该限制时，AWS 控制台会通知您(通过电子邮件)。

## 让我们把手弄脏吧！！👩‍💻 👨‍💻

*   假设您已经登录到控制台。点击右上角栏中您的**用户名**，并选择**计费仪表板**。或者，您也可以在主页的搜索栏中搜索**账单**。

*   在左侧选项卡的**首选项**部分，点击**计费首选项**。

[![Preference Screen - Haseeb Burki AWS Billing Alarm](img/9315ceeefaa45eacfc81ada13263f218.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n0HXjQLG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8cuveiuxd8zysb8mzj1m.png)

*   勾选**接收计费提醒**并按下**保存首选项**。

> 注意: AWS 最近更新了它的用户界面(UI)，所以网上现有的许多教程仍然基于旧的 UI。

*   之后，你需要导航到 **CloudWatch** 。打开**服务**下拉菜单(顶部栏)并搜索 CloudWatch。它应该归**管理和治理**。

*   在左侧选项卡上，点击**计费**，然后点击**创建报警**按钮。

> 💡CloudWatch 仅显示美国东部(N. Virginia)地区的所有账单提醒。因此，您必须在该区域创建所有警报。

*   **创建警报**按钮将带您进入设置向导。向导中有四个步骤:

1.  指定指标和条件
2.  配置操作
3.  添加描述
4.  预览和创建

*   在第一步中，您可以添加警报的名称、货币和触发警报的条件。

[![Specify Metrics - Haseeb Burki AWS Billing Alarm](img/bd309116121dbdebffd7f9e4f808b0c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XVIrebEi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qw20whamjy12hsqh16ce.png)

*   第二步，您可以配置何时触发警报以及警报发送给谁。要定义接收通知的人，您需要创建或选择一个现有的 SNS 主题。

*   完成以下步骤；

1.  在“选择 SNS 主题”副标题下选择**创建新主题**
2.  如果需要，您可以更改主题名称。
3.  添加您的电子邮件地址。您可以添加多个用逗号(，)分隔的电子邮件地址
4.  按下**创建主题**

[![Creating SNS Topic - Haseeb Burki AWS Billing Alarm](img/385ab6d41d8cfeaca05afd93c2a9bdf1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8W_eEhox--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ovjpwulk0pri99r1vn86.png)

*   在第三步中，只需为警报添加一个唯一的名称和描述(可选)。这是为了让您能够在将来可能创建的警报中轻松识别该警报。

*   最后一步让您预览在设置向导中添加的所有信息，并创建警报。

> **注意:**创建闹钟后，您需要访问您的电子邮件并确认订阅。