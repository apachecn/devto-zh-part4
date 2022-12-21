# 设置 Azure 凭据文件

> 原文：<https://dev.to/azure/setting-up-the-azure-credentials-file-41o3>

当我回顾我写的关于设置[测试厨房](https://dev.to/azure/building-infrastructure-with-test-kitchen-and-chef-5g99)的帖子时，我意识到在服务主体创建输出和凭证文件之间返回的参数之间存在一些问题。在这篇文章中，我提供了这些参数之间的映射。

当设置一个[服务主体](https://dev.to/azure/you-gotta-keep-privileges-separated-509)来使用脚本或工具来自动化你的 Azure 基础设施时，你可以设置一个凭证文件。天青国书文件 *~/。azure/credentials* 包含一些参数。您可以在创建服务主体时获得这些参数。

*为 rbac* 创建的[输出看起来像:](https://dev.to/azure/you-gotta-keep-privileges-separated-509) 

```
{
  "appId": "00000000-1111-0000-0000-000000000000",
  "displayName": "SigjeServiceDemoPrincipal",
  "name": "http://SigjeServiceDemoPrincipal",
  "password": "df111111-0000-0000-0000-100000000000",
  "tenant": "11111111-0000-0000-0000-000000000000"
} 
```

Enter fullscreen mode Exit fullscreen mode

Azure 凭证文件的配置如下所示:

```
[00000000-0000-0000-0000-100000000000]
client_id=00000000-1111-0000-0000-000000000000
secret=df111111-0000-0000-0000-100000000000
tenant=11111111-0000-0000-0000-000000000000 
```

Enter fullscreen mode Exit fullscreen mode

从创建服务主体接收到的输出到创建凭证文件的映射:

*   *appId* 映射到 *client_id* ，是服务主体的具体标识。

*   *密码*映射到*秘密*，是服务主体的凭证。

*   *租户*在两者中是相同的，并且是您在其中创建该服务主体的 Active Directory 实例的特定标识符。

*   最后一个必需的配置参数是*订阅 ID* 。订阅 ID 是唯一标识订阅的标识符，以便使用此凭据创建和管理任何资源和服务。这就是配置中括号内的内容。

现在，您应该能够设置您的 Azure 凭据文件了。如果在创建服务主体时没有跟踪密码，[重置服务主体](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?WT.mc_id=devto-blog-jedavis&view=azure-cli-latest#reset-credentials)的凭证。

想要了解更多关于 Azure 的真实场景，请访问 Azure 提示和技巧网站。