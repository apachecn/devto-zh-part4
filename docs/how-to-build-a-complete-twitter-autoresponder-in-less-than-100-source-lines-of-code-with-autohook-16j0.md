# 如何使用 Autohook 用不到 100 行源代码构建一个完整的 Twitter 自动回复器

> 原文：<https://dev.to/twitterdev/how-to-build-a-complete-twitter-autoresponder-in-less-than-100-source-lines-of-code-with-autohook-16j0>

*本教程最初发表于 [Twitter 开发者](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/guides/twitter-account-activity-tutorial-autohook?utm_source=autohook_tutorial_devto&utm_medium=devto&utm_campaign=autohook_tutorial)网站*

[账户活动 API](https://developer.twitter.com/en/docs/accounts-and-users/subscribe-account-activity/overview.html?utm_source=autohook_tutorial_devto&utm_medium=devto&utm_campaign=autohook_tutorial) 是 Twitter 开发者平台中最通用的 API 之一。有了这个 API，人们可以让你的应用程序获得他们在 Twitter 上活动的通知。最棒的是，它使用 webhooks 提供实时更新。

开发人员可以通过帐户活动 API 实现很多功能。例如，公司可以使用这个 API 来了解围绕他们的产品和服务的全球对话是如何展开的。一个流行的使用案例是客户服务，其中您最喜爱的品牌可以实时回复您的直接消息，并确定最佳的下一步行动。这通常包括使用帐户活动 API、配置 webhook、确定 OAuth，以及理解如何使用额外的 Twitter APIs 发回消息。

如果你没有合适的工具，这听起来是一项艰巨的任务。谢天谢地， [Autohook](https://github.com/TwitterDev/autohook) 在这里让事情变得非常简单。

Autohook 是一个 Node.js 模块，也是一个为您设置 webhooks 的命令行工具。这样，你就可以不用花时间去弄清楚一个 webhook 是如何工作的，而是专注于在 Twitter 上做一些很棒的事情。

Autohook 让事情变得非常简单——简单到你可以用不到 100 行代码自动完成 Twitter 上的 DM 对话。我们将构建一个简单的自动回复器，它会问候直接给你的用户发消息的人。这是我们要做的:

*   我们将建立一个项目，包括一个启用了帐户活动环境的 Twitter 应用程序
*   我们将设置 Autohook，因此它将为我们做所有的艰苦工作
*   我们将构建一个逻辑来只响应传入的直接消息
*   我们将添加一种显示已读邮件的方式
*   在发送消息之前，我们将显示一个输入指示器
*   我们将发回一封自动回复邮件的样本

# 你的 Twitter 应用

首先:为了使用 Twitter 的账户活动 API，你需要在 Twitter 上创建一个应用程序，并授予它**读、写和直接发送消息**的权限。您还需要为此应用程序分配一个有效的开发环境。您可能已经这样做了:如果是这样，您应该在您的[开发环境](https://developer.twitter.com/en/account/environments)页面中看到类似这样的内容:

[![](img/f0e70ae454cb6bea1042aa68310afb65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ALio91kZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jqk0v444qie07dubjph.png)

如果您还没有创建开发环境，只需点击**设置开发环境**，键入标签并将其分配给应用。标签可以是任何东西，但是一定要记住它，因为你以后会用到它。

您需要从您的 [Twitter 应用仪表板](https://developer.twitter.com/en/apps)获取访问令牌。从包含您刚刚创建的环境的应用程序中，点击**细节**，然后点击**密钥和令牌**。记下以下项目中报告的值:

*   API 密钥
*   API 密钥
*   访问令牌
*   访问令牌秘密

在您的主文件夹中创建一个名为`.env.twitter`的文件，添加您在该页面中找到的值，以及您的环境标签:

```
TWITTER_CONSUMER_KEY=Details ➡️ API key 
TWITTER_CONSUMER_SECRET=Details ➡️ API secret key 
TWITTER_ACCESS_TOKEN=Details ➡️ Access token 
TWITTER_ACCESS_TOKEN_SECRET=Details ➡️ Access token secret 
TWITTER_WEBHOOK_ENV=Your env label 
```

开发人员经常对消费者密钥和访问令牌感到困惑。长话短说，把它们想象成加密的用户名和密码。消费者密钥/秘密对识别你应用，而访问令牌是用户凭证。这意味着您的消费者密钥/密码不会改变，但您的访问令牌/密码会根据使用您的应用进行身份验证的用户而改变。

[![This images shows that different users authenticate using different access tokens, while the consumer key and secret are always the same, because they belong to the authenticating app.](img/35080c39bad1b42cf2da071348a5de67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uv2Zlkro--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cmlnvlfrxyc1ss2hvmdz.png)

你可能已经注意到，如果你是自己应用程序的所有者，`TWITTER_ACCESS_TOKEN`和`TWITTER_ACCESS_TOKEN_SECRET`会表明你的身份。如果是这样的话，您真的不需要通过 OAuth 来识别您自己——我们已经为您生成了这些令牌(请记住这一点，它将在以后派上用场)。

# 安装自动挂钩

接下来，我们将安装 Autohook。它的包装有 npm 和 Yarn 两种。

```
npm i -S twitter-autohook 
```

我几乎希望有更多的步骤，但仅此而已。Autohook 就是这么简单！

# 创建新项目

我们将开始一个新项目。我们将它设置在您的个人文件夹中，但它自然可以存在于任何地方:

```
mkdir ~/autohook-tutorial
cd autohook-tutorial 
```

让我们创建一个名为`index.js`的文件。我们将添加代码来调用 Autohook，并确保一切都按照预期进行了配置。为了让我们可以从一个干净的环境开始，这段代码将从您的环境中删除所有现有的 webhooks。如果您不想失去现有的 webhooks，请从您的开发人员仪表板中选择一个不同的开发环境。或者，您可以删除对`removeWebhooks()`的调用，并用`startServer()`替换`start()`。