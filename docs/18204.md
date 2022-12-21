# 使用功能标志🏳️的最佳实践

> 原文：<https://dev.to/theodesp/best-practices-for-using-feature-flags-in4>

[**阅读原文**](https://codethat.today/article/best-practices-feature-flags/)

当谈到开发软件时，尤其是在企业级，有大量隐藏的风险因素。为了解决这个问题，软件架构师采用各种技术和模式来确保客户面临的一切都不会遭受灾难。

他们使用的技术之一是**特征切换、特征标志或特征开关**。一般来说，这些语句放在代码库中的战略位置，允许系统以不同的方式运行——而无需更改或部署任何东西。这些信息通常是根据需要从外部 API 请求或推送的，用于暴露或隐藏应用程序的功能。

功能标志在 CI 方案中起着关键的作用，在 CI 方案中，功能不断地被部署，但不一定“发布”到产品中。在本文的剩余部分，我们将讨论在实践中应用它们的最佳方式。

## 在生产中使用特征标志

在其非常简单的形式中，特性标志只是一个条件检查，它根据提供的值切换代码。这里有一个例子:

```
 if featureFlagService.isEnabled("PUSH-NOTIFICATIONS") {
  fmt.Println(“Push notifications are enabled”)
} else {
  fmt.Println(“Push notifications are not enabled”)
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，这比看起来要复杂得多。以下是使用功能标志时需要考虑的六个最重要的事情:

**1。使用依赖反转**。
开发软件时一个非常重要的技术(在你的应用程序中添加功能切换时很有用)是应用依赖倒置原则。不是任意导入 featureFlag 对象并在不同的地方使用它，而是在构造函数中提供它，这样它就可以在私有属性中被赋值。请看下面的例子:

```
 // featureFlagService method
launchMissileToTarget(payload) {
   if (this.featureFlagService.isEnabled(“EXPERIMENTAL_TECHNOLOGY”) {
       // guide missile with advanced targeting algorithms
    } else {
       // guide missile with common targeting algorithms
    }
}
class ImportantDecisions
  constructor(featureFlagService) {
    this.featureFlagService = featureFlagService;
  }
  launchMissileToTarget(payload) {
    // do something with payload param
    this.featureFlagService.lanuchMissileToTarget(payload);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

对标志处理的控制已经从`ImportantDecisions`类转移到`featureFlagService`类。这样，当您将不同的配置传递给调用者并验证结果时，也可以使您的代码更容易测试。

**2。在您的功能包中添加元数据。**
对于您部署的软件的每个版本，可能有一个或多个特性需要标记为打开或关闭。有些功能可以添加，有些功能可以删除。为了跟踪什么在哪里，拥有与每个功能块相关的附加元数据是很重要的。例如，您可能需要添加标签或标注来标记具有额外信息的特定要素。当您通过管理控制面板创建或更新标志时，这很有用，您可以在管理控制面板中查看不同组的概览。当您展开标志时，您可以看到应用了哪些标签。如果您有多个标签，您可以查看不同的目标是否启用了某个功能。这也将提供对特性管理的细粒度控制。

**3。让他们数据驱动，而不是知识驱动。**
如果你能控制数据流，而不是控制 if 和 else 语句在代码库中的位置，会不会更好？在这种情况下，您可以使您的应用程序对更改反应更快，而无需根据功能状态实际添加或删除代码部分。例如，您可以让客户端订阅基于唯一命名空间的事件，并在订阅处于活动状态时接收数据。一个实际的应用是交易平台，在这里客户可以接收基于交易符号的事件。然后，在您的仪表板中，您可以控制这些符号是否可以通过轻触按钮来订阅。如果您禁用其中一些，则客户端将不会收到更新，因此不会在屏幕上显示任何内容。如果您设计的应用程序对事件的反应更快(通过数据驱动的开关)，那么控制是反向的，更容易保护。

**4。使用功能标志管理仪表板。**
你不应该忽视运营团队在提供增量功能部署方面的作用，或者在出现问题时按下紧急按钮。拥有一个好的、易于使用的特性标志管理仪表板将会非常有帮助，特别是当应用程序增长，并且有许多利益相关者需要特性的时候。您可以从一个简单的开关复选框页面开始，但是稍后，如果有许多不同状态的功能，并且您需要正确地监控它们，您将会遇到麻烦。因此，为了适应这些额外因素，投资开发(或从货架上抓取)更复杂的解决方案是值得的。

**5。确保当标志改变时，整个系统都知道它。**
开启或关闭某些功能的一个重要含义是，它们会实时改变正在运行的系统的行为。为了获得更好的可见性和警觉性，您可以在创建/更新或打开/关闭功能标志时发送通知。一个好的解决方案是使用聊天平台，这样感兴趣的人可以通过公司聊天室这样的公共媒介接收信息。这最终可以作为跨部门团队的良好沟通模式，以便相互协调发布。

**6。清理未使用的标志**
特性标志的确增加了技术债务和应用程序整体的复杂性。他们容易犯错误，因为一些标志可能会留在代码库中并被遗忘。为了更好的可读性和可发现性，建议在标志前加上前缀，或者更好地加上注释。例如，在 Python 这样的语言中，您可以创建一个@feature decorator，它带有一个可选的状态参数，可以在工作区中轻松地搜索到。这也将有助于监控，因为您可以为每个功能使用情况附加报告工具。稍后，您可以将它们标记为@deprecated-feature，这样您就可以通知任何阅读代码的开发人员关于特性生命周期的状态。

## 帮助您入门的框架

有一些好的框架可以帮助你实现特性标签。[launch darky](https://launchdarkly.com/)是一个商业特性标志即服务框架，有许多客户端库。 [Togglz](https://www.togglz.org/) 是一个面向 Java 的开源特性标志框架。 [Rollout](https://github.com/fetlife/rollout) 也是 Ruby 的开源特征标志框架。

特性标志是推出功能的好方法，但在频繁创建版本和新功能时，它们也是保持应用程序质量的好方法。