# 关于 JSONPath、SOAP 和 Amadeus 与 PowerShell 的帖子介绍

> 原文：<https://dev.to/sarafian/introduction-to-posts-about-jsonpath-soap-and-amadeus-with-powershell-4i3k>

# 航空业

航空公司和一般的旅游业在很大程度上依赖 SOAP 来集成系统。旅游和运输行业的所有公司都基于 Amadeus、Sabre 等成熟的平台开展业务。这些站台是在机场办理登机手续的柜台后面，或者在登机口或许多其他你作为乘客看不到的地方。随着航空业被国际航空运输协会(IATA)高度标准化，航空公司没有太多的区分空间，这就是为什么我们，作为乘客，以一致的方式体验航空旅行的过程，而不管是哪家航空公司。这些平台存在了很长时间，它们不容易改变。围绕他们的 API 和标准化实现了大量的业务流。这意味着技术也不容易改变。你可以认为它们是遗留系统，但它们仍然在正常运行。

例如，大多数航空公司甚至不拥有预订系统。如果你真的回顾一下历史，那么当世界其他地方发现了软件即服务的概念时，旅游行业早在 70 年代就已经在做 SAAS 了。

我特别参与的那家航空公司使用 [Amadeus](https://www.amadeus.com) (又名 1A)作为他们的主要驾驶员。 [Amadeus 的 API](https://developers.amadeus.com/enterprise) 是一个有状态 SOAP API，更糟糕的是，它在使用的数据契约方面非常复杂。

请**注意**我不是 [Amadeus](https://www.amadeus.com) 的专家，我不会讨论分析 [Amadeus API](https://developers.amadeus.com/enterprise) 的功能。另外，这个帖子不仅仅是关于阿玛迪斯的。每个人都在寻找一条通向 PowerShell 和 SOAP 的更简单的道路。
{:。注意-提示}

# UI 驱动流程

当我的旅程开始时，我注意到人们使用像 [SOAPUI](https://www.soapui.org/) 这样的 UI 工具作为各种过程的开发工具，例如:

*   API 的可视化器像其他人一样使用 [Postman](https://www.getpostman.com/) 。
*   概念证明(POC)流程。
*   某种工作流，其中测试用例与 groovy 代码相结合，以 csv 作为输入，进行一些处理，然后导出到 csv。

主要的迹象是，你可以在许多人的屏幕上认出它的 UI，包括开发人员、架构师和产品所有者。我还注意到，那些抱怨 SOAPUI 的人寻找并找到了一个具有相似特性但 UI 更现代的替代产品。无论如何，方法和用法保持不变，同时继承了一些上述过程的相同问题。

# 为什么选择 PowerShell

以我的自动化背景，这感觉非常奇怪和麻烦。我总是使用 UI 工具作为视觉加速器来帮助我建立一个基本的理解。但是当事情需要组合或者预计会重复的时候，那么代码就是我的首选。

事实上， [Amadeus 的 API](https://developers.amadeus.com/enterprise) 是自动有状态的，这意味着需要一些定制代码从一个调用中提取会话变量，然后将它们传递给下一个调用。缺省情况下，SOAPUI 不能做到这一点，因此在捕获会话的第一个请求和随后需要使用它的请求之间需要 Groovy 代码。完成会话时需要格外小心。回顾我在 PowerShell 中使用 soap 的经历，我记得曾经有一个来自 [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) 的 SOAP 客户端，然后在不同的调用中自动管理标题，就像浏览器管理 cookies 一样。

所以，我满怀希望地开始调查这件事。

# 天堂里的烦恼

不幸的是，PowerShell 的事情并不容易，有很多复杂的问题。这种复杂性并不容易解决，我可以理解为什么有人会跳过这种方法，而快速退回到 UI 工具，特别是当其他人都在做同样的事情时。但是，通常对于软件来说，大多数人所做的并不是最好的解决方案，创新需要避免既定的方法。

我遇到的问题如下:

*   [New-WebServiceProxy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/new-webserviceproxy?view=powershell-5.1) 相关问题。
    *   它有局限性。这是因为主要部件是`System.Web.Services`组件，它曾经是 WCF 前身 ASMX 技术的驱动程序。对于这种特殊情况，预期的报头在 WSDL 中不被识别和理解，并且在创建的代理上不可用。这不可避免地会导致与坏标题相关的错误。
    *   它在 PowerShell 6/Core 范围内不可用，主要是因为 SOAP 没有得到。网核心团队。请记住,`System.Web.Services`程序集是关于 ASMX 的，这是一项过时的技术，很难证明其移植到。网芯。
    *   生成的接口和数据契约被编译到内存中。大多数人把这理解为当前的 PowerShell 会话或 windows。虽然有一些预防性代码，但实际情况是，cmdlet 的多次调用会使来自不同程序集的多个同名类型的会话混乱。这通常需要重启 PowerShell 会话，这在开发解决方案时非常烦人。
*   Amadeus 的 API 的操作所引用的数据契约非常复杂，很难从脚本语言中理解。唯一好的可视化是 XML 片段，但是 XML 没有揭示 WSDL 中描述的类型和关系，也没有驱动代码生成。
*   必须使用 cmdlet 生成的数据协定类型，因为它们隐式包含驱动 XML 序列化的注释。这必须非常具体，因为这个过程产生非常严格的 SOAP 信封，这被认为是 SOAP 的优势之一。任何偏离生成类型和使用动态对象的尝试都不会序列化为预期的 XML。事实上，变量甚至不会被接受为操作的参数，因为类型不匹配。

这些问题是可以管理的，但是 Amadeus 的 API 把它们扩大到了一定程度，揭示了使用脚本语言时 SOAP 的弱点。

让我们看一个例子，当使用 Amadeus 的 API 的 T2 数据结构时会遇到一些问题。下面是一个用于输入`PNR_Retrieve`操作的 XML 片段的例子。

```
<retrievalFacts>
  <retrieve>
    <type>2</type>
  </retrieve>
  <reservationOrProfileIdentifier>
    <reservation>
      <companyId>SN</companyId>
      <controlNumber>MTHOH2</controlNumber>
    </reservation>
  </reservationOrProfileIdentifier>
</retrievalFacts> 
```

基于这个 XML，人们会认为像`retrievalFacts.reservationOrProfileIdentifier.reservation.companyId`这样的路径是有意义的，但实际上它是`retrievalFacts.reservationOrProfileIdentifier.companyId`。当使用具有强大 IDE 的类型化语言进行开发时，这相对容易发现和连接这些点，但是在 PowerShell 中，关于属性的错误开始出现。

请记住，没有生成可用于结构查询的代码文件。尝试建立类似数据结构的唯一方法是使用`GetType()`和`Get-Member`并开始深入研究。但是这是一个非常繁琐和困难的过程。任何有 C#背景的人可能会在这一点上放弃，转而使用 C#项目并生成受尊敬的服务引用。但是我想让它保持动态，不是因为这是脚本语言的本质，而是因为我在 Amadeus 的 API 中注意到了一些东西。他们的 API 真的很大，而且操作被组织在他们称之为的不同的功能区域中。此外，他们不会为每个客户提供所有的操作。事实上，他们所做的是，他们要求候选消费者请求他感兴趣的操作，并且他们只使用这些操作组成一个特定的端点。他们通过某种 API 网关来做到这一点。例如，我会从`PNR`、`Security`和`Inv`的功能区向 [Amadeus](https://www.amadeus.com) 请求以下操作:

*   PNR _ 检索
*   Inv_AdvancedGetFlightData
*   安全性 _ 注销

当使用代码生成方法(例如 Visual Studio)时，此特定代码仅适用于给我的这个特定端点。这意味着，任何基于这种静态方法的解决方案都是非常特定的，不适合像 PowerShell 这样的动态脚本语言。即使有人会构建一个二进制模块，这个模块也会非常具体，只为我的特定目的服务，最多充当其他端点的代码模板。

更糟糕的是，我的航空公司通过我们自己的名为 [Sentinet](https://www.nevatech.com/sentinet) 的 API 网关添加了第二层 SOAP 组合。使用 Sentinet，我们可以将自定义 Amadeus 端点和其他内部开发的 SOAP 端点所提供的操作组合在一起。这种组合对 WSDL 文件的改变如此之大，以至于静态代码生成的代理无法对抗 [Amadeus](https://developers.amadeus.com/enterprise) 端点和来自 out [Sentinet](https://www.nevatech.com/sentinet) 的端点。

静态代码生成肯定不是解决方案。

环顾四周，我很快意识到没有任何可用的东西，我的选择变成了:

*   接受这种情况并退回到 SOAPUI
*   编写生成 SOAP 信封并执行 POST 的代码。这将是一个非常肮脏的解决方案，从一开始就感觉非常不吸引人。
*   想出 PowerShell 的解决方案。

# 我的目标

我有点固执，不容易接受用代码做不到的事情，所以我很自然地选择在 PowerShell 中解决这个问题，并与社区分享。基于已经面临的问题和挑战，我为解决方案设定了以下目标:

1.  使处理请求和响应类型的操作变得更加容易。任何脚本语言最好的方面之一是它能够利用动态对象，并允许像`$var.A.B.C.D="a value"`这样的表达式。在 PowerShell 中设置值并不完全是这样，但是如果这个过程可以改善 PowerShell 的体验，那就更好了。
2.  保护会话不受`New-WebServiceProxy` cmdlet 为同一端点生成的多个程序集的影响。
3.  包装 [Amadeus](https://developers.amadeus.com/enterprise) 会话状态的生命周期管理，使代码更简单。**这实际上是阿玛迪斯**的唯一目标。我设定这个目标是因为我想要总体上尽可能干净和简单的代码。如果要求开发人员不断地改变会话值，从而破坏了代码体验，那将是一种耻辱。
4.  为`New-WebServiceProxy`缺失的表头寻找解决方案。

在接下来的文章中，我将针对每个目标单独讨论解决方案。不幸的是，唯一一个我还没有解决的目标，就是最后一个。我的解决方法是利用 [Sentinet](https://www.nevatech.com/sentinet) 的(API Gateway)处理管道在必要时注入缺失的头。我认为这个问题无法解决，因为`New-WebServiceProxy`所依赖的技术太老了，而且 cmdlet 创建的代理没有提供太多的灵活性来进行定制的头注入。另一个目前没有解决的问题是缺少对 PowerShell Core 的支持。这使得解决方案特定于 Windows 平台。目前唯一的可能性是提出另一个问题，[向 PowerShell](https://github.com/PowerShell/PowerShell/issues/9838) 或请求 SOAP 支持。网芯。在这篇[评论](https://github.com/PowerShell/PowerShell/issues/9838#issuecomment-504886982)中，我们可以清楚地看到，为什么在 PowerShell 中我们会获得对 SOAP 的跨平台支持还有很多不确定性。

# 1ASOAP 储存库

我对上述每个目标的解决方案已经可以在[1 soap](https://github.com/Sarafian/1ASOAP/)库中找到。[1 soap](https://github.com/Sarafian/1ASOAP/)提供了 3 个独立的 PowerShell 模块，目前驻留在这个单一的存储库中。目前没有一个模块发布到 [PowerShell gallery](https://www.powershellgallery.com/) 上，但是它们确实彼此独立工作。

下表提供了每个模块的摘要以及更详细文章的链接(如果已经发布)。

| 组件 | 范围 | 描述 | 邮政 |
| --- | --- | --- | --- |
| [SOAPProxy](https://github.com/Sarafian/1ASOAP/tree/master/Source/SOAPProxy) | 一般 | 用`New-WebServiceProxy`初始化和管理 SOAP 代理。建立默认代理的概念。简化给定操作的请求对象的创建。 | [PowerShell 中改进的 SOAP 代理管理](https://sarafian.github.io/amadeus/2019/06/28/powershell-soap-proxy-new-webserviceproxy.html) |
| [JSONPath](https://github.com/Sarafian/1ASOAP/tree/master/Source/JSONPath) | 一般 | 执行`Set`和`Find`操作时，用`A.B.C`等表达式提供 [JSONPath](https://goessner.net/articles/JsonPath/) 方法。`Trace`功能是作为一个大加速器开发的，有助于理解结构和接收的数据，并使开发人员和调试体验更加顺畅。 | [PowerShell 中复合类型的 JSONPath，带有额外的调试工具](https://sarafian.github.io/2019/07/16/jsonpath-powershell-extra.html) |
| 1 soap | [Amadeus API](https://developers.amadeus.com/enterprise) | 对 Amadeus API 状态管理的抽象。**不包含任何操作的细节，因此适用于任何组合端点。** | 尚未发布 |