# 探索性测试的应用流程建模

> 原文：<https://dev.to/eviltester/modelling-application-flow-for-exploratory-testing-5d1o>

*TLDR；一个简单功能的应用流程建模的例子。建模有助于思考覆盖率和测试思路，并思考下一步测试的内容。*

我选择了 Google 上非常简单的我觉得幸运的功能，作为支持探索性测试的建模示例应用程序。我录下了建模过程，并在过程中解释了我的思维过程。

这原本是 2018 年 2 月 7 日发布的 Patreon 帖子。我几乎每天都在 Patreon 上创建内容，所以我开始重新访问其中的一些内容，并将其中的一部分重新用作博客内容。近 18 个月来，帕特里翁的支持者一直独家访问这些内容。

Patreon post 有没有广告的原始视频和文字记录。这篇博客文章是基于文字记录和一些额外的总结想法。

## 造型练习和现场解说

[https://www.youtube.com/embed/1pLlROEwAvU](https://www.youtube.com/embed/1pLlROEwAvU)

*   模特不一定要正式
*   笔和纸很好，你支持你的测试
*   你可以用你的手机来永久记录你所做的事情
*   “覆盖率”是关于模型的，所以让你的模型更直观，可以帮助你回顾你的覆盖率

## 关于造型练习

一种我们不经常使用的技术是绘制系统的流程图。

许多图表技术是可用的。视频中提到的书是詹姆斯·马丁和卡玛·麦克卢尔所著的《[分析师和程序员图表绘制技巧》。它很老了，但是它有很多可能有用的图表方法。](https://www.amazon.co.uk/Diagramming-Techniques-Analysts-Programmers-Martin/dp/0132087944)

以下是一些其他的网络资源:

*   [图表方法列表](http://www.umsl.edu/~sauterv/analysis/dfd/DiagrammingMethods.html)
*   [维基百科 UML](https://en.wikipedia.org/wiki/Unified_Modeling_Language)
*   [维基百科流程图和其他方法](https://en.wikipedia.org/wiki/Flowchart#See_Also)

绘制应用程序的图表可以帮助我们理解它们。它们不必是正式的状态图。它们可以非常简单和非正式。

有时我们试图区分图表的类型:

*   这是结构模型吗？
*   这是逻辑模型吗？

在图表制作的早期，这通常并不重要。我用这个图来画出或模拟我认为正在发生的事情。

这让我不必把所有东西都记在脑子里就可以回顾我的理解，我可以看到差距和我没有跟上的流程。

## 建筑示意图

[![](img/ead55d8024719a2055cde87c648944b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lUn4GFJ_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.eviltester.cimg/blog/2019/simple-model/google-feel-lucky-flow.png)

我通过以下方式构建图表:

*   测试
*   模拟我所能看到的
*   模拟发生了什么
*   寻找覆盖缺口
*   画我能看到的，我还没用过的东西
*   写下问题
*   标记我走过的路
*   对于尚未遵循的路径有开放的箭头
*   写下我正在使用的数据

## 思想上的图解和过程

我试图保持模型小而简单。否则我的模型会变得太大。任何时候我们在做模型的时候，我们都必须约束我们所做的事情。否则它会变得太大。我把它简化为一个非正式的流程模型，而不是正式的状态模型。

我使用纸张逐步构建模型，因为这给了我最大的灵活性，允许我在页面上添加额外的注释和问题。稍后，如果我想再次使用它或使它更正式，我可以将它转换成图表。

我经常随意画一些手绘的图表，并给它们拍照保存在我的测试笔记中。

我模拟了我还没有走过的路，所以我可以一边走一边回顾它。

我在建立模型的时候犯了一些错误。因为我在努力理解这个申请。这是我的笔记。不一定要特别易读。这是为了帮助我了解发生了什么事。它不必是完美的。

图表变得有点混乱。所以我先写在纸上。我没有使用正式的图表工具，我只是在纸上把它整理出来。因为我可以稍后再画。

与任何方法一样(尤其是大声说话时)，我们会遗漏一些东西，但是在我测试的时候拥有应用程序的可视化表示可以帮助我注意到我第一眼可能会遗漏的功能线索。因为我能看出图表和系统的区别。

这是一个建模过程。这不一定是正式的建模过程。

我最终得到的是一个相对完整的地图，在这个意义上，我知道所有的流程都有起点和终点。然后，我可以根据路径来研究图表。图中的一些东西是绝对的，它们对于路径是固定的，例如，我正在使用的按钮，无论我使用的是现在屏幕上的按钮，还是对话中的按钮，它们都是固定的。我更加意识到变化的是数据。路径上的数据。

搜索词在路径上是变化的，这是我在这个层次上所知道的，因为我在 GUI 上工作。

我的模型受到我正在工作的层次的限制，但是我意识到了这一点，因为应用程序的一些更深层次没有在我的图中表示出来。

如果我选择开始查看网络流量，或者开始查看页面上的变量，或者开始查看 cookies。我可能会看到发送回服务器的消息有更多的变化，例如，因为我可能有不同的会话 cookies，也可能有不同的会话 id。

如果我在一个更大的系统上做这个，我做了一个这样的地图，我过去做的是用红色或绿色的笔标出我覆盖的路径，并跟踪我在路径注释旁边使用的数据。这使得直观地看到我已经覆盖了什么和没有覆盖什么变得很容易。

我们必须知道的一件事是测试中的覆盖率是基于模型的。它总是基于模型的。即使是这样的非正式场合。我们所说的覆盖范围是指我们覆盖某种模型，即使是一组测试条件或一组想法，那也是我们的覆盖范围，因为那是我们已经有的模型。

给你。当我有了一个更图形类型的模型时，我就可以对它进行路径测试，这样我就可以覆盖“我在沿着什么路径走”。我可以记录我在这些路径上做了什么数据。

如果你没有做这种工作，或者如果你从来没有做过这种图表制作过程，或者试图在你的测试中使用它，我建议你尝试一下，因为它是一个有用的东西，你不需要像软件工程书籍和图表那样正式。

熟悉图的正式类型是值得的，因为这样你就会意识到人们过去建模的事物的类型，以及你可能如何接近建模。但是你可以很快地将图表整合到你的测试中，这可能真的会帮到你。

*如果你觉得这类信息有用，那么你可能会对我上传到[patreon.com/eviltester](https://patreon.com/eviltester)T3】的其他内容感兴趣*

## 维基百科上一些图表方法的列表

*   [活动图](https://en.wikipedia.org/wiki/Activity_diagram)
*   [控制流程图](https://en.wikipedia.org/wiki/Control_flow_diagram)
*   [控制流程图](https://en.wikipedia.org/wiki/Control_flow_graph)
*   [数据流图](https://en.wikipedia.org/wiki/Data_flow_diagram)
*   [部署流程图](https://en.wikipedia.org/wiki/Deployment_flowchart)
*   [雷达图](https://en.wikipedia.org/wiki/DRAKON)
*   [流程图](https://en.wikipedia.org/wiki/Flow_map)
*   [流程图](https://en.wikipedia.org/wiki/Flowchart)
*   [功能流程框图](https://en.wikipedia.org/wiki/Functional_flow_block_diagram)
*   [纳西-施奈德曼图](https://en.wikipedia.org/wiki/Nassi%E2%80%93Shneiderman_diagram)
*   [状态图](https://en.wikipedia.org/wiki/State_diagram)
*   [沃尼尔/奥尔图](https://en.wikipedia.org/wiki/Warnier/Orr_diagram)
*   [为什么——因为分析](https://en.wikipedia.org/wiki/Why-because_analysis)

## 你可能会觉得有用的其他关于建模的帖子

*   [10 个实验来提高你的探索性测试笔记](https://www.eviltester.com/2013/09/10-experiments-to-improve-your.html)
*   软件测试中的可视化思维

*这最初是在 2018 年 2 月上传到[帕特里翁](https://patreon.com/eviltester)的。我逐渐在博客上发布了一些 Patreon 的独家帖子。你可以注册并获得我在 [Patreon](https://patreon.com/eviltester) 上几乎每天的帖子。*