# PowerShell 中改进的 SOAP 代理管理

> 原文：<https://dev.to/sarafian/improved-soap-proxies-management-in-powershell-6hf>

虽然 SOAPProxy 是一个独立的解决方案，用于改进 PowerShell 上的 SOAP，但它是作为一个更大问题的一部分开发的，这个问题在[关于 JSONPath、SOAP 和 Amadeus 与 PowerShell 的帖子简介](https://sarafian.github.io/amadeus/2019/06/24/powershell-jsonpath-soap-proxy-amadeus.html)中讨论。

# 在 New-WebServiceProxy 的引擎盖下

当使用 [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) 时，PowerShell 将下载 API 的 WSDL，并使用它来生成代理接口、数据契约和头的类型。这都是通过利用`System.Web.Services`组件实现的。NET 是 ASMX 的主力，这是一项相当古老的技术。ASMX 是 WCF 的前身，也因支持 SOAP 1.1 而闻名。

大多数人不清楚的是，生成的类型驻留在内存中的程序集中。让我们想象一下，我们创建一个针对 SOAP 接口的代理，该接口包含一个接受类型为`DoSomethingRequest`的参数并返回类型为`DoSomethingResponse`的输出的操作`DoSomething`。`DoSomethingRequest`和`DoSomethingResponse`是数据契约。

```
$proxy1=New-WebServiceProxy  -Uri  $uriDoSomething  -Namespace  Example 
```

`-Namespace`参数是可选的，如果没有指定，它将从 cmdlet 中获取一个随机值。为了简化这篇文章，我们将继续使用值为`Example`的参数，所以所有提到的名称空间都以`Example`为根。

创建代理时，以下类型应该可用

*   `Example.DoSomethingRequest`
*   `Example.DosomethingResponse`

如果`DoSomethingRequest`或`DosomethingResponse`引用了 WSDL 中的其他复杂类型，那么这些类型也将在`Example`命名空间中创建。期望下面的代码片段足以初始化一个`Example.DoSomethingRequest`的实例，并使用它来调用`DoSomething`操作。

```
$request=[Example.DoSomethingRequest]::new()  $request.Property1="something"  $response=$proxy.DoSomething($request) 
```

所有类型都是具有随机名称的内存中程序集的一部分。为了帮助这篇文章，我们假设这个程序集被命名为`5wx5pbrx, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null`。

# 问题

与此同时，如果再次执行`$proxy=New-WebServiceProxy -Uri $uriDoSomething -Namespace Example`，那么问题开始浮现，因为新的执行创建了另一组相同类型但在不同汇编中的集合，例如`4qodbdsw, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null`。这两次调用将导致以下类型集:

| 祈祷 | 程序集限定名 |
| --- | --- |
| 第一 | `Example.DoSomethingRequest, 5wx5pbrx, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null` |
| 第一 | `Example.DosomethingResponse, 5wx5pbrx, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null` |
| 第二 | `Example.DoSomethingRequest, 4qodbdsw, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null` |
| 第二 | `Example.DosomethingResponse, 4qodbdsw, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null` |

对于那些不精通。NET 中，类型名不足以在运行时标识一个类型。相反，程序集的全名也被用来产生一个类型的唯一标识符，也称为**程序集限定名**。
{:。注意-提示}

`$proxy1`期望类型为`Example.DoSomethingRequest, 5wx5pbrx, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null`的实例作为参数，`$proxy2`期望类型为`Example.DoSomethingRequest, 4qodbdsw, Version=0.0.0.0, Culture=neutral, PublicKeyToken=null`。问题是，当执行像`[Example.DoSomethingRequest]::new()`这样的代码时，不清楚具体是哪个类型被实例化了，因为我们没有指定程序集限定名，这种情况在。NET 运行时。这可能导致来自`5wx5pbrx`汇编的类型与来自`4qodbdsw`汇编的类型一起使用的情况，从而导致没有意义的错误。

soap proxy PowerShell 模块有助于解决这个问题。

# SOAPProxy

| Cmdlet | 功能 |
| --- | --- |
| `Initialize-SOAPProxy` | 从 URI 初始化代理。代理可以被标记为默认，并且在没有明确提及 URI 的情况下被获取。 |
| `Get-SOAPProxy` | 查找并返回由`Initialize-SOAPProxy`初始化的代理。如果未指定 URI，则返回默认值。 |
| `Get-SOAPProxyInfo` | 列出 SOAP 代理上可用的每个操作，包括预期的*请求*类型和返回的*响应*类型。 |
| `New-SOAPProxyRequest` | 实例化代理中的操作所期望的请求对象。 |
| `Trace-SOAPProxy` | 帮助理解操作的请求和响应类型是如何构造的。这实际上是从 [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) 模块到`Trace-JSONPath`的重定向。 |

该模块的主要功能是包装`New-WebServiceProxy`并确保仅在必要时调用它。该功能由`Initialize-SOAPProxy`支持，在内部，cmdlet 在全局变量中维护一种代理缓存。当`Initialize-SOAPProxy`的参数匹配一个现有的代理时，那么`New-WebServiceProxy`不被执行，而使用来自存储器的那个。匹配现有代理的条件是`-Uri`。`-Uri`也被`Get-SOAPProxy` cmdlet 用来检索给定 URI 的代理。这不仅更快，还有助于避免与程序集混淆。

`Initialize-SOAPProxy`还可以利用全局变量来跟踪默认代理。当代理被默认创建时，模块中的所有 cmdlets 都可以使用，而无需指定`-URI`参数。这是一种非常常见的情况，因为脚本通常以一个端点为目标，并且可以被标记为默认。然后，脚本的其余部分假设有一个默认的代理，这使得代码更加清晰易读。这也增加了它的可维护性。

例如，假设一个脚本以变量`$uri1`和`$uri2`中指定的 SOAP 端点为目标。在下面的例子中，`$uri1`的代理被标记为默认代理。

```
# Initialize for $uri1  Initialize-SOAPProxy  -URI  $uri1  -AsDefault  # Initialize for $uri2  Initialize-SOAPProxy  -URI  $uri2 
```

为了检索`$uri1`的代理，我们可以调用暗示默认代理的`Get-Proxy`或者通过指定`-URI`参数。

```
 # Get default proxy for uri1  $proxy1=Get-SOAPProxy  # Get proxy for uri1  $proxy1=Get-SOAPProxy  -Uri  $uri1 
```

为了检索`$uri2`的代理，我们需要显式地指定`-URI`参数。

```
# Get proxy for uri2  $proxy2=Get-SOAPProxy  -URI  uri2 
```

对于其余的例子，我们将假设默认代理是这样创建的`Initialize-SOAPProxy -URI $uri -AsDefault`。

# 不支持 PowerShell 核心

随着 [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 模块依赖 [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) ，意味着不支持高于 5.1 的 PowerShell 版本。不幸的是， [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) 是 ASMX 时代的技术，由`System.Web.Services`程序集提供支持。所以移植到 PowerShell Core 似乎很不吉利，因为程序集需要移植到。这可能永远不会发生。ASMX 是旧技术，继任者是 WCF，但即使这样也不是 100%的支持。NET Core 和团队没有计划做得更好，基于他们最近的[路线图](https://devblogs.microsoft.com/dotnet/introducing-net-5/)公告。不管怎样，我在 PowerShell 存储库[上创建了另一个问题，请求 PowerShell](https://github.com/PowerShell/PowerShell/issues/9838) 提供 SOAP 支持。在我的[评论](https://github.com/PowerShell/PowerShell/issues/9838#issuecomment-504886982)中，很清楚为什么我们会在 PowerShell 中获得对 SOAP 的跨平台支持还有很多不确定性。

这个限制最不幸的副作用是依赖于 [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 模块的脚本不能在

*   带有 PowerShell 的 Azure 函数。
*   非 Windows 平台。

# 使用 soap 代理

[SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 模块提供了一些额外的 cmdlets 来帮助在脚本环境中使用代理。所有例子都假设默认代理是用`Initialize-SOAPProxy`初始化的。

`Get-SOAPProxyInfo`将列出操作以及它们的输入和输出类型。例如

```
C:>  Get-SOAPProxyInfo  Operation  RequestType  ResponseType  ---------  -----------  ------------  DoSomething  Example.DoSomethingRequest  Example.DoSomethingResponse 
```

为了创建`Example.DoSomethingRequest`的实例，模块提供了`New-SOAPProxyRequest`，它将通过指定操作名从代理创建一个实例。

```
New-SOAPProxyRequest  -Operation  DoSomething 
```

`Get-SOAPProxyInfo`和`New-SOAPProxyRequest`也可以使用在 [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 模块预览之外创建的 soap 代理。这是可能的，因为 [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 管理由 [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) 创建的代理。以下所有代码都是有效的，并且使用了正常创建的代理

```
$proxy=New-WebServiceProxy  -Uri  $uri  Get-SOAPProxyInfo  -Proxy  $proxy  $proxy|Get-SOAPProxyInfo  New-SOAPProxyRequest  -Operation  DoSomething  -Proxy  $proxy  $proxy|New-SOAPProxyRequest  -Operation  DoSomething 
```

# 调试和跟踪代理

请求和响应对象通常非常复杂，引用其他复合类型和数组。如果没有代码可见性(典型的脚本环境),这将变得非常困难，并且需要大量的试验和错误。 [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) 提供的功能将在另一篇关于 [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) 的帖子中讨论。 [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) 为`Trace-SOAPProxy`提供了两个选项:

*   一系列有助于理解数据契约关系的 JSON path like 表达式。
*   为每个可能的 [JSONPath](https://goessner.net/articles/JsonPath/) 表达式设置值的代码。作为开发人员，您可以保留您感兴趣的行，以形成有效的请求。

对于下面的例子，我们将使用来自 [Amadeus API](https://developers.amadeus.com/enterprise) 的`PNR_Retrieve`操作

`Trace-SOAPProxy -Proxy $doSomethingProxy -Operation PNR_Retrieve -Request`输出

```
settings.printer.identifierDetail.name="String"
settings.printer.identifierDetail.network="String"
settings.printer.office="String"
settings.printer.teletypeAddress="String"
settings.options[0]="String"
retrievalFacts.retrieve.type="String"
retrievalFacts.retrieve.service="String"
retrievalFacts.retrieve.tattoo="String"
retrievalFacts.retrieve.office="String"
retrievalFacts.retrieve.targetSystem="String"
retrievalFacts.retrieve.option1="String"
retrievalFacts.retrieve.option2="String"
retrievalFacts.reservationOrProfileIdentifier[0].companyId="String"
retrievalFacts.reservationOrProfileIdentifier[0].controlNumber="String"
retrievalFacts.reservationOrProfileIdentifier[0].controlType="String"
retrievalFacts.personalFacts.travellerInformation.traveller.surname="String"
retrievalFacts.personalFacts.travellerInformation.passenger.firstName="String"
retrievalFacts.personalFacts.productInformation.product.depDate="String"
retrievalFacts.personalFacts.productInformation.product.depTime="String"
retrievalFacts.personalFacts.productInformation.product.arrDate="String"
retrievalFacts.personalFacts.productInformation.boardpointDetail.cityCode="String"
retrievalFacts.personalFacts.productInformation.offpointDetail.cityCode="String"
retrievalFacts.personalFacts.productInformation.company.code="String"
retrievalFacts.personalFacts.productInformation.productDetails.identification="String"
retrievalFacts.personalFacts.productInformation.productDetails.subtype="String"
retrievalFacts.personalFacts.ticket.airline="String"
retrievalFacts.personalFacts.ticket.ticketNumber="String"
retrievalFacts.frequentFlyer.frequentTraveller.companyId="String"
retrievalFacts.frequentFlyer.frequentTraveller.membershipNumber="String"
retrievalFacts.accounting.account.number="String" 
```

`Trace-SOAPProxy -Proxy $doSomethingProxy -Operation PNR_Retrieve -Request -RenderCode`输出

```
$requestPNR_Retrieve=New-SOAPProxyRequest  -Operation  "PNR_Retrieve"  $requestPNR_Retrieve=$requestPNR_Retrieve  |  Set-JSONPath  -Path  "settings.printer.identifierDetail.name"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "settings.printer.identifierDetail.network"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "settings.printer.office"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "settings.printer.teletypeAddress"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "settings.options[0]"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.type"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.service"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.tattoo"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.office"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.targetSystem"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.option1"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.retrieve.option2"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.reservationOrProfileIdentifier[0].companyId"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.reservationOrProfileIdentifier[0].controlNumber"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.reservationOrProfileIdentifier[0].controlType"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.travellerInformation.traveller.surname"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.travellerInformation.passenger.firstName"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.product.depDate"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.product.depTime"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.product.arrDate"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.boardpointDetail.cityCode"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.offpointDetail.cityCode"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.company.code"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.productDetails.identification"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.productInformation.productDetails.subtype"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.ticket.airline"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.personalFacts.ticket.ticketNumber"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.frequentFlyer.frequentTraveller.companyId"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.frequentFlyer.frequentTraveller.membershipNumber"  -Value  "String"  -PassThru  |  Set-JSONPath  -Path  "retrievalFacts.accounting.account.number"  -Value  "String" 
```