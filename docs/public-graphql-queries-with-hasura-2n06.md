# 使用 Hasura 的公共 GraphQL 查询

> 原文：<https://dev.to/mikewheaton/public-graphql-queries-with-hasura-2n06>

最近，我一直在做一个兼职项目。这是一个应用程序，可以跟踪你正在学习的在线课程，并查看其他人正在学习的课程。想想好的课程读物。

我这个兼职项目的目标之一是学习如何使用 GraphQL 建立一个后端，我发现 [Hasura](https://hasura.io/) 是最快和最直观的方法。我花了不到一个小时在 Heroku 上设置好它，建模出关系数据，并成功地用 GraphQL 查询和修改它。哇！

虽然我可以从 Hasura 的内置 [GraphiQL](https://github.com/graphql/graphiql) 中查询数据，但当我试图用 [React Apollo](https://github.com/apollographql/react-apollo) 连接到它时，我收到了错误。端点拒绝了我的请求，因为它不是来自经过身份验证的用户。但是不需要登录的应用呢？

## 哈苏拉拒绝未经认证的用户

Hasura 使用[角色](https://docs.hasura.io/1.0/graphql/manual/auth/authorization/roles-variables.html)来确定谁有权读取、插入、更新和删除数据。

结果是 Hasura(明智地)拒绝了所有对 GraphQL 端点的未经验证的请求。这可以防止有人访问私人数据或向服务器发送大量请求，但对于应该允许注销用户访问的应用程序来说，这并没有什么帮助。

幸运的是，它们提供了一种为未经身份验证的用户配置默认角色的方法。让我们开始工作吧。

## 配置 Hasura 的未认证角色

让我们改变 Hasura 的默认行为，以便来自未经验证的用户的请求被分配一个默认的安全角色，而不是被直接拒绝。

登录 Heroku 并选择您的 Hasura 应用程序。如果您是 Hasura 的新手，[快速入门指南](https://docs.hasura.io/1.0/graphql/manual/getting-started/heroku-simple.html)将在几分钟内让您上手并运行。

<figure>[![Overview tab for a Heroku app](img/6828ee2541fbd5cadaca2a9710e37991.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--juA530mZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wheaton.dev/static/0a1d9e9ca98f56700edcf337d3262cca/47573/heroku-overview.png)

<figcaption>Heroku app 的</figcaption>

</figure>

总览标签

导航到“设置”选项卡，然后单击“显示配置变量”以显示应用的配置变量。添加一个新变量，它的键为`HASURA_GRAPHQL_UNAUTHORIZED_ROLE`，值可以是您希望调用该角色的任何值，比如`anonymous`。

<figure>[![Settings tab for a Heroku app, with config vars shown](img/ab9fc9da2ef7e96eee2e8700e89b4d53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jPC7wVzU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wheaton.dev/static/3e4d380fe1ec285cf3e58d97ebfdbe87/47573/heroku-settings.png)

<figcaption>Heroku app 的设置标签，显示配置变量</figcaption>

</figure>

点击“打开应用程序”启动 Hasura 控制台，并继续下一部分。

## 设置表的权限

选择“数据”,然后选择一个您希望公开访问的表。

<figure>[![Hasura console with a table selected](img/7167169eba06d7917bd58bd47c05e75b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9UmyR2d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wheaton.dev/static/412a609dd7aab94d3c5a605ca5b1aedd/47573/hasura-table.png) 

<figcaption>哈苏拉控制台带表选中</figcaption>

</figure>

选择“权限”选项卡并输入新角色。这个名称**必须与之前的配置变量**的值相匹配。我的情况是`anonymous`。

您将看到新角色被添加到表格中。默认情况下，所有操作类型(插入、选择、更新和删除)的权限都被阻止，如 X 图标所示。

<figure>[![Permissions tab for a table, with the new anonymous role added](img/99b65f814226d18effa2e07ac32e8174.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gsZ7ozVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wheaton.dev/static/9600e6fd299642a2454a4335f50ef62e/3532c/hasura-permissions.png) 

<figcaption>权限页签为一个表格，新增了匿名角色</figcaption>

</figure>

单击“选择”列中的铅笔。几乎在所有情况下，您都应该保留插入、更新和删除的默认(阻止)权限。

在“行选择权限”下，选择“无任何检查”。对于“列选择权限”,选择应该在查询中可用的列。我把它们都选了。

<figure>[![Hasura permissions, granting anonymous users full access to all rows and columns](img/dce03a098f8799aafab5cf306abfc727.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EAnBI3Sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wheaton.dev/static/12644a75b652ca33038dd092d3a6754c/2a207/hasura-select-permission.png) 

<figcaption>Hasura 权限，授予匿名用户对所有行和列的完全访问权限</figcaption>

</figure>

点击“保存权限”,一切就绪。对注销用户应该可以访问的任何其他表重复此过程。您现在无需登录就可以查询您的 GraphQL 端点了！