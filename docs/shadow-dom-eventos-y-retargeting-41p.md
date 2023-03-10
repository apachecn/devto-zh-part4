# 暗影 DOM: eventos，目标 y 冒泡。

> 原文：<https://dev.to/gugadev/shadow-dom-eventos-y-retargeting-41p>

∞为了更好地理解本文，有必要在 JavaScript 中理解‘t0’冒泡〖t1〗的意思。

* * *

影子礼物背后的想法是包装。就目标而言，与某些设计模式和/或抽象技术并无多大区别。

当我们谈论封装时，我们通常指的是我们希望保留在安全环境中但无法在安全环境之外访问的数据和信息。Shadow DOM 封装资料:**DOM tree、样式和 JavaScript 程式码，但也封装另一件事:**事件**。**

## 影子礼物中的事件

Shadow DOM 内发生的事件只有在 shadow DOM 内才有其生命周期。也就是说，如果影子 DOM 内的按钮触发“点击”事件，效果*冒泡*将贯穿 DOM 树钻至`host`，即*自定义元素*。一旦到达那里，就无法向外前进(光天赋)。也就是说，`host` **是事件的屋顶**。但是，我们得到了一个原始事件的“版本”，省略了诸如“T8”事件的起源等重要部分。

让我们来看看它是如何与一个例子一起工作的。在下面的示例中，我们有一个元素`fancy-button`，它是一个*自定义元素*，其中包含一个带有某些样式的按钮。如果在此元素上添加事件`click`的侦听器，并得到*目标*，我们将得到该元素本身。

测试并打开 codesandbox 控制台以查看结果:

[https://codesandbox.io/embed/shadow-dom-events-vz4gh](https://codesandbox.io/embed/shadow-dom-events-vz4gh)

在这里我们可以清楚地看到，活动的‘t0’冒泡’是作为主机本身的‘屋顶’；因此，**光天赋**完全没有意识到由此产生的事件。

但是，当我们需要访问‘t0’目标真实时会发生吗？

## [T3】🚀冒泡，`composed` y `composedPath`:](#bubbling-raw-composed-endraw-y-raw-composedpath-endraw-)

一些事件(实现接口[事件](https://developer.mozilla.org/en-US/docs/Web/API/Event)具有一个名为 [`composed`](https://developer.mozilla.org/en-US/docs/Web/API/Event/composed) 的布尔属性*表示即使在影子 DOM 之外，事件的整个冒泡字符串是否也可以在事件有效负载中访问。*

> 由 UI 元素(单击、粘贴、更改等)触发的所有事件。)默认情况下，它们在“`true`”中具有此属性。

当此属性位于`true`时，阵列将包含事件所环绕的所有元素。为了访问该阵列，我们采用 [`composedPath`](https://developer.mozilla.org/en-US/docs/Web/API/Event/composedPath) 方法。

让我们看一下前面的相同示例，但这一次，访问数组中的第一个元素，该元素将成为引发事件的第一个元素。我们将关闭它 1.5 秒，然后再打开它。

[https://codesandbox.io/embed/shadow-dom-events-composed-pnpvo](https://codesandbox.io/embed/shadow-dom-events-composed-pnpvo)

* * *

我希望你们已经发现本教程很有趣，更重要的是，你们已经学到了一些新的东西。如果你喜欢，别忘了分享

下次见！