# 使用 CSS 制作诗歌动画

> 原文：<https://dev.to/blindeman/animating-poems-using-css-2354>

### 一些历史

回想当年，似乎有五分之一的网站是完全用 Flash 构建的，我会用 Flash 来制作我写的诗的动画。Inge Raadschelders 和我一起做这件事，我们称自己为[盲管者& Raadsman](http://blindschelders.nl/) 。当 Flash 死去的时候，我们逐渐停止生产它们，因为没有一个明显的继任者。

2012 年，我试图用 HTML 和 CSS 重新创作一首动画诗歌，对效果相当满意，但也立即失去了兴趣。大多数浏览器不支持 CSS 动画，所以分享它们并不有趣，我对 CSS 很满意，但 Inge 不满意。我们都独自制作了一些诗歌电影，但合作是富有成效的，如果我们不能一起做，那就没那么有趣了。我们开始制作其他东西。

### 计划

昨天，我有一个想法，制作一些由滚动触发的动画诗歌可能会很好。这意味着速度是由读诗的人决定的，就像读一本书一样，而不是由我决定的，当我把它们拍成小电影的时候。这个想法让我非常高兴，昨晚我一直在想这个问题，突然想起我曾经做过的一次尝试。这个不是滚动触发的，我可能很快就会把它变成一个代码笔，但是我很高兴今天看到它。

这是 2012 年的一首动画小诗:[blindschelders.nl/mobile/sneeuwkerk](http://blindschelders.nl/mobile/sneeuwkerk.html)它远非完美，它非常简单，目前它非常兼容跨浏览器，可以在台式机、笔记本电脑和手机上工作。在过去的 7 年里发生了如此多的事情，滚动触发动画似乎是一个显而易见的选择，也许是一种我会喜欢的格式。

### 资源

现在我必须想出最好的方法来处理这件事。我看了一些图书馆。我想到的第一个是格林斯托克。我曾经在 Flash 中使用它们，但它们的功能远不止于此，这让我动心了。在我搜索的时候，我又遇到了几个引起我注意的。这是一个小清单，其中的注释是给我的。我还没有测试过任何一个。

*   TweenLite 能满足我的需求吗？我愿意为完整版付费吗？如果你想使用 cssanimation.io，我想你需要它…但是我需要吗？
*   对我来说，ScrollMagic 似乎真的很大。很多可能性，但是在他们所有的例子中，滚动感觉就像在泥泞中拖着东西。
*   卷轴上的动画(AOS) 看起来不错，轻巧又百搭。我不知道。也许吧。
*   [CSS3 动画它](https://jackonthe.net/css3animateit/)嗯。
*   [anime.js](https://github.com/juliangarnier/anime) 这个好像很有意思。尤其是如果你看看[文字](https://tobiasahlin.com/moving-letters/) [特效](https://codepen.io/collection/XLebem/)。不确定学习曲线，但可能值得。
*   速度可能会让卷轴魔法更有效。它加快了 jQuery 动画的速度，实际上是它的替代品。
*   [转运](http://ricostacruz.com/jquery.transit/)显然在压力下速度更快，这可能是相关的。
*   [Animista](http://animista.net) 哦！我喜欢这个。预制的 CSS 动画，只复制你想要的。
*   关于如何使用 JavaScript 检查一个元素是否已经被滚动到视图中的很好的教程，在这一点上你可以使用 CSS 动画。我很喜欢这个。
*   关于如何使用 jQuery 来检测一个元素是否已经被滚动到视图中的教程。如果没有必要，我宁愿不加载 jQuery。

### 项目

我会试着在下面重现这个。我确信重做它会改变这首动画小诗，因为重做任何东西总是会改变事情，因为技术上的差异和显而易见的:我希望它由滚动触发。

[https://player.vimeo.com/video/152154454](https://player.vimeo.com/video/152154454)