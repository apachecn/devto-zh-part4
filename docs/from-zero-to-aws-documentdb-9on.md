# 从零到 AWS 文档 b

> 原文：<https://dev.to/valgaze/from-zero-to-aws-documentdb-9on>

在这篇文章中，我们将从零到一个带有公共端点的 DocumentDB 数据库，通过它您将能够写入数据(通过 API Gateway &一个 Lambda 函数)

我们现在将跳过 [Cloudformation](https://aws.amazon.com/cloudformation) / [无服务器](https://github.com/serverless/serverless)模板，并在大约 10 分钟内“手动”启动并运行一切。(为了稍后将本笔记中的提示纳入源代码管理，请参见[这里的](https://docs.aws.amazon.com/documentdb/latest/developerguide/quick-start-with-cloud-formation.html)获取一个 starter CloudFormation 模板&以及来自无服务器的[组件工具)](https://github.com/serverless/components)

下面的视频可以在这里找到:[https://www.youtube.com/playlist?list = plnbevj 82 pzgxw 9 vzva 5h 7 osepryylut 6d](https://www.youtube.com/playlist?list=PLNBEvJ82PZGxW9vzva5H7OsepryYlUT6D)

# 继续之前要做的两件方便的事情

1)获得一个类似于[邮差](https://www.getpostman.com/)或[好友](https://www.producthunt.com/alternatives/postman)的 API 测试工具

2)克隆这个 git 存储库:

```
$ git clone https://github.com/valgaze/documentdb-fun 
```

Enter fullscreen mode Exit fullscreen mode

# “亚马逊 DocumentDB(与 MongoDB 兼容)”

由 AWS 于 2019 年 1 月发布的 DocumentDB 是一个 NoSQL 文档数据库，超载了运行在亚马逊 infra 上的便捷功能。它使用起来很简单，可以在各种场景中很好地工作，但是它确实可以作为运行 MongoDB 的一些工作过度且手动管理的 EC2 实例的“可伸缩”替代物。

DocumentDB 让您不必太担心:

*   低于 64TB 的存储要求(在所有读取实例之间共享的 SSD 上，以 10GB 为增量自动增长到 64TB)

*   供应/管理“集群”(您可以非常快速地启动/关闭最多 15 个“助手”实例)

*   补丁和备份(备份可配置为持续 5 分钟，数据跨 3 个可用性区域复制 6 次)

*   加密(使用 TSL)

重要的是要清楚 DocumentDB 绝对不是“云 MongoDB”——如果你正在寻找它，请参见 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)

文档声明 DocumentDB *"[...]通过模拟 MongoDB 客户端期望从 MongoDB 服务器获得的响应来实现 Apache 2.0 开源 MongoDB 3.6 API，允许您将现有的 MongoDB 驱动程序和工具与 Amazon DocumentDB 一起使用。”*换句话说，在交换了一些登录凭证之后，目标为 3.6 的 MongoDB 驱动程序的应用程序应该可以使用 DocumentDB。如果你好奇，有一些[合理知情的猜测](https://news.ycombinator.com/item?id=18870397)它[可能是幕后的 Postgres](https://www.enterprisedb.com/blog/documentdb-really-postgresql) 。

只要您使用的 AWS 帐户拥有创建 IAM 角色的权限，并且能够访问我们将使用的资源(即 DocumentBD、Lambda 和 API Gateway)，您就应该处于良好的状态。我们开始吧！

# 第一步:设置 Lambda 角色

这是一项简单而重要的任务。DocumentDB 没有公开的端点——它生活在自己的“VPC”中(虚拟私有云——本质上是自己的网络段/名称空间),因此 Lamba 函数必须能够访问同一个 VPC，以便与数据库集群进行交易。但是为了改变它的 VPC，Lambda 函数需要一个 IAM 角色来授予它这样做的权限(具体来说是**AWSLambdaVPCAccessExecutionRole**),我们将在下面创建它:

[https://www.youtube.com/embed/wpZPfX4HvjM](https://www.youtube.com/embed/wpZPfX4HvjM)

**温馨提示:**

*   AWSLambdaVPCAccessExecutionRole:*管理弹性网络接口以将您的功能连接到 VPC 的权限。*

# 第二步:创建 Lambda 函数+ API 网关

在这里，我们将创建一个名为“mylambda”的 Lamba 函数，并配置/部署一个 API 网关端点，向它发送数据，这将调用 lambda 函数

[https://www.youtube.com/embed/vhSCvNt_2MA](https://www.youtube.com/embed/vhSCvNt_2MA)

**温馨提示:**

**λ函数:**

*   使用前面创建的 IAM 角色 *"lambda-vpc"* 创建您的 lamba 函数(我们需要将 lambda 切换到与 DocumentDB 集群相同的 vpc)

**API 网关:**

*   一旦你部署了你的 API，确保你用正确的资源发布到正确的 URL 如果你看到{ message:" Missing authentic ation Token " }作为响应，确保你确实发布到了正确的 URL

# 第三步:创建 DocumentDB 集群

这里，我们将建立 DocumentDB 集群，并注意它的连接字符串:

[https://www.youtube.com/embed/xM01Fze0Mx8](https://www.youtube.com/embed/xM01Fze0Mx8)

**温馨提示:**

**文档数据库:**

*   记下您的数据库的用户名和密码以及连接字符串，我们稍后会用到它们

*   您现在可以坚持使用默认配置(如果您预计会出现大量活动，您可以最多读取 15 个副本)

# 第四步:用新代码+连接字符串更新 Lambda 函数

选项 A(最快):只需上传文件 function.zip，并在 config.js 中更改 CONNECTION_STRING

[https://www.youtube.com/embed/_e_R2CqR3l8](https://www.youtube.com/embed/_e_R2CqR3l8)

选项 B(如果 [npm](https://www.npmjs.com) 在你的系统上可用):修改 config.js 中的 CONNECTION_STRING，用
创建一个新的 function.zip

```
$ npm run zip_me 
```

Enter fullscreen mode Exit fullscreen mode

然后上传 function.zip 文件来替换您现有的 Lambda 代码

# 第五步:配置λ

我们的最后一步是配置 Lambda 的 VPC、子网、安全组和超时——因为我们创建了 *lamba-vpc* 角色，Lambda 可以进行这些修改(没有它，保存将[失败](https://stackoverflow.com/questions/55760149/aws-lambda-function-not-saving-on-vpc-selection/55780781#55780781)

[https://www.youtube.com/embed/R5wwa6zFhn4](https://www.youtube.com/embed/R5wwa6zFhn4)

**温馨提示:**

*   **VPC:** 找到你的 DocumentDB 集群，向下滚动直到找到 VPC 名字(ID)，这应该是 Lambda 的 VPC

*   **子网:**与 DocumentDB 的 VPC 相同的子网

## (重要)关它自己

DocumentDB 不是免费的——参见下面的视频，了解如何正确确保资源得到清理。

[https://www.youtube.com/embed/dve7wcB0eXM](https://www.youtube.com/embed/dve7wcB0eXM)

如果您还在坚持，那么现在您应该有一个 DocumentDB 部署，它带有一个工作的公共端点，您可以随心所欲地扩展/简化它——应该可以了！

回购看这里:[https://github.com/valgaze/documentdb-fun](https://github.com/valgaze/documentdb-fun)

点击这里查看嵌入视频的播放列表:[https://www.youtube.com/playlist?list = plnbevj 82 pzgxw 9 vzva 5h 7 osepryylut 6d](https://www.youtube.com/playlist?list=PLNBEvJ82PZGxW9vzva5H7OsepryYlUT6D)

# 进一步阅读:

*   [https://docs . AWS . Amazon . com/document db/latest/developer guide/what-is . html #概述](https://docs.aws.amazon.com/documentdb/latest/developerguide/what-is.html#overview)

*   [https://blog . webiny . com/connecting-to-AWS-document db-from-a-lambda-function-2b 666 c9e 4402](https://blog.webiny.com/connecting-to-aws-documentdb-from-a-lambda-function-2b666c9e4402)

*   [https://medium . com/@ michaelrbock/no SQL-摊牌-MongoDB-atlas-vs-AWS-document db-5d FB 00317 ca 2](https://medium.com/@michaelrbock/nosql-showdown-mongodb-atlas-vs-aws-documentdb-5dfb00317ca2)

*   [https://www . MongoDB . com/blog/post/optimizing-AWS-lambda-performance-with-MongoDB-atlas-and-nodejs](https://www.mongodb.com/blog/post/optimizing-aws-lambda-performance-with-mongodb-atlas-and-nodejs)

*   [https://docs.aws.amazon.com/lambda/latest/dg/vpc-rds.html](https://docs.aws.amazon.com/lambda/latest/dg/vpc-rds.html)

# 延伸阅读-λ+VPC

*   [https://docs . AWS . Amazon . com/lambda/latest/DG/lambda-intro-execution-role . html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)

*   [https://docs . AWS . Amazon . com/lambda/latest/DG/lambda-intro-execution-role . html](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)

*   [https://docs.aws.amazon.com/lambda/latest/dg/vpc.html](https://docs.aws.amazon.com/lambda/latest/dg/vpc.html)

*   [https://docs.aws.amazon.com/lambda/latest/dg/vpc-rds.html](https://docs.aws.amazon.com/lambda/latest/dg/vpc-rds.html)

*   [https://medium . com/@ justianotherspyy/how-to-connect-your-lambda-function-securely-to-your-private-rds-instances-in-your-VPC-29789220 a33](https://medium.com/@justanotherspyy/how-to-connect-your-lambda-function-securely-to-your-private-rds-instances-in-your-vpc-29789220a33)

# 进一步阅读- Lambda 优化:

*   [https://docs . AWS . Amazon . com/lambda/latest/DG/running-lambda-code . html](https://docs.aws.amazon.com/lambda/latest/dg/running-lambda-code.html)

*   [https://www . MongoDB . com/blog/post/optimizing-AWS-lambda-performance-with-MongoDB-atlas-and-nodejs](https://www.mongodb.com/blog/post/optimizing-aws-lambda-performance-with-mongodb-atlas-and-nodejs)

*   [https://docs . atlas . MongoDB . com/best-practices-connecting-to-AWS-lambda/](https://docs.atlas.mongodb.com/best-practices-connecting-to-aws-lambda/)

*   [https://blog . cloud boost . io/I-wish-I-know-how-to-use-MongoDB-connection-in-AWS-lambda-f91cd 2694 AE 5](https://blog.cloudboost.io/i-wish-i-knew-how-to-use-mongodb-connection-in-aws-lambda-f91cd2694ae5)

# 延伸阅读- Lamba 代理整合

*   [https://stack overflow . com/questions/56188864/AWS-lambda-clarification-on-retrieving-data-from-event-object/56191784 # 56191784](https://stackoverflow.com/questions/56188864/aws-lambda-clarification-on-retrieving-data-from-event-object/56191784#56191784)

*   [https://stackoverflow.com/a/52240132/3191929](https://stackoverflow.com/a/52240132/3191929)