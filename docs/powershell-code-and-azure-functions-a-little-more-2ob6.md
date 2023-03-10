# PowerShell 核心和 Azure 函数，操作方法

> 原文：<https://dev.to/omiossec/powershell-code-and-azure-functions-a-little-more-2ob6>

Azure Functions v2 中的 PowerShell 可以很简单。你打开门户，写几行代码，如此而已。对于概念验证或某些测试来说是这样的。但是一旦你需要真正的资源，就不一样了。

让我们从头开始，函数将在其中运行的函数应用程序的名称，除非您计划在 PowerShell 中创建无服务器 API，否则函数应用程序的名称没有那么重要。只需要明确一点。但请记住名称，也是功能应用程序 URL(xxx.azurewebsite.com)的一部分，它必须是全球唯一的。

为了部署一个新的功能应用程序，我使用了一个像这样的 ARM 模板

```
{  "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion":  "1.0.0.0",  "parameters":  {  "functionAppName":  {  "type":  "string"  },  "location":  {  "type":  "string",  "defaultValue":  "[resourceGroup().location]"  }  },  "variables":  {  "hostingPlanName":  "[concat(parameters('functionAppName'), '-plan')]",  "storageAccountName":  "[concat('stofunc', uniquestring(resourceGroup().id))]",  "runtimeStack":  "powershell",  "timezone":  "UTC"  },  "resources":  [  {  "name":  "[parameters('functionAppName')]",  "type":  "Microsoft.Web/sites",  "apiVersion":  "2018-02-01",  "location":  "[parameters('location')]",  "kind":  "functionapp",  "dependsOn":  [  "[resourceId('Microsoft.Web/serverfarms/', variables('hostingPlanName'))]",  "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"  ],  "identity":  {  "type":  "SystemAssigned"  },  "properties":  {  "siteConfig":  {  "appSettings":  [  {  "name":  "FUNCTIONS_WORKER_RUNTIME",  "value":  "[variables('runtimeStack')]"  },  {  "name":  "AzureWebJobsStorage",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2017-06-01').keys[0].value)]"  },  {  "name":  "FUNCTIONS_EXTENSION_VERSION",  "value":  "~2"  },  {  "name":  "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",  "value":  "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2017-06-01').keys[0].value)]"  },  {  "name":  "WEBSITE_CONTENTSHARE",  "value":  "[toLower(parameters('functionAppName'))]"  },  {  "name":  "WEBSITE_TIME_ZONE",  "value":  "[variables('timezone')]"  }  ]  },  "name":  "[parameters('functionAppName')]",  "clientAffinityEnabled":  false,  "serverFarmId":  "[resourceId('Microsoft.Web/serverfarms/', variables('hostingPlanName'))]"  }  },  {  "type":  "Microsoft.Web/serverfarms",  "apiVersion":  "2018-11-01",  "name":  "[variables('hostingPlanName')]",  "location":  "[parameters('location')]",  "properties":  {  "name":  "[variables('hostingPlanName')]"  },  "sku":  {  "name":  "Y1",  "tier":  "Dynamic",  "size":  "Y1",  "family":  "Y",  "capacity":  0  }  },  {  "name":  "[variables('storageAccountName')]",  "type":  "Microsoft.Storage/storageAccounts",  "apiVersion":  "2018-07-01",  "location":  "[parameters('location')]",  "kind":  "StorageV2",  "tags":  {  "type":  "Function Storage",  "FunctionName":  "[parameters('functionAppName')]"  },  "sku":  {  "name":  "Standard_LRS",  "tier":  "Standard"  },  "properties":  {  "accessTier":  "Hot"  }  }  ],  "outputs":  {  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对此没什么好说的。你可以注意到，功能 App 只是 Web App 的一种。因此，与 web 应用程序相关的一切都可以使用函数。你需要一个托管计划和一个存储帐户来存储应用程序文件(功能，配置，日志等)

你会注意到一个额外的应用程序设置，网站 _ 时区。默认情况下，函数 App 使用 UTC 时区运行。如果您需要运行计时器触发功能，可能会有问题，因为 UTC 距离巴黎有 2 个小时的路程。此设置会更改函数的时区。该值可以在处找到

在本地，函数 app 只是一个包含 3 个文件 host.json、profile.ps1 和 Requirements.psd1 的文件夹。

Host.json，定义函数 App、扩展、日志、超时...默认情况下，它包含运行时版本(version= "2.0 ")和 ManagedDependency 字段。该文件控制功能应用程序中的所有配置选项以及所有功能。

*   Functiontimeout，函数实例的默认超时为 5 分钟。在消费计划中，最长可达 10 分钟，在专用计划中最长可达 30 分钟。

*   Hosthealthmonitor，默认情况下是活动的，运行状况监视器检查性能计数器(连接、进程和线程)。如果计数器接近阈值，主机停止启动新功能，直到计数器恢复正常。您可以控制检查间隔和检查窗口，也可以禁用它(但这不是个好主意)

*   Http，用于控制路由前缀、并发请求数和队列中的请求数

*   日志记录，控制日志级别，禁用控制台日志记录和应用程序洞察设置。您可以通过函数控制日志记录。

*   ExtensionBundle，用于安装应用程序中的功能所需的扩展

还有几个参数。这里有一个示例 host.json，它定义了 10 分钟的超时，并记录了除 OnFunction 之外的所有函数的属性。

```
{  "version":  "2.0",  "managedDependency":  {  "enabled":  true  },  "extensionBundle":  {  "id":  "Microsoft.Azure.Functions.ExtensionBundle",  "version":  "[1.*, 2.0.0)"  }  ,  "logging":  {  "logLevel":  {  "default":  "Information",  "MyFunction.OnFunction":  "Trace"  },  "fileLoggingMode":  "always"  },  "functionTimeout":  "00:10:00"  } 
```

Enter fullscreen mode Exit fullscreen mode

另外两个文件是

Profile.ps1，它在功能应用启动时运行，默认情况下，如果启用了托管身份，它会使用 connect-azAccount 创建到 Azure 的连接

Requirements.psd1，以加载 Azure PowerShell 模块。

现在，我们有了一个函数应用程序，可以讨论函数了。函数只是函数应用程序根路径中的一个文件夹。这个文件夹只需要 2 个文件，定义函数的 function.json 和代码的 run.ps1。文件夹的名称是在功能应用程序中部署的功能的名称。

你什么都不做。在 function App 文件夹内用这两个文件创建一个文件夹即可。

Function.json 定义了函数的配置，模式很简单。您可以定义一个且仅一个触发器和零到多个绑定。关于 PowerShell Azure Functions 的[绑定，你可以查看我上一篇关于 Azure Functions 的帖子。](https://dev.to/omiossec/azure-functions-v2-trigger-and-binding-with-powershell-core-2f71)

但是有一点你需要明白，绑定和触发，除了定时器，需要扩展。当您在门户上时，您应该注意到当您添加触发器或绑定时，您可以选择安装扩展。当然，当您在本地工作时，您没有这样的选项，但是我们可以选择编辑 host.json 并添加一个扩展包对象

```
 "extensionBundle":  {  "id":  "Microsoft.Azure.Functions.ExtensionBundle",  "version":  "[1.*, 2.0.0)"  } 
```

Enter fullscreen mode Exit fullscreen mode

在下一次部署时，函数应用程序将安装绑定所需的扩展。

但是在编写 Azure 函数时，你可能想要重用你代码的某个部分，或者使用某种单元测试。

Azure functions 允许你在 function app 里面添加一个名为 modules 的文件夹。该文件夹将不被视为功能，系统将从此目录导入每个 PowerShell。

如果您需要公共存储库中的模块，您可以使用保存模块 cmdlet

```
Save-module  –name  "<Module Name>"  -path  "x:\FunctionAppFolder\modules\" 
```

Enter fullscreen mode Exit fullscreen mode

你可以编写模块在 Azure 函数中使用。它有助于分离核心逻辑、执行逻辑和数据结构。

写一个 PowerShell 核心模块，特别是用 Class 可以帮助很大。您可以创建复杂对象，并将函数用作接口。

您可能会遇到需要执行由不同事件触发的相同任务的情况。有了作为数据结构的类和作为模块接口的函数，你只需要编写一次这些任务。

powershell 模块的一个优势是我们必须用 Pester 进行单元测试的便利性。

测试一个模块比测试一个 azure 函数更容易。很难重现 Azure 函数的不同状态，在 CI/CD 链中自动重现更难。模块测试更容易。纠缠是一个完美的工具。inModuleScope 命令允许测试模块的内部功能。

让你模仿命令，这样你就可以专注于函数本身。您甚至可以通过使用 new-AzStorageContext -local 来模拟 Azure 存储行为。

在 PowerShell 工具制作中，使用带有 Pester 的单元测试非常重要。它确保对代码库的更改不会破坏任何东西，并有助于为您需要编写的每个 Azure 函数创建 CI/CD 工具。

我们有几个选项来将功能应用部署到 Azure。可以用 GitHub 和 Azure DevOps 管道。Azure functions 将从您的存储库中的分支安装任何新内容，Azure DevOps 用于在合并该分支中的内容之前运行单元测试。

另一种方法是使用 Publish-AzWebApp cmdlet。它获取一个 zip 文件，并将其发送到 Azure 中的函数应用程序。

zip 文件中的文件需要在第一级。所有必需的文件都需要在 zip 文件、host.json、profile.ps1、Requirements.psd1、modules 文件夹和所有函数中。zip 文件的内容将替换 Azure 中的当前功能。

构建它的一种方法是使用这种脚本

```
$TmpFuncZipDeployPath  =  c:\temp\mytmpFunction.zip  $excludeFilesAndFolders  =  @(".git",".vscode","bin","Microsoft",".funcignore",".gitignore")  $FileToSendArray  =  @()  foreach  ($file  in  get-childitem  -Path  $this.FunctionAppPath)  {  if  ($file.name  -notin  $excludeFilesAndFolders)  {  $FileToSendArray  +=  $file.fullname  }  }  compress-archive  -Path  $FileToSendArray  -DestinationPath  $TmpFuncZipDeployPath  Publish-AzWebapp  -ResourceGroupName  $RessourceGroup  -Name  $FunctionAppName  –ArchivePath  $TmpFuncZipDeployPath  -force 
```

Enter fullscreen mode Exit fullscreen mode