# 使用 Azure 实例元数据服务

> 原文：<https://dev.to/omiossec/azure-vm-metadata-5h36>

从 Azure VM 参数中查询数据可能非常容易。您可以使用 PowerShell、AZ CLI、Rest API 或门户获取有关虚拟机的信息。但是，如果您需要来自虚拟机内部的信息，该怎么办呢？

有一些解决方案，如安装 AZ PowerShell 模块，并设置一个系统，为虚拟机分配托管身份，以在 Azure 上执行一些关于自身的查询，这是可行的，但效率低下，有点危险。系统分配的托管身份应仅在需要的情况下使用(访问数据库或管理任务)。

有更好的方法。每个主要的云提供商都管理一个元数据服务，您可以使用该服务从进行查询的虚拟机实例中检索信息。

对于 Azure VM，您可以通过使用特殊的不可路由 IP 地址 169.254.169.254 来使用元数据服务。这个 IP 仅在 Azure 中有效，即使虚拟机没有互联网接入也有效。Azure 实例元数据服务是基于 restful API 的。它可以通过使用 HTTP 请求来使用。有 3 种元数据服务:

*   实例:检索关于当前虚拟机的信息

*   ScheduledEvents:有关虚拟机或可用性集的计划维护的信息

*   认证:确保虚拟机运行在 Azure 上，而不是其他地方

要访问这些服务，您只需提供 URI 中的路径。

*   /实例

*   /计划事件

*   /已证明

你还需要提供一个 API 版本，版本会不时变化，最新版本是 2019-03-11(可能不是所有 Azure 地区都有)。

网络服务 URI 看起来像什么

**[http://169 . 254 . 169 . 254/元数据/实例？API-version = 2013-03-11](http://169.254.169.254/metadata/instance?api-version=2013-03-11)T3**

让我们进一步了解一下实例服务。

为了查询元数据服务，我使用 PowerShell (PS Core)，在发送任何东西之前，我需要发送一个特殊的头。没有这个头，任何请求都会失败。

```
$MetaDataHeaders  =  @{"Metadata"="true"} 
```

Enter fullscreen mode Exit fullscreen mode

每个对服务的 http 请求都必须带有这个头。

```
Invoke-RestMethod  -Method  GET  -uri  "http://169.254.169.254/metadata/instance?api-version=2018-10-01"  -Headers  $MetaDataHeaders 
```

Enter fullscreen mode Exit fullscreen mode

它将返回一个 PSCustomObject，它有两个成员，Compute 和 Network。

Compute 包含关于实例的信息，而 network 包含用于具有私有和公共 IP 地址的所有接口的数组。

我有什么好担心的？

从虚拟机上的当前部署中获取一些信息可以简化配置管理和软件交付。

看一看返回的模式

```
"compute":  {  "azEnvironment":"AzurePublicCloud",  "customData":"",  "location":"northeurope",  "name":"vmtest",  "offer":"WindowsServer",  "osType":"Windows",  "placementGroupId":"",  "plan":  {"name":"","product":"","publisher":""},  "platformFaultDomain":"0",  "platformUpdateDomain":"0",  "provider":"Microsoft.Compute",  "publicKeys":[],  "publisher":"MicrosoftWindowsServer",  "resourceGroupName":"requirements-rg",  "resourceId":"/subscriptions/2a21f4a3-c253-47fc-a05d-1c04de8ed626/resourceGroups/requirements-rg/providers/Microsoft.Compute/virtualMachines/vmtest",  "sku":"2019-Datacenter-smalldisk",  "subscriptionId":"2a21f4a3-c253-47fc-a05d-1c04de8ed626",  "tags":"env:lab;labname:requirements",  "version":"17763.557.20190604",  "vmId":"32351821-a67d-449d-a00b-ff73cb802781",  "vmScaleSetName":"",  "vmSize":"Standard_B2s",  "zone":""  } 
```

Enter fullscreen mode Exit fullscreen mode

各种信息，Azure 内部信息，如 ResourceID 或 vmId，当前部署信息，如 VMName(可能与主机名不同)可用性集或 VM 位置。最后一个标签。

标签是用来识别 Azure 中资源的键/值对。您可以使用它来识别所有者，即应用程序名称。每个资源最多可以有 15 个标签。因此，您也可以在配置管理或软件交付过程中使用它。

由于标签可以在资源生命周期中更新，所以它们可以被视为从 Azure 端向工作负载端发送信息的一种方式。

这就是要点，在交付过程中，每次发布新的应用程序版本时，您都需要提供不可变的虚拟机。你可能有不同的角色(前台，后台，数据处理，…)，不同的环境(生产，质量，开发，…)和不同的包。

您可以只拥有一个利用标签的工具，而不是针对每种情况使用一个配置工具。您可以使用版本号标记作为版本号下载到服务器上，您可以使用角色标记通过 PowerShell DSC、Requirements 或 Chef 来提供服务。

可能性不是无限的，你只有 15 个标签，你需要其中一些来识别资源。但是您只需要更改标签，而不需要更改供应和交付工具。

Azure 实例元数据以字符串形式返回标签

```
"tags":"env:lab;labname:requirements", 
```

Enter fullscreen mode Exit fullscreen mode

最好有一个散列表。我们可以使用 ConvertFrom-StringData cmdlet。它将字符串转换成哈希表。为此，我们需要转换标签的值。ConvertFrom-StringData 在字符串中查找键/值对，":"需要替换为" = "，并且 cmdlet 使用新行来分隔键/值对，"；"需要替换为“` n”

`` powershell
$ metadata object = Invoke-rest Method-Method GET-uri "[http://169 . 254 . 169 . 254/metadata/instance？API-version = 2018-10-01](http://169.254.169.254/metadata/instance?api-version=2018-10-01)-Headers $元数据标题`

 `$ AzureInstanceTags = $ metadata object . compute . tags

$ AzureInstanceTags = $ AzureInstanceTags。替换("；"，" ` n ")

$ AzureInstanceTags = $ AzureInstanceTags。替换(":"，" = ")

$ AzureInstanceTagsHash = convert from-string data $ AzureInstanceTags

$ AzureInstanceTags = $ metadata object . compute . tags

$ AzureInstanceTags = $ AzureInstanceTags。替换("；"，" ` n ")

$ AzureInstanceTags = $ AzureInstanceTags。替换(":"，" = ")

$ AzureInstanceTagsHash = convert from-string data $ AzureInstanceTags
`

 `这是我们利用元数据服务和标签的一种方式，但是还有许多其他的解决方案。元数据服务还包括比实例更多的服务，您可以检查计划的维护，并且您还可以验证您的实例是否在 Azure 中运行，以及被证明的服务(如果您在 Azure Market Place 中提供服务或虚拟机映像，这很有用)。``