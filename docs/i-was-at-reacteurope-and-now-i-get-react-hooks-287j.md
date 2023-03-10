# 我在反应堆里，现在我“得到”反应堆钩子

> 原文：<https://dev.to/kahlil/i-was-at-reacteurope-and-now-i-get-react-hooks-287j>

上周，我去了巴黎，和家人一起度过了一段时间，并参加了一个培训。

基于他们网站平庸的设计，我并不期望在会议的组织上花太多心思。我期待着酒店里沉闷的会议室和一些有趣的谈话。

我在巴黎一个沉闷、丑陋的地区得到了一些有趣的演讲和一个沉闷、沉闷的会议室。不过这个房间对这么多人来说太小了。空气很糟糕，我周围的人都在咳嗽和打喷嚏。我不能在那里呆太长时间。

相反，我花了一些时间通读了 React Hooks 文档。让我花一点时间来说一下这个文档写得有多好，有多深思熟虑。真正的世界级。不像 ReactEurope 会议。

我也开始重写来自[的](https://github.com/preactjs/preact)[我的 Grit 编辑器](https://github.com/kahlil/grit)Preact和 [htm](https://github.com/developit/htm) 来对钩子做出反应。只是为了熟悉他们。

如果没有尝试过，很难表达这样的特性是如何影响您的开发体验的。到目前为止，我只重写了它的一小部分，但我非常喜欢它。

当涉及到简化代码时，定制钩子的概念似乎特别具有影响力。

这里有一个例子。

为了让 Grit 工作，用户必须在设置中存储他们的 Markdown 文件的路径。我通过使用 Sindre Sorhus 的名为 electronic-store 的 ace 包来存储设置(对他大喊，没有他出色的开源工作，我该怎么办？！).

这个路径需要通过一个状态变量显示在 UI 中，如果它被改变，它必须被同步回电子商店。

因此，我创建了这个名为 useElectronStore 的定制钩子来从电子存储中读取路径，并从中设置 filePath 状态变量，并在状态变量和电子存储发生变化时用新值进行设置。

这使得获取和设置电子商店的价值变得非常容易。API 基本上是这样的:

```
const [someValue, setSomeValue] = useElectronStore('someValue', ''); 
```

Enter fullscreen mode Exit fullscreen mode

`'someValue'`-字符串是电子存储中的路径，第二个值是它的默认值。

我现在可以说我“得到”了反应钩子，因为反应钩子并没有那么好。对我有用。