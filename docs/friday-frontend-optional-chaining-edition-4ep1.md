# 星期五前端:可选链接版本

> 原文：<https://dev.to/kball/friday-frontend-optional-chaining-edition-4ep1>

[编辑注:我在重新发布到 dev.to 上落后了，所以这将在几周后发布。不好意思！]

希望你度过了愉快的一周！快到周末的时候，我因为一个小小的家庭紧急事件而出轨了，所以我们这周的文章有点少，但还是有些很棒的东西。

我特别推荐两篇 CSS 文章:一篇关于自定义属性和动画，另一篇关于 CSS 架构。在 JavaScript 部分，可选的链接文章展示了该语言的一个非常令人兴奋的附加功能。最后一节对不同的 web 实践如何影响功耗的探究非常有趣！

周末愉快！尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [使用自定义属性处理关键帧动画中的变化](https://css-tricks.com/using-custom-properties-to-wrangle-variations-in-keyframe-animations/)

使用 CSS 自定义属性使事情变得更简单的一个很好的例子。查看如何使用属性范围来影响 CSS 关键帧动画的特征，以前需要多个动画。

##### [未完成的正文](http://adrianroselli.com/2019/09/under-engineered-text-boxen.html)

有趣的是，看看如何使用尽可能简单的 CSS 来设计文本框。随着 CSS 变得越来越强大，有了更多的选择，有趣的是看到有人不看“我能做多少”，而是“我如何尽可能少地做足够多的事情”。

##### [CSS 中具有方向感知的幽灵按钮](https://css-tricks.com/ghost-buttons-with-directional-awareness-in-css/)

这是“我能用 CSS 做多少事”的另一方面的一个很好的例子——创建“幽灵按钮”(没有填充)，在悬停时填充，但使悬停来自你的鼠标所在的一侧。a 采用了剪辑路径和多个元素的组合，但是你只用 HTML 和 CSS 就能做的事情确实令人印象深刻。

##### [用可扩展的 CSS 保持简单](https://hankchizljaw.com/wrote/keeping-it-simple-with-css-that-scales/)

大篇幅介绍 CSS 实践和架构方法，以便在扩大项目规模时保持 CSS 的可维护性和有效性。强烈推荐通读。

#### JavaScript

##### T2】😲🤯您需要了解的 Javascript 中最突出的新特性:可选链接

可选链接是惊人的，这是一个伟大的快速阅读了解它是什么和它是如何工作的。因为这是 JavaScript，所以当然有一个 babel 插件，你可以马上开始使用它。

##### [在普通 Javascript 中安全访问嵌套对象的 4 种方法](https://medium.com/better-programming/4-ways-to-safely-access-nested-objects-in-vanilla-javascript-8671d09348a8)

如果您在一个不想或不能使用上面的 babel 插件的环境中，并且您不想等待可选的链接，请查看这篇文章，了解一些仅用普通 JavaScript 安全处理嵌套对象的好方法。

##### [如何用 Jest 测试 JavaScript】](https://www.freecodecamp.org/news/how-to-test-javascript-with-jest/)

关于 Jest 测试的扎实而深入的介绍。

##### [RxJS 模式:效率与性能](https://blog.bitsrc.io/rxjs-patterns-efficiency-and-performance-10bbf272c3fc)

RxJS 是那种感觉有点矫枉过正的库和方法，直到它不再矫枉过正，然后它变得非常神奇。这是一个以比手工管理承诺和 async/await 更健壮的方式处理和组合异步性的工具包，但挑战之一是 API 的表面积足够大，很难处理。如果你还不太熟悉 RxJS，我会从[文档介绍](https://rxjs.dev/guide/overview)开始，但是如果你已经使用过它，并且想了解更多的 API 空间和潜力，这是一篇很棒的文章。

#### 其他牛气

##### [网络内容如何影响用电量](https://webkit.org/blog/8970/how-web-content-can-affect-power-usage/)

webkit 团队深入研究了影响功耗的因素，以及电池寿命的所有重要因素。随着越来越多的消费转向移动，这是一个越来越大的关注领域。他们很好地解释了什么使用更多的能量，以及您可以使用什么工具来跟踪问题。

##### [5G 肯定会让网页变慢，也许](https://www.filamentgroup.com/lab/5g/)

通过发送更复杂的软件来抵制硬件技术每一次进步的趋势。大力宣传减少我们发送给客户的 JavaScript 代码量。

##### [点击按钮时浏览器间行为不一致](https://zellwk.com/blog/inconsistent-button-behavior/)

我没有意识到这一点，但显然浏览器在按钮点击后处理焦点的方式上有很大的不同。看看这个。一个不错的研究，加上一些推荐的 JavaScript 来使情况更加一致。

##### [(为什么)有些 HTML 是“可选的”](https://dev.to/remy/why-some-html-is-optional-2e11)

有趣的 HTML 历史，以及为什么有些元素有“可选的”结束元素。哦，我们已经走了多远...

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】