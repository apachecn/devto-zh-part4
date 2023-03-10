# 你的快速#a11y 备忘单

> 原文：<https://dev.to/misselliev/your-quick-a11y-cheat-sheet-30ko>

最近，在一次每个人都会去的 Youtube 潜水中，我偶然发现了一个关于可访问性的老苹果商务网站，它以我以前从未考虑过的方式引起了我的共鸣。

> “克服它—可访问性不是可选的”。——微软首席无障碍官 Jenny Lay-Flurrie。

当你开始你的编码之路，开始做基本的网站制作时，当你不确定要寻找什么，首先要学习什么，并且你主要担心为什么你的 css 没有正确链接时，在这些阶段没有很多关于如何使你的网站友好的内容。*(罪名成立)*

## 那么，网页可访问性到底是什么意思呢？

W3C 将网页可访问性定义为网站、工具和技术的设计和开发，以便残障人士可以使用。

需要考虑什么样的条件？

*   视觉:部分或完全不能看见或感知颜色。
*   运动技能:很难做出精确的动作(使用鼠标)
*   听力:听力下降或不存在。
*   光敏性癫痫发作:动画中的闪光灯会引发癫痫发作。

## 为什么重要？

通过对你的代码做一些小的调整，你将能够使用户体验尽可能的平等，这样每个人都将能够交互、理解、导航、为网络做贡献，不管他们的环境或能力如何。

你可以帮助别人感到受欢迎和被包容，这不是很好吗？
[![alt text](img/5924482d9d57a5423ef3235a87e5efd3.png "happy coding people")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--nt2svx5i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dzs9suju3zi6wzj58op5.jpeg)

受益的不仅仅是残疾人。Web 可访问性是由使用移动设备或慢速网络连接的人使用的。此外，这与良好的代码实践重叠(耶！)

## 提示你现在可以实施了

*   给图片添加替代文本
    标题很好，但是如果你的图片没有被加载，用户怎么知道它在看什么？
    [![alt text](img/936e52ddf5255b3a232a5bc7c053cbd8.png "alt text example")](https://res.cloudinary.com/practicaldev/image/fetch/s--_i6jJGoL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1sxhrf98pu0cmrdfkimi.jpg) 
    屏幕读者也从中受益。额外收获:替代文本也可以提高你的搜索引擎优化！确保写得有描述性，使用有意义的关键词。

*   巧妙运用你的颜色
    红色背景和绿色文字并不是最好的选择。你使用对比对于你的页面有多少能被真正的看到和理解是很重要的。你可以在这里快速检查一下你的配色方案

*   使用你喜欢的标题
    用正确的标题标签给你的网站一个正确的流程和结构。作为一个建议，只有你的主要标题应该与 H1，然后使用相应的重要性或相关性副标题。

*   使用标题标签
    它对屏幕阅读器很有帮助，有助于组织你的内容。使用一个简短的标题来说明你的网站是关于什么的。

[![alt text](img/f66d936d3e325acb4953ea4e9a0213dd.png "title-header-example")](https://res.cloudinary.com/practicaldev/image/fetch/s--tnUTMv6I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1pkbuql80gty7tqdkf5y.png)

*   标签仍然可以很酷
    虽然占位符在表单上看起来比标签更好，但在一些屏幕阅读器中它们被忽略了，所以用户到达了一个没有描述的文本字段。我不是告诉你停止使用它们(我对它们情有独钟),而是包括一个标签，即使它是隐藏的。确保标签和占位符具有相同的文本。

*   成为 ARIA 的朋友-label attribute
    可访问的丰富互联网应用程序(Aria)是一组 HTML 属性，它们定义了使 Web 内容和 Web 应用程序对残障人士更易访问的方法。你可以查看更多关于如何使用它的信息[这里](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-label_attribute)。

## 我的网站 a11y 友好吗？

你可以在[这里](https://webhint.io/)查看你的网站在可访问性(速度、安全性等)方面的排名。

还有什么要补充的吗？如有任何问题、评论或迷因，请随时联系。