# React 团队在做什么？(第一部分):暂停组件

> 原文：<https://dev.to/eddiecooro/what-react-team-is-working-on-part-one-suspenselist-component-1k9b>

大约一个月前，我在 React 存储库的主分支上看到了一些有趣的提交。
*React 团队的首席开发人员塞巴斯蒂安·马克贝吉*正在开发一个叫做`SuspenseList`的东西:一个应该指导和协调其他几个`Suspense`组件的组件。

[<悬疑>演示](https://eddie-cooro.github.io/suspense-list-together-demo/)

通过第一次提交，`SuspenseList`组件的基本功能就完成了，这给了我们一个这样的 API:
[![](img/8fcea51e5a27158e4cb67d92bb2c352e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcITgWxR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r4rfvggp0hxgflu26xon.png)

让我们假设在这个例子中有两个组件:第一个组件**和第二个组件**。
这些组件在第一次渲染时会抛出一个承诺，然后会被暂停。****

 **因此，在第一次渲染之后，我们将在屏幕上看到“`loading first`”和“`loading second`”跨度。
现在 **FirstSuspendingComponent** 解析它的承诺并试图显示它的内容，但是它不能。这是因为**seconds suspending component**仍然处于挂起状态，并且它们的第一个`SuspenseList`父组件的“revealOrder”属性等于“`together`
因此，**第一暂停组件**仍然显示其回退(`loading first`跨度)并等待**第二暂停组件**完成其工作。之后，它们将同时显示。

[https://github.com/facebook/react/pull/15902](https://github.com/facebook/react/pull/15902)**