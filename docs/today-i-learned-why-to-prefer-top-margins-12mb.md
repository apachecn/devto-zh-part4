# 今天我学到了:为什么更喜欢最高利润

> 原文：<https://dev.to/grodier/today-i-learned-why-to-prefer-top-margins-12mb>

*最初发布在我的个人网站上，[georgerodier.com](https://georgerodier.com/2019-8-24-why-to-prefer-top-margins/)。*

最近我一直在研究 [Tailwind CSS](https://tailwindcss.com) 以及如何使用实用类 CSS 框架。我认为它非常适合与 React 组件一起工作，并希望尽快写下它。为了做更多的研究和学习，我一直在看[亚当·瓦森的](https://twitter.com/adamwathan) [关于利用顺风 CSS 进行设计的优秀视频](https://tailwindcss.com/screencasts/)。在其中一个截屏中，作为题外话，Adam 谈到了为什么在用 CSS 分隔元素时，他更喜欢使用上边距而不是下边距。

在过去，我从来没有过多考虑元素的间距。元素 A 需要在元素 B 上面间隔 10px，所以我给元素 A 一个 10px 的下边距。或者可能元素 C 需要在元素 D 下面 20px，所以我给元素 C 一个 20px 的上边距。这是一种随意的方法，我不推荐给任何人。

如果你曾经设计过一个页面，你可能在某个时候遇到过页面看起来不像预期的问题，并且发现[折叠边距](https://css-tricks.com/what-you-should-know-about-collapsing-margins/)是罪魁祸首。当顶部页边距和底部页边距彼此接触时，它们将最终重叠产生一个单一的页边距。因此，许多开发人员和框架最终会在一个方向(顶部或底部)应用边距，以避免边距崩溃的可能性。

在讨论[用 Tailwind CSS](https://tailwindcss.com/course/making-text-content-feel-designed) 设计卡片时，Adam 建议他使用上边距。原因很直观，对我来说也很有意义:

> 对我来说，当你给元素添加上边距时，让元素移动的感觉比你给元素添加下边距时更好，这样实际上会推开其他内容。我喜欢我正在添加的类的元素实际上发生变化的想法。亚当·瓦森

我喜欢这样一个事实，即样式感觉像是在控制它所应用的元素，而不是修改页面上其他元素的位置。这种逻辑引起了我的共鸣，并足以说服我遵循类似的方法。但由于我以前从未听说过偏好顶部而非底部，我很好奇是否还有其他人对此事有任何看法，在这样做的过程中，我发现了偏好顶部利润的另一个原因。

在由[马修·詹姆斯·泰勒](https://twitter.com/mattjamestaylor)撰写的博客文章 [CSS: margin top vs bottom](https://matthewjamestaylor.com/css-margin-top-vs-bottom) 中，他认为因为 CSS 只在向前的方向上级联，所以只有连续的下一个元素可以在上下文中考虑。

马修·詹姆斯·泰勒首先展示了应用了上下页边距的元素列表，从而证明了这一点。然后，他试图通过插入需要不同样式的元素来修改每个列表。通过使用[相邻兄弟组合符](https://developer.mozilla.org/en-US/docs/Web/CSS/Adjacent_sibling_combinator)，可以从插入的元素开始定位下一个元素。您将无法定位前一个元素的底部，因为如果不在别处添加额外的逻辑，您就无法定位 CSS 中的前一个元素。

我会阅读这篇文章以获得更好的解释，但结论是修改上边距允许我们更好地覆盖元素以实现通用的 CSS 原则:

> 如果一个样式可以应用于许多元素中的一个，总是使用 HTML 源代码中最后一个可能的元素，以允许通过上下文重写。马修·詹姆斯·泰勒

由于我发现的争论，我认为我是一个顶边空白转换者，并且会尽可能积极地使用顶边空白而不是底边空白。我相信有很多边缘情况下可能需要底部空白，但是当需要增加垂直间距时，这似乎是一个很好的起点。