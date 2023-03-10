# 你的生命有多短？使用 React 找出答案。(第二部分)

> 原文：<https://dev.to/santper/how-short-is-your-life-use-react-to-find-out-part-2-2i14>

[ **免责声明，再次声明**:codePen 嵌入可能会使页面看起来有些奇怪。要欣赏他们的辉煌，请打开我的开发人员个人资料上的 CodePen 链接或 lifedots github repo

上次我们已经到达可视化部分，但我们仍然只看到蓝色和红色的点。在这一部分中，我们将添加一些绿色的来代表在不同活动中度过的生活部分(例如，工作或学习)。

让我们从增加你生活中将要工作的部分开始。

[ **免责声明 2** :如果应用程序没有关于你的年龄和国家的数据，将不会呈现任何点，因为应用程序不知道你是谁。要修复它，请输入 CodePen 链接，或者在您的计算机上复制它，克隆 GitHub repo]

[https://codepen.io/sntgp/embed/MNmKwy?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/MNmKwy?height=600&default-tab=result&embed-version=2)

我无法找到世界上每个国家的退休年龄，当然退休是个人决定，即使在同一个国家，也不是每个人都在同一年龄退休。所以我继续估算，你要工作五十年才能退休。如果你是成年人，我们还要扣除你已经工作的年限。

我们如何在 React 上做到这一点？我们已经创建了一个 const **样式**，它使用一个三元运算符将 *backgroundColor* 属性赋给代表过去几年的球“蓝色”，代表未来的球“红色”。为了增加一个新的条件，我们使用一种叫做嵌套三元运算符的东西。那么，信函样式应该是这样的:

`let style = {backgroundColor: this.state.count < age ? 'blue': this.state.count < age + work ? 'green' : 'red'}`

请记住， *this.state.count* 是一个计数器，每当 componentWillMount()中的 for 循环呈现一个新的“年份”(即三个点)时，它就会增加。如果 this.state.count 小于用户的年龄，圆点将为蓝色。如果 this.state.count 小于用户的年龄+用户将工作的年数(但不小于用户的单独年龄)，则圆点为绿色。如果 this.state.count 大于该总和，则圆点为红色。

下一步是增加你睡觉的时间。这里的计算稍微简单一点:我们假设你每天睡 8 个小时，也就是每天的 1/3。显然这并不完全正确:例如，你可能在最年轻的时候睡得更多，然后随着你开始工作，睡得更少。但是因为我们没有时间机器(如果我们有，我们可能不会用它来跟踪某人的睡眠)，我们需要使用这样的估计。

[https://codepen.io/sntgp/embed/GVmqXG?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/GVmqXG?height=600&default-tab=result&embed-version=2)

同样，逻辑是相同的:我们使用三元运算符来处理三种不同的情况:如果一年已经过去，则将背景色指定为“蓝色”,如果一年将用于工作或睡眠，则为绿色，如果一年都不为真，则为红色。

我们做同样的事情来增加用户学习和使用社交媒体的时间。

[https://codepen.io/sntgp/embed/zgwBbK?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/zgwBbK?height=600&default-tab=result&embed-version=2)

为了计算这个，我们做了一个假设:我们猜测你会经历小学，初中和高中，外加四年大学。

[https://codepen.io/sntgp/embed/ymbaBY?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/ymbaBY?height=600&default-tab=result&embed-version=2)

这个来自于一项[实际研究，你可以在这里阅读](https://www.socialmediatoday.com/marketing/how-much-time-do-people-spend-social-media-infographic)

这个最后的实例有两个我们还没有写的新行:我们用来在 localStorage 上存储 GreenTime(工作、学习、睡觉和使用社交媒体的时间)和 RedTime(预期寿命减去 GreenTime 减去年龄)的行。有了这些存储的新数据，我们可以进入这个项目的最后阶段:总结。

严格来说，React 并不难做到:三个组件(BlueTime、RedTime 和 GreenTime)分别从 localStorage 中呈现一个值。

[https://codepen.io/sntgp/embed/XvRjdM?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/XvRjdM?height=600&default-tab=result&embed-version=2)

我们的 React 教程到此结束！

你可以在[这个 github repo](https://github.com/SantiagoPereira/lifedots) 里找到整个项目。

**这是我在 dev.to 的第一篇文章的第二部分！你觉得这个教程简单易懂吗？关于 React 你学到了以前不知道的东西吗？请在评论中留下您的反馈，如果您喜欢，请在这里和 Twitter 上关注我👇👇**

[![santper image](img/edacd1fada92f636da4055c368574375.png)](/santper)

## [圣地亚哥·佩雷拉](/santper)

[16 year old wannabe programmer on the way to greatness (?](/santper)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)桑佩尔 _ ](https://twitter.com/santper_) [ ![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)桑迪亚戈佩雷拉](https://github.com/SantiagoPereira)