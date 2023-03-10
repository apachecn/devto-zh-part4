# 俄罗斯方块:使用 JavaScript 构建游戏

> 原文：<https://dev.to/sleeplessbyte/tetris-building-a-game-using-javascript-3j6f>

我一直是复古、街机和复古街机游戏的粉丝，主要是因为它们的局限性经常导致非常有创意的游戏机制，这些机制很容易学习，但很难掌握。来自[游戏制作工具包](https://www.youtube.com/watch?v=7daTGyVZ60I)的马克·布朗给[做了一个关于“万能动词”的优秀视频](https://www.youtube.com/watch?v=7daTGyVZ60I)，在一些优秀的游戏中，推荐大家观看。老款游戏功能较少，但由于它们大多是为*街机*编写的，通常输入非常有限，这些游戏以各种方式利用它们仅有的几个按钮。

> 🎮在这个系列中，我将向你展示构建俄罗斯方块克隆的所有步骤，遵守[俄罗斯方块指南](https://tetris.fandom.com/wiki/Tetris_Guideline)，这是[俄罗斯方块公司](https://tetris.fandom.com/wiki/The_Tetris_Company)为制作所有新的(2001 年及以后)*俄罗斯方块*游戏产品在形式上相似而强制执行的当前规范。
> 
> 🛑 *俄罗斯方块*是有许可的，这意味着如果你打算用这一系列的文章来建造你自己的街机拼图，确保遵守法律，如果你打算商业发行它。即使你免费提供了一个克隆，你仍然可以得到一个停止和终止。[这个 reddit 帖子](https://www.reddit.com/r/gamedev/comments/5kkk82/law_concerns_when_creating_a_game_inspired_by/)非常全面地介绍了如何去做这件事。此外，[这篇 Ars Technica 文章](https://arstechnica.com/gaming/2012/06/defining-tetris-how-courts-judge-gaming-clones/)以*俄罗斯方块*和所谓的克隆*宋旻浩*为例，深入探讨了法庭如何评判游戏克隆体。
> 
> 📚这个系列纯粹是一个教育，非商业资源。我们将仅使用 *fandom wiki* 作为资源，并且仅使用俄罗斯方块这个名称来表示游戏的*类型，而不是实际的公司、游戏或品牌。*

在这第一篇文章中，我们将着眼于游戏的**域**，意思是游戏中存在哪些**名词**(宾语/主语)和**动词**(动作/功能)。

[![Photo titled 'Arcade Dreams', Fowler's Live, Adelaide, Australia](img/33165846947a25a39ba5b63bc9f895d9.png "Photo by Ben Neale (https://unsplash.com/@ben_neale) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--fxBFaazw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oy2sxfwrpxgokzil3vpz.jpg)

## 定义和概念

首先，在编写任何代码之前，让我们看看所有的游戏定义和概念。我通常在编写任何代码之前进行领域建模的这一步，因为它可能有助于做出正确的抽象。

### 机芯和按钮

在 1984 年发布的经典俄罗斯方块中，输入的数量与为 iPod 开发的版本(俄罗斯方块 2006)基本相同:

*   向左移动下落的*河豚的按钮*
*   向右移动下落的*四分音符*的按钮
*   一个按钮，用于放下并锁定下落的*四连号*
*   旋转*四分音符*的按钮。

标准按钮映射还增加了一些功能:

*   放下(但不锁定)下落的*四连音*的按钮
*   一个向另一个方向旋转*四分音符*的按钮(所以是逆时针对顺时针)
*   使用保持的按钮

虽然这些动作本身看起来很简单，但与其他游戏规则一起，它们可以在广泛的词汇上起作用。

将一个特定的*动词*绑定到一个按钮上就是我们所说的**映射**。该地图有由[俄罗斯方块指南](https://tetris.fandom.com/wiki/Tetris_Guideline)定义的标准。

### 操场

在 NES (1989)的*俄罗斯方块手册中，**游乐场**被定义为:*

> 操场:这是活动的地方。

它构成了网格，在网格上*的蚱蜢*落下并停止。在大多数游戏中，宽度为 10 块，高度从 16 块到 24 块不等。在网格上方，有 20 行被“隐藏”，这被称为*消失区*。

### 河豚

有各种各样的*俄罗斯方块*:从游戏场顶部落下的形状。它们的定义如下:

*   青色: *I*
*   黄色: *O*
*   紫色: *T*
*   绿色: *S*
*   红色: *Z*
*   蓝色: *J*
*   橙色 *L*

[![The different variations of Tetrominos](img/fe5f44b62d29945735c8ec86b348e8d6.png "All the Tetrominos")](https://res.cloudinary.com/practicaldev/image/fetch/s--UZY2d30h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/caxwtvm3qp3lz3nzt6xq.png)

他们还定义了*开始位置*:

*   中间: *I* 和 *O*
*   左-中:其他一切

它们都应该水平产卵*，其中 *J* 、 *L* 和 *T* 首先产卵平侧，并在可见的 playfied 上方产卵(在*消失区*内)，但如果其路径上没有任何东西(变得可见)，则减少一个空格。*

 *### 发电机

有一个[随机发生器](https://tetris.fandom.com/wiki/Random_Generator)来产生将出现在操场上的棋子。标准实现是所谓的 7 袋随机生成器，这意味着在生成另一个背面之前，从一个袋中随机抽取所有七个单侧的四亚甲基。

有[种不同的随机发生器](https://tetris.fandom.com/wiki/TGM_randomizer)。

### 保持区/保持件

玩家可以按下一个按钮，将下落的蚱蜢发送到保留框中，任何曾在保留框中的蚱蜢都会移动到屏幕顶部并开始下落。

### 鬼片

*幽灵棋子*指示了*蚱蜢*将降落的位置，如果它被掉落，它会大大减少误落的次数。

### 旋转

一个*四分图*的旋转实际上不是直线的。处理这个问题的系统和规范被称为*超级旋转系统* (SRS)。

[![All the four rotation states of all the 7 Tetrominos](img/105c7974c0af449ead911f4034faa3a2.png "The various rotations when using the SRS.")](https://res.cloudinary.com/practicaldev/image/fetch/s--5dJhlIbK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n5rfghw6x0b7qh1r9vg2.png)

一般来说，当无障碍时， *Tetrominos* 会像你想的那样旋转，除了:围绕一个点。因为这纯粹是数学上的**旋转**而没有任何**平移**，所以有这样的状态，其中 *J* 、 *L* 、 *S* 、 *T* 和 *Z* *Tetrominos* “浮动”在它们的边界框上方。

然而，当遇到障碍时，游戏会试图将*俄罗斯方块*踢到附近的另一个位置。这些规则在[关于踢墙的章节](https://tetris.fandom.com/wiki/SRS#Wall_Kicks)中有描述。

### 关卡

玩家通过清理线条(完全填满)或执行“T 型旋转”来升级。更高的等级通常有更高的掉落速度和更高的分数(-乘数)。

### 计分

[指引评分系统](https://tetris.fandom.com/wiki/Scoring#Guideline_scoring_system)规定某些*通关方式*与*其他方式*得分不同。我们现在不会进入不同的组合，b2b，旋转等等，但这将在我们实现游戏时出现。

## 下一步

还没有编写任何代码。接下来的步骤是找出:

*   有哪些模式和关注点？这将通过将上述定义和概念中的各种名词映射到编程名称空间和函数名来实现。
*   我们应该使用什么工具链？这将由*需求*决定。这意味着决定一个库/框架(如果有的话)，以及输出的风格。
*   我们想要实现什么独特的游戏元素？俄罗斯方块越少越好！一定要独一无二。

> 🔜本系列的下一部分将建立整个工具链！这里可以找到[。](https://dev.to/sleeplessbyte/tetris-choosing-the-tools-and-setting-up-the-project-4kh6)

[![Photo of the Eramus Bridge in Rotterdam, The Netherlands, at night](img/b09479c19422afda443c40d696392844.png "Photo by Harold Wijnholds (https://unsplash.com/@harold) on Unsplash (https://unsplash.com/)")](https://res.cloudinary.com/practicaldev/image/fetch/s--LzbOAyxs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1s39v2nctx1uoa018abs.jpg)*