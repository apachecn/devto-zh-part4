# 教程:为 Slack 构建一个无服务器的 API 后端[第 1 部分]

> 原文：<https://dev.to/azure/funcy-a-serverless-slack-app-using-azure-functions-4m84>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions/](https://docs.microsoft.com/azure/azure-functions/)了解更多关于微软 Azure 如何实现你的无服务器功能。

Webhook 后端是无服务器功能的一个流行用例。FaaS (Functions-as-a-service)产品使得提供一个托管 Webhook 逻辑的 HTTP 端点变得相对容易，这可以像发送电子邮件一样简单，像回复有趣的 gif 一样有趣！

在本教程中，我们将探索 **funcy** -一个无服务器的 webhook 后端，它是令人敬畏的 [Giphy for Slack](https://get.slack.help/hc/en-us/articles/204714258-Giphy-for-Slack) 的精简版本。(最初的)Giphy Slack 应用为一个搜索词返回一堆 gif，用户可以从中选择一个。 **funcy** 使用 [Giphy Random API](https://developers.giphy.com/docs/#operation--gifs-random-get) 通过简单地返回一个(单个)搜索关键词的随机图像来稍微调整一下。

这篇博文提供了一步一步的指导，让应用程序部署到 [Azure Functions](https://azure.microsoft.com/en-in/services/functions/?WT.mc_id=servsept_devto-blog-abhishgu) ，并将其与您的 Slack workspace 连接起来。GitHub 上有[的代码供你查找。](https://github.com/abhirockzz/funcy-azure-functions/)

> 如果你有兴趣学习使用 Azure 功能进行无服务器开发，只需[创建一个免费的 Azure 账户](https://azure.microsoft.com/en-us/free/?WT.mc_id=servsept_devto-blog-abhishgu)就可以开始了！我强烈推荐查看文档中的[快速入门指南、教程和代码示例](https://docs.microsoft.com/en-in/azure/azure-functions/?WT.mc_id=servsept_devto-blog-abhishgu)，如果您喜欢，可以使用[指导学习路径](https://docs.microsoft.com/en-in/learn/paths/create-serverless-applications/?WT.mc_id=servsept_devto-blog-abhishgu)，或者下载[无服务器计算指南](https://azure.microsoft.com/en-in/resources/azure-serverless-computing-cookbook/?WT.mc_id=servsept_devto-blog-abhishgu)。

[![](img/fd2d0cf4396b8b80a0ee4e3afa618118.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1VUqCThw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pltswm81s6j50dfb2qqk.jpg)

为了保持这篇博客的简洁，代码的细节将在随后的文章中介绍。

[![Microsoft Azure](img/a44140291734a0e75ccb2fdfa9feb97a.png)![](img/9a04e9837f5702f3c1ee8a551fba1379.png)](/azure) [## 教程:为 Slack 构建一个无服务器的 API 后端(第 2 部分)

### abhishek Gupta for Microsoft Azure 1919 年 9 月 4 日 7 分钟阅读

#serverless #cloud #java #showdev](/azure/code-walkthrough-for-funcy-a-serverless-slack-app-using-azure-functions-3gmd)

### 概述

[![](img/e40e3c8cd515c977efb172b7f7971214.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Uyyu0BDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m0k2emsg9ntyxvrhu4cu.jpg)

`funcy`是作为 Slack 内的斜线命令建立的。作为用户，您可以使用`/funcy <your search term>`从您的 Slack 工作区调用它。这反过来调用了我们部署到 [Azure Functions](https://azure.microsoft.com/en-in/services/functions/?WT.mc_id=servsept_devto-blog-abhishgu) 的 webhook 它只不过是一堆 [Java 代码](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-java/?WT.mc_id=servsept_devto-blog-abhishgu)。它调用 [Giphy 随机 API](https://developers.giphy.com/docs/#operation--gifs-random-get) 并将结果返回给用户。

例如，使用`/funcy serverless`从您的 Slack 工作区调用它将会返回一个随机的 GIF。

[![](img/abf443f250cc5b0992c86d69b3ddfc1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqPyWyvX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/MGdfeiKtEiEPS/giphy-downsized.gif)

接下来的部分将指导您完成以下内容:

*   先决条件
*   松弛设置和配置
*   部署到 Azure 功能

### 先决条件

在您继续之前，请确保您已经准备好了以下内容——这不会花太长时间

*   **Maven**—[用于 Azure 功能的 Maven 插件](https://docs.microsoft.com/java/api/overview/azure/maven/azure-functions-maven-plugin/readme?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)用于构建和部署你的 Java 功能。如果没有 Maven，请从这里安装`v 3.0`或以上。
*   **Azure CLI** - [按照说明](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=servsept_devto-blog-abhishgu)设置并登录 Azure CLI。
*   **宽松工作空间** - [如果您没有宽松工作空间](https://slack.com/create)，请创建一个。
*   **GIPHY API key** -你需要创建一个 GIHPY 账户(免费的！)和[创建一个 app](https://developers.giphy.com/dashboard/?create=true) 。您创建的每个应用程序都有自己的 API 密钥。

> 请记下您的 GIPHY API 密钥，因为您稍后会用到它

[![](img/23f6a063e1b4924c545ff68fab6c2820.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_aWrcOwW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/img/giphy-dashboard.jpg)

### 配置时差

请注意，本节中的说明改编自[松弛文档](https://api.slack.com/slash-commands#creating_commands)

#### 创建 Slack App

登录您的[空闲工作区](https://slack.com/signin)。从[创建一个新的 Slack 应用](https://api.slack.com/apps/new)开始

[![](img/a306aedbc6321a21904b1a0646e92dbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g_Od4IdL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0rjtgorqyqmmwozp5p5b.jpg)

#### 创建斜线命令

一旦你完成了应用程序的创建，进入应用程序的设置页面，然后点击导航菜单中的*斜线命令*功能。

[![](img/1dddba310842e0429e3e4165067f5e43.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wh0OlYks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tvpzmlcnjamfbv5mcx5m.jpg)

你会看到一个标有*创建新命令*的按钮，当你点击它时，你会看到一个屏幕，要求你用需要的信息定义你的新*斜线命令*。

[![](img/67314dbdce015adbb3b7671c66af79a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--apdZq6hX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpued2a9qgdvs5rvnc6m.jpg)

输入所需的信息。请注意,**请求 URL** 字段是您将输入函数的 HTTP 端点的字段，在您部署它之后将可用。您可以暂时使用虚拟 URL 作为占位符，例如`https://temporary.com:4242`

[![](img/fbbb176edcf177ae865713abdd8c30c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_G40XEYO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c77tvc0jbihl5xrjsncd.jpg)

完成后，点击**保存**完成。

#### 将应用安装到您的工作区

一旦你完成创建 Slash 命令，进入你的应用程序的设置页面，点击导航菜单中的*基本信息*功能，选择*将你的应用程序安装到你的工作区*，然后点击*将应用程序安装到工作区*——这将把应用程序安装到你的 Slack 工作区，以测试你的应用程序并生成你需要与 Slack API 交互的令牌。

[![](img/0e5fcf0b2b41e92b66b689e2babe07cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0i85iosR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jf9nu57x24s4lopbwao9.jpg)

一旦你完成应用程序的安装，*应用程序凭证*将出现在同一页面上。你需要从那里拿到你的*松弛签名秘密*

> 记下你的应用签名密码，因为你稍后会用到它

[![](img/92508e7e762a685cb14e4c3bdbe22a91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HVrENkh---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/daem7h5ej0lzyo5phcum.jpg)

### 部署到 Azure

首先克隆 GitHub 存储库，然后切换到应用程序目录

```
 git clone https://github.com/abhirockzz/funcy-azure-functions
    cd funcy-azure-functions 
```

Enter fullscreen mode Exit fullscreen mode

`pom.xml`文件包含 Azure Functions Maven 插件使用的以下属性——应用程序名称(`functionAppName`)、区域(`functionAppRegion`)和资源组(`functionResourceGroup`)。上面的参数都有默认值，所以如果您愿意，可以选择继续使用它们。

*   `functionAppName` - funcyapp
*   `functionAppRegion`——韦斯特乌斯
*   `functionResourceGroup`-Java-功能-群组

> 请注意，应用名称在 Azure 中必须是唯一的。

如果您希望更改这些值，请查看来自`pom.xml`的这个片段，它突出显示了需要更新的`<properties>`

```
 <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <azure.functions.maven.plugin.version>1.3.1</azure.functions.maven.plugin.version>
        <azure.functions.java.library.version>1.3.0</azure.functions.java.library.version>
        <functionAppName>YOUR_APP_NAME</functionAppName>
        <functionAppRegion>AZURE_REGION</functionAppRegion>
        <functionResourceGroup>RESOURCE_GROUP_NAME</functionResourceGroup>
        <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    </properties> 
```

Enter fullscreen mode Exit fullscreen mode

> 该函数的名称不同于应用程序名称(通过`pom.xml`配置),而是使用函数的 Java 代码中的`@FunctionName`注释来指定——在本例中，名称是`funcy`。

现在，您可以构建该功能并将其部署到 Azure

```
//build
mvn clean package

//deploy
mvn azure-functions:deploy 
```

Enter fullscreen mode Exit fullscreen mode

成功部署的结果如下所示

```
[INFO] Successfully updated the function app.funcyapp
[INFO] Trying to deploy the function app...
[INFO] Trying to deploy artifact to funcyapp...
[INFO] Successfully deployed the artifact to https://funcyapp.azurewebsites.net
[INFO] Successfully deployed the function app at https://funcyapp.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS 
```

Enter fullscreen mode Exit fullscreen mode

使用 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&WT.mc_id=servsept_devto-blog-abhishgu) 列出您的功能应用

```
az functionapp list --query "[].{hostName: defaultHostName, state: state}" 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到一个 JSON 输出

```
[
    {
        "hostName": "funcyapp.azurewebsites.net",
        "state": "Running"
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

> 你应该可以在 [Azure 门户](https://portal.azure.com)中看到该功能(在`Function App`菜单下)

一旦部署成功，该函数应该准备好为请求提供服务，并且可以在以下端点通过 HTTP(s)进行访问- `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>`

对于一个名为`funcyapp`的应用程序和一个名为`funcy`的函数，端点应该是

```
https://funcyapp.azurewebsites.net/api/funcy 
```

Enter fullscreen mode Exit fullscreen mode

你就快到了！

#### 更新你的 Azure 功能应用

既然 Azure Functions 应用程序已经启动并运行，您需要更新它以将`Giphy API Key`和`Slack Signing Secret`作为环境变量。

```
az functionapp config appsettings set --name <APP_NAME> --resource-group <RESOURCE_GROUP_NAME> --settings "SLACK_SIGNING_SECRET=<SLACK_SIGNING_SECRET>" "GIPHY_API_KEY=<GIPHY_API_KEY>" 
```

Enter fullscreen mode Exit fullscreen mode

例如

```
az functionapp config appsettings set --name funcyapp --resource-group java-functions-group --settings "SLACK_SIGNING_SECRET=foobarb3062bd293b1a838276cfoobar" "GIPHY_API_KEY=foobarrOqMb5fvJdIuxTCr3WUDfoobar" 
```

Enter fullscreen mode Exit fullscreen mode

> 详情请参考关于[如何在 Azure 门户](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings/?WT.mc_id=servsept_devto-blog-abhishgu)中管理一个功能 app 的文档。

### 更新 Slack app

转到应用程序的设置页面，然后在导航菜单中单击*斜杠命令*功能。编辑命令，用您的函数 HTTP(s)端点替换*请求 URL* 字段的值

[![](img/91e8c6836cd22122267d927f5f03dba7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sYkxusMQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jczkdyz4sy898ch52n3d.jpg)

### `funcy`时间！

从您的工作区中，调用命令

`/funcy <search term>`

既然你对猫不会错，那就试试吧

`/funcy cat`

[![](img/7894ec21e0cff2f75b1f59b5c5c43e1b.png)](https://i.giphy.com/media/13HBDT4QSTpveU/giphy.gif)

如果您在第一次调用后看到 Slack 的`Timeout error`,请不要担心。这是由于“冷启动”问题，该功能需要几秒钟来引导，但 [Slack 期望在 3 秒钟内得到响应](https://api.slack.com/slash-commands#responding_basic_receipt)。只要重试(几次)就可以了。

> 如果你的应用程序无法承受由于“空闲冷启动”而导致的延迟，你可能想看看[Azure Functions Premium plan](https://azure.microsoft.com/en-us/blog/announcing-the-azure-functions-premium-plan-for-enterprise-serverless-workloads/?WT.mc_id=servsept_devto-blog-abhishgu)，它提供了*“预热的实例来运行你的应用程序，在空闲后没有延迟……”*

### 资源

下面提到的资源是专门用来开发这篇博文中展示的演示应用程序的，所以你可能会发现它们也很有用！

*   [Azure Functions 开发者指南(通用)](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference/?WT.mc_id=servsept_devto-blog-abhishgu)和 [Azure Functions Java 开发者指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-java/?WT.mc_id=servsept_devto-blog-abhishgu)
*   [如何使用 Java 创建函数并将其发布到 Azure Functions 的快速入门](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-java-maven/?WT.mc_id=servsept_devto-blog-abhishgu)
*   用于 Azure 功能的 Maven 插件
*   [如何在本地编码和测试 Azure 功能](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-local/?WT.mc_id=servsept_devto-blog-abhishgu)
*   [如何在 Azure 函数中管理连接](https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections/?WT.mc_id=servsept_devto-blog-abhishgu)
*   [GitHub 上 Azure 服务的 Maven 插件](https://github.com/microsoft/azure-maven-plugins)

> 别忘了看看这篇博文的第二部分[，在那里我已经详细介绍了代码](https://dev.to/azure/code-walkthrough-for-funcy-a-serverless-slack-app-using-azure-functions-3gmd)

我真的希望你喜欢这篇文章，并从中学到了一些东西！如果你做了，请喜欢并跟随。很高兴通过 [@abhi_tweeter](https://twitter.com/abhi_tweeter) 获得反馈或发表评论。