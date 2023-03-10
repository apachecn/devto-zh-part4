# Wordpress 概念:动作和过滤挂钩，Wordpress 的动力和基础

> 原文：<https://dev.to/amirition/wordpress-concepts-action-and-filter-hooks-power-and-foundation-of-wordpress-m90>

坦率地说，不了解 wordpress 钩子，你不可能成为一名 Wordpress 开发者。什么是 wordpress 动作和过滤器？有很多关于 Wordpress 动作钩子和过滤器钩子的好的和详细的文章，我不打算重复他们以前说过的。

#### 有些情况下要用钩子

因为我现在不打算写代码，所以我认为最好的方法是展示一些例子来让你清楚这个概念。
**Wordpress 过滤钩**
让我们假设你想在每篇帖子内容下面添加一些文字。基本上你是在改变一个内容，这就是过滤钩的意思；您可以更改内容。但是怎么做呢？
Wordpress 开发者参考或旧的 Wordpress codex 是很好的资源，他们有你需要的所有钩子，有详细的信息和一些例子。你只需要寻找合适的钩子。
回到我们的例子，你只需要搜索如下内容: *Wordpress 帖子内容过滤器*
这些搜索可能会把你带到 Wordpress 官方文档，在那里你可以找到一些使用它的好例子。

[![](img/3b0927d46793c8f91a816fd59dc1af74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PXc3LiIA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2xh1j90d08hm6j0q9h9x.png)

这是过滤器，您可以使用它来更改过滤器定义的一些特定数据。现在让我们假设你正在开发一个插件，你想让人们改变这个插件中的一些数据。这是可能的，也是非常标准的，你可以为你想要的数据定义一些过滤器，另一个或者甚至你自己将能够连接到那个过滤器并改变数据。你可以在 Wordpress 开发者网站找到更多相关信息。

你想直接看例子还是用一些莫名其妙的词来定义它？好吧！
每当有新帖子发布在你的网站上，你都想得到通知**。所以你想在某件事发生时执行一段代码，对吗？但是 Wordpress 如何知道何时运行你的代码呢？在这种情况下，你可以使用动作钩子。就像过滤器钩子的例子一样，你应该知道你想要使用什么动作。在 Wordpress 开发者网站上用这样的关键词搜索:*在发布行为*
之后，你会得到一些非常清晰的例子和代码，你可以理解它。你也可以有自己的行动。这样你就可以为人们打开一个简单的方法，当然将来你也可以很容易地在你代码的某些地方添加代码。你可以在 Wordpress 开发者网站找到更多关于这个的信息。**

那么，你认为钩子是 Wordpress 的主要动力吗？我将很高兴听到或想法。

**上一篇:** [Wordpress 概念系列:做一个好的 Wordpress 开发者](https://dev.to/amirition/wordpress-concepts-series-be-a-good-wordpress-developer-1m3n)
**下一篇**:谁知道？