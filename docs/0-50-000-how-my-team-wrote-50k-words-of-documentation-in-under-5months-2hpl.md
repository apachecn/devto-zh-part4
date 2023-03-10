# 0-50，000:我的团队如何在不到 5 个月的时间里写出 50，000 字的文档

> 原文：<https://dev.to/alexantra/0-50-000-how-my-team-wrote-50k-words-of-documentation-in-under-5months-2hpl>

人们倾向于像对待保险一样对待文件。这是他们知道他们应该有的东西。他们明白拥有它的好处，在屎打了粉丝却还是不做的时候后悔没有拥有它。

我以前写过的东西

## 文章不再可用

因此，就背景而言，我目前是一个分析师团队的团队负责人，该团队负责处理企业内部的报告请求。

当我今年 4 月接管我的团队时，我们几乎没有任何文档。就在几个月前的 9 月份，我自己的入职培训是一些幻灯片。我通过询问我的同事和自己找出答案来学习我工作的其他部分。

这导致了一个非常低效的团队，每个员工都以他们自己的方式工作，仅仅基于他们所能记得的。与其说这是一个团队，不如说是四个不同的人在做同一种工作。

现在把任何责任归咎于我之前的人是不完全公平的，我还没有看到一个工作场所理解并花时间做文档。我所在的团队总是因为太多的工作而忙得不可开交，即使他们想记录一些东西，公司也不会给他们时间。

虽然团队缺少文档，但这由当前从事这项工作多年的分析师所拥有的经验来弥补，因此团队并没有像人们所认为的那样遭受太多损失。

然而，这个等式很快就变了。2019 年初，我们失去了一名经验丰富的员工，团队只剩下 3 名员工，我们将在 5 月底解雇下一名员工。

基本上，当我接管团队时，我将失去这两个经验人员提供的经验缓冲，以弥补文档的缺乏。除此之外，我还需要雇佣和培训两名新员工。

看来我不能在他们开始之前神奇地获得几年的经验，我必须做些什么。因此，我没有把自己变成一个知识筒仓，而是着手建立一套全面的文档。

值得注意的是，我曾作为信息管理顾问工作过几年。这意味着从哪里开始以及如何到达那里是一个好主意。但是我想分享我是如何做到的，因为优秀文档的秘密并没有被锁在由图书馆员守卫的神庙里。

## 第一步:把需要记录的东西映射出来，制定计划。

从一开始就明确你将如何存储和访问你的文档将会有很大的不同。如果像写文档一样简单，每个人都会这么做，世界将会淹没在过多的“final-Final_1.docx”中

在开始之前，您需要了解以下信息管理的关键规则:

*   信息必须存储在公司中任何人都可以容易访问的地方:我的经验是，你的 c 盘或电子邮件不是一个容易访问的地方；我的首席执行官可以随时访问这些信息吗？
*   信息必须是自然可发现的。有人可以不用搜索引擎，有组织地找到这份文件吗？基本上，它是存储在大多数人逻辑上期望找到它的地方吗？
*   信息必须是自我描述的。无论是它的名字还是它周围的环境都必须让读者快速理解文档的内容。例如，如果某个东西在一个名为“流程文档”的文件夹或部分中，你不需要将其命名为“流程文档”，只需将其命名为“流程文档”即可。但是仍然使用描述性命名。
*   以上三条规则必须遵守亲吻原则。复杂的文档结构会随着时间的推移而失效，并妨碍第一点和第二点。不要做得太过火，要习惯一些“这份文件不适合任何一个桶”会发生的糟糕情况，要适应这种情况，这都是为了大局。

记住这些规则后，下一步是构建文档结构图。首先列出你可能需要每一份文件。

一旦你有了你的清单，开始把它们分成高层次的类别，不要超过六个。一个桶应该反映为什么员工会去那个桶，而不是主题的主题。

到最后，你应该至少有两个水桶。一个是“如何完成工作”,另一个是“非工作特定的文档”,例如，如果你是一名开发人员，一个概述如何向 UAT 环境提交代码的过程将位于前一个桶中，而关于如何申请病假的文档将位于后一个桶中。

对我的团队来说，我们最终有四个桶，它们是:

*   团队特定的流程信息，即如何完成工作
*   支持文件，即我们的团队奖项提名页面。
*   主题信息，关于公司主题的深入信息，有助于工作的开展。
*   技术信息，即如何上传文件到 S3 和复制到我们的红移环境。这也变得有点包罗万象。

接下来要考虑的是你将在哪里存储你的信息。

把所有的东西都存放在一个地方可能很诱人，如果可以的话，这就是你应该做的，但是通常不会这么直接。

我的团队需要存储 sql 脚本、票证信息、我们的内部信息和客户信息。我们的代码存储在 google drive 中，每张票都有一个文件夹。关于这张票的其他信息都储存在售票软件中。我们的内部信息在公司的内部网上，客户需要阅读的资料存储在每个人都使用的帮助中心。

在以前的工作中，我们将我们的流程存储在我们的内联网上，但我们的主题信息存储在我们的票务软件中，因为这有助于我们处理票务，我们的呼叫中心也可以访问它。

然而，这种分割应该是合理的，或者是广为人知的。你不能有矛盾。

因此，在这一点上，您应该有一个文档结构图，它概述了您将如何组织您的信息以及信息将去往何处。

## 第二步:对需要做的事情进行优先排序

现在你知道如何存储你需要的信息了，但是通常需要的数量是惊人的。像任何项目一样，我建议优先考虑它。因此，回到您的文档结构图，并将其分类如下:

*   昨天需要的
*   急需
*   未来 6 个月内需要
*   或者以后。这将有助于决定你做这件事的顺序。

对我和我的团队来说，这是一项容易的任务，因为我知道我将在 6 月份招聘两名员工，我的目标是获取将他们纳入工作所需的所有文档，并将其他所有事情放在“以后的清单”上。

## 文章不再可用

## 第三步:让它成为一个互动的过程

我们成功的最大关键是将文档处理作为一个迭代过程。

我开始用一套模板制作空的内部网页面，并标记不完整的页面，然后在几周的时间里，我们根据需要慢慢地添加每个页面。这些页面中的一些在我的新员工开始工作后一直处于基本状态，其中一些在他们学到东西后能够为这些页面做出贡献。

## 第四步:融入文化和工作流程

我们作为一个四人团队在如此短的时间内达到 50，000 单词的关键在于我们如何使它成为我们工作流程和文化的一部分。

在每张票的开始，他们会搜索我们的文档，看看我们在那个主题上写了什么，在每张票的结尾，我们会把我们在做票时学到的任何东西添加到我们的文档中。我们还将该文档链接回特定的票证，这将有助于以后查看。我还给员工留出时间，让他们不去做票，而是去跨线获取一些关键文档，例如，我们数据库中的一个关键表有 50 多个类型代码，所以我让其中一个团队找出所有 50 个代码。

通过用文档检查标记过程的两端，团队可以立即看到记录他们所知道的东西的价值。我们还没有覆盖每个区域，所以当我们收到一张未记录区域的罚单时，与记录的许多其他事情相比，团队真的感到很痛苦，这推动了不留下任何未记录岩石的愿望。

## 第五步:腾出时间

到目前为止，这是最难的一步，但是拥有文档可以在以后为您节省时间。我在代码运行时或项目之间抽出时间编写文档。有时我会对客户撒谎，延长交货日期，所以我讨厌花时间完成一些文件。

## 结论

建立文档很难，但并不十分困难，现在我们已经度过了最艰难的时期，它正在发挥作用。新员工比他们的前任更快加入，现有员工和公司的其他人都感谢我们写下了人们头脑中存在的东西。我们能够揭穿一些不一致的地方，因为我们已经把所有的东西都写了下来，团队也把他们学到的东西作为第二天性写了下来。

把所有事情都写下来打开了之前没有打开的门。我们已经开始基于获得最多视图的文档构建模板化代码。能够改进现有流程并自动执行一些关键的管理任务，因为它们不再存在于人们的头脑中。我也有信心离开我的团队，因为我们的每个流程都被记录下来，如果他们需要根据经验做出决定，他们可以依靠我们更广泛的团队中的某个人。

就像保险一样，文件是值得的。

#### 我是谁？

> ![Ronsoak 🏳️‍🌈🇳🇿 profile image](img/060155790f77f9a631013e9ca11dd477.png)朗索克🏳️‍🌈🇳🇿@ Ron soak![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我是谁？🌈。Xero 的高级数据分析师。博主[dev.to/ronsoak](https://t.co/kJy8nsYdeq)-[medium.com/@ronsoak](https://t.co/zKVBza2SVQ)。我有时会画 instagram.com/ronsoak_art/，写一些还没有人看过的科幻小说。我的推文不代表我的公司。💬好好干，好好干！2019 年 06 月 02 日 00:30[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1134980546340085762)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1134980546340085762)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1134980546340085762)