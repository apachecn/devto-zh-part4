# 将 Azure 资源部署到多个资源组

> 原文：<https://dev.to/tylerd/deploying-azure-resources-to-multiple-resource-groups-3872>

Azure 资源管理器(ARM)模板提供了一个优秀的内置资源配置和部署解决方案。你可以找到丰富的模板来部署任何东西，从 Azure 应用服务上的 [Wordpress 站点到私人 VNET](https://github.com/Azure/azure-quickstart-templates/tree/master/wordpress-app-service-mysql-inapp) 上的完整 [HDInsight 集群。](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-secure-vnet)

我经常与需要超越 ARM 模板基础的客户一起工作，使用不同的 RBAC 权限跨多个资源组部署复杂的解决方案。

因此，在这里我将分享一些在创作复杂模板时可能对你有帮助的提示和技巧。

## 部署到多个 Azure 资源组

首先，一个很常见的问题，也是这篇帖子的标题，跨多个资源组部署 Azure 资源。您可以通过三种方式实现这一点:

1.  使用脚本或部署引擎多次部署(Azure DevOps 管道)
2.  部署到“主要”资源组[，嵌套模板部署到其他资源组](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)
3.  使用[订阅级资源模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/deploy-to-subscription)来定义所有资源组和嵌套模板

### 使用脚本(#1)

这是迄今为止最简单的解决方案，但是也是最容易出错的。你必须编写 Azure 部署系统会为你处理的特性，比如依赖、失败和排序。很可能需要一个脚本，但是最好尽可能简单，将所有配置添加到 ARM 模板中。

### 资源组部署其他资源组(#2)

这是使用可以在`"Microsoft.Resources/deployments"`类型上设置的`"resourceGroup"`属性完成的，也称为嵌套模板。总的来说，如果您已经在使用嵌套模板，这是一个最小的变化。

您还可以使用`"subscriptionId"`属性部署到多个订阅。

这里有几个问题，一个是子资源组需要在嵌套部署之前存在(就像在执行模板部署时需要定义现有的 RG 一样)。您可以在“主”RG 上运行部署之前编写所有 RG 的创建脚本，或者使用嵌套模板上带有`dependsOn`属性的`"Microsoft.Resources/resourceGroups"`资源类型。

这里有一个例子

```
{
    "type": "Microsoft.Resources/resourceGroups",
    "apiVersion": "2018-05-01",
    "location": "[parameters('location')]",
    "name": "[parameters('msiResourceGroup')]",
    "properties": {}
},
{
    "name": "msiDeployment",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2017-05-10",
    "resourceGroup": "[parameters('msiResourceGroup')]",
    "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('msiResourceGroup'))]"
    ],
    "properties": { ... }
} 
```

此外，根据嵌套模板的方式，`resourceGroup()`函数的行为会有所不同。如果你有一个嵌入的模板`"template": {}`，`resourceGroup()`函数将引用父模板 RG。或者，如果你有一个链接模板`"templateLink": { "uri": "..."}`,`resourceGroup()`函数将引用子模板 RG。这同样适用于`subscription()`功能。

### 订阅级模板(#3)

这可能是我部署复杂的多 RG 解决方案的首选方法。大多数概念与跨 RG 部署相同，但是没有“主”RG。通过这种方法，你可以部署到一个完全空白的订阅，这就是为什么它经常作为一种“订阅工厂”模式与 [Azure Blueprints](https://docs.microsoft.com/en-us/azure/governance/blueprints/overview) 结合使用。

要创作订阅模板，您需要使用不同的模板模式`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`并使用`New-AzDeployment`或`az deployment create`命令执行部署。

下面是 Azure docs 的一篇文章，详细内容如下:[在订阅级别创建资源组和资源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/deploy-to-subscription)

订阅模板相当轻，大部分繁重的工作都在嵌套模板中完成。订阅模板中有一些功能是不可用的，比如`resourceGroup()`，这意味着您不能将`resourceGroup().location`用作默认部署位置。

您需要向模板添加一个`"location"`参数，并在创建资源组时使用该值。

这里有一个例子:

```
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": { 
        "hdiResourceGroup": {
            "type": "string",
            "defaultValue": "DL-HDI"
        },
        "msiResourceGroup": {
           "type": "string",
           "defaultValue": "DL-MSI"
        },
        "location": {
            "type": "string",
            "defaultValue": "westus2"
        } ...
    },
    "variables": {...},
    "resources": {
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('location')]",
            "name": "[parameters('hdiResourceGroup')]",
            "properties": {}
        },
    }
    ....
} 
```

## 额外提示:使用`templateLink.uri`属性

我不太喜欢为嵌套模板 URL 和 SAS 令牌使用额外的参数。你可能在前面有下划线的例子中见过它们，比如`"_sasToken"`或`"_templateRoot"`

当您使用模板链接 URL(在`raw.githubusercontent.com`或 Azure Blob 存储上)创建部署时，您可以访问[部署模型](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-functions-deployment#deployment)上的`templateLink`属性

如果你使用的是公共 URL，你可以只使用嵌套模板的`uri()`函数。

`"msiTemplate": "[uri(deployment().properties.templateLink.uri, 'dl-msi.json')]`

如果您想使用 Azure Blob 存储 SAS 令牌来保护模板，您可以使用一些字符串函数将 SAS 令牌从 TemplateLink 属性中提取出来。

例如:

```
"variables": {
  "templateRoot":"[deployment().properties.templateLink.uri]",
  "hasToken":"[not(equals(indexOf(variables('templateRoot'),'?'), -1))]",
  "sasToken":"[if(variables('hasToken'),substring(variables('templateRoot'),indexOf(variables('templateRoot'),'?')),'')]",
  "msiTemplate": "[concat(uri(deployment().properties.templateLink.uri, 'dl-msi.json'), variables('sasToken'))]",
} 
```

注意，这个例子支持公共和访问令牌 URL，这增加了条件语句的复杂性。我尽量保持简单。

本练习假设您在运行任何部署之前部署模板。这不适用于本地文件或内联 JSON 部署。