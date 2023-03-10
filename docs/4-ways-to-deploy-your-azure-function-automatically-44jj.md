# 如何自动部署你的 Azure 功能(4 种方式，使用 ARM 模板)

> 原文：<https://dev.to/fboucheros/4-ways-to-deploy-your-azure-function-automatically-44jj>

能够在我们的解决方案中添加一些无服务器组件，让它们变得更好，这真是太好了。但是我们如何管理它们呢？在这篇文章中，我将解释如何创建一个 Azure 资源管理器(ARM)模板来部署任何 Azure 功能，并展示我如何使用这个结构来部署我这些天一直在工作的开源项目。

## 第 1 部分-手臂模板

ARM 模板是一个描述我们架构的 JSON 文件。要部署 Azure 功能，我们至少需要三种资源:functionApp、服务计划和存储帐户。

[!["Generic Simple ARM"](img/45c84fa18c6a17abe576cda0517a739d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Df0WDF7o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6g36tkfiqe0ucwvmu6vt.png)

FunctionApp 当然是我们的函数。服务计划可以设置为动态的，或者描述您的功能将使用的资源类型。存储帐户是我们的代码在哪里。

[![](img/c42e9aee593ceae45d1f628af8d3e65b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2E1Hnrt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wnm6j3422te418bxa96u.png)

在上一个图像中，您可以看到这些组件之间如何进行更多的交互。在函数内部，我们将有一个属性列表。其中一个属性是**运行时**，例如，在 AZUnzipEverything 演示中，它将是 *dotnet* 。另一个属性是我们的存储帐户的连接字符串，它也是我们的 ARM 模板的一部分。由于该资源尚不存在，我们将需要使用动态代码。

功能节点将包含一个类型为`storageAccount`的子资源。这是我们指定代码的地方，所以它不能被克隆到 Azure。

### 建立手臂的简单功能

让我们来看一个简单的 Azure 函数的模板，它不需要任何依赖，我们将在后面检查它。

> 您可以使用任何文本编辑器来编辑您的 ARM 模板。然而，带有扩展 [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools&WT.mc_id=devto-blog-frbouche)和 [Azure 资源管理器片段](https://marketplace.visualstudio.com/items?itemName=samcogan.arm-snippets&WT.mc_id=devto-blog-frbouche)的捆绑包 [VSCode](https://code.visualstudio.com/?WT.mc_id=devto-blog-frbouche) 特别有效。

```
{  "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion":  "1.0.0.0",  "parameters":  {},  "variables":  {},  "resources":  [  {  "type":  "Microsoft.Storage/storageAccounts",  "apiVersion":  "2018-07-01",  "name":  "storageFunc",  "location":  "[resourceGroup().location]",  "tags":  {  "displayName":  "storageFunc"  },  "sku":  {  "name":  "Standard_LRS"  },  "kind":  "StorageV2"  },  {  "type":  "Microsoft.Web/serverfarms",  "apiVersion":  "2018-02-01",  "name":  "servicePlan",  "location":  "[resourceGroup().location]",  "sku":  {  "name":  "Y1",  "tier":  "Dynamic"  },  "properties":  {  "name":  "servicePlan",  "computeMode":  "Dynamic"  },  "tags":  {  "displayName":  "servicePlan"  }  },  {  "apiVersion":  "2015-08-01",  "type":  "Microsoft.Web/sites",  "name":  "functionApp",  "location":  "[resourceGroup().location]",  "kind":  "functionapp",  "dependsOn":  [  "[resourceId('Microsoft.Web/serverfarms', 'servicePlan')]",  "[resourceId('Microsoft.Storage/storageAccounts', 'storageFunc')]"  ],  "properties":  {  "serverFarmId":  "[resourceId('Microsoft.Web/serverfarms', 'servicePlan')]",  "siteConfig":  {  "appSettings":  [  {  "name":  "AzureWebJobsDashboard",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', 'storageFunc', ';AccountKey=', listKeys('storageFunc','2015-05-01-preview').key1)]"  },  {  "name":  "AzureWebJobsStorage",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', 'storageFunc', ';AccountKey=', listKeys('storageFunc','2015-05-01-preview').key1)]"  },  {  "name":  "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', 'storageFunc', ';AccountKey=', listKeys('storageFunc','2015-05-01-preview').key1)]"  },  {  "name":  "WEBSITE_CONTENTSHARE",  "value":  "storageFunc"  },  {  "name":  "FUNCTIONS_EXTENSION_VERSION",  "value":  "~2"  },  {  "name":  "FUNCTIONS_WORKER_RUNTIME",  "value":  "dotnet"  }  ]  }  },  "resources":  [  {  "apiVersion":  "2015-08-01",  "name":  "web",  "type":  "sourcecontrols",  "dependsOn":  [  "[resourceId('Microsoft.Web/sites/', 'functionApp')]"  ],  "properties":  {  "RepoUrl":  "https://github.com/FBoucher/AzUnzipEverything.git",  "branch":  "master",  "publishRunbook":  true,  "IsManualIntegration":  true  }  }  ]  }  ],  "outputs":  {}  } 
```

#### 存储账户

模板中列出的第一个资源是帐户存储。这没什么特别的。

#### 服务计划

服务计划是列表中的第二个资源。需要注意的是，为了能够使用 SKU **动态**，你至少需要将 **apiVersion** 的 API 版本设为“2018-02-01”。然后指定 SKU。

```
 "sku":  {  "name":  "Y1",  "tier":  "Dynamic"  } 
```

当然，如果你愿意，你可以用另一个 SKU。

#### 功能 App

最终的资源被添加到 mixt 中，这是所有部分聚集在一起的地方。需要注意的是，Azure 在部署时不考虑列出资源的另一个(只针对我们；) ).让 Azure 知道你需要添加依赖项。

```
"dependsOn":  [  "[resourceId('Microsoft.Web/serverfarms', 'servicePlan')]",  "[resourceId('Microsoft.Storage/storageAccounts', 'storageFunc')]"  ] 
```

这样，Azure 功能将在服务计划和存储帐户可用后创建。然后在**属性**中，我们将能够使用引用构建到 blob 存储的 ConnectionString。

```
{  "name":  "AzureWebJobsDashboard",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', 'storageFunc', ';AccountKey=', listKeys('storageFunc','2015-05-01-preview').key1)]"  } 
```

拼图的最后一块是 FunctionApp 中的子资源 **sourcecontrol** 。这将定义 Azure 应该从哪里以及在哪个分支中克隆代码。

```
"resources":  [  {  "apiVersion":  "2015-08-01",  "name":  "web",  "type":  "sourcecontrols",  "dependsOn":  [  "[resourceId('Microsoft.Web/sites/', 'functionApp')]"  ],  "properties":  {  "RepoUrl":  "https://github.com/FBoucher/AzUnzipEverything.git",  "branch":  "master",  "publishRunbook":  true,  "IsManualIntegration":  true  }  }  ] 
```

为了确保一切都是全自动的，属性 **publishRunbook** 和 **IsManualIntegration** 必须设置为`true`。否则，您将需要在 Git(在本例中是在 GitHub 上)和 Azure 中的 Git 之间进行同步。

> 有很好的文档解释了在 Azure Functions 中为你的 function 应用程序自动化资源部署的许多不同场景

### 真实样本:Azure 解压一切

为了在 GitHub 上部署项目[azunzipeyeverything，我还需要一个带有预定义容器(文件夹)的 Azure 存储。](https://github.com/FBoucher/AzUnzipEverything)

[![](img/c941b13dbb35ab70fbf8f15294110ea6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--idKNPDpz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3a9qxuhszz2vt7oxsqg7.png)

当然，Azure 函数和 ARM 模板的所有源代码都可以在 GitHub 上获得，但让我强调一下容器是如何从 ARM 模板定义的。

```
"resources":  [  {  "type":  "blobServices/containers",  "apiVersion":  "2018-07-01",  "name":  "[concat('default/', 'input-files')]",  "dependsOn":  [  "storageFiles"  ],  "properties":  {  "publicAccess":  "Blob"  }  }  ] 
```

就像使用 **sourcecontrol** 一样，我们需要向我们的存储帐户添加一个子资源列表。名称必须以`'default/'`开头。

## 第二部分-四个部署选项

现在我们有了一个描述我们需求的模板，我们只需要部署它。有多种方法可以做到这一点，但让我们看看其中的四种。

### 从 Azure 门户部署

从您最喜欢的浏览器导航到 Azure 门户([https://azure.portal.com](https://azure.portal.com))，然后直接在位于屏幕顶部(中间)的搜索栏中搜索“部署自定义模板”。或者去 https://portal.azure.com/#create/Microsoft.Template 的。在自定义部署页面中，点击链接**在编辑器中构建自己的模板**。从那里，您可以复制粘贴或上传您的 ARM 模板。您需要保存它才能看到真正的部署表单。

[![](img/6953201aa8f7e0a88ce2101aedb45ecb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXI8ZHi2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/77mtjip9h4ch4wubr732.png)

### 用脚本部署

是在 PowerShell 中还是在 Azure CLI 中，您可以使用这两个命令轻松部署您的模板。

在 Azure CLI 中

```
 # create resource group
    az group create -n AzUnzipEverything -l eastus

    # deploy it
    az group deployment create -n cloud5mins -g AzUnzipEverything --template-file "deployment\deployAzure.json" --parameters "deployment\deployAzure.parameters.json" 
```

在 PowerShell 中

```
 # create resource group  New-AzResourceGroup  -Name  AzUnzipEverything  -Location  eastus  # deploy it  New-AzResourceGroupDeployment  -ResourceGroupName  AzUnzipEverything  -TemplateFile  deployment\deployAzure.json 
```

### 部署到 Azure 按钮

帮助人们在其 Azure 订阅中部署您的解决方案的最佳方式之一是“部署到 Azure”按钮。

[![](img/96b4ef8d97bc8f7d78ccc7049dc87a20.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FFBoucher%2FAzUnzipEverything%2Fmaster%2Fdeployment%2FdeployAzure.json?WT.mc_id=cloud5mins-github-frbouche)

您需要创建一个图像链接(在 HTML 或 Markdown)到这个特殊的目的地建立两部分。

第一个是 Azure 门户的链接:

```
https://portal.azure.com/#create/Microsoft.Template/uri/ 
```

第二个是你手臂模板的位置:

```
https%3A%2F%2Fraw.githubusercontent.com%2FFBoucher%2FAzUnzipEverything%2Fmaster%2Fdeployment%2FdeployAzure.json 
```

但是，这个 URL 需要编码。网上有很多编码器，但是你也可以在终端上用下面的命令来实现(非常感谢 [@BrettMiller_IT](https://twitter.com/BrettMiller_IT) 在我的一次直播中向我展示了这个技巧)。

```
[System.Web.HttpUtility]::UrlEncode("https://raw.githubusercontent.com/FBoucher/Not-a-Dog-Workshop/master/deployment/deployAzure.json") 
```

点击该按钮会将用户带到 Azure 门户上的同一页面，但在用户订阅中。

### Azure DevOps 管道

从 Azure DevOps 门户([https://dev.azure.com](https://dev.azure.com))，选择您的项目并创建一个新的发布管道。点击**+Add a artifact**按钮来连接您的 Git 库。

[![](img/c4616cf13e7b3207cb44ec329e406115.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bL5HxsVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c6rqf0c23kc0rag5trp0.png)

添加后，您需要将任务添加到当前作业中。点击链接 **1 作业，0 任务** (4)。现在，您只需要指定您的 Azure 订阅、资源组的名称，并选择您的 ARM 模板在存储库中的位置。为了使部署在存储库中每次推送时都是自动的，单击那个小闪电并启用*连续部署触发器*。

[![](img/c0cacbdf14cbb7ccf3bdfd37a84882e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JvXOH7h3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vm5drd4d2ox4qixg6tse.png)

## 收尾

瞧，你知道有四种不同的方式来自动部署你的 Azure 功能。但是不要相信我的话，你自己试试吧！如果你需要更多的细节，你可以访问 GitHub 上的[项目，或者观看我演示这篇文章内容的视频。](https://github.com/FBoucher/AzUnzipEverything)

[https://www.youtube.com/embed/b54Guh_nDjo](https://www.youtube.com/embed/b54Guh_nDjo)