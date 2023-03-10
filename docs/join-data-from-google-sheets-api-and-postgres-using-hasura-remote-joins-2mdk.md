# 使用 Hasura 远程连接连接来自 Google Sheets API 和 Postgres 的数据

> 原文：<https://dev.to/hasurahq/join-data-from-google-sheets-api-and-postgres-using-hasura-remote-joins-2mdk>

## TL；速度三角形定位法(dead reckoning)

使用 Hasura Remote Joins 将 Postgres 数据库中的数据与 GraphQL 中的 Google Sheets API 连接起来。样板文件-> [谷歌工作表](https://github.com/praveenweb/google-sheets-remote-schema)

[![](img/2eefb665f3d4c7cc95c6323a82c627af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LzaSuubf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/07/google-sheets-cover-image.png)

这篇文章是我们的[远程连接](https://blog.hasura.io/remote-joins-a-graphql-api-to-join-database-and-other-data-sources/)(在预览版中可用)系列的一部分。Hasura 中的远程连接允许您跨表和远程数据源连接数据。如果你想在我们发布时得到通知，你可以在这里注册[。前往](https://hasura.io/remote-joins)[公关](https://github.com/hasura/graphql-engine/pull/2395)查看更多细节，阅读预览文件，并尝试一个你可以旋转的测试 Hasura 图像。跳到我们的[不和谐](https://discord.gg/hasura)或者评论 [github](https://github.com/hasura/graphql-engine) ，让我们知道你的想法！

在这个例子中，我们将看看如何使用 Hasura 远程连接将 Google Sheets API 中的数据与 Postgres 中的现有数据连接起来。假设有一个包含列`id`、`name`、`email`的`users`表。有一个 Google 表单，其中有一个映射到 URL 的电子邮件 id 列表。现在我们想查询用户数据以及 Google Sheets 中映射到每个用户的电子邮件 id 的 url 数据。让我们看看如何使用定制的解析器来实现这一点。

[![](img/6ac86c6add04f782e692ea2e0ae3c76d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CWFaHSrQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/07/google-sheets-example.png)

## 添加 Google Sheets API 作为远程模式

为了能够通过 Hasura 查询 Google Sheets 数据，需要使用 Hasura 控制台将其添加为`Remote Schema`。

### 部署自定义解析器

[![](img/0bbe47db71628974bb6c039604762110.png)](http://glitch.com/edit/#!/import/github/praveenweb/google-sheets-remote-schema)

在 glitch 的`.env`文件中添加以下环境变量。

```
GOOGLE_SHEETS_API_KEY=xxx
SPREADSHEET_ID=xxx
PORT=3000 
```

*   通过访问[Authorize Requests Sheets API](https://developers.google.com/sheets/api/guides/authorizing#APIKey)页面获得 Google Sheets API 密钥。
*   将 API 键设置为`GOOGLE_SHEETS_API_KEY`环境变量。
*   我们需要输入从哪里获取数据的电子表格 id。将其设置为`SPREADSHEET_ID`环境变量。我们可以在 url 中找到工作表 ID。
*   这个自定义解析器用于接受用户的电子邮件，并将返回与该用户相关的数据的过滤列表。

从 Glitch 获取 GraphQL API 端点，并将其添加为远程模式。

[![](img/9315ae6a9d07349a3f0643ff4fd5c74a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fw8BKefv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/07/google-sheets-remote-schema.png)

现在让我们添加名为`sheets`的远程关系

[![](img/96a7dee94a97189a8c1be5064c67b61a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtrKNmTv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/07/remote-relationship-google-sheets.png)

现在，在单个 API 调用中获取这些数据的 GraphQL 查询如下所示:

```
query {
  users {
    id
    name
    email
    sheets {
      urls
    }
  }
} 
```

注意，嵌套查询`sheets`来自 Google Sheets API，它将应用过滤器 users.email = sheets.author，从而只给出当前用户的数据。

查看我们在远程连接系列中的其他帖子:

*   [与盖茨比一起打造音乐播放列表 app，心满意足](https://blog.hasura.io/building-a-music-playlist-app-with-gatsby-contentful-auth0-hasura/)
*   [使用 GraphQL 跨 Google Places API 和 Postgres 连接数据](https://blog.hasura.io/graphql-join-data-google-places-api-postgres-postgis-location/)
*   [使用 Contentful 和 Postgres 构建内容丰富的应用](https://blog.hasura.io/build-apps-with-rich-content-contentful-hasura-remote-joins/)

远程连接打开了许多令人兴奋的用例，这些样板是探索可能性的简单例子。