# 与 Girikon 的 Salesforce 支持团队的 WalkMe 集成步骤

> 原文：<https://dev.to/girikon/walkme-integration-step-with-girikon-s-salesforce-support-team-28p5>

Girikon 的**[Salesforce support](https://www.girikon.com.au/salesforce-support/)**团队经常被问到关于 WalkMe 与 sales force 接口的问题，他们最初需要强调集成后的功能和优势。作为 Salesforce 实施合作伙伴的 Girikon 积极主动，在他们的 **Salesforce Support** 顾问的协助下，概述了 WalkMe 的优势，如在培训期间加速能力培养、提高数据完整性、推广新的 Salesforce 功能和提高生产力。Girikon 的团队与现有客户密切合作，将 Salesforce 集成到 WalkMe，因为他们知道，作为 Salesforce 实施合作伙伴，这些类型的集成很有可能在新客户的项目范围内。

WalkMe 是一个云平台，可以帮助产品经理和客户成功经理创建产品游览和管理应用内体验。毫无疑问，WalkMe 是用户入职市场的先行者之一。由于其巨大和可识别的品牌，它可能是许多公司的首选。通过在应用程序中创建不同的演练，它可以帮助您接纳更多的用户。通过 WalkMe 的仪表盘，您可以轻松查看所有用户的入职数据和分析。由于其优势和能力，WalkMe 是大型企业的一个很好的工具。但由于其价格和难以使用，它不是初创企业、SaaS 或中小型企业的最佳选择。

使用 Salesforce 集成，您可以将 WalkMe Insights 数据推送到 Salesforce 对象，以增强您对最终用户行为的了解。Girikon 的 Salesforce 顾问 Deepanshi 带领我们一步步地设置 WalkMe 和 Salesforce 集成。

# 要求

在尝试设置 WalkMe 之前，需要以下项目:

*   您必须是 Salesforce 管理员或联系您的 Salesforce 管理员，以便向您的目标 Salesforce 对象添加自定义字段
*   您必须拥有拥有 Salesforce 管理员访问权限或修改数据权限的 Salesforce 用户，以授予 WalkMe 对 OAuth 2.0 授权的访问权限，从而读取和更新 Salesforce 对象的元数据(Salesforce 管理员可以随时撤销此访问权限)

Walkme 是一个本地解决方案，因此出于不同的目的，您必须在使用它之前安装它。这可能会变得有点混乱，因为在您的系统中安装 Walkme 需要大量的技术知识。所以，你需要付出很多努力，只是为了看看 Walkme 是如何工作的。

**整合**

使用此链接([www.insights.walkme.com/login.html](http://www.insights.walkme.com/login.html))您可以重定向到 WalkMe 登录页面。

[![WalkMe Integration](img/9f5faebfd9853b68fa82d47620ba6c69.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration.png)

输入您的电子邮件地址和密码后，您将能够看到 walkme 的 insight 控制台。

## 第一步:选择您的集成

在 Insights 控制台([https://insights.walkme.com](https://insights.walkme.com/))中，点击左侧栏中的集成，然后选择您想要实现的集成。

[![WalkMe Integration](img/61b95048a29f1c4ea3b62811ac7bcc06.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration1.png)

### 第二步:连接环境

连接您的 Salesforce 环境:

[![WalkMe Integration](img/6784d25a0ff6d4ed06e5dfef701a68e1.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration2.png)

单击要连接的环境旁边的连接按钮。之后，您将被转到 Salesforce 身份验证屏幕，在此您将授予 WalkMe 向该帐户推送数据的权限。

[![WalkMe Integration](img/ff4b7e0df8ab04172af9d14c3d3ee4f8.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration3.png)

插入帐户的电子邮件和密码后，您将被重定向回 Insights 控制台的 Salesforce integration 部分。

### 第三步:创建集成

[![WalkMe Integration](img/f0d5891c8898bf7b3c4ab5f548e8bc69.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration4.png)

单击“新建集成”按钮启动集成向导。

之后，您可以看到您连接的 Salesforce 环境

[![WalkMe Integration](img/c47f71f46cdac3b091e34d7080398d4d.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration5.png)

点击继续继续。

### 步骤 4:在集成向导中选择 Insights 报表&匹配的 Salesforce 对象

[![WalkMe Integration](img/04796700b5f55a5bbc73928057662f9b.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration6.png)

请遵循以下步骤:–

*   从包含您的可用 Insights 报表的下拉列表中选择一个报表，然后选择应与之同步的 Salesforce 对象。
*   从两个下拉菜单中选择项目后，选择每个报告的唯一标识符(键)并将其与 Salesforce 对象标识符匹配
*   单击继续按钮继续

### 步骤 5:在集成向导中映射要更新的字段

[![WalkMe Integration](img/1dd50455c293784d40b5d163b744753c.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration7.png)

您将在此决定在 Salesforce 中更新哪些数据。此映射为最终用户提供了一个 UI，显示在 Salesforce 对象中更新了源报表中的哪些数据:

*   Insights report 列包含报告中的所有字段，包括在屏幕 2 上选择的关键字段。
*   Salesforce 对象列由所选对象的所有字段组成，这些字段:
*   尚未在屏幕 2 中使用
*   对应于屏幕 2 上选择的按键
*   您可以单击“添加列”来添加字段到对象的另一个映射
*   当鼠标悬停在某一行上时，可以单击 X 按钮删除该行

### 第六步:执行你的整合

[![WalkMe Integration](img/90b87a9babe5ff82ed852146444e555b.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration8.png)

单击“立即运行”选项运行此集成。

这将执行集成，并尝试将任何相关数据推送到指定的帐户。如果全部正确执行，日志和网格都将指示集成成功完成。

每当发生同步时，都会生成一个日志。当您将鼠标悬停在 Insights 的集成部分中的某个集成上时，会出现铅笔图标，单击该图标旁边的三个点即可找到该日志:

*   当您单击日志时，会出现一个覆盖窗口，您可以查看从 WalkMe 到 Salesforce 的数据推送历史
*   该日志包含报表名称、集成的 Salesforce 对象以及以下信息:
*   开始日期:以 UTC 时间表示的运行日期和时间
*   运行时间:运行的持续时间(结束时间减去开始时间)
*   状态:运行的状态:完成或失败

一旦集成完成其执行，最后一次运行列将会出现，并指示它是成功还是失败

[![WalkMe Integration](img/15d73badf83bf44f8830eaa1d7d78d40.png)](https://www.girikon.com/wp-content/uploads/2019/06/WalkMe-Integration9.png)

如果成功，我希望在 Salesforce 环境中看到这些字段。

帖子 [WalkMe 与 Girikon 的 Salesforce 支持团队的整合步骤](https://www.girikon.com/blog/walkme-integration-step-with-girikons-salesforce-support-team/)首先出现在 [Salesforce 咨询公司，美国、澳大利亚的全球办事处](https://www.girikon.com/)。