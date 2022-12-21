# 反应上下文提供者问题

> 原文：<https://dev.to/mrmurphy/react-context-provider-gotcha-lmf>

我在 React 本机应用程序中使用了一个全局状态存储来存储需要在多个屏幕上使用的数据。我用了一个库来做这个:
[https://github.com/jamiebuilds/unstated-next](https://github.com/jamiebuilds/unstated-next)，但基本上只是 React 的`useState`和`useContext`的组合。

问题是，屏幕 B 将数据添加到全局存储中，然后导航回屏幕 A。但是屏幕 A 永远不会有更新的状态。这几乎就像有两个独立的商店，一个页面一个🧐.

当我想起 React 的上下文“Provider”确实会为树中它自己下面的任何子节点创建一个新的状态副本时，我才开始考虑这个问题。我把每一页都包装在它自己的提供程序中！一旦我将整个应用程序包装在一个提供者组件中，两个页面再次共享相同的状态，页面 B 触发的更新立即反映在页面 a 上。

# 反应#状态#上下文