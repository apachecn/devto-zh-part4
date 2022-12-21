# 快速入门:Gridsome + Craft CMS GraphQL API

> 原文：<https://dev.to/jakedohm_34/quick-start-gridsome-craft-cms-graphql-api-47cj>

Craft CMS 是一个非常棒的 CMS，与 Gridsome(或者任何静态站点生成器)一起使用变得更加容易。Craft 团队似乎已经注意到了人们开始(或希望)将 Craft 用作“无头 CMS”的趋势。现在，在[最新版本(Craft 3.3)](https://craftcms.com/blog/craft-33) 中，他们已经添加了一个开箱即用的 GraphQL API，它非常适合将您的内容拉入 Gridsome 或 Gatsby 之类的静态站点生成器。

* * *

让我们直接进入如何使用新的 GraphQL API 来与 Gridsome 集成。

## 5 个步骤

### 1。设置 Craft 安装

要使用 GraphQL API，您需要安装运行 3.3+的 Craft，并获得 Craft Pro*许可。如果你有一个当前的 Craft 安装并运行比 3.3 更低的版本，你可以通过将你的`composer.json`中的`craftcms/cms`版本改为`"^3.3.0"`，然后运行`composer update`来进行更新。

*在开发中，您可以使用 Craft Pro 试用版

### 2。创建模式

模式是通过 GraphQL 访问工艺数据的方式。每个模式都有一个访问令牌，您可以提供这个令牌来创建 GraphQL 查询，以识别从哪个模式提取数据。每个模式都有自己的权限集，因此您可以根据允许查询的模式来限制对数据类型的访问。

对于这一步，转到`Control Panel > GraphQL > Schemas`，然后创建一个新的模式，赋予它适当的数据权限，并复制访问令牌。

### 3。设置到 GraphQL API 的路由。

将以下路线添加到`routes.php`。这将允许您向`example.com/api`发送 GraphQL 查询。

```
// routes.php
return [
  'api' => 'graphql/api'
]; 
```

### 4。将 Craft API 设置为 Gridsome 数据源

假设您已经安装并运行了 Gridsome，那么将 CMS 数据集成到 Gridsome GraphQL 存储中是非常简单的！

首先，您需要安装 GraphQL 的 Gridsome 源代码插件:

```
npm install @gridsome/source-graphql
yarn add @gridsome/source-graphql 
```

然后，将以下内容添加到您的`gridsome.config.js` :

```
// gridsome.config.js
{
  use: '@gridsome/source-graphql',
  options: {
    url: process.env.CRAFT_API_URL,
    fieldName: 'craft',
    typeName: 'craft',
    headers: {
      Authorization: `Bearer ${process.env.CRAFT_API_TOKEN}`,
    }
  }
} 
```

这让我们实现了 90%的工作集成，但它仍然不会工作*完全*！您可能已经注意到我们的 API URL 和令牌对`process.env`变量的引用。这是我们将在下一步，也是最后一步中设置的。

## 5。在 Gridsome 项目中创建一个`.env`

如果你熟悉 Craft，你以前见过一个`.env`文件。`.env`包含您所有的“环境变量”:特定于您工作环境的信息。Gridsome 对环境变量采用了同样的方法，所以我们将在 Gridsome 项目中创建(或添加)一个`.env`文件。

```
# .env - in Gridsome project
CRAFT_API_URL=http://example.test/api
CRAFT_API_TOKEN=schemaAccessToken 
```

## 就是这样！

现在开始跑步，你就要出发去参加比赛了！现在，您应该能够在 Gridsome 中的任何地方查询您的工艺数据。为了测试一切，请前往 GraphQL 操场，尝试发送以下请求:

```
query  {  craft  {  ping  }  } 
```

如果一切正常，那么`ping`字段应该返回`pong`。

如果您有任何意见/问题，请不要犹豫，留下您的意见！我也喜欢听到我的文章对你有帮助，所以如果这些步骤对你有用，请留下评论！