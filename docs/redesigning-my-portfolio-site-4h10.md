# 重新设计我的投资组合网站

> 原文：<https://dev.to/theryanfurrer/redesigning-my-portfolio-site-4h10>

~~**编辑**:*2019 年 8 月 19 日上午 9 点 45 分*
我已经把这个分行和我的总分行合并了。所有链接现在都指向我当前的网站， [ryanfurrer.dev](https://www.ryanfurrer.dev/)~~

**编辑**:*2019 年 8 月 22 日上午 7 点 10 分*
我发现了 Netlify 做的这个很酷的事情，它给所有推送到原点的东西一个唯一的 URL——这意味着我仍然可以显示我的[旧网站](https://5d408fe1ad61da000acea425--ryanfurrer-portfolio.netlify.com/)！

### 相关链接

如果你只是想看看新旧网站，你可以点击下面的链接:

*   [旧投资组合网站](https://5d408fe1ad61da000acea425--ryanfurrer-portfolio.netlify.com/)
*   [新的投资组合站点](https://ryan-furrer-v2.netlify.app/) -在我与主分支机构合并之前，位于临时网络域名

* * *

# 重新设计很有趣

在我旅程的早期，我创建了我的初始作品集网站，作为我的第一个“大”HTML & CSS 项目。在我看来，这是一个非常基础但不是 T2 最差的网站。

由于我将在接下来的 1-1.5 个月，即 2019 年 9 月中下旬申请工作，我想重新创建将容纳我的项目的投资组合网站。

## 为什么要重新设计？

我想重新设计我的网站有几个原因:

*   在过去的几个月里，我学到了很多东西，想把它们都应用到一个项目中
*   我觉得目前的网站不值得重新分解代码，我宁愿从头开始
*   我很难长时间看同一个设计。

*   虽然这是一个玩笑，但我经常说我有一个问题，因为我每周都会换一次手机的主屏幕(Android)😂

## 设计流程

在为我的网站选择外观和感觉时，我想创造一种轻松(感觉，而不是颜色)、干净、简单和有意义的感觉。页面上的所有内容都必须有目的。

> 我提到颜色是因为在我的网站上会有黑暗模式切换

我也知道我想在其中加入一个“卡片”的概念。这些卡片将包含网页的实际内容。

[![Blog section of https://www.ryanfurrer.dev/](img/a2ff9dbc323af585949659d5e6dd7a3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_VuXFnKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d74p1sxif3lk1900lyj7.PNG)

这些是我出发时仅有的真正的总体想法。相反，我首先通过用 HTML 创建内容来创建页面的框架。对于我还没有决定内容的部分，我只是放置了占位符文本。

接下来，我制作了我的横幅。我知道我想在加载页面时占据 100%的屏幕，所以我将`<header>`的高度设置为`100vh`。你可以在下面的手机和桌面上看到它。

[![My portfolio site's landing page on a Dell XPS 13 Laptop](img/0da074e68c0f4f6b3b1cf7bc61bd84fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Edq0Ljem--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhreyxcidtaei0crdmzj.png)

### 先移动

你可能听说过这种建立现代网站的“移动优先”的方法。虽然我个人不喜欢设计移动网站，我认为它们很无聊，但我觉得从小到大更容易。

“卡片”设计在移动和桌面上都很重要，所以我创建了一个名为`.content-container`的类，并将其应用到我的页面上我想要的每个部分——可重用 CSS 是一个美丽的东西。

我也知道我不想在我的页面顶部使用标准的移动导航。汉堡菜单很棒，但是它们到处都在使用，甚至在一些桌面网站上。你可以在下面看到，标题中的我的移动网站导航看起来和在大屏幕上一样。

[![My portfolio site's landing page on an Apple iPhone X](img/6cc73852e419af85f9c3d205cd45e086.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2_VUfLP3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eb2ubda8ijoe4ixonkt9.png)

## 优化

这是我第一次真正利用谷歌的 [Lighthouse 审计](https://developers.google.com/web/tools/lighthouse/),这是内置在开发工具中的。这是一个神奇而强大的工具，如果你还没有，我强烈推荐你去看看。

审计将你的视线分为四个不同的部分:性能、可访问性、最佳实践和搜索引擎优化。

表现是你的网站表现得有多好。加载的速度有多快，图像的优化程度有多高，代码的精简程度等等。

可访问性目前是一个非常大的话题，而且理所当然。对于那些视力受损的浏览网页的人来说，他们需要一种导航方式，很多人使用一种叫做[屏幕阅读器](https://www.afb.org/blindness-and-low-vision/using-technology/assistive-technology-products/screen-readers)的东西。Lighthouse 确保每个人都能访问你的网站，这也是我推荐使用它的最大原因之一。

**最佳实践**就是这样，确保你使用可能的最佳编码实践。其中一个我经常忽略的是“*避免请求页面加载的通知权限*😒

如果你想让你的网站出现在搜索结果中，SEO 非常重要。SEO 代表“搜索引擎优化”，虽然我不是这方面的专家，但谷歌是，这意味着灯塔是。

我认为 Lighthouse 最好的一点是，如果你不理解它告诉你做的事情，审计的每个部分都有一个“了解更多”的链接！

<figure>

[![Lighthouse results on new portfolio site](img/83c6fd1a6d4c71547bc13ec1eadcc72a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jFL7w5Dd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3gbqy7gowcoypyptcd0b.png)

<figcaption>I still have some work to do but I hope to have straight 100's by the end of the weekend</figcaption>

</figure>

## 事情还没做完

这些是我想在将这个分支与主分支合并之前完成的项目。当这完成后，它将取代目前的网站在[我的领域](https://www.ryanfurrer.dev/)。

*   修复过于压缩的照片
*   最终确定字体大小
*   将 LinkedIn 链接添加到页脚
*   添加收藏夹图标
*   清理代码

*   多余/不必要的 CSS
*   确保 HTML 是语义化的

*   调整边距和填充

*   增加边距/填充，以留出更多空间来透气
*   确保它在该在的地方都是平等的

*   给网站添加黑色主题

*   我将使用 [ezToggle](https://dev.to/ryan_furrer/simple-javascript-theme-toggle-46ea)

*   当经过标题时添加浮动导航

## 关闭思绪

我非常喜欢这个项目，迫不及待地想开始下一个项目。如果你一直在等着看新网站，[这里就是](https://www.ryanfurrer.dev/)！

我真的很感谢你花时间阅读我的文章，如果你有任何问题、意见和建设性的批评，请在下面的评论中或在 T2 的推特上联系我...任何事！

~~**编辑**:*2019 年 8 月 19 日上午 9 点 45 分*
我已经把这个分行和我的总分行合并了。所有链接现在都指向我当前的网站， [ryanfurrer.dev](https://www.ryanfurrer.dev/)~~

**编辑**:*2019 年 8 月 22 日上午 7 点 10 分*
我发现了 Netlify 做的这个很酷的事情，它给所有推送到原点的东西一个唯一的 URL——这意味着我仍然可以显示我的[旧网站](https://5d408fe1ad61da000acea425--ryanfurrer-portfolio.netlify.com/)！