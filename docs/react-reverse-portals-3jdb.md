# 反向门户反应

> 原文：<https://dev.to/pimterry/react-reverse-portals-3jdb>

嘿戴夫托！

我刚刚为 React 建立了一个新的库:[https://github.com/httptoolkit/react-reverse-portal](https://github.com/httptoolkit/react-reverse-portal)

它允许您在一个地方定义、安装和呈现一个组件，然后将它放在 React & DOM 树中完全不同的地方，稍后在树中上下移动它，甚至将它完全拉出页面并放回原处，所有这些都不需要重新安装或重新呈现。

它类似于内置的 React 门户(并且它在内部使用它们)，但实际上正好相反。不是在树中定义一些内容，然后通过原始 DOM 把它推到其他地方，而是在其他地方定义内容，然后把它放到 React 树中您希望它出现的地方。

不需要 DOM 工作:只需得到一个节点，将一个`InPortal`和你的内容&放在节点的某个地方，将一个`OutPortal`放在节点的另一个地方，你的内容就会神奇地从一个节点发送到另一个节点。

如果您希望避免不必要的组件重新创建，这很有用。这样可以避免丢失 react 状态或 DOM 状态(例如播放视频元素)，避免重新创建昂贵的初始化组件，并且通常将组件的定义和使用分开。

自述文件中有一个例子，还有一本关于真实例子的[故事书](https://httptoolkit.github.io/react-reverse-portal/)。

让我知道你的想法！