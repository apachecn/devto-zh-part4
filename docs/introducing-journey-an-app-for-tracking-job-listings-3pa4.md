# 介绍 Journey——一个追踪工作列表的应用程序

> 原文：<https://dev.to/theworstdev/introducing-journey-an-app-for-tracking-job-listings-3pa4>

[![Alt Text](img/c7834c7610e1461b9382b89a0d326f80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H1FGHmta--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3719v5z0r4ix6zgj5cx8.jpg)

最近，我一直在帮助很多人找工作，我注意到了一个趋势。当你申请了很多地方，每个公司都想让你带回家作业、面试等等时，跟踪你的求职过程真的很难。我也一直在帮助那些刚开始开发 React 和 AWS Amplify 的人，这样他们可以很快获得一些构建全功能应用程序的经验，并希望同时给他们一些知识和信心。

这就是我建造《征途》的原因。这是一个足够小的应用程序，可以相对容易地找到它，它也有一个目的，希望许多人会发现有用的。

## 它有什么作用

Journey 允许您跟踪感兴趣的职位列表，并提供以下功能:

*   📝在 Markdown 中保存注释
*   ❤️添加收藏夹
*   👮‍认证
*   🔥无服务器后端
*   🚀GraphQL
*   💻在几分钟内部署后端

## 工作原理

应用程序的代码位于[这里](https://github.com/kkemple/journey)。

在项目中，您会注意到一个名为`amplify`的文件夹。该文件夹包含应用程序的后端，可以在任何人的帐户中重新部署。在`amplify`文件夹中，你会看到一个`backend`文件夹。在该文件夹中，您将看到两个主要功能的配置:

*   认证服务(由 Amazon Cognito 提供支持)
*   GraphQL API(使用 AWS AppSync 构建)

在`backend/api`文件夹中，你会看到 GraphQL API 配置以及基本的 [GraphQL 模式](https://github.com/kkemple/journey/blob/master/amplify/backend/api/jobassist/schema.graphql)。

这是基本的 GraphQL 模式。您将看到基础模式如下所示:

```
type  Listing  @model  @auth(rules:  [{  allow:  owner  }])  {  id:  ID!  title:  String!  company:  String!  url:  String!  status:  Status!  favorite:  Boolean!  notes:  String  relatedDate:  String  contactName:  String  contactEmail:  String  contactPhoneNumber:  String  createdAt:  String  updatedAt:  String  }  enum  Status  {  APPLIED  TRACKING  INTERVIEWING  TAKE_HOME_ASSIGNMENT  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前从未使用过 Amplify，你可能不知道`@model`指令。这些是 Amplify CLI 的 [GraphQL 转换](https://aws-amplify.github.io/docs/cli-toolchain/graphql)库的一部分。

**@ model**——用这个指令修饰任何基本类型，以获得 CRUD 并列出查询和变异定义、DynamoDB 表以及为 GraphQL 操作创建的解析器。

## 部署 App

该应用程序可以配置为仅供一个人使用，或者允许任何人注册并使用该应用程序。请务必注意说明，因为共享应用程序的一些内容略有不同。

> 如果你是训练营的一员，或者想让其他人在求职中使用这个应用程序，共享实例是一个很好的选择。

### 部署后端，运行 app

克隆存储库并安装依赖项

```
~ git clone https://github.com/kkemple/journey.git
~ cd journey
~ npm install 
```

Enter fullscreen mode Exit fullscreen mode

初始化并部署 Amplify 项目

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

添加一个用户供您登录(如果您计划托管多个用户并允许注册，请跳过此步骤)

```
~ amplify auth console

> user pool 
```

Enter fullscreen mode Exit fullscreen mode

在“用户和组”选项卡下，单击“创建用户”。取消选中“向该新用户发送邀请？”复选框并输入必要的属性，然后单击“创建用户”。

启动应用程序并登录

```
~ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

应用程序启动后，输入您在上一步中使用的用户名和密码。系统将提示您更改密码。

### 部署前端

用您选择的 git 服务创建一个新的存储库

将项目推送到您的新存储库

```
~ git remote add origin <your_new_repository>
~ git push --set-upstream master 
```

Enter fullscreen mode Exit fullscreen mode

连接到 [AWS 放大器控制台](https://console.aws.amazon.com/amplify/home)并等待构建开始。您将获得一个生产网址，您已经准备好跟踪您的工作列表！

> **如果你希望允许用户注册并使用这个应用程序，然后选择`shared`分支连接到 Amplify 控制台，否则使用`master`。**

## 定制 GraphQL 模式

此模式可以编辑。如果需要其他字段，可以通过执行以下操作来更新后端:

更新模式(位于 amplify/back end/API/job assist/schema . graph QL)。

重新部署后端

```
amplify push 
```

Enter fullscreen mode Exit fullscreen mode

如果您或您认识的任何人需要帮助来启动和运行此应用程序，请通过 [Twitter](https://twitter.com/kurtiskemple) 联系我，我很乐意提供帮助！