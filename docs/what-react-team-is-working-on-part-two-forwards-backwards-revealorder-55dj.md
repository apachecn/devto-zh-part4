# React 团队在做什么？(第二部分-向前和向后显示顺序)

> 原文：<https://dev.to/eddiecooro/what-react-team-is-working-on-part-two-forwards-backwards-revealorder-55dj>

好吧。合并此提交后的几天:

[![eddiecooro image](img/65aa4e432077b23f0bf2efa74c1b8234.png)](/eddiecooro) [## React 团队在做什么？(第一部分):暂停组件

### 埃迪 Jul 22 ' 19 分钟阅读

#react #suspenselist #reactcore](/eddiecooro/what-react-team-is-working-on-part-one-suspenselist-component-1k9b)
Sebastian opened another pull request to the master branch. In this pull request, He added two more options for the "revealOrder" property.

[工作演示](https://eddie-cooro.github.io/suspense-list-forwards-demo/)

# 1。向前

此选项使子悬念组件按照从第一个到最后一个的顺序显示其内容。
例如，考虑上一篇文章中的相同代码；但是这次将“revealOrder”属性设置为“forwards”:

[![](img/bfa4bd9533b91022f5788d654c27d225.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---3QCywOq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qu33xhvcywbmqd84kg96.png)

现在，如果 FirstSuspendingComponent 首先解析，它将立即显示其内容。但是，如果 SecondSuspendingComponent 在 FirstSuspendingComponent 之前解析，它将等待 FirstSuspendingComponent 解析。之后，它们将同时显示各自的内容。

# 2。倒

“向后”实际上是“向前”的反义词。在显示其内容之前，每个悬念组件将等待在它之后出现的其他悬念解决。

PR:

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 添加前进和后退选项到悬疑列表 #15918](https://github.com/facebook/react/pull/15918) 

[![sebmarkbage avatar](img/fb906bbb15ed392f34142bb09cebe60a.png)](https://github.com/sebmarkbage) **[sebmarkbage](https://github.com/sebmarkbage)** posted on [<time datetime="2019-06-19T02:15:07Z">Jun 19, 2019</time>](https://github.com/facebook/react/pull/15918)

建立在#15902 之上

我对这个实现还不是很满意，但是它看起来很有效。我还需要更多的测试。

所以把它放出来作为早期回顾。

[View on GitHub](https://github.com/facebook/react/pull/15918)