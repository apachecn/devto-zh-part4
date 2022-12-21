# 凹板构图

> 原文：<https://dev.to/matsp/composition-in-concave-3pea>

这是我的[博客](https://www.matspfeiffer.dev/blog/2019-06-19_composition-in-concave.html)的交叉帖子。

[凹](https://github.com/concave-org/concave)中组件的组成不同于其他基于组件的框架或编译器。在 concave 中，你不需要构建整个组件，你只需要构建渲染函数，它将返回 HTML。

## 举例

我创建了一个简单的 Todo 应用程序，向您展示一些代码。

[https://codesandbox.io/embed/e5tmc](https://codesandbox.io/embed/e5tmc)

导入[列表](https://github.com/concave-org/codesandbox-composition/blob/master/src/renderfns/list.js)和[列表项](https://github.com/concave-org/codesandbox-composition/blob/master/src/renderfns/listItem.js)都只是返回 HTML 的函数。数组将由 [html](https://github.com/concave-org/concave#html) 函数自动连接，以减少模板中的代码。

## 正反两面

我创建 concave 是为了有一个简单的框架，它不会对我隐藏复杂的任务。我想要的是简单易懂的代码，不需要“魔法”。有了模板组合，我可以专注于如何转换数据并显示给我的用户。对于其他框架，你有能力拥有子组件并与它们通信，但是随着你引入更多的组件，这变得更加复杂。

另一方面，很难隔离组件中的公共代码，例如数据的延迟加载。有了像 React 这样的流行框架，这当然更容易。这不是不可能的——在我的[作品集](https://www.matspfeiffer.dev)中就是这样做的——但是需要更多的经验来弄清楚事情是如何在凹环境中运作的。最后它只是 JavaScript！

## 结论

当你熟悉这个概念时，concave 中的组件与其他框架略有不同。在 concave 中，组件组成渲染函数，并使用道具和状态进行渲染。在我看来，渲染函数的可重用性比整个组件好得多，因为我不需要考虑这个组件的内部。如果您需要组件到组件的通信，您可以分派一个动作，并用另一个组件中的 [actionReducer](https://github.com/concave-org/concave#component) 对其做出反应。

## 链接

*   [凹面](https://github.com/concave-org/concave)