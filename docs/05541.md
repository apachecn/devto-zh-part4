# LWC-将数据传递给子组件

> 原文：<https://dev.to/brettmn/lwc-passing-data-to-a-child-component-4oh7>

[https://www.youtube.com/embed/ia1KJ9IFS2Y](https://www.youtube.com/embed/ia1KJ9IFS2Y)
你好，我是布雷特与 WIPDeveloper.com。上次，我们向一个定制的 lightning web 组件添加了一个子组件。这一次，让我们尝试将一些信息传递给子组件。

这将看起来非常类似于使用 Salesforce 提供的 lightning web 组件，因为从父母的角度来看，语法实际上是相同的，而从孩子的角度来看，我们必须做一些准备。

因此，在屏幕的下半部分有一个子组件。我们要做的是转到 JavaScript，我们要创建一个属性 current `ourProperty`。现在我们可以从父节点绑定数据到它，我们必须使用 API 装饰器。对于 API 装饰器，我们必须从 LWC 中导入它们。所以我们把它导入 online five，用它来装饰`ourProperty`。现在我们拯救了我们的教会，因为我们没有在任何市场上使用任何改变。

所以让我们把它添加到我们的标记中，刷新它，应该只有一个破折号，因为我们实际上没有设置我们的属性。

现在，让我们在屏幕的右上角或右上角添加父组件，我们将复制子组件，我们将添加`our-property`，我们将为它赋值。

保存它，我们应该在这里得到两个组件，一个表示子从父中删除，另一个表示子现在删除。

因为它是用 API 装饰器装饰的，我们并不试图通过其中一个目标来公开它。因为即使我们试图通过目标来暴露它，我也不知道在这种情况下哪一个会起作用。因此，要设置默认值，我们将使用在 JavaScript 文件中分配一个适当的赋值。这可能会导致如果您使用 lightning 调试模式，可能会看到一些关于设置属性的警告。但这是我现在所知道的代替默认值的方法。

现在设置默认值为 from child，我们可以刷新页面。我们应该有两个组件子组件父组件子组件子组件，这就是我们如何将数据从父组件传递到子组件，以及如何将属性设置为默认值。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[LWC——传递数据给子组件](https://wipdeveloper.com/lwc-passing-data-to-a-child-component/)最早出现在[WIPDeveloper.com](https://wipdeveloper.com)上。