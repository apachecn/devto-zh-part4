# webMethods.io 集成教程-了解 webMethods.io 集成 1.4

> 原文：<https://dev.to/techcommunity/webmethods-io-integration-tutorial-understanding-webmethods-io-integration-1-4-3h79>

# 基础知识:术语

### 工作流程

工作流是两个或多个 web 应用程序或服务之间的连接。创建工作流意味着定义要自动执行的重复性任务的步骤。您只需定义这些步骤一次，webMethods.io 集成平台每次都会为您自动执行。简而言之，创建工作流就像定义您的条件:

当“服务 A”中发生事件时，在“服务 B”中执行一些操作，并(可选地)将数据传递给“服务 C”、“服务 D”和“服务 E”。

让我们看一个示例工作流来更好地理解这一点。

当我的 Evernote 帐户中创建了一个新便笺时，将该便笺翻译成西班牙语(使用谷歌翻译文本)，并将翻译后的文本发送到我的 Gmail 帐户。

[![](img/98bf2da0703b97b98972d762fc08af0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nIEsJ4o_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/workflow.jpg/a565496e-4d2a-4773-b940-3f7acefc2477%3Fstatus%3D0)

这是它的工作原理。

工作流程会定期检查你的 Evernote 账户。一旦创建了新的注释，它就提取正文，并使用 Google Translate Text 将其转换为西班牙语。翻译后的文本将发送到您的 Gmail 帐户。所有这些都将在几秒钟内自动完成，无需任何人工干预。

这是一个简单工作流程的示例。您可以通过在单个工作流中使用多个操作来创建更复杂的工作流。

要了解如何创建您的第一个工作流程，请参考我们的[分步指南](https://docs.webmethods.io/workflow-building-blocks/creating-first-workflow)。

### 触发

当定义的事件发生时，触发器会自动启动工作流。所以在前面的例子中，Evernote 是触发器。

每个工作流只能有一个触发器。

webMethods.io 集成提供了两种类型的触发器:

<u>轮询触发器:</u>轮询触发器定期检查外部服务和应用程序的变化，间隔为 5 分钟。他们可能不会实时向 webMethods.io 集成发送数据。在触发器列表中，所有轮询触发器都标有“时钟”图标。

<u>Webhook 触发器:</u>web hook 触发器持续检查更新，并实时向 webMethods.io Integration 发送数据，这可确保您的工作流在外部应用程序或服务中发生指定事件时立即执行。

[![](img/c8f7aef89949a220823412d9de408c52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xpie0zzQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/trigger.jpg/eb068c96-4dfc-4d95-829c-1781939614d9%3Fstatus%3D0)

阅读更多关于[触发器](https://docs.webmethods.io/workflow-building-blocks/triggers)的信息。

### 动作

操作指定了您希望工作流执行的任务。例如，在我们之前讨论的工作流中，Google Translator 和 Gmail 是操作。

与触发器不同，来自不同服务的多个操作可以添加到同一个工作流中。每个连接器都有几个您可以使用的操作。

webMethods.io 集成根据其用途提供了四类操作:

```
 **Services                            Utility                                     Premium                                     Custom                  ** 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/249e86e76099b6ca2fb8cedf8937dfb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vpMZ6hdq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/action_1.jpg/e6116198-e279-4c83-8445-34b07a8d2637%3Fstatus%3D0)[![](img/da02ca7a235910661a89b20386a197cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hQSy_zsE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/action_2.jpg/4aa2ea22-1c6c-4099-81c7-a2e26951ef26%3Fstatus%3D0)[![](img/70a5211fafe9d85b5b4527facc9e5a6e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xZrX68QX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/action_3.jpg/0bdc5884-f7f6-43a4-a4cc-1a8cdd7ddd9b%3Fstatus%3D0)[![](img/804abf0a991c041af821cc8270f566f3.png)T11】](https://res.cloudinary.com/practicaldev/image/fetch/s--dF2LuK_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/action_4.jpg/a2ccd1b5-e3bf-4911-b748-ae64db0f4c3a%3Fstatus%3D0)

阅读更多关于[动作](https://docs.webmethods.io/workflow-building-blocks/actions)的信息。

### 项目

项目对应于组织所有工作流程的文件夹。每个 webMethods.io 集成租户都有一个默认项目。用户可以根据自己的需求选择创建其他项目。将工作流添加到项目中时，与该工作流相关联的所有配置也会存储在项目中。

[![](img/6efdf095725f803407df4f81220624c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i0L_fLJp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/project.jpg/6812d48e-751d-4b0b-8359-26f6fc2daee1%3Fstatus%3D0)

了解更多关于[项目](https://docs.webmethods.io/workflow-building-blocks/projects)的信息。

### 房客

租户位于 webMethods.io 集成帐户的层次结构的顶部。它封装了用户、项目以及项目中的工作流，允许您轻松地集中管理所有资源。

### 账号

帐户是授予 webMethods.io 集成在第三方应用程序或服务中执行某些任务的权限的过程。例如，如果您在工作流程中使用与 Gmail 相关的操作，您必须首先登录您的 Gmail 帐户，然后授权 webMethods.io Integration 代表您执行某些任务。

[![](img/d1aeb3af6a4cd580e99e3396deb7b254.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nqsc4Ttg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/account.png/6e20ff93-af4d-44ac-9165-1b8983638fc5%3Fstatus%3D0)

在 webMethods.io 集成中创建的所有帐户都具有全局范围，这意味着为特定服务创建的授权可用于在您帐户中的所有工作流中执行该服务的其他操作。

了解更多关于[账户](https://docs.webmethods.io/workflow-building-blocks/accounts)的信息。

### 条件

条件允许您在工作流中的两个操作之间或一个触发器和一个操作之间设置多个条件。只有满足指定的条件，工作流才会继续。定义条件时，您需要指定“输入”、“条件”和“预期值”。

假设您希望 Evernote 上的新笔记只有在内容包含单词“lead”时才会被通知。在这种情况下，content 将是输入，contains 将是条件，lead 将是预期值。您在条件中使用的输入通常是前面的操作或触发器的输出。

[![](img/e8366f81ad32d8f5a6d5111c3e1cd3f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NMUTUh5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/conditions.png/6ddee380-d648-45bb-a2d0-06324d438eca%3Fstatus%3D0)

了解如何设置[条件](https://docs.webmethods.io/additional-features/conditions)。

### 滤镜

过滤器允许您在工作流中的触发器上添加条件。设置过滤器后，只有满足指定的条件，工作流才会继续。测试之后，可以从触发器的输出屏幕访问过滤器窗口。

[![](img/b7adc1d9aebb36708850296671cb717c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fLGyVFiD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/filters.jpg/02c63489-34d4-4968-9fea-d856140f9c50%3Fstatus%3D0)

### 项目参数

项目参数可让您为项目设定自定义的键值对，并且消除了反复输入特定值的需要。设置参数后，只需单击一下，就可以在该项目的所有工作流程中使用它。

[![](img/b5c4191448190eedc4802bac54ec2e8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CH-6gS-b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/project_parameters.png/4e42a32a-efa3-4a78-9f0f-71c309cf22d1%3Fstatus%3D0)

例如，如果您想向同一个人发送多封电子邮件，您可以定义一个参数，将“Email”作为键，将他们的电子邮件地址作为值。这允许您在所有工作流中插入键值对作为输入，而不必重复键入整个电子邮件 ID。

开始使用[项目参数](https://docs.webmethods.io/additional-features/project-parameters)。

### Webhook

Webhook 是将 webMethods.io 集成到各种外部应用程序和服务的众多方法之一。webMethods.io 集成支持传入的 webhooks，以允许外部源发布关于特定事件的通知。

如果为工作流启用了 webhook，webMethods.io 集成会为该工作流生成一个唯一的 webhook URL，您需要将它提供给所需的外部应用程序。一旦这个外部应用程序中发生事件，它就会通过 HTTP POST 请求向 webMethods.io 集成发送一个 JSON 有效负载，其中包含事件的详细信息。然后，可以在您的工作流程中使用此有效负载。

[![](img/4c0627be8bca9fc105bd404742476702.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8tF-BL27--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/webhook.jpg/e446f8f5-40cc-4f28-8da1-e1a0c3226913%3Fstatus%3D0)

webhook 可以作为工作流的触发器。除了设置触发器，您可以简单地将 Webhook 添加到您的工作流画布中，并将 webhook URL 提供给外部源。例如，您可以向 Evernote 提供 webhook URL，并定义每当在您的帐户中创建新的笔记时都应该通知该 URL。有了这个，每次你的账户中添加一个笔记，Evernote 就会用新笔记的细节发出 HTTP POST 请求，并立即触发工作流。

要了解它是如何工作的，请参考我们的 [Webhooks 指南](https://docs.webmethods.io/developer-guide/webhook)。

### 容器

容器是一个隔离的运行时环境。它包含所有的依赖项，包括配置文件、二进制文件、系统库、代码等。运行工作流所需的。容器的大小决定了工作流的执行速度。尺寸越大，速度越好。目前，可用的容器大小为 256 MB 和 512 MB。

[![](img/f5273b089f60d5613447e7db15452c9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6rUhASLs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/container.jpg/46a99e00-aefe-4bd4-b52c-79265556a108%3Fstatus%3D0)

该容器消除了操作系统发行版和底层基础设施的差异，允许 webMethods.io 集成以标准方式在所有系统上运行。您可以从工作流设置下的执行设置部分查看或更改现有的容器大小。

阅读更多关于[容器](https://docs.webmethods.io/accounts-and-settings/container)的信息。

# 基础知识:了解 GUI

webMethods.io 集成主页是您登录租户时看到的第一个页面。主页屏幕为您提供了租户及其总体使用情况的概述。以下是构成 webMethods.io 集成主屏幕的主要组件:

1.  项目
2.  班长
3.  方法
4.  通知
5.  证明文件
6.  应用切换器
7.  租户设置

[![](img/2c3449e0cf0d01be94393c36b177d241.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pl0ilk6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/understanding_the_gui.jpg/b6bccfc1-f9ea-4ea7-9a2e-9a9abbf1069c%3Fstatus%3D0)

让我们详细了解这些不同的组件。

**1。项目**

所有现有的项目都以文件夹的形式出现在主屏幕上。每个文件夹显示项目的名称及其包含的工作流数量。文件夹上显示的图标是该项目工作流中使用的连接器或服务。

[![](img/213323fe8cad7882c17afc88790455b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lF7v3vze--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_1.jpg/88126b76-5473-4e35-89a1-132d09cf604f%3Fstatus%3D0)

注册后，所有租户都将获得一个默认项目。您可以通过点击+新建项目按钮来创建一个新项目。

要更改项目名称或删除项目，请单击项目文件夹右上角的垂直省略号图标。

[![](img/1c7a1d44e0959c3bd4fc01e5fc03b282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qi2l4DZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_2.jpg/b3a0f37f-de2e-450d-a484-809118d0c7a7%3Fstatus%3D0)

阅读更多关于[项目](https://docs.webmethods.io/workflow-building-blocks/projects)的信息。

当您单击项目文件夹时，您将被重定向到项目的单独页面。让我们看看这里的所有组件。

## - **切换项目:**下拉菜单让你轻松切换各种项目。

**工作流程:**“工作流程”选项允许您查看和修改项目中的所有工作流程。每个工作流程卡都显示工作流程的名称、上次修改日期和执行次数。该卡还包含一个用于激活或停用工作流的切换按钮。

[![](img/430d78615a6e125e226e461faaa650e8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Iao5A4od--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_3.png/c26e2fcb-2dbb-4c9c-a241-89fdd4fc9122%3Fstatus%3D0)

您可以将工作流程复制到另一个项目，将其导出到本地存储，并使用工作流程卡上的垂直省略号图标将其删除。将鼠标悬停在卡片上，您可以选择查看或编辑工作流程。

[![](img/31672fa633a46245d4cca6ba50c47778.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--BL4DxcFo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_4.jpg/80a5e1cc-60b5-4858-88b2-678f34c8be1c%3Fstatus%3D0) [ ![](img/2ead17b0f9103b33a2eb32dfdd506388.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qxSYCRV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_5.png/a34294ca-e7a9-4cb0-989a-20effdd953c3%3Fstatus%3D0)

*   **配置:**这允许您创建和管理与项目的不同工作流相关联的 webhooks、帐户、触发器和参数。

[![](img/a54f34122f0de3cc22e30e8029edf8ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wvW8cYA9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/projects_6.jpg/9be9980f-5ca1-4bf8-84a0-eace1689b9db%3Fstatus%3D0)

*   **日志:**“日志”链接显示项目工作流程中执行的所有活动的列表。

**2。监视器**

“监视器”部分提供了四个选项卡，您可以在其中执行这些操作:

*   **仪表板**

此部分允许您跟踪您的租户的总信用消耗和工作流执行情况。

[![](img/c837fae66e3cfc0af8b74dfefd24f25a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YFsFKFyr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/monitor_1.jpg/17eae588-ef26-4ec4-b434-fc5e653d335f%3Fstatus%3D0)

*   **审计日志**

审核日志记录了租户的所有用户执行的所有活动和更改。了解关于[审计日志](https://docs.webmethods.io/accounts-and-settings/audit-logs)的更多信息。

[![](img/71b7ae15a95e571e39b483e9c6806018.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n5GF3_rK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/monitor_2.jpg/a43d1a87-3d1b-48d5-9f04-92b87e02b36d%3Fstatus%3D0)

*   **活动日志**

活动日志允许您查看与您的帐户工作流相关的所有活动的顺序。阅读更多关于[活动日志](https://docs.webmethods.io/accounts-and-settings/activity-logs)的信息。

[![](img/43a7c7e5ef7d374b8507ae8df7884cad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KIfM5_A1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/monitor_3.jpg/c140d43c-c355-453e-8264-c23e528e7167%3Fstatus%3D0)

*   **预警规则**

此部分允许您定义向特定用户发送电子邮件的规则，以通知他们工作流执行的状态。了解更多关于[预警规则](https://docs.webmethods.io/additional-features/alert-rules)的信息。

[![](img/52c4ca53c04ada583a20c13c2c97294d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ibo0YaUV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/monitor_4.png/1ca30acf-db6a-4114-a81c-cf42905bc219%3Fstatus%3D0)

**3。食谱**

您可以使用 Recipes 部分将工作流导入 webMethods.io 集成。配方使您能够访问 webMethods.io 集成管理共享的公共工作流。您可以将这些工作流中的任何一个导入到项目中。

[![](img/743473a28506651ed8741374a1bdf49c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--yY73-yIN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/recipes_1.jpg/d32f06b3-9917-473a-8a38-d242d9733db7%3Fstatus%3D0) [ ![](img/3ca5a784ae5bcf0216ab756226566380.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CI84fs-C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/recipes_2.jpg/f1ead706-ed90-4f97-9204-4d398e1f8248%3Fstatus%3D0)

了解更多[食谱](https://docs.webmethods.io/workflow-building-blocks/recipes)。

**4。通知**

通知图标显示与您的租户工作流相关的最新通知列表。新通知由通知图标顶部的红点表示。

[![](img/a61b68cb6c69ba3c432cbd77078f06c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oQ2seSK6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/notifications.png/4726c5af-1781-4ad7-9471-c24ce7f6cfb1%3Fstatus%3D0)

**5。文件**

问号图标会将您带到[文档站点](https://docs.webmethods.io/)，在这里您可以找到有关 webMethods.io 集成平台特性和功能的更多信息。

**6。应用切换器**

通过应用切换器图标，您可以在 webMethods.io 集成平台、您已注册的其他软件 AG 云产品、webMethods.io 流编辑器和我的云之间进行切换。在我的云上，您可以找到其他 Software AG 云产品和管理设置的链接。

[![](img/5146cb912e2238ba1a7ff9d22e36fd5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1jNsBtUg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/app_switcher.jpg/4d462d57-904e-4830-9d68-99acfd797a69%3Fstatus%3D0)

**7。租户设置**

在此部分，您可以查看和管理您的租户设置。它允许您:

*   查看租户详细信息，创建新的租户角色，查看现有用户并为其分配角色
*   获取关于 webMethods.io 集成的最新更新
*   查看信用消耗和工作流执行统计数据
*   注销您的租户

[![](img/8caf1a4bc5af1c14018807b1a33355b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WntPSkX1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/tenant_settings.png/a9d72de4-6379-47da-ab0b-5b74b9f38c8c%3Fstatus%3D0)

阅读更多关于[租户设置](https://docs.webmethods.io/accounts-and-settings/tenant-settings)的信息。

# 基础知识:账户和设置

### 注册流程

任何用户都可以使用这个[注册](https://signup.softwareagdev.cloud/%23/?product=webmethodsioint)链接注册 webMethods.io 集成。在注册过程中，用户需要提供一个云名称，即他们的租户 ID。在所有后续会话中，用户必须输入此 ID 才能访问他们的软件云实例。

### 租户设置

[![](img/5feb4f48e3aecafcfa1feeec420b0b58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--51QbR6ch--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/tenant_settings_1.png/a6b620b7-318d-4a59-afbb-b2f302f01f77%3Fstatus%3D0)

webMethods.io 集成允许您集中查看和管理租户的设置。要访问您的租户设置，请单击位于主屏幕右上角的配置文件图标，并从显示的选项列表中选择设置。

这里列出了 3 个选项卡:

*   [我的信息](https://docs.webmethods.io/accounts-and-settings/tenant-settings#my-information):查看您的租户详细信息，如电子邮件 ID、租户名称、角色、计划和开发者密钥。
*   [角色](https://docs.webmethods.io/accounts-and-settings/tenant-settings#roles):角色是您分配给用户的一组权限。在该选项卡中，租户所有者和管理员可以查看现有角色，也可以添加新角色。
*   [用户](https://docs.webmethods.io/accounts-and-settings/tenant-settings#users):查看现有用户及其分配的角色。

### 容器

容器是一个独立的运行时环境，允许您控制工作流的执行速度。[了解](https://docs.webmethods.io/accounts-and-settings/container)什么是容器，它如何工作，以及如何为您的工作流执行选择容器大小。

### 活动日志

活动日志是一组记录，包含与租户工作流相关的所有活动的顺序。日志记录包括时间戳、标题、执行状态等详细信息，以及查看详细日志的选项。

[![](img/84efa0dca4106f84eedf05c0cd0a5d16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p1bFRWew--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/activity_logs.png/c33a49b9-16c2-4be6-a278-e0582b6eea83%3Fstatus%3D0)

有两种方法可以访问[活动日志](https://docs.webmethods.io/accounts-and-settings/activity-logs):

*   从画布访问工作流执行日志
*   从“活动日志”页面访问所有与工作流相关的日志

### 度量标准

指标页面可让您快速了解 webMethods.io 集成帐户的配额使用情况。要访问此页面，请单击个人资料图标并选择[指标](https://docs.webmethods.io/accounts-and-settings/metrics)。

度量页面分为三个主要部分:

1.  每月信用使用指标
2.  线形图
3.  特定于集成的信用使用详细信息

### 审计日志

[审计日志](https://docs.webmethods.io/accounts-and-settings/audit-logs)部分维护一个租户内执行的所有操作的记录，包括执行的操作类型、执行操作的用户以及日期/时间等详细信息。您可以通过应用基于项目、工作流或警报规则的过滤器来检索特定日志。

# 基础知识:数据访问和安全

安全性是 webMethods.io 集成的关键部分。该平台提供高级安全工具，帮助您设置访问控制并安全地访问业务数据。

### 防火墙友好的 IPs

webMethods.io 集成提供了一个[公共 IP 地址](https://docs.webmethods.io/data-access-and-security/firewall-friendly-ips)的列表，您可以将其列入白名单以访问受防火墙保护的数据。

### 数据屏蔽

通过屏蔽输入和输出的部分或全部键，为应用程序数据增加一层额外的安全性。了解如何屏蔽和取消屏蔽数据。

[![](img/baaa851bb398fd034a232d7cb090ca47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sn5N5hR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/data_masking.jpg/8cd0bbcf-2428-40f7-b000-966e74dbddb7%3Fstatus%3D0)

这里有一个开发者指南列表，可以帮助你深入了解 webMethods.io 集成中最流行的特性。

### 连接器生成器

使用我们的开发人员平台创建自定义连接器(一组触发器和动作),并与其他用户共享。如果您有一个私有 API，webMethods.io 集成允许您使用连接器构建器为这些 API 创建自定义触发器和操作。

[连接器构建器](https://docs.webmethods.io/developer-guide/connector-builder)是一个定制的 Node.js 应用程序，您可以使用任何 web 服务的 API 在 Node 版本 4.x.x 和 8.14.X 之间构建它。一旦您在 webMethods.io 集成上部署了应用程序，它将首先被验证，然后供您在本地使用。

### Node.js

虽然 webMethods.io 集成为您的工作流提供了 1000 多个操作，但您也可以使用 [Node.js](https://docs.webmethods.io/developer-guide/creating-custom-actions-with-node.js) 操作创建自己的自定义操作。它提供了一个动作生成器，允许您插入自定义代码并创建动作，这些动作的外观和工作方式与其他动作完全相同。

[![](img/1f9bf44c937304502f0871b7f00b43ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FBiJ03XI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/node_js.jpg/4a2fc482-80d2-4631-a31c-8199ec05a6cb%3Fstatus%3D0)

### Webhook

使用 webhooks 将 webMethods.io 集成工作流与外部 web 服务和应用程序集成。webhook 是用户定义的 HTTP 回调，它允许第三方应用程序通知 webMethods.io 集成有关特定事件的信息。了解什么是 webhooks 以及它们是如何工作的。

### HTTP 请求

HTTP 操作允许您向服务器发出 HTTP 请求。了解如何在您的工作流中配置 HTTP 操作。

[![](img/46b1239a0b1b5ccd877108e5f76e18a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8FloK6c---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/http_request.png/919527ca-3273-4afc-a7f8-7c3efe106ce8%3Fstatus%3D0)

### 参数化授权

将授权作为参数传递，以便与多个用户安全地共享单个工作流。参数化授权允许您根据您在 webhook 中发送的加密授权来动态更改您想要访问的帐户。理解[参数化授权](https://docs.webmethods.io/developer-guide/parameterized-authorization)如何工作。

### 混合集成

混合集成功能允许您访问通常由于安全限制、防火墙或许可问题而无法访问的本地应用程序。该功能在 webMethods.io 集成平台和防火墙后的服务器之间建立安全连接，允许您添加本地应用程序，并随后在您的工作流中访问它们的数据。了解有关[混合集成](https://docs.webmethods.io/developer-guide/hybrid-integration)的更多信息。

[![](img/bf87164af892d8fabe03f0740eb2677b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fE-KinFe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://techcommunity.softwareag.com/documents/portlet_file_entry/10157/hybrid_integration.png/7793a4b1-a507-49be-9a97-78c83a29daa8%3Fstatus%3D0)

### 流量编辑器

webMethods.io 流编辑器为您提供了一个图形拖放工具，帮助您创建复杂的集成并在工作流中使用它们。流编辑器具有丰富的数据映射、熟悉的调试机制、大量内置服务和用于监控集成的仪表板。了解如何使用[流编辑器](https://docs.webmethods.io/developer-guide/flow-editor)创建集成。

接下来阅读: [webMethods.io 集成教程——开发者指南](http://techcommunity.softwareag.com/web/guest/pwiki/-/wiki/Main/webMethods.io+Integration+tutorial+-+Developer+Guide)