# 清洁分析的实用方法

> 原文：<https://dev.to/davidwells/a-practical-approach-for-clean-analytics-3dhg>

> 最初发布于 [davidwells.io](https://davidwells.io/blog/clean-analytics)

收集干净的公司范围的指标是困难的。

当你的公司开发的每一个应用程序、网站和工具都由不同的开发者以不同的方式使用时，下游的影响可能会相当麻烦。

> 这是垃圾进，垃圾出的经典案例。

这个文档概述了一个组织的所有项目的度量收集的干净方法的提议。

这种方法在 less & Netlify 的许多数据科学、产品和营销活动中非常有效。

让我们投入进去吧！

## 为什么要标准化？

随着公司和产品的增长，关键是要有适当的工具来跟踪事物是如何发展的，以及相对于 KPI，事物是如何改进的。

清洁数据的第一步是围绕命名约定进行标准化，并确保使用正确的工具来实施这些约定。

从项目开始就标准化有助于确保数据是可靠的、可读的，并且将随着产品方向的变化而扩展。

这些分析的命名约定对于以下方面非常重要:

1.  以编程方式处理数据
2.  组织+在下游分析 UI 中搜索数据
3.  数据一致性和可靠性
4.  确保活动名称符合不同的产品线
5.  简化团队新成员的入职流程

## 如何？

为了实现上面列出的好处，有几个注意事项需要考虑。

1.  建立的模式必须能够适应多种产品/接触点
2.  这个模式必须能被人类和机器解析
3.  所有产品/接触点必须符合该模式
4.  验证+强制也相当重要。我们将在下面的实现部分讨论这个问题。

那么，好的命名约定是什么样的呢？

## 事件命名约定

在对“合理的”命名约定进行了大量研究后，我们发现了 from segment 的“对象-动作框架”的一个略微修改的版本，该版本来自他们的干净数据指南。

**图案**

`Context => Object => Action`

该格式回答了以下问题:

*   事件来自哪里？`Context`
*   事件会产生什么影响？`Object`
*   采取了什么行动？`Action`

这在各种情况下看起来如何的一些例子:

*   应用= >站点= >已部署
*   App = >函数= >已调用
*   站点= >文档= >评级
*   站点= >文档= >已搜索
*   CLI = >用户= >登录
*   API = >用户= >密码重置

事件起源的`context`一般很短，但仍然足以让人们理解它们起源于哪里。

`context`上下文的一些例子有`app` (web ui)、`site`(营销站点)、`cli`(命令行界面)、`api`(后端 api 服务)等。

这些将取决于您的应用程序/组织结构。

### 格式/语法

当然，语法取决于您。

在我们的例子中，所有事件都是这样格式化的，带有单词`camelCased`

```
productName:objectName_actionName 
```

以下是一些例子:

*   `site:newsletter_subscribed`
*   `app:site_deployed`
*   `cli:user_login`
*   `api:site_created`

上面的语法实现了几件事情。首先也是最重要的，它是人类可读的，人们很容易准确地说出一个事件起源于哪里。

例如，`app:user_emailChanged`发生在主应用程序中，影响了用户，并确切地告诉我他们做了什么(更改了他们的电子邮件)。

在上下文前面加上产品名称，也使得在下游营销工具中消化数据变得更加容易。

很容易过滤出所有源自你的网站、你的应用程序、命令行界面等的事件。这让产品和营销人员非常清楚哪些数据可用于激活和保留活动。

[![](img/fd7d12bfeaadb803c5a2587943730bbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iguzoGyB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/assets.davidwells.io/blog/clean-analytics-list.jpg)

### 约定俗成

以下是建议遵循的惯例:

*   以产品名称作为所有事件的前缀
*   所有的东西
*   用`:`分隔产品名称和对象/动作
*   用`_`将对象和动作分开
*   即`productName:objectName_actionName`
*   不要不必要地缩写
*   避免以编程方式创建事件名称
*   对于页面加载，使用“已浏览”，而不是“已访问”、“已加载”等。

## 实现

尽快优化这些收集点非常重要。改造杂乱无章的分析实施更具挑战性。这可能需要更多的跨团队协调和调查工作，以确保下游系统仍将按预期运行。

以下是让事物处于“更干净”状态的一些实用的后续步骤:

### 1。绘制出现有项目&的属性

首先，收集和绘制跨属性的对象和动作。存在哪些产品线？事情会如何发展？哪些重要的快乐途径和事件值得追踪？

### 2。决定命名约定

然后，找出对您的组织有意义的命名约定。

### 3。向组织传达变更

然后，与其他利益相关者讨论并就命名约定达成一致。

每个人都说“是的，我们会这样做”很好，但更好的是将命名约定验证直接嵌入代码。这将防止新人引发命名不当的事件，并污染下游的数据科学工作。可以把这想象成分析的“林挺”。

让坏数据无法通过将使每个人的生活变得更容易，因为他们试图用它来做事情。

### 4。弈&舰

如何执行这些惯例完全取决于你自己。

我建议在整个组织中使用标准库&利用一些现有的工具来简化这一过程。

[分析库](https://getanalytics.io/)通过其插件中间件架构简化了验证。

每个页面视图、自定义事件和识别调用都通过中间件链传递，该中间件链允许添加安全防护，以确保流入第三方工具的数据是干净的。

当应用程序代码调用`analytics.track()`时，数据流经中间件链，如果事件无效，您可以自动格式化它们以符合标准，或者警告开发人员遵守约定。

**跟踪事件流经此链**

[![](img/1f809483199b90a75d2e895d2400491b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Wz5M4uH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-2.amazonaws.com/assets.davidwells.io/blog/analytics-track-lifecycle.png)

打开[调试](https://getanalytics.io/debugging/)模式，你可以在浏览你的应用程序时观察事件的流程。

## 使用分析插件

在您跟踪事件的任何地方添加验证检查可能是一项**大型**任务。这就是为什么分析库是一个很好的抽象层来帮助在一个地方完成这项工作的原因之一。

我们可以简单地添加[事件验证插件](https://getanalytics.io/plugins/event-validation/)，而不是进入 20 个不同的文件并添加`if/else`语句。

在这篇文章中描述的命名惯例被编入插件中，并将`noOp`任何畸形的事件。

```
import Analytics from 'analytics'
import eventValidation from 'analytics-plugin-event-validation'
import customerIOPlugin from 'analytics-plugin-customerio'

const analytics = Analytics({
  app: 'awesome-sauce',
  plugins: [
    eventValidation({
      /* Context of where events are coming from */
      context: 'app',
      /* Allowed objects */
      objects: [
        'sites', // example app:sites_cdConfigured
        'user',  // example app:user_signup
        'widget' // example app:widget_created
      ],
      /* throw on validation errors if using in only dev env */
      // throwOnError: true
    }),
    customerIOPlugin({
      siteId: '123-xyz'
    }),
  ]
})

// Event names must now conform to this format:
analytics.track('app:sites_whatever')
analytics.track('app:user_action')
analytics.track('app:widget_deleted') 
```

## 自带约定

如果你不喜欢提议的事件语法`content:object_action`，你可以推出自己的分析插件，并带来自己的验证逻辑。

这里有一个例子:

```
import Analytics from 'analytics'
import googleAnalytics from 'analytics-plugin-ga'

/* Bring your own plugins */
const customValidationPlugin = {
  NAMESPACE: 'company-xyz-event-validation',
  trackStart: ({ payload, abort }) => {
    /* Your custom event validation logic */
    if (!payload.event.match(/^foo/)) {
      /* Abort the call or throw error in dev mode */
      return abort('Event name does not meet validation requirements')
    }
  }
}

const analytics = Analytics({
  app: 'app-name',
  plugins: [
    // Attach custom validation plugin
    customValidationPlugin,
    googleAnalytics({
      trackingId: 'UA-121991123',
    })
  ]
})

// valid event
analytics.track('foo button clicked')

// invalid event
analytics.track('blahblah bad event name')
// ^ no ops or throws depending on your validation logic in `trackStart` 
```

关于编写定制插件的更多信息，请参见[文档](https://getanalytics.io/plugins/writing-plugins/)

## 研究&链接

有大量很好的资源可以学习更多关于高级分析的知识。

有关其他分析信息，请参见以下资源:

*   [https://segment.com/academy/collecting-data/](https://segment.com/academy/collecting-data/)
*   [https://segment . com/academy/intro/how-to-create-a-tracking-plan/](https://segment.com/academy/intro/how-to-create-a-tracking-plan/)
*   [https://segment.com/blog/segment-tracking-plan/](https://segment.com/blog/segment-tracking-plan/)
*   [https://segment . com/academy/collecting-data/naming-conventions-for-clean-data/](https://segment.com/academy/collecting-data/naming-conventions-for-clean-data/)
*   [https://mattmazur . com/2015/12/12/analytics-event-naming-conventions/](https://mattmazur.com/2015/12/12/analytics-event-naming-conventions/)
*   [http://maddy Williams . com/6-things-to-avoid-when-creating-a-Google-analytics-event-naming-strategy/](http://maddywilliams.com/6-things-to-avoid-when-creating-a-google-analytics-event-naming-strategy/)
*   [http://www . seerinteractive . com/blog/event-tracking-naming-strategy/](http://www.seerinteractive.com/blog/event-tracking-naming-strategy/)
*   [https://www . optimize smart . com/event-tracking-guide-Google-analytics-simplified-version/](https://www.optimizesmart.com/event-tracking-guide-google-analytics-simplified-version/)
*   [https://support.google.com/analytics/answer/1033068?hl=en](https://support.google.com/analytics/answer/1033068?hl=en)
*   [https://segment . com/academy/collecting-data/naming-conventions-for-clean-data/](https://segment.com/academy/collecting-data/naming-conventions-for-clean-data/)
*   [http://www . krista seiden . com/events-best-practices-for-hierarchy-and-naming-conventions/](http://www.kristaseiden.com/events-best-practices-for-hierarchies-and-naming-conventions/)
*   [http://drakondigital . com/blog/Google-analytics-event-tracking-naming-strategy/](http://drakondigital.com/blog/google-analytics-event-tracking-naming-strategy/)