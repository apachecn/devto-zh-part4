# 使用 Microsoft Azure 函数和 Cosmos DB 作为输入绑定

> 原文：<https://dev.to/darius1/using-microsoft-azure-functions-with-cosmos-db-as-an-input-binding-c31>

在本文中，我将提供对 Microsoft Azure、Azure Functions 和 Cosmos DB 的概述，以及如何创建由 HTTP 触发并利用 Cosmos DB 作为输入绑定的 Azure 函数的教程。

## 微软 Azure 是什么？

微软 Azure 是一种“云计算服务，由微软创建，用于通过微软管理的数据中心构建、测试、部署和管理应用程序和服务。”它使用户能够将其基础架构和应用程序扩展到全球范围，提高安全性和工作效率。用户在 Azure 上的体验可以以最适合他们需求的方式定制。

### 什么是 Azure 函数？

Azure Functions 是微软的功能即服务。谷歌的 FaaS 产品被称为谷歌云功能，亚马逊的被称为 Lambda 功能。Azure Functions 允许开发人员创建无状态、事件驱动的应用程序，可以利用云提供的所有优势。它们可以通过多种方式触发，比如 HTTP、定时器，或者每当 Cosmos DB 中发生变化时。Azure Functions 还通过使用输入和输出绑定帮助开发人员更快地创建应用程序。绑定提供了一种将附加资源附加到函数的机制。关于绑定的更多详细信息可以在[这里](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)找到

### 什么是 Cosmos DB？

Cosmos DB 是“微软的全球分布式多模型数据库服务”它提供了高可用性，以及跨多个区域扩展吞吐量和存储的能力。它具有极低的延迟，并支持 SQL 和 SQL and。

## 创建一个 HTTP 触发的 Azure 函数，该函数使用 Cosmos DB 作为输入绑定

我在创建本教程时，假设您已经创建了一个 Microsoft Azure 帐户。如果没有，你可以点击[这里](https://azure.microsoft.com/en-us/free/)注册一个免费账户，为你提供 30 天 200 美元的信用。

### 在 Cosmos DB 中创建条目

首先，导航到 [Azure 门户](https://portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)并点击位于屏幕左侧收藏夹选项卡下的 Azure Cosmos DB。

[![Azure Portal with Cosmos DB Highlighted](img/07dd74ba7b3161eb7659e1c98e211916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2B3Ww4SW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uivb877pxfrwn3mi9w91.png)

单击添加创建一个新的 Cosmos DB 帐户

[![Create new Cosmos DB account](img/890d6a3cf70c87bb16eee2ef3a21c4d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pOqnjAcy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cm7neyi977ix71ae24m4.png)

现在，我们可以为我们的帐户指定详细信息。首先指定一个资源组名。我决定将我的矿命名为**测试资源组**。接下来提供一个帐户名称。我选择把我的账号命名为**教程——账号**。最后，选择一个最适合你的地方。完成后，选择 Review + Create。然后在下一个屏幕上单击创建。现在，我们需要等待几分钟来创建我们的 Cosmos DB 部署。

[![Specify Cosmos Account Details](img/33e650d9a2e7988a3502a9eaa3409d43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yvPODn_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m2d369vtlrjdmyra1hgm.png)

导航回 Cosmos DB 页面，我们应该能够看到我们新创建的帐户

[![Cosmos DB account](img/d01f993f5eb96692dc6ad7a7cd8e8efc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nm1vJvVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pxomihpn30bdmd4xpwxk.png)

单击概述并添加容器

[![Cosmos DB Overview](img/1f52e142acdca4026cd5b17018866370.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yUNKMr4q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/323izbalp1ztcddiaw5b.png)

指定数据库 id、容器 id、分区键，然后选择确定。我设置我的数据库 id 为 **tutorial-db** ，我的容器 id 为 **tutorial-container** ，我的分区键为 **/facts**

[![Specify Container Information](img/70c5f82c28a1bace5975fb35fa59fdfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GUc1490X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gt4kct71ufsefjkzo0z5.png)

从这里，我们可以进入我们的新数据库。选择**教程-db** ，然后选择**教程-容器**，然后选择**物品**。最后点击**新增项目**。

[![New Item](img/27fb21c56f9736a6f600f8102147016b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jsAAa26O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r1ysn8yk58cwggh6cj6k.png)

我们现在应该在这样的页面上

[![Blank New Item](img/a679446367e6451d9889b5fd54ad13cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_wu4equS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ipv9ncjztix3lb7b5owd.png)

现在我们可以创建将要使用的 JSON 文件。在我们的第一个文件中，我们将设置 id 为**hard _ details**并创建一个名为 **facts** 的新键，其值为:**制表符比空格好**。然后单击保存。(将添加额外的键-值对，但它们对每个人都不同，可以忽略。)我们的第一个文件应该是这样的

[![Tabs > Spaces](img/91cbe3724bddabc3587a2df6e0faffc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xP92R1Fs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/14mztvhgyg5j4w8v4mgi.png)

创建第二个 JSON 文件，id 为 **colors** ，关键字为 **facts** ，值为:**红色、蓝色和绿色是颜色**。第二个文件应该如下所示

[![Colors](img/5d8ef7083e23656399ed7fa4489e8b2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wU7bU9vg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8o3vafh2h46huemq6age.png)

### 创建 Azure 函数

现在，单击左侧菜单中的功能应用程序，导航至功能应用程序页面

[![Function App Page](img/dccc4829eb5071dadfb86ec3ff7f586b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2o1Sh3aw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6wpvy0a76lhj8w0mv44z.png)

点击添加开始配置我们的新功能应用

[![Create Function App](img/786d9b8a07a7350c6eaec443409bd410.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JB7uQdch--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cfstdlzouqz0zwwez5up.png)

创建应用程序名称、位置和运行时堆栈。我将我的应用程序命名为 factsmachine，将我的位置设置为美国东部 T2，选择 T4 节点作为我的运行时堆栈。选择创建，等待几分钟，让您的功能应用程序部署完毕。**注意:应用程序名称必须唯一！**

[![Function App Details](img/93cfd26de3f65ebd0972b664436c7872.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R7GdIuN_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wet9z3fahcucrfq7w7hv.png)

导航至新功能应用程序，并选择新功能

[![New Function](img/411d65b8131d0252fd06a48008ad950c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VCHcU3xT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xm6sbcd681v0g09e8mvd.png)

我们现在将学习新功能 Quickstart。为开发环境选择门户

[![Function Development Environment](img/3ba7f244dd52b30fb708daf8f554312e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dmsj-3sM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qdqno51m6sypmw8hxbnh.png)

为创建函数选择 Webhook + API，然后单击创建。

[![Webhook](img/ac872f311acda64e5e91ea7c19825b5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4UaHNKsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3fyf65ptv8ktzxs4d4th.png)

创建了一个名为 **HttpTrigger1** 的新函数，它包含以下代码

[![Default Function Code](img/2c9e5c3e7e0d66a5b2f77c01c58beafa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZwJWhuEC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/spaeddi8nuthudaxy96j.png)

### 集成 Cosmos DB 和 Azure 函数

选择**集成**，然后选择**新输入**并选择 **Azure Cosmos DB** 点击选择并安装 Azure Cosmos DB 依赖项，如果它们还没有安装的话。

[![Function Integration](img/3c3d982369dd73b5b2ecf1c64e5b91ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hnVEGjFG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7wli2ot8i6m4fdnw3hop.png)

要将 Cosmos DB 配置为我们的输入绑定，我们需要首先指定一个文档参数名。默认情况下，它是**输入文档**。接下来，我们指定集合名称。这对应于我们为 Cosmos DB 文件条目创建的容器的名称，即**教程-容器**。我们现在需要设置我们的 SQL 查询。这将允许我们按 id 过滤数据库中的文件。当我们运行函数时，我们将提供 id 作为 HTTP 请求参数。我们的 SQL 查询将是 **SELECT * FROM c，其中 c.id = {id}** 我们的数据库名称将是 **tutorial-db** ，我们可以通过单击“新建”并从弹出菜单的下拉列表中选择我们的数据库来指定我们的 Azure Cosmos DB 帐户集合。最后，我们将分区键指定为 **/facts** ，然后单击 Save

[![Function Input](img/73637c262a34f4575035f24c789c5179.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bI6b4-K5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sup6eilpynse7roy35cp.png)

导航回我们函数的 index.js 文件，并将代码编辑为

[![Function Code](img/46793598238481b6e9202d43534d0a0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f0y8kuqn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wupp1wqo3h1n1i6ao9ak.png)

我们通过使用**context . bindings . { document parameter name }**来访问存储在 Cosmos DB 中的文档。因为我们关心的信息是**事实**键的值，所以我们使用**context . bindings . input document[0]。事实**得到这个信息。

现在我们终于可以测试我们的功能了！单击屏幕右侧的测试，打开测试选项卡。

我们的 HTTP 方法是 **GET** 我们的查询参数是 **id** ，值为**hard _ details**。单击保存并运行，我们可以看到我们的输出是:**我们的事实:制表符比空格好**

[![Test First Fact](img/c54883632a7649ded63988cdfc427a27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4qJP2d0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o7ofz4yn6x5axfsn2e1x.png)

如果我们改变我们的 id 值为**颜色**我们可以看到我们的输出改变为**我们的事实:红色、蓝色和绿色是颜色**

[![Test Second Fact](img/aefb5b170307f098e06477f941f43d68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zpaAfCPM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9oz3y11ldkhjriqa9jqj.png)

## 恭喜恭喜！

就是这样！现在，您已经创建了一个包含两个 JSON 文件的 Cosmos DB 实例，并且创建了一个由 HTTP 请求触发的 Azure 函数，该函数可以通过 HTTP 请求中提供的 id 参数从 Cosmos DB 获取一个文件！