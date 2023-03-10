# 要衬线还是不要(无)衬线？

> 原文：<https://dev.to/marvindanig/to-serif-or-not-to-sans-serif-210c>

通常当我厌倦编码的时候，我会仰望设计。

艺术是赏心悦目的，它帮助我尽可能地将注意力从开发上移开。这一次，我决定深入印刷术，了解我们在 [Bubblin](https://bubblin.io) 上出版的书上什么样的字体好看，为什么？

要衬线还是不要衬线，这是个问题。

说到网页排版，这或多或少是一场已经确定的战斗。如果你是为网页设计，没有衬线字体，但是如果你是为印刷设计，那就选择衬线字体，这种字体可以超越极简主义。那句[格言](http://alexpoole.info/blog/which-are-more-legible-serif-or-sans-serif-typefaces/)告诉我们什么在网络上行得通，什么行不通，反之亦然。

但问题是，*为什么是*？

1.  为什么无衬线字体在网页上效果更好，而有衬线字体却不行？

2.  为什么衬线字体的繁荣迫使印刷书籍的阶级和权威？

不言而喻，遵循设计指南(比如在网页上使用无衬线字体)对包容性至关重要。

总是为形式和功能两者而设计。

通过这篇文章，我们将首先看看为什么要有这些指导方针，以及我们如何正确地排版我们的网站以达到可访问性。

> "最好的字体选择是那些读者会注意到信息的地方."

* * *

让我们先来看看字体的结构:

[![](img/bd39d43affc2d13f4677d2e6cfe12c33.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--bN7bcfJX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/marvindanig/assets/master/anatomy-of-typography.png)

【图片鸣谢:[设计师洞见](https://www.designersinsights.com/designer-resources/anatomy-of-typography/)

衬线是笔画末端的那些装饰性的花体。从上面的剖析中你可以看到，“衬线”字体看起来更花哨，装饰也更好，但它们的华丽只有在足够大的尺寸下才能发挥作用。当字体缩小到某个阈值以下时，这些华丽就会变成噪音，降低整体易读性——也就是说，它们无助于我们快速识别单个字母或单词的能力，从而影响视觉可达性。>衬线用于引导眼睛的横向“流动”；据说，衬线的缺乏导致了无衬线的垂直重音，这种重音被认为是与阅读的水平方向相竞争的。–[De Lange 等人，1993 年](http://cajun . cs . nott . AC . uk/comp sci/EPO/papers/volume 6/issue 3/rudi . pdf)。在我们开始讨论什么是眼睛的“流动”以及垂直应力如何有助于网页的可读性之前，让我们暂时离开排版，先来看看动画。-是的，例如，迪士尼或皮克斯的典型动画电影。动画是讲述时间的函数。换句话说，时间是讲故事和故事运动(即流动)之间的粘合剂。你可能会感到惊讶，这个动作或故事的流程也可以在一个单一的画面上描绘出来。例如，看看下面的闪电麦昆:这是一张静止的照片，但其中也有动作。
[![](img/077ae86e9f298a9def40b21d9c7f96cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vVdsuGAg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/marvindanig/assets/master/mcqueen.jpg)

[图片来源:[汽车电影@皮克斯](https://en.wikipedia.org/wiki/Cars_(film))

请注意赛道边上模糊的字母“E”和“D ”,这表明麦昆正在加速穿过赛道。这种模糊被称为运动模糊或全景模糊，取决于相机或主体在拍摄帧时如何移动并留下痕迹。E & D 字母表中那些模糊的属性代表了时间的流动，它们暗示了我们运动的方向和速度。

下面是另一张拉达克藏野驴横行的图片。

[![](img/5d5205b13771c388707196e00939dbcc.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--DGVVwaeA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/marvindanig/assets/master/tibetan-wildass.jpeg)

[图片来源:[萨特延德拉·夏尔马](https://bubblin.io/book/ladakh-by-satie-sharma/137)

在这张照片上，你可以看到动物在向前跑，但它们的头也在上下摆动。尽管照片很模糊，动物的属性也不是很清楚，但是由于[扫视感知](https://en.wikipedia.org/wiki/Saccadic_masking#Intrasaccadic_perception:_relationship_with_saccadic_movements_and_motion_blur)，从单个镜头来看运动是明显的。

* * *

字体上的衬线同样暗示着水平运动。

它们引导眼睛的水平“流动”,正如 De Lange 在他们的印刷可达性研究中所建议的那样。这在印刷品上非常有意义，因为我们水平地从左到右翻页，沿着一条轨迹滚动的字体推动我们沿着故事的流向前进——唉，线条跟踪。

然而在网络上，故事是垂直展开的，流动是向下滚动的。因此，使用带有水平标记的衬线字体会与卷轴的流向相冲突。这就是为什么在网页上看起来很好、感觉很好的字体更高，而且是三衬线字体，也就是说，垂直方向的字体比水平方向的要高。

这就是本周 gyan 帖子的全部内容。仔细分析字体设计让我很开心。你有吗？

* * *

作者:马文·丹尼，泡泡超级本的首席执行官。关注我的[泡泡林](https://bubblin.io/marvin)！