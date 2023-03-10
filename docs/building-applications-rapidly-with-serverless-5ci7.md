# 使用无服务器快速构建应用程序

> 原文：<https://dev.to/garethmcc/building-applications-rapidly-with-serverless-5ci7>

在过去的几年里，无服务器作为一种架构模式已经引起了一些关注。以至于我一度决定去兔子洞好好看看。从那以后将近 4 年，我已经到了不能用任何其他方式构建应用程序的地步；到目前为止，无服务器应用程序的优点远远超过了缺点。在那段时间里，我也花了很多时间与无服务器社区互动，试图帮助其他人发现这种，坦率地说，革命性的软件构建方式。以至于最流行的无服务器应用程序开发框架——无服务器框架——的维护者 Serverless，Inc，邀请我加入这个团队，把我以前兼职做的事情作为全职工作来做。

现在我在这里，写这篇我几年前就应该写的博文，希望向其他开发人员介绍构建无服务器应用程序所带来的生产力和性能。因此，与其像许多其他人一样在本文的前半部分谈论理论和历史，不如让我们直接构建一个简单的“入门”应用程序，任何阅读本文的人都可以跟随它。为什么？嗯，从概念上讲，无服务器似乎很抽象。只有当你第一次真正构建一些东西时，你才会意识到用这种方式构建应用程序的真正力量。

首先，我们先过最烦的那一关。我们将在 AWS 上构建这个解决方案，所以如果你还没有 AWS 帐户，现在是时候注册一个了。但是不用担心。我们今天要构建的应该会花费您 0 美元，因为 AWS 为我们将使用的服务提供了慷慨的免费层，我们不会接近这些限制。

要注册 AWS，请前往[https://aws.amazon.com/](https://aws.amazon.com/)并点击橙色的“创建 AWS 账户”按钮。然后只需按照指示，让帐户激活。

太棒了。这真的是最烦人的部分。现在说说有趣的事情。让我们用[无服务器框架](https://www.serverless.com/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)来设置自己。要安装就:

```
npm install -g serverless 
```

现在我们需要设置我们的第一个服务，为此我们将使用全新的入职体验。在 CLI 上，只需输入`serverless`并按回车键。然后这样回答问题:

*   **未检测到项目。您想创建一个新的吗？(Y/n):** `Y`
*   我们从列表中选择 Node.js
*   **你想给这个项目起什么名字？:**我要给我的矿取名`serverless-quick-start`
*   你可以用一个免费的无服务器账户来监控、诊断和测试你的新服务。:免费监测和测试…是，请
*   **您想要启用它吗？(Y/n):** 打 Y
*   **你想注册吗？(Y/n):** 如果出于某种原因，您已经注册了一个无服务器框架帐户，请选择`n`，否则请选择`Y`*然后只需为您的新帐户提供一些凭据。

一旦您通过 onboarding 向导设置了新的服务和无服务器框架帐户，请在 CLI 中输入`serverless dashboard`,您应该会在浏览器中看到如下内容:

[![](img/912e4882ee3c4e2f1743b6513805c0d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c57hUosY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tnwkp16hqpnpgpt5ihao.png)

无服务器应用程序通常由多个无服务器服务组成，就像上面我们用`serverless`命令引导的服务，每个服务执行一些特定的任务。想一想微服务，但是没有基础架构问题…实际上…没有基础架构问题。

让我们点击左上方的`profiles`。您应该只列出一个配置文件；`default`。单击它，您应该会看到:

[![](img/f91ecace87b1e1a97fe2b42937fb805b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HMnMQErq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cna5lc77rq2ncywta36f.png)

为了创建我们的无服务器应用程序，我们需要某种方式让我们的代码和配置在本地机器上到达我们的 AWS 帐户。如果您展开`how to add a role`链接，您应该会看到`Create a role wizard`的链接。点击它将会在你的浏览器中打开一个新的标签，指向你的 AWS 账户。此时，您只需在向导中单击“Next ”,直到看到类似以下内容的通知:

[![](img/a592b76b14ae0cc5562ee53a5073945a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yEY5razP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h2nkzkc8ld3ozdkttf3k.png)

单击蓝色的角色名称，在下一页您会看到一个标有`Role ARN`的行项目。复制类似于`arn:aws:iam::1234567890:role/serverless-enterprise_serverless-quick-start`的整个字符串。然后返回到我们之前使用的浏览器的控制台页面，将您的 ARN 粘贴到文本框中:

[![](img/c25a22da90d5aa6577d4f9049c831b7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m_2axyfY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/utxrhpgatd50q6i0geez.png)

点击`save and exit`。现在，最后一步是将我们将要构建的内容连接到我们刚刚创建的新帐户。回到终端中我们引导服务的文件夹，在您喜欢的文本编辑器中打开`serverless.yml`文件。

这个文件是我们保存所有配置的地方，我们需要这些配置来告诉无服务器框架在我们的 AWS 帐户上创建什么。这也是我们可以告诉 It 在我们的无服务器框架企业帐户上连接到什么组织和应用程序的地方。为此，在文件顶部添加以下内容(显然替换您自己的详细信息):

```
app: myapp
org: garethmccumskey 
```

那么我们刚才对控制台做了什么？为了让我们能够从本地机器连接到我们的 AWS 帐户，我们需要获得具有创建 Lambda 函数和 HTTP 端点等权限的凭证。现在，我们正在构建的服务通过`app`和`org`连接到我们的无服务器框架企业帐户，当我们发出 deploy 命令时，会创建一组临时凭证并传递回我们的本地机器，然后这些凭证由无服务器框架使用，从我们的机器部署到我们的 AWS 帐户。

但是我们首先需要创建一些可以实际部署的东西。打开`serverless.yml`文件，让我们再做一些编辑。找到`service`属性，并将其更改为新服务的某个惟一名称。我打算用`serverless-quick-start`。继续向下滚动，您可以看到我们有一个设置为 AWS 的提供者(是的，无服务器框架可以帮助您在 Azure 等其他提供者上构建无服务器应用程序，但我们这次不打算看这个)，我们将为我们的代码使用 Node 10 运行时。

滚动浏览所有带注释的配置，您应该会发现如下所示的部分:

```
functions:
  hello:
    handler: handler.hello 
```

为了构建无服务器应用程序，我们使用 AWS 提供的 FaaS (Functions as a Service)服务，称为 Lambda。Lambda 允许我们上传一段由我们设置的事件触发的代码。与其让我来解释这一切，不如让我们来建造它，这样你就能直接明白我的意思了。

在我们的小演示中，我们将创建一个返回“Hello World！”的 HTTP 端点。是的，我完全没有创意，我们正在做一个 Hello World 的例子。为此，编辑我们之前看到的配置，使其看起来像这样(注意缩进，如果缩进不正确，YML 会有点生气):

```
functions:
  hello:
    handler: handler.hello
      events:
        - http:
            method: get
            path: hello 
```

现在，让我们打开文件`handler.js`并将内容编辑成这样:

```
'use strict'

module.exports.hello = async event => {
  return {
    statusCode: 200,
    body: 'Hello World!'
  }
} 
```

然后，回到终端，进入`serverless deploy`。

> 注意:由于您在最初注册`serverless login`时可能已经注册了一个新帐户，如果您收到任何错误消息，您可能需要再次注册`serverless login`才能正确验证。

deploy 命令应该会在您的终端中产生如下内容:

```
Serverless: Packaging service...
Serverless: Excluding development dependencies...
Serverless Enterprise: Safeguards Processing...
Serverless Enterprise: Safeguards Results:

   Summary --------------------------------------------------

   passed - no-secret-env-vars
   passed - allowed-regions
   warned - require-cfn-role
   passed - framework-version
   passed - allowed-stages
   passed - no-wild-iam-role-statements
   warned - allowed-runtimes

   Details --------------------------------------------------

   1) Warned - no cfnRole set
      details: https://git.io/fhpFZ
      Require the cfnRole option, which specifies a particular role for CloudFormation to assume while deploying.

   2) Warned - Runtime of function hello not in list of permitted runtimes: ["nodejs8.10","nodejs6.10","python3.7","python3.6","ruby2.5","java-1.8.0-openjdk","go1.x","dotnetcore2.1","dotnetcore2.0"]
      details: https://git.io/fjfkx
      Limit the runtimes that can be used.

Serverless Enterprise: Safeguards Summary: 5 passed, 2 warnings, 0 errors
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
.....
Serverless: Stack create finished...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading artifacts...
Serverless: Uploading service serverless-quick-start.zip file to S3 (66.46 KB)...
Serverless: Validating template...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
................................................
Serverless: Stack update finished...
Service Information
service: serverless-quick-start
stage: dev
region: us-east-1
stack: serverless-quick-start-dev
resources: 16
api keys:
  None
endpoints:
  GET - https://abcdefg.execute-api.us-east-1.amazonaws.com/dev/hello
functions:
  hello: serverless-quick-start-dev-hello
layers:
  None
Serverless Enterprise: Publishing service to the Enterprise Dashboard...
Serverless Enterprise: Successfully published your service to the Enterprise Dashboard: https://dashboard.serverless.com/tenants/garethmccumskey/applications/myapp/services/serverless-quick-start/stage/dev/region/us-east-1 
```

在所有这些的结尾，在一个标签为`endpoints`的部分下，提供了一个 URL(例如:[https://abcdefg . execute-API . us-east-1 . Amazon AWS . com/dev/hello](https://abcdefg.execute-api.us-east-1.amazonaws.com/dev/hello))。继续在浏览器中打开它:

[![](img/a8fbf0416591ec3eb9057cfe8b654553.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wmq9PY3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ouia52u33kt4xrv3lut.png)

太好了…这里刚刚发生了什么？我们创建了一个 Lambda 函数，它可以通过 HTTP 接收到一个端点的 GET 请求。我们为此编写的唯一代码只有几行，但我们得到了更多……让我们更详细地看看这个，让它看起来有多酷。

我们现在拥有的端点只接受 GET 请求。我们可以让它成为 POST 请求，并允许函数在主体中接受数据。但这还不是全部:

*   端点使用 AWS 的 API 网关服务，默认情况下每秒可以处理多达 10，0 00 个请求，并且可以通过对 AWS 的支持请求增加到更高的值。
*   当这个端点接收到一个请求时，它创建一个请求对象，然后这个请求对象被发送给运行在我们编写的 AWS Lambda 上的一小段代码。
*   默认情况下，AWS Lambda 可以同时运行该代码的 1000 个副本，并发性可以通过向 AWS 发出请求来增加。
*   我们不为存储在 AWS 中的任何代码付费，也不为端点付费。API Gateway 上的免费层允许在任何计费发生之前每月进行一百万次 API 调用。
*   我们也不为函数代码的任何执行时间付费。AWS Lambda 自由层允许每月 100 万个请求和 400 000 GB 秒。由于 AWS 每 100 毫秒执行一次就开一次账单，这意味着在我们收到账单之前，我们的函数可以运行 800 000 秒。如果我们在`serverless.yml`上调整我们的配置，我们甚至可以获得 320 万秒的自由执行时间。
*   由于 AWS 设计 API Gateway 和 AWS Lambda 的方式，我们还获得了分布在三个数据中心的完全冗余解决方案(AWS 地区总是有三个独立的数据中心，相隔几英里，通过专用光纤链路连接)。将会发生一场区域性的灾难来摧毁我们的终点，即便如此，它也可能仍然存在。

最后，通过这个简单的例子，我们配置并部署了一个高度可伸缩的、高度冗余的解决方案，这将是许多开发-运营从业者羡慕的；大约 15 分钟后。除非我们大量使用，否则它不会花我们一分钱。我们不必提供我们自己的服务器(因此是无服务器的)，我们不必安装操作系统、运行时、回退、备份、灾难恢复、负载平衡。我们不需要监控 CPU 容量和内存。

换个角度来看，如果您正在使用 Express 或任何其他传统的 web 应用程序框架构建应用程序，并且必须将其部署到 AWS 上的虚拟机，要获得同等的冗余和可伸缩性，您需要:

*   t3.micro 的 3 个 EC2 实例(最便宜的选项)，每个实例位于该地区的一个单独的可用性区域。这需要每小时 0.0104 美元。
*   一个负载平衡器，帮助管理所有三个实例的负载，最低价格为每小时 0.0225 美元。

在任何流量进入该基础设施之前，运行上述内容的总成本为三个 EC2 实例 22 美元，负载平衡器 16.20 美元。不要忘记，设置好这一切可能需要几个小时，并且需要继续维护；由于发现了新的零日漏洞，是否需要应用关键的操作系统更新？您需要确保应用了补丁。

您很可能需要比 t3.micro 更大的 EC2 实例。我估计，一般认真对待流量的 web 应用程序至少需要 3 个 t3.large 实例，每小时花费 0.0832 美元。这意味着你将花费大约 60 美元，而不是 22 美元。同样，这是在任何流量到达之前。负载平衡器的意义在于，您可以让它扩展并运行更多的 EC2 实例，从而增加成本。

相比之下，无服务器应用程序在空闲时的成本为 0 美元(而不是 60 美元+16.20 美元)，并且缩放是即时的。凌晨 1 点，当你所有的顾客都睡着了，没有任何交通流量？那你为什么要付钱呢？

因为我们正在查看不同之处，所以在命令行上运行以下命令:

```
serverless logs -f hello 
```

您将日志记录作为整个解决方案的一部分。对于我们讨论过的每月花费近 40-80 美元的虚拟机，我们还没有任何简单的方法来查看我们的日志。这仍然需要配置。这增加了账单。

[![](img/88a3e8b870102ee03ea1362c71b1750b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y0ZzqQQT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/002jr5e2quuuhh7biawz.png)

在浏览器中返回到您的无服务器框架帐户，单击`applications`，展开您的应用程序，您将看到您刚刚部署的服务被列出。打开您的服务，尽情享受关于您刚刚部署的服务的详细统计数据；它被执行了多少次，任何错误，部署，冷启动。

[![](img/592deef1ca27493e5a63352e836b2fab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YeEFhm1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2cvqvfahjw3cnwyq3nsn.png)

当然这是一个非常有限的例子，但是如果你看一看无服务器网站上可用的[大量文档以及社区](https://serverless.com/framework/docs/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)发布的大量[例子，你会立刻发现无服务器应用不仅仅适用于微小的 GET 请求。](https://serverless.com/examples/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)

所有这些可能有点难以接受。如果你的兴趣被激起了，现在去哪里呢？嗯，无服务器框架有一些很好的文档来帮助你开始。

*   [关于框架的主要文档](https://serverless.com/framework/docs/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)
*   [看一看](https://serverless.com/examples/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)的例子
*   [这个博客很好地收集了开发应用程序的真实公司的操作方法和用例](https://serverless.com/blog/?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july)

如果你有任何关于无服务器的问题，请随时打电话给我或者通过推特联系我。还有在[的无服务器框架社区、论坛](https://forum.serverless.com)和[的 Slack Workspace](https://www.serverless.com/slack?utm_source=devio&utm_medium=blog&utm_campaign=framework-lifecycle-launch-july) 。