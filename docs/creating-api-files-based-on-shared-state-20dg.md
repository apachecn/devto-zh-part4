# 基于共享状态创建 API 文件

> 原文：<https://dev.to/francosirena/creating-api-files-based-on-shared-state-20dg>

在过去的一周里，我遇到了一个有趣的挑战，采用了一些常规的方法，称为 API，它们依赖于状态值来发出请求。这些方法过去只是直接访问 store observable 并执行`getState()`来访问 redux 的最新状态(但是，它们会吗？).

因为我经历了通过 store observable 删除所有对状态的直接访问的传奇，所以我必须找到一种方法来处理这样的方法。意识到这些文件有意义有点奇怪，因为:

*   如果你完全依赖 redux，那么，请求的地方应该是中间件，在我们的例子中，是 sagas
*   如果你把你的请求隔离在那里，没有理由再有一个叫做 API 的抽象；
*   如果这些调用依赖于状态，那么应该使用中间件。

但是，就像 React evolved 和 Redux 一样，有一些状态片段，特别是在像我们这样的大型应用程序中，不应该存储在全局状态中，而可以，甚至必须存储在本地状态中的短期内存中。因此，为了将 API 集中在一个地方，我们开发了这些 API 文件。

所以，我必须想出一个解决方案来删除直接访问，这可以用在传奇和组件，棘手。

然后我突然想到，S E L E C T O R S:)

有了它们，我可以简单地使用 mapStateToProps 访问组件中的 API，并在 sagas 中进行产量选择。

因此，我创建了所谓的`APIgenerator`方法，它将接受状态作为输入，并使用 createSelector 进行一些记忆，读取 API 所依赖的状态的所有部分，并在最终方法中返回一个包含要在消费者内部调用的方法的对象，如下所示:

[https://gist . github . com/Franco sirina/94f 956 b 9 C4 a2 E1 c 380d 2919 B3 c 01 c 622](https://gist.github.com/FrancoSirena/94f956b9c4a2e1c380d2919b3c01c622)

:)

OBS:如果您的方法依赖于不同的状态块，您可以公开任意多的选择器，让最终的方法返回另一个要在组件中调用的函数，比如，`(stateChunk) => () => fecth`。

我倾向于将我的大部分状态保存在一个上下文中，甚至保存在组件本身中，但是，在很多情况下，我们可能需要将它保存在 redux 中，即使 react APIs 越来越好，redux 也是一个强大的 pkg。

所以，不要毁灭 redux，小心使用它，最重要的是，知道如何使用它，如何利用它