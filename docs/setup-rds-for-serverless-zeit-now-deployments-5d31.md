# 为无服务器 Zeit Now 部署设置 RDS

> 原文：<https://dev.to/codemochi/setup-rds-for-serverless-zeit-now-deployments-5d31>

RDS 是托管数据库的绝佳选择。它内置了自动数据库备份和日志记录，并且可以随着用户的增加而扩展实例。一个非常适合您的应用程序的数据库实例可以确保您不会为不需要的服务器成本买单。虽然 RDS 不像 DynamoDB 那样放手不管，因为您仍然需要根据需要调整它的大小，但 MySQL 和 Postgres 是更受欢迎的数据库选项，因此它们往往会得到更好的支持。

在本帖中，我们将介绍如何创建自己的 RDS MySQL 实例，该实例由 AWS 托管，并为连接到 Zeit Now 服务进行了所有设置。如果你想无服务器地部署你的 Prisma 2 后端，你将需要这个设置，就像我们将在下一篇文章中展示的那样，但是这个选项对于其他后端 ORM 包也是需要的，比如用于连接到数据库的`sequelize`。设置好数据库后，我们还将介绍如何配置您的安全组，以便您可以从外部世界或您的 lambda 函数连接到它。

*本帖视频:*
[https://www.youtube.com/embed/yK6cwPbyy5I](https://www.youtube.com/embed/yK6cwPbyy5I)

**第一步:**

转到 AWS 控制台，然后转到 RDS 页面。在“资源”面板中，单击“数据库实例”链接。然后单击创建数据库按钮。

[![Go to RDS and start the database creation process.](img/959bb35210392534c8a869b18bebaee1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9OquZvz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/b39aa719db79e9e55b6399f018122d4e/5d442/figure1.png)

**步骤 2:配置 RDS 实例和模板类型**

创建一个 MySQL RDS 类型，然后单击自由层。对于你的目标来说，这可能已经足够好了。它将只运行一个数据库实例，这意味着如果服务器停止运行，它将不能容错。将来，您总是可以在不同可用性区域的三个或更多数据库实例之间增加冗余。

[![Create a MySQL Database.](img/6a57148d60018374c6a3b0ae839837d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAh0gPbl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/b99d28b761a5f05064edf601cb16b712/5d442/figure2.png)

**步骤 3:配置 RDS 设置**

我们可以选择实例标识符，这就是我们将从内部调用的数据库。注意，这是数据库名称的*而不是*——我们将在后面设置。接下来，我们可以设置 root 用户名和密码。我们总是可以在将来创建更多的用户，使用更小范围的权限来限制我们可以写入的表。

[![Configure the RDS user and password settings.](img/7037cb20d065e079dffdc40c735a7b3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4NMxblM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/198cd50e1ed1c758c93f274ce516fa87/5d442/figure3.png)

我们还可以禁用存储自动扩展，并分配 20 GB 来启动。

**步骤 4:配置 VPC 设置**

VPC 是云的一个封闭部分。我们想创建一个新的 VPC，这样这个数据库实例就可以生活在它自己的小世界里，我们想确保我们进入高级设置，并将其设置为对世界开放。如果我们不采取额外的步骤，我们将无法从我们的开发机器访问它，现在后端实例将无法到达它。

接下来，我们必须设置安全组——把它想象成一个防火墙。我们可以通过选择性地打开端口来控制对数据库的访问，还可以指定允许请求来自哪个 IP 地址。默认情况下，所有流量都被允许流出，但是只有端口 3306 被允许从我们的开发机器的 IP 地址流出。我们将需要在教程的后面解决这个问题。

[![Configure VPS and security group settings.](img/89a26f0f6c64efe1f498989a393ab161.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LrjoR3Lt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/5d01faa67be799858e03a30aa837cc07/5d442/figure4.png)

**步骤 5:附加配置**

在这里，我们可以创建一个默认数据库。在我们的例子中，我想创建一个名为 prisma 的，因为这是我在配置中告诉 prisma 2 服务要写入的内容。我们还可以指定是否需要自动备份。对于生产数据库，您肯定希望这样，但这是出于开发目的，所以我不会担心。

[![Specify the default database and automated backups.](img/4b6b218fd3030d020b7350db880375f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_KS9jAQz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/ed22f4fb77bf19437c9d7834f84b8bc2/ae753/figure5.png)

**第六步:创建数据库**

创建数据库后，AWS 会告诉您需要几分钟才能完成。

[![Waiting for the database to finish creating.](img/76047f68d50fc6395a1057d97342dec0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QIq4uIkN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/d3bf30caa4e3b97ecf23cd7c60034780/ae753/figure6.png)

[![Database connectivity and security panel.](img/ed87309c819f1cb4d67bc94c8b2b7d14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HV3LCxp---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/6cb0133a31f8a77c626b9f3cf525a4f5/ae753/figure8.png)

**第 7 步:向世界开放您的 RDS IP**

接下来，我们需要更改安全组，以便它对全世界开放。默认情况下，它将只对你当前的 IP 地址开放，这对调试来说很好，但它不会对 Zeit 的 Now 服务起作用，因为它在 AWS 上，所以 IP 将是不同的。更复杂的问题是，Zeit 并不坚持特定的 IP 地址子集，这是不幸的，因为这意味着我们需要向整个世界开放我们的 RDS 实例，我们不能将其局限于 Zeit lambda 函数。这意味着我们选择一个非常安全的用户名和密码非常重要，这样即使端口暴露了，其他人也无法进入。

[![Security group for the RDS instance.](img/8c4c848477f6b73bde8268b0759bac19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6iTYIHoG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/dae0ebff7c5cfe13727aae794e0b8203/ae753/figure9.png)

[![Open port 3306 to the world in the security group.](img/d8934c13a42c1e008973bcf2084b105b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Te_38Bz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/a25ba0b230f795eb16e958c4c4cae9a9/ae753/figure10.png)

**步骤 8:连接到我们的数据库**

最后，我们现在可以连接到我们的数据库。我们可以从 RDS 窗格中复制数据库 URL。它将类似于:

```
yourRds-instance-someRandomId-yourRegion.rds.amazonaws.com 
```

使用我们之前设置的用户名和密码以及端口 3306。你可以用任何你喜欢的程序连接到它，但我是它的粉丝(免费！)[续作 Pro](https://www.sequelpro.com/) 。

当您想要创建一个连接字符串时，它将采用以下形式:

```
mysql://yourUsername:yourPassword@yourRds-instance-someRandomId-yourRegion.rds.amazonaws.com:3306/yourDatabasename 
```

就是这样！确保如果你在 Zeit 中使用这个连接字符串，你永远不会，*永远不会，*永远不会*把这个字符串提交给 Github。如果您不小心在 Github 或其他地方泄露了您的连接字符串，请确保立即更改您的密码。相反，使用`now secrets`来管理您的连接字符串。我们在关于无服务器部署的帖子中讨论了这一点，[，所以请务必查看一下](https://www.codemochi.com/blog/2019-08-12-prisma-2-now/)！*

从那里来的还有更多！
[点击这里给我们您的电子邮件，我们会在发布新内容时通知您。](https://gmail.us20.list-manage.com/subscribe?u=37f38485b2c7cff2f3d9935b5&id=e3bc056dde)我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-08-07-setup-rds-for-zeit-now-deployments/) 。