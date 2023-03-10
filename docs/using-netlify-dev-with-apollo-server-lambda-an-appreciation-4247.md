# 在 Apollo 服务器 Lambda 上使用 Netlify Dev:欣赏

> 原文：<https://dev.to/eveporcello/using-netlify-dev-with-apollo-server-lambda-an-appreciation-4247>

一年多以前，我们需要发布 GraphQL Workshop 网站，并决定使用 Netlify 进行托管。在部署站点之前，我需要设置安全证书来启用 HTTPS。在此之前，这通常需要很长时间，所以我在日历上划出了一些时间。然后，我迅速找到了一个一键式选项来保护 HTTPS 的网站，整个过程在大约 37 秒内自动完成。哦，而且是免费的。

从那天起，我就成了 Netlify 的信徒，但我一直认为 Netlify 是前端网站的托管提供商，而不是站在 GraphQL 服务器上。事实证明，在这个问题上，我错了，就像我错了用 Netlify 部署一个安全的网站需要多长时间一样。你 100%可以用 Netlify 做到这一点。让我们更深入地了解一些有助于实现这一目标的工具:

### Netlify Dev

2019 年 4 月宣布， [Netlify Dev](https://www.netlify.com/products/dev/) 是一款命令行工具，可以让你在电脑上运行 Netlify 的平台。这允许您使用无服务器功能在本地测试您的站点(即创建 React App ),这些无服务器功能处理特定的任务，如运行您用 Apollo Server 创建的 GraphQL 服务器。Netlify Dev 还为您提供了一些常见任务的初学者工具包，您将在下面的示例中看到。

### 网络功能

Netlify 函数允许你将服务器端代码(你的 GraphQL API)部署到 AWS，而不需要 AWS 账户在你的前端。这意味着您不必为前端和后端设置不同的存储库。一切都可以在同一个平台上从同一个项目中运行。此外，Netlify 具有受人喜爱的部署预览特性，因此当您发送一个拉请求时，您可以测试预览以确保前端和后端都按预期工作。

### 阿波罗服务器λ

我们已经在文章[五分钟内构建一个 GraphQL 服务器](https://moonhighway.com/building-a-graphql-server-in-five-minutes)中讨论了如何设置默认的 Apollo 服务器。Apollo Server Lambda 是该服务器的变体，它允许您使用 Lambda 函数部署服务器。

### 创建 React App

[Create React App](https://facebook.github.io/create-react-app/) 允许您生成一个 React 项目，我们将使用它作为部署的启动工具包。

现在我们已经熟悉了各个部分，让我们开始吧。

## 1。创建 React 项目

使用`npx`为 React 项目生成文件:

```
npx create-react-app netlify-apollo
cd netlify-apollo 
```

## 2。全局安装 Netlify CLI

我们需要 Netlify CLI 来使用 Netlify Dev。如果你在 Mac 上，全局安装:

```
npm install netlify-cli -g 
```

在 PC 上，以管理员身份打开命令提示符并运行:

```
npm install netlify-cli 
```

## 3。运行 Netlify Dev 命令

因为您当前位于`netlify-apollo`文件夹中，所以您可以运行 Netlify Dev 命令。当您运行此命令时，Netlify 使用项目检测器来确定文件夹中的项目类型(Create React App？盖茨比？雨果。).然后它将运行开发服务器和您在项目中创建的任何功能。这模拟了您的应用程序在生产环境中的工作方式，因此您可以用最少的配置进行本地测试。

```
netlify dev 
```

Netlify 将检测到我们正在使用 Create React App，并将在 Create React App 的默认端口`localhost:3000`和 Netlify 的默认端口`localhost:8888`上运行我们的服务器。

## 4\. Add netlify.toml

这是 Netlify 的配置文件。您将描述在创建 Netlify 函数时应该将它们放在哪里。

```
[build]
  functions = "src/functions" 
  publish = "build" 
```

## 5。创建网络功能

这是用于创建 Netlify 函数的命令。

```
netlify function:create 
```

这将提示您使用箭头键选择想要使用的起始模板。这里有很多:`node-fetch`、`google-analytics`、`hello-world`等。我们想用`apollo-graphql`的。点击向下箭头，直到你选择了它，并按下回车键。这将安装项目的所有依赖项，并为您提供一个服务器模板，您可以开始使用自己的类型创建自己的 GraphQL 服务器。

然后会提示您命名该函数。我们可以坚持使用默认值:`apollo-graphql`。

现在你可以签出`src`文件夹了。在这里你会发现一个名为`functions`的新文件夹和一个名为`apollo-graphql`的子文件夹。在`apollo-graphql.js`文件中，您将看到一个用于作者数据的 starter GraphQL 服务器。在这里，您可以用您希望在服务中可用的类型和查询来替换它。

## 6。运行 Netlify Dev 来测试服务器

我们希望看到我们的应用程序运行，所以我们将再次运行 Netlify 命令:

```
netlify dev 
```

当您访问`localhost:8888`时，您将看到默认的 Create React 应用程序正在运行。要查看 GraphQL 游乐场，您可以前往`http://localhost:8888/.netlify/functions/apollo-graphql`。在那里，您将看到它正在运行，并且您可以发送一个测试查询来确保一切都按预期运行。

```
query  {  allAuthors  {  name  }  } 
```

我们走吧！我们已经为一个使用 GraphQL 和 React 的项目搭建了前端和后端。接下来的步骤是调整模式和解析器。

Netlify Dev 和 Apollo Server Lambda 是快速构建 GraphQL APIs 的绝佳组合。在以后的文章中，我们将讨论如何使用 Apollo Client 在前端 React 应用程序中显示 GraphQL API 提供的数据。

如果您想了解这些工具的更多信息，我推荐以下资源:

*   [Netlify 开发文档](https://www.netlify.com/docs/cli/#netlify-dev-beta):起点。
*   [宣布 Netlify 开发视频](https://www.youtube.com/watch?v=RL_gtVZ_79Q):这是 Matt Biilmann 在四月份的 JAMstack Conf 上发布的 Netlify 开发公告
*   [无服务器 GraphQL with Netlify Dev - video](https://www.youtube.com/watch?v=rCYOpTQReD4) :这是王自如在 2019 年 5 月阿波罗日的一次伟大演讲。