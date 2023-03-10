# 如何用不到 100 行代码创建生成性艺术

> 原文：<https://dev.to/thebuffed/how-to-create-generative-art-in-less-than-100-lines-of-code-1k9m>

与任何编程主题一样，如果你从未尝试过，生成艺术可能会令人生畏。我一直对它很感兴趣，因为我喜欢寻找新的方法来创造性地利用编程。此外，我认为任何人都可以欣赏艺术作品的概念。

## 什么是生成艺术？

生成艺术是一个系统的输出，这个系统对作品做出自己的决定，而不是人类。这个系统可以像一个 Python 程序一样简单，只要它有规则和一些随机性。

对于编程来说，制定规则和约束非常简单。所有的条件语句都是如此。话虽如此，找到让这些规则创造出有趣的东西的方法可能会很棘手。

<figure>

[![](img/9140501b4711cb1180ab4ccfa0a52a3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W7BnOX62--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6830f10y6db7rqf33ig.gif)

<figcaption>Conway's Game of Life</figcaption>

</figure>

生命的[游戏](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)是一套著名的四个简单规则，决定了系统中每个细胞的“生”和“死”。每一条规则都在每一代人推进系统的过程中发挥了作用。虽然规则简单易懂，但复杂的模式很快就会出现，并最终形成迷人的结果。

规则可能是创造有趣事物的基础，但即使像康威的《生活游戏》这样令人兴奋的东西也是可以预测的。因为这四个规则是每一代的决定因素，所以产生不可预见结果的方法是在细胞的起始状态引入随机化。从随机矩阵开始将使每次执行都是独特的，而不需要改变规则。

生成性艺术的最好例子是那些找到可预测性和随机性的结合，以创造出一些有趣的东西，而这些东西在统计学上是不可复制的。

## 为什么要尝试？

并非所有的副业都是平等的，你可能不愿意花时间在生殖艺术上。但是，如果您决定参与一个项目，那么您可以期待这些好处:

*   **体验**——生殖艺术只是磨练一些新旧技能的又一次机会。它可以作为实践算法、数据结构甚至新语言等概念的门户。
*   有形的结果——在编程的世界里，我们很少能看到我们的努力有任何有形的成果，至少我看不到。现在，我的客厅里有几张海报，展示我的生殖艺术作品，我喜欢编程对此负责。
*   有吸引力的项目——我们都有过向某人解释个人项目的经历，甚至可能是在面试的时候，却没有一种简单的方式来传达项目的努力和结果。生成艺术是不言自明的，大多数人都会被你的创作所打动，即使他们不能完全理解其中的方法。

## 应该从哪里开始？

开始创作艺术和任何项目都是一样的过程，最关键的一步是提出一个想法或找到一个可以建立的想法。一旦你心中有了目标，你就可以开始研究实现目标所需的技术。

我的大部分生成艺术项目都是用 Python 完成的。这是一种相当容易习惯的语言，它有一些令人难以置信的软件包可以帮助图像处理，如 [Pillow](https://pillow.readthedocs.io/en/5.3.x/) 和 [PyCairo](https://pycairo.readthedocs.io/en/latest/) 。

幸运的是，您不需要搜索太多的起点，因为我在下面提供了一些代码供您使用。

## 精灵生成器

当我看到一个展示用 Javascript 编写的精灵生成器的帖子时，这个项目开始了。该程序创建了 5x5 像素的艺术精灵，带有一些随机的颜色选项，其输出类似于多色空间入侵者。

我知道我想用 Python 练习图像处理，所以我想我可以试着自己重新创造这个概念。此外，我认为我可以对它进行扩展，因为原始项目在精灵的大小上是如此有限。我希望不仅能够指定大小，还能够指定它们的数量，甚至图像的大小。

下面是我最终得到的解决方案的两个完全不同的输出:

<figure>

[![](img/726ae1bb9b4b875960dc270f88c6a3cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--raUs88tI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e2dt7cz2zuja2h2isakw.png)

<figcaption>9x9</figcaption>

</figure>

<figure>

[![](img/0fd9923d0d90354dc98007f3fc909c04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pwer7Dg4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79mdf2afvlq3xadqasza.png)

<figcaption>37x37</figcaption>

</figure>

这两幅图像一点也不相似，但它们都是同一个系统的结果。更不用说，由于图像的复杂性和 sprite 生成的随机性，即使有相同的参数，这些图像也很有可能永远是独一无二的。我喜欢它。

如果你想要数以千计的例子来展示结果的多样性，看看我在树莓 Pi 上设置的这个 twitter 机器人,它每 20 分钟就会发布一条新消息。

> ![unknown tweet media content](img/e54bc391756749f4e856d2b1c9c6624b.png)![SpriteGenerator profile image](img/818f9bab67169f279dd16d7d247fce3e.png)sprite generator@ generator sprite![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)9x 9-32-150019:05PM-10 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1138160180213030913)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1138160180213030913)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1138160180213030913)0

## 环境

如果你想开始使用精灵生成器，首先要做一些基础工作。

用 Python 建立一个合适的环境可能很棘手。如果你以前没有使用过 Python，你可能需要下载 [Python 2.7.10](https://www.python.org/download/releases/2.7/) 。我计划最终更新 Python 3+的教程，因为 2.7 已经过时了。

Python 项目中经常出现依赖性问题。如果你开始遇到问题，你可以像我一样建立一个虚拟环境。最后但同样重要的是，确保你也安装了枕头。

一旦设置好环境，就可以将我的代码复制到扩展名为。py 并使用以下命令执行:

`python spritething.py [SPRITE_DIMENSIONS] [NUMBER] [IMAGE_SIZE]`

有一点**非常重要**需要注意，那就是精灵的尺寸必须是奇数**。背后的原因是对称的处理方式，当你深入研究实现时，这将更有意义。**

 **## 代码**