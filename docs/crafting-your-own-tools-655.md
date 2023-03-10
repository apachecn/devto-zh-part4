# 制作自己的工具

> 原文：<https://dev.to/sourdoughdev/crafting-your-own-tools-655>

从我记事起，我就一直痴迷于跟踪和管理我正在做的事情。

起初，当特雷罗第一次出现时，所有的东西都被分解成卡片，随之而来的是一点混乱。

可能像大多数订阅某种“把事情做完”esk 框架的人一样，我开始尝试强加一种完美的方式来管理一切。

标签，专业栏目。所有的事情都被跟踪和宗教更新。

事情一有进展，我就开始觉得我需要一些更专注、更严格的东西。

然后是 [Club House](https://clubhouse.io/) ，不过下手有点重。

在这里花了一两周的时间解决了[的事情](https://culturedcode.com/things/)、[的事情](https://todoist.com)，甚至还把[的工作流程](https://workflowy.com/hi/)都做了。

甚至决定养成用自己的`work`终端应用程序创建每日日志的习惯。

<figure>[![](img/87d5a9c17cc3673a75e83e256f436b54.png)](///asseimg/crafting-your-own-tools/work.png) 

<figcaption>`work`:生活在你终端上的微小 app。</figcaption>

</figure>

## 拥抱方法论而不是工具

我最大的一个体会是，尽管我在所有这些待办事项应用程序中翻来覆去，但我从未尝试过自己的工作管理流程。

通常我会一头扎进去，一个项目接一个项目地浏览，希望这个工具能引导我到达一个生产力极高的乐土。

这让我几乎想到了建立另一个待办事项列表。

幸运的是，一个好朋友对这个想法说了些有意义的话。

最后，我终于找到了一种体面的工作方式:专注。

不是工具的问题，而是过程的问题。

我只坚持了三条规则:

1)一次做一件事。

2)不要编辑，加了就忘了。

3)自动化+存档。

## 工具

我正式回到使用特雷罗，但我并不专注于保持事情极其简单。到标签，到复杂的卡片。东西很小，而且是可以运输的。

<figure>[![](img/e4f9e5fb902b1fa3e3c5fe9228413b6a.png)](///asseimg/crafting-your-own-tools/trello.png) 

<figcaption>我正在遵循一个稍微自动化版本的[艾米·霍伊的 JFS·特雷罗设置](https://trello.com/b/dG64n160)。</figcaption>

</figure>

我还添加了几条[管家](https://trello.com/power-ups/5935cab6b26816f9d49fd814/butler)规则，帮助每周自动存档完成的卡片，并清除超过一个月的东西*(当我的收件箱变大时)*。

在很大程度上，这很好地涵盖了我的两个核心规则，但是为了更接近那种只是记下潜在待办事项的更用心的状态，我实际上使用了两种不同的工具。

### 阿尔弗雷德·特雷罗的工作流程

我用的是@MikoMagni 的 [Trello 工作流程的修改版(](https://github.com/MikoMagni/Trello-Workflow-for-Alfred)[要点](https://gist.github.com/tamcgoey/8057428de3d1022dceebb2856d1064dc))给 Alfred 。

<figure>[![](img/c65b912e7912ad7b7c7ccdcf23a30e75.png)](///asseimg/crafting-your-own-tools/alfred.png) 

<figcaption>特雷罗的工作流程为阿尔弗雷德、[稍加修改](https://gist.github.com/tamcgoey/8057428de3d1022dceebb2856d1064dc)。</figcaption>

</figure>

这让我可以轻松地从 mac 上创建新卡片，让我可以快速捕捉待办事项，而无需考虑如何分散注意力。

本质上，我只需输入:

```
t Card Title 
```

我受够了。如果我今天想做这个任务，我会简单地加上`Today`，比如:

```
t Card Title; Today; Card Description 
```

通常情况下，我不会费心去写描述。我觉得有点过了。

当我在苹果电脑上时，这个功能很好用，但在移动中就有点糟糕了。

一打开 Trello，我就开始浏览未完成的卡片，并试图清理我的收件箱。

### 一个简单的反应式应用程序，用于动态创建 Trello 卡片

这个周末，我开发了一个超级快捷的应用程序，无论我在哪里，都可以轻松地添加待办事项。

<figure>[![](img/ea24d259cd0266949e45923760b0c495.png)](///asseimg/crafting-your-own-tools/inbox.png) 

<figcaption>收件箱，是一款超级简单的反应式应用，专为我的一个 Trello board 打造。</figcaption>

</figure>

收件箱遵循我创建卡片的规则，没有描述或标签的选项，因为我不使用它们。

这是超级快速和公正的工作。有时候最好的工具是那些一两个小时就粘在一起的工具。

如果你对它的样子很好奇，或者想创造自己的版本，下面是 [`App.tsx`](https://gist.github.com/tamcgoey/984609d5a33f44592b7b585c5878180f) 的样子。

我最初的收件箱计划是一个复杂的反应式应用程序，带有 GraphQL 驱动的 API，有助于消除 Trello API 的一些尖锐部分，但老实说，这太过了。

如果你想谈谈开发人员的工作效率或管理自己时间的策略[请联系](https://twitter.com/tamcgoey)！

* * *

*最初发布在我的博客上: [sourdough.dev](https://sourdough.dev/crafting-your-own-tools/) 。*