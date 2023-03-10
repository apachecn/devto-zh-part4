# Prisma 2 潜行峰-未来看起来快速而安全

> 原文：<https://dev.to/codemochi/prisma-2-sneak-peak-3k9l>

在过去的一周里，Prisma 2 的测试版发布了。它仍处于早期阶段，但这个版本对流行的开源 GraphQL API 层进行了许多令人兴奋的改进。Prisma 1 通过抽象出与数据库的接口，彻底改变了 javascript GraphQL 后端服务器的编写方式。它通过在数据库前面运行 Prisma 服务器并公开 CRUD(创建、读取、更新、删除)接口来实现这一点，该接口允许开发人员编写直接的 javascript 代码，然后 Prisma 会将这些代码转换为数据库读写操作。

￼ [![Prisma 1 Architectural diagram.](img/3b65a314f2bfbca54a34ea958e7b08ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tw3P9zU0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/f88517b42d28ba6c2bbf8d84bebb5027/3b8ee/prisma1.png)

乍一看，Prisma 类似于诸如 Mongoose 或 Dynamoose 之类的模型库，但它通过检测模式变化，然后自动化任何所需的数据库迁移，改进了这一思想。这使得开发人员可以在后端定义一个模式，并让它从数据库表一直传播到后端解析器。为您的模式提供一个真实的来源可以使更新更加无缝，并且消除了对您的服务器进行更新时的大问题。

Prisma 2 完全重写，解决了第一次实现中的几个问题。Prisma 服务器已经被用 rust 编写的查询引擎所取代。javascript 包装器围绕着查询引擎，这使得与 javascript 后端接口变得非常容易。查询引擎不再像 Prisma 1 那样是一个独立的服务器，而是一个捆绑的可执行文件，与 javascript 后端一起运行在同一个服务器上。这使得服务器架构更加简单，因为我们现在只需要处理一个后端服务器，而不需要同时运行 Prisma 和 javascript 后端服务器。它还允许无服务器配置，因为将来有可能在一个 lambda 函数中同时运行 rust 查询引擎和 javascript 代码。

￼ [![Prisma 2 Architectural diagram.](img/d977f7037049c61123a896cf19584944.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N2WFTIRT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/b3726c9a68708e5fb989c0fdf07ec931/3b8ee/prisma2.png)

Prisma 2 还将 Prisma 1 的两个功能分成两个独立的项目。Photon 包含 rust 运行时引擎和 javascript 包装器，并表示我们的后端可以用来与数据库通信的包。一个名为 Lift 的新项目将处理所有的数据库迁移。将 Prisma 1 的两个关键功能分成两个项目，让我们在如何部署和管理基础设施方面更加灵活，因为我们可以使用 Photon、Lift 或两者兼而有之。

在下面的视频系列中，我将一步一步地指导您设置一个演示 Prisma 2 项目，并展示如何将它连接到 sqlite 和 mysql 数据库，运行 Lift 以对数据库应用迁移，以及使用 Photon 在后端和数据库之间进行通信。我们使用新的 Prisma Studio，这是一个很棒的管理门户，用于查看和修改您的数据，以及 GraphQL Playground UI，用于将样本查询和变化发送到您的后端服务器。在这个过程中，我们处理了一些从 Prisma 2 的 v0.0.90 开始出现的 bug。

我们包括以下步骤，以便于复制视频中的内容，但如果您急于观看，请直接跳到视频的末尾，因为我们在两个视频中都涉及了相同的主题。

**安装 Prisma 2**

确保您使用的是最新版本的节点:

```
node -v 
```

我用过 v8.10 和 v10.8，它们都运行良好，但你的里程数可能会有所不同。现在我们可以安装`prisma2`并检查版本是否大于 v0.0.90:

```
npm install -g prisma2
prisma2 -v 
```

确保选择一个`sqlite`数据库和一个 graphQL 项目。您可以选择 javascript 或 typescript，但是出于本教程的目的，我选择了 typescript。

现在，我们迁移到我们的开发目录并创建一个示例项目，导航到该目录，并执行`npm install` :

```
prisma2 init my-prisma-project
cd my-prisma-project
npm install 
```

**设置 Sqlite 数据库、Prisma 2 和后端服务器**

现在我们有了一个很好的项目——让我们使用 Photon 来创建和应用一个迁移。当它询问您的迁移应该使用什么名称时，您可以选择任何对您有意义的名称。

```
prisma2 lift save
prisma2 lift up 
```

如果我们查看我们的代码库，我们会看到有一个 prisma 文件夹，它现在有一个`migrations`文件夹和一个`dev.db`文件。随着我们将来进行更多的迁移，会有新的文件夹添加到 migrations 文件夹中，每个文件夹都包含每次迁移所涉及的所有步骤。

现在我们准备启动 Prisma Studio，这是一个用户界面，允许我们一目了然地查看、创建、更新和删除我们所有的数据。在命令行中键入以下内容:

```
prisma2 dev 
```

现在导航到`http://localhost:5555`，你应该会看到 prisma UI。请注意，虽然 Prisma Studio 对于开发和生产来说很方便，但对于后端服务器与数据库通信来说，这并不是严格必需的。现在让我们开始启动我们的后端服务器。打开一个新标签并启动后端服务器

```
npm start 
```

**运行我们的第一个查询和突变**

现在我们可以在`http://localhost:4000`打开我们的后端服务器，它将加载 GraphQL Playground UI。这个演示项目的模式是一个有许多文章的用户。让我们通过提交一些变化和查询来确认这个后端服务器正在工作。做一个新的用户突变并提交:

```
 mutation  {  signupUser(data:{  email:  "your@email.com",  name:  "Stephen"  }){  id  name  email  }  } 
```

我们应该看到它返回一个没有任何错误的有效响应。现在，让我们创建一个新的草稿。确保将`authorEmail`字段更改为您在`signupUser`变异中使用的任何电子邮件。

```
 mutation  {  createDraft(title:  "My first draft",  content:  "my glorious work",  authorEmail:  "your@email.com"){  id  content  title  }  } 
```

我们可以去 Prisma 工作室，我们应该会看到一个新的帖子和用户。我们将看到的问题是，尽管我们将用户传递到了变异中，但是用户是空的。这是因为 create post prisma 函数调用的`connect`功能被注释掉了，所以现在让我们修复它。转到`src/index.ts`并取消注释掉的行，以便创建函数调用如下所示:

```
return ctx.photon.posts.create({
  data: {
    title,
    content,
    published: false,
    author: {
      connect: { email: authorEmail },
    },
  },
}) 
```

一旦我们保存了文件，我们可以在终端中确认服务器已经重新加载，我们可以再次尝试这种变化。我们现在应该看到第二个帖子被添加到 Prisma Studio 的数据库中，这一次我们有了一个附加的用户条目。太棒了——我们已经展示了如何在 Prisma 2 中创建模型之间的关系。

现在让我们测试一下查询。在 Prisma Playground 中运行以下查询:

```
query  {  feed{  id  title  }  } 
```

我们应该看到它返回一个空数组，因为没有发布任何帖子。但是，如果我们进入 Prisma Studio，我们可以将第二个 post 条目的`published`字段更改为`true`。如果我们随后确认更改并重新运行查询，我们现在将看到 post 出现在返回数据中。

**将我们的数据库从 Sqlite 改为 Mysql**

让我们从 Sqlite 数据库切换到 AWS 支持的 RDS Mysql 实例。关于设置 Mysql 实例的完整细节，请参见下面的第 5 部分视频。从
中删除`prisma/migrations`文件夹、`prisma/dev.db`数据库并更新`prisma/project.prisma`中的 datasource 块

```
datasource db {
  provider = "sqlite"
  url      = "file:dev.db"
  default  = true
} 
```

对此:

```
datasource db {
  provider = "mysql"
  url      = "mysql://USERNAME:PASSWORD@YOUR_RDS_ENDPOINT:3306/YOUR_DATABASE”
} 
```

现在我们可以运行`prisma2 dev`,它将运行 Lift 并迁移我们的 Mysql 数据库，以匹配 Sqlite 的数据库。如果我们重新启动我们的后端服务器，我们应该能够重新运行我们上面运行的相同的突变，我们应该看到相同的行为。

有用！随时保持这个项目的迭代和黑客攻击，如果你需要更多的帮助，请参阅我们刚刚在下面覆盖的视频系列。

**通过 AWS 自动部署 Gatsby 增强了分步指南**

在下面的视频系列中，我们将带您逐步了解 Prisma 2 的入门指南。我们将讨论如何创建一个新项目，使用 Lift 将我们的模式迁移到 Sqlite 和 Mysql 数据库，以及如何在我们的后端服务器中使用 Photon。

**第 1 部分:概述**

[https://www.youtube.com/embed/eH5mleAvnms](https://www.youtube.com/embed/eH5mleAvnms)

**第二部分:安装 Prisma 2 并配置数据库**

在这个视频中，我们将安装`prisma2` npm 包，展示如何创建一个新的 Sqlite 项目，并使用 Lift 对我们的数据库执行第一次迁移。

[https://www.youtube.com/embed/cc2lauvaMts](https://www.youtube.com/embed/cc2lauvaMts)

**第 3 部分:运行 Prisma Studio 并启动后端服务器**

在这里，我们将展示如何启动我们的后端服务器，并执行我们的第一次突变。

[https://www.youtube.com/embed/edqIT6NfI5M](https://www.youtube.com/embed/edqIT6NfI5M)

第四部分:发布你的初稿

我们将展示如何使用 Prisma Studio 修改数据库中的数据，并在 Prisma 2 中探索模型关系。

[https://www.youtube.com/embed/GVepGrIXMc4](https://www.youtube.com/embed/GVepGrIXMc4)

**第 5 部分:连接到 RDS MySQL 服务器**

最后，我们将展示如何将 Sqlite Prisma 2 项目过渡到 Mysql 数据库。我们将在 AWS 中创建一个 RDS 数据库，然后使用`prisma2`创建一个新项目，这次要小心选择一个 Mysql 项目。然后，我们将把生成的连接块复制到我们的项目中，这将允许我们在 Mysql 中使用它，而不是我们的本地 Sqlite 数据库。

[https://www.youtube.com/embed/81PtwbpQEck](https://www.youtube.com/embed/81PtwbpQEck)

从那里来的还有更多！
[点击这里给我们您的电子邮件，我们会在发布新内容时通知您。](https://gmail.us20.list-manage.com/subscribe?u=37f38485b2c7cff2f3d9935b5&id=e3bc056dde)我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-06-26-prisma-2-sneak-peak/) 。