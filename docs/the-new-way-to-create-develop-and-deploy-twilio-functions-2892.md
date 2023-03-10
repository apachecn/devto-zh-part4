# 创建、开发和部署 Twilio 功能的新方法

> 原文：<https://dev.to/twilio/the-new-way-to-create-develop-and-deploy-twilio-functions-2892>

[信号](https://signal.twilio.com)，我们的客户和开发者大会，就在几天前。如果你错过了，你可以看看来自[第一天](https://youtu.be/TTo37IRoMz4)和[第二天](https://www.youtube.com/watch?v=WFNegpSsMlM)的主题演讲，以及 YouTube 上的许多[产品会议](https://www.youtube.com/watch?v=VAKe521Hryk&list=PLqrz4nXepkz5ZT-bRpdhmqrpu4fiLckfu&index=1)。

虽然有许多激动人心的产品发布，但有人知道吗？)，有一件事让我从椅子上站起来大喊“地狱耶”！

现在可以通过 API 部署 Twilio 功能，这改变了使用 Twilio 开发的人数。

在无服务器环境中进行开发可能具有挑战性，并会引发许多问题。如何在本地开发无服务器项目？如何将您的功能部署到生产中？首先，您如何构建您的代码？

幸运的是，两个产品公告回答了所有这些问题。在这篇文章中，我想分享一下[新的无服务器工具包](https://www.twilio.com/docs/labs/serverless-toolkit)和[新的 Twilio 运行时 API](https://www.twilio.com/docs/runtime) 将如何改变 Twilio 函数的创建、开发和部署流程。

## 开始之前…

要开发和部署 Twilio 功能，您需要:

*   一个免费的 Twilio 账户
*   已安装的 [Twilio CLI](https://www.twilio.com/docs/twilio-cli/quickstart)

您可以使用 npm 包 [twilio-run](https://www.npmjs.com/package/twilio-run) 形式的无服务器工具包作为独立工具，但是，您也可以通过[Twilio 无服务器插件](https://github.com/twilio-labs/plugin-serverless)将其集成到 Twilio CLI 中。Twilio CLI 涵盖了数百个 Twilio 功能和 API 端点，将其与无服务器插件配对，您就拥有了一个提供您所需的所有功能的工具。

安装 CLI 后，您的环境中就有了`twilio`命令。通过在终端中运行以下命令来确保您得到授权:

```
twilio login 
```

Enter fullscreen mode Exit fullscreen mode

*如果您需要更多关于如何开始使用 Twilio CLI 的信息，请查看 Twilio 视频提示**[“Twilio CLI 简介！”](https://youtu.be/iFTy8HTbpEI)* *。*

全新安装后，Twilio CLI 缺少`serverless`命令。要添加这些命令，请从 Twilio CLI 本身安装无服务器插件。运行`plugin:install`命令:

```
twilio plugins:install @twilio-labs/plugin-serverless 
```

Enter fullscreen mode Exit fullscreen mode

插件安装完成后，像`twilio serverless:deploy`和`twilio serverless:start`这样的新命令变得触手可及。🎉

**关于插件的快速说明；**Twilio CLI 不仅能安装插件，还能提供保持插件最新的功能。无服务器插件仍然很新——确保不时运行`twilio plugins:update`来接收更新。

## 新建一个无服务器项目

要开始一个新的 Twilio 运行时项目，只需要一个命令。

```
twilio serverless:init your-project-name 
```

Enter fullscreen mode Exit fullscreen mode

`twilio serverless:init`创建新的目录和强制文件。它还一次性安装了所有必需的依赖项。

```
twilio serverless:init serverless-tryout
✔ Creating project directory
✔ Creating project directories and files
✔ Downloading .gitignore file
✔ Installing dependencies
╭─────────────────────────────────────────────────────────────────────────────╮
│                                                                              │
│   Success!                                                                   │
│                                                                              │
│   Created serverless-tryout at /your/dir                                     │
│                                                                              │
│   Inside that directory, you can run the following command:                  │
│                                                                              │
│   npm start                                                                  │
│     Serves all functions in the ./functions subdirectory and assets in the   │
│     ./assets directory                                                       │
│                                                                              │
│   Get started by running:                                                    │
│                                                                              │
│   cd your-project-name                                                       │
│   npm start                                                                  │
│                                                                              │
╰─────────────────────────────────────────────────────────────────────────────╯ 
```

Enter fullscreen mode Exit fullscreen mode

导航到新目录并查看包含的文件。它应该看起来类似于下面的树。

```
.
├── assets
│   ├── index.html
│   ├── message.private.js
│   └── style.css
├── functions
│   ├── hello-world.js
│   ├── private-message.js
│   ├── sms
│   └    └── reply.protected.js
├── node_modules
│   ├──...
│   ├──...
│   └──...
├── .env
├── .gitignore
├── .nvmrc
├── package-lock.json
└── package.json 
```

Enter fullscreen mode Exit fullscreen mode

新项目包括配置文件和两个主目录:`assets`和`functions`。这些目录中包含的文件将[映射到 Twilio 函数和资产](https://www.twilio.com/docs/runtime#functions-assets-beta)，在您部署本地文件后，它们将在线可用。例如，JavaScript 文件`./functions/hello-world.js`将在`/hello-world`可用。

### 文件名决定了一切——公共访问、私人访问和受保护的访问

一些生成的函数和资产在它们的文件名中包含一个`protected`或`private`关键字。无服务器工具包使用文件名评估访问权限，并在部署后省略公共 URL 中的关键字。文件`/functions/sms/reply.protected.js`将产生一个`/sms/reply`端点。

功能的访问级别包括:

*   `public`(默认)–公开发布
*   `protected`–仅可由 Twilio 通过 webhooks 等方式访问

资产的访问级别是:

*   `public` -公开发布
*   `private` -只能通过 [`Runtime.getAssets()`](https://www.twilio.com/docs/runtime/client) 进入功能内部

您可以在文档中查看[功能](https://www.twilio.com/docs/runtime/functions-assets-api/api/function-version)和[资产](https://www.twilio.com/docs/runtime/functions-assets-api/api/asset-version#assetversion-properties)的所有配置。

让我们总结一下这个新项目，它包括:

*   三项资产(其中一项是`private`，不会公开访问)
*   三个函数(其中一个是`protected`，不能公开访问)

现在是时候看看他们都做些什么，并在本地运行这个项目了！

## 一个全新的 Twilio 在你的电脑上运行

开发本地无服务器项目总是一件痛苦的事情。幸运的是，现在有一个命令可以在您的机器上模拟 Twilio 运行时，使得开发更加方便。

在项目内部运行`twilio serverless:start`。

```
twilio serverless:start
┌────────────────────────────────────────────────────────────────────┐
│                                                                    │
│   Twilio functions available:                                      │
│   ├── /hello-world | http://localhost:3000/hello-world             │
│   ├── /private-message | http://localhost:3000/private-message     │
│   └── [protected] /sms/reply | http://localhost:3000/sms/reply     │
│                                                                    │
│   Twilio assets available:                                         │
│   ├── /index.html | http://localhost:3000/index.html               │
│   ├── /style.css | http://localhost:3000/style.css                 │
│   └── [private] /message.js | Runtime.getAssets()['/message.js']   │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘ 
```

Enter fullscreen mode Exit fullscreen mode

`twilio serverless:start`命令启动本地服务器，创建将在运行时部署的端点，并通过提供类似 [`Runtime`客户端](https://www.twilio.com/docs/runtime/client)的东西来模拟环境。你的`localhost`将会像 Twilio 的基础设施一样运转！🎉

当你访问本地端点时，你会发现所有的函数都用一些 [TwiML](https://www.twilio.com/docs/glossary/what-is-twilio-markup-language-twiml) 来响应，涵盖了日常用例，比如[对收到的短信](https://www.twilio.com/docs/sms/twiml/message)进行回复。现在，您可以使用这些函数并根据自己的需要进行调整。

[![Two browser windows – one showing localhost:3000/index.html responding with instructions and localhost:3000/private-message returning some TwiML](img/eaf82d738d84c2c806f5690fea762ebe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0WyjOlrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/T1p-QyhjOH6sVN2Y1JsNKL3p-cj09_u_n5hdLBxN6zLVG-.width-800.jpg)

如需进一步的说明和解释，请导航至包含的资产`[http://localhost:3000/index.html](http://localhost:3000/index.html)`。示例 HTML 页面让您更深入地了解无服务器插件的功能、资产和基础——底层工具；twilio-run 。

关于本地开发的最后一件事是—`twilio serverless:start`提供了许多标志，可以帮助您处理环境变量、调试和实时重载。它甚至内置了本地隧道工具 [ngrok](https://ngrok.com) 。确保用命令`twilio serverless:start --help`查看所有选项或阅读[这篇文章](https://www.twilio.com/blog/locally-developing-and-debugging-twilio-functions)。

只需两个命令，您就可以建立一个新的本地无服务器项目，该项目可以根据特定需求进行调整。但是你如何扩展这个项目，创建新的功能，并在一个无服务器的世界中获得一些如何控制 Twilio 服务的灵感呢？

## 为其他用例创建新功能

增加更多功能的一种方法是在`functions`或`assets`目录中创建额外的文件。不过，这个过程有点像手工操作。如果我告诉你有一种更快的方法来设置函数，你会怎么想？

命令`twilio serverless:new`有助于减少重复任务的数量，并提供了一种使用预定义模板创建新功能的方法。

```
twilio serverless:new 
? Select a template
  Hello Voice - Function to get you started with Twilio Prog. Voice
  Hello Messaging - Function to get you started with Twilio Prog. Messaging
  Never gonna give you up - Never gonna let you down. Plays a song to a phone call
❯ Forward Call - Forwards an incoming call to another number
  Forward Message - Forwards incoming messages to another number
  Forward Message to Multiple Numbers - Forwards incoming messages to a set of numbers
  Forward Message to Email via SendGrid - Uses SendGrid to forward incoming messages via email
(Move up and down to reveal more choices) 
```

Enter fullscreen mode Exit fullscreen mode

选择模板和命名空间目录后，安装过程将创建新文件，并给出其他安装操作的提示。例如，下面的模板需要定义某个环境变量。

```
twilio serverless:new
? Select a template Forward Call - Forwards an incoming call to another number
? What should be the namespace your function(s) are placed under? private
  ✔ Configuring Environment Variables in .env
  ✔ Installing Dependencies
  ✔ Creating function: forward-call.js
INFO Make sure to configure MY_PHONE_NUMBER in the .env file
SUCCESS Downloaded new template into the "new-service" subdirectories 
```

Enter fullscreen mode Exit fullscreen mode

您希望看到哪些功能用例？所有的功能模板都可以在 GitHub *上找到并编辑*[——我们很想听听你的想法！](https://github.com/twilio-labs/function-templates)

创建新功能后，通过再次运行`twilio serverless:start`重启本地服务器。新端点将在本地可用。对于上面的函数，这个端点就是`/private/forward-call`。

本地开发只是故事的一半——您如何将功能部署到云中，以便随时扩展？

## show time–部署，部署，部署！

当您对本地功能和资产满意时，运行`twilio serverless:deploy`来部署它们。

```
twilio serverless:deploy

Deploying functions & assets to the Twilio Runtime

Account         SK6b3e6df6812298f87594567895e55ede
Token           kegH****************************
Service Name    serverless-tryout
Environment     dev
Root Directory  /private/tmp/serverless-tryout
Dependencies
Env Variables   MY_PHONE_NUMBER

✔ Serverless project successfully deployed

Deployment Details
Domain: serverless-tryout-6774-dev.twil.io
Service:
   serverless-tryout (ZSc...)
Environment:
   dev (ZEa...)
Build SID:
   ZB8...
Functions:
   [protected] https://foo-6774-dev.twil.io/sms/reply
   https://serverless-tryout-6774-dev.twil.io/hello-world
   https://serverless-tryout-6774-dev.twil.io/forward-call/forward-call
   https://serverless-tryout-6774-dev.twil.io/private-message
Assets:
   [private] Runtime.getAssets()['/message.js']
   https://serverless-tryout-6774-dev.twil.io/index.html
   https://serverless-tryout-6774-dev.twil.io/style.css 
```

Enter fullscreen mode Exit fullscreen mode

这一条命令将代码部署到云中，并创建公共可用的资产和功能端点。

如果您想要检查已部署的运行时，有两种方法可以做到这一点。第一种是使用`twilio serverless:list`命令:

```
twilio serverless:list functions --service-sid=ZSc7...
Account      SK6b...
Token        kegH****************************
Service      ZSc7...
Environment  dev

Functions for environment ZEa5...

│ /sms/reply [Visibility protected]
│ /hello-world
│ /private-message
│ /forward-call/forward-call 
```

Enter fullscreen mode Exit fullscreen mode

该命令接受几个配置选项，以便您可以检查您感兴趣的确切区域。如果你想看看有哪些旗帜，记住`--help`旗帜将是你的朋友。

另一种方法是前往 Twilio 控制台中的[函数 API 区域，并在那里查看。](https://www.twilio.com/console/functions/api)

[![Twilio Console showing the read-only UI for Twilio API deployed functions](img/a4f01b46487310410496e2a5fe0751e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DVRL7AZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/zZZBlxVYo3EMuT7UyTLP-LB0I2cLM0vzsNS-R1Zqx5v2e2.width-800.jpg)

对创建的端点及其新域的更深入的观察清楚地显示了新的 [函数和资产 API](https://www.twilio.com/docs/runtime/functions-assets-api) 是如何构造的，以及它与仅支持 UI 的[Twilio 函数](https://www.twilio.com/docs/runtime/functions)有何不同。让我们简单看一下。

### 新功能和环境适应性

新部署的服务的领域揭示了一个重要的改进，这是以前没有的新 API 带来的。

```
 Random number
                          \  /
https://serverless-tryout-6774-dev.twil.io/sms/reply
        \        /             \ /
       Service name        Environment
                          Domain suffix 
```

Enter fullscreen mode Exit fullscreen mode

无服务器 API 支持T4】环境。当您在几分钟前部署您的项目时，CLI 使用默认值作为环境—`dev`。每个环境都由它自己的域来表示。要部署另一个环境，使用带有`serverless:deploy`命令的`--environment`标志。

环境特性现在看起来可能没什么大不了的，但在将来，它会是一个大问题。当您运行大型项目，许多开发人员在同一个无服务器代码库上工作时，环境是至关重要的。

由于有了环境，您可以在不影响您的生产设置的情况下，在它们自己的域中测试和部署变更。然后，只有当您准备好切换时，您才可以使用命令`twilio serverless:activate`将一个环境升级到另一个环境(例如`dev`到`prod`)。环境是向自动化和实施适当的 CI/CD 工作流迈出的一大步，当您在更大规模上运行时，这两个过程都是强制性的。

如果你想看看新 API 的所有部分是如何组合在一起的，看看参考文档或围绕主题的[张秀坤·昆德尔斯的演讲。](https://speakerdeck.com/dkundel/signal-19-leveraging-the-serverless-api-to-manage-your-deployment-process)

### Twilio 设置的自动化

新的 Twilio 运行时 API 与无服务器工具包相结合，是在现有基础架构中自动执行任务，甚至从头开始创建新安装的缺失部分。

如果我告诉你，你现在可以 [购买号码](https://www.twilio.com/docs/phone-numbers)，创建 [同步服务](https://www.twilio.com/sync)，通过运行单个命令的功能连接你的通信渠道，会怎么样？简而言之，只需运行一个 shell 脚本就可以完成所有与 Twilio 相关的事情。嗯……几天前我就这么做了，这让我从椅子上站起来说，“太好了！”。注意空间，我会在接下来的教程中告诉你如何编写 Twilio 设置脚本。👋

如果你和我一样对这个新 API 感到兴奋，请告诉我。你可以通过以下渠道联系到我。

*   Email: [sjudis@twilio.com](//mailto:sjudis@twilio.com)
*   Github: [司提反](https://github.com/stefanjudis)
*   推特:t0 @ stefanjudis￥t1