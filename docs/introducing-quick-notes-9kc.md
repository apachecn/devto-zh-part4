# 介绍快速笔记

> 原文：<https://dev.to/theworstdev/introducing-quick-notes-9kc>

[![Alt Text](img/317c6a1ebbdb0d81da6b87e95ddc987f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Em_kztfs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ja8yzlgstr2vc95a3ej.jpg)

**部署全栈网络应用程序，帮助您在旅途中整理笔记。**

前几天我发布了 Journey，这是一个很小的应用程序，可以帮助你在找工作的时候追踪你的工作列表。我收到了很多积极的反馈，我想我会收集更多的例子来说明你可以用 AWS Amplify 和支持亚马逊服务来做什么。

这就是我创建快速笔记的原因。这是一个足够小的应用程序，可以相对容易地找到它，它也有一个目的，希望许多人会发现有用，就像旅程一样。

## 它有什么作用

快速笔记允许您快速记下和访问笔记，并提供以下功能:

*   🎙记录笔记
*   🔈回放笔记
*   📖阅读笔记
*   👮‍认证
*   🔥无服务器后端
*   🚀GraphQL
*   💻在几分钟内部署后端

## 工作原理

应用程序的代码位于[这里](https://github.com/kkemple/quick-notes)。

该项目使用亚马逊 ML 服务(亚马逊 Polly 和亚马逊转录)和 Amplify `Predictions`类别来[将文本转换为语音](https://github.com/kkemple/quick-notes/blob/master/src/components/Note.js#L68)以播放笔记，并用于[将记录的笔记转换为文本](https://github.com/kkemple/quick-notes/blob/master/src/components/Record.js#L96)以存储在 AWS AppSync (GraphQL 服务)中。

在项目中，您会注意到一个名为`amplify`的文件夹。该文件夹包含应用程序的后端，可以在任何人的帐户中重新部署。在`amplify`文件夹中，你会看到一个`backend`文件夹。在该文件夹中，您将看到四个主要功能的配置:

*   认证服务(由 Amazon Cognito 提供支持)
*   GraphQL API(使用 AWS AppSync 构建)
*   语音到文本生成(用 Amazon Transcribe 构建)
*   文本到语音生成(用 Amazon Polly 构建)

在`backend/api`文件夹中，你会看到 GraphQL API 配置以及基本的 [GraphQL 模式](https://github.com/kkemple/quick-notes/blob/master/amplify/backend/api/quicknotes/schema.graphql)。

这是基本的 GraphQL 模式。您将看到基础模式如下所示:

```
type  Note  @model  @auth(rules:  [{  allow:  owner  }])  {  id:  ID!  title:  String!  text:  String!  createdAt:  String  updatedAt:  String  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未使用过 Amplify，你可能不知道`@model`和`@auth`指令。这些是 Amplify CLI 的 [GraphQL 转换](https://aws-amplify.github.io/docs/cli-toolchain/graphql)库的一部分。

**@ model**——用这个指令修饰任何基本类型，以获得 CRUD 并列出查询和变异定义、DynamoDB 表以及为 GraphQL 操作创建的解析器。

**[@ auth](https://dev.to/auth)**——用该指令修饰任何基类型或字段，以获得为受保护数据设置的细粒度身份验证和授权。

## 部署 App

要自动部署应用程序，请单击橙色大按钮👇

[![amplifybutton](img/51508332abcfc80db7c7ad175ea69bbf.png)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/kkemple/quick-notes)

> 如果您希望手动部署应用程序，请遵循以下说明。

### 部署后端，运行 app

1.  克隆存储库并安装依赖项

```
~ git clone https://github.com/kkemple/quick-notes.git
~ cd quick-notes
~ npm install 
```

Enter fullscreen mode Exit fullscreen mode

1.  初始化并部署 Amplify 项目

```
~ amplify init
? Enter a name for the environment: dev (or whatever you would like to call this env)
? Choose your default editor: <YOUR_EDITOR_OF_CHOICE>
? Do you want to use an AWS profile? Y

~ amplify push
? Are you sure you want to continue? Y
? Do you want to generate code for your newly created GraphQL API? N
> We already have the GraphQL code generated for this project, so generating it here is not necessary. 
```

Enter fullscreen mode Exit fullscreen mode

1.  启动应用程序并注册一个新用户

```
~ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

### 部署前端

1.  用您选择的 git 服务创建一个新的存储库

2.  将项目推送到您的新存储库

```
~ git remote add origin <your_new_repository>
~ git push --set-upstream master 
```

Enter fullscreen mode Exit fullscreen mode

1.  连接到 [AWS 放大器控制台](https://console.aws.amazon.com/amplify/home)并等待构建开始。您将获得一个制作 URL，并准备好做一些笔记！

## 定制 GraphQL 模式

此模式可以编辑。如果需要其他字段，可以通过执行以下操作来更新后端:

更新模式(位于 amplify/back end/API/quick notes/schema . graph QL)。

重新部署后端

```
amplify push 
```

Enter fullscreen mode Exit fullscreen mode

如果您或您认识的任何人需要帮助来启动和运行此应用程序，请通过 [Twitter](https://twitter.com/kurtiskemple) 联系我，我很乐意提供帮助！