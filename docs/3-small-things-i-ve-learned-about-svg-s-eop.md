# 我从 SVG 中学到的 3 件小事

> 原文：<https://dev.to/bjornlindholmdk/3-small-things-i-ve-learned-about-svg-s-eop>

SVG 很棒。而且不是超级复杂。但是在过去的几年里，我学到了一些让我的 SVG 看起来更好的技巧。

## 1。将所有图标放在共享大小的背景上

你可能在某个时候已经下载了一个图标包，比如材质图标。如果你在 Sketch 中打开这些图标中的一个，你会看到它位于 24px 24px 的背景上。

我以前觉得这个超级讨厌。这导致了对齐问题，我不喜欢图标周围的这个隐含框。但事实证明，这种疯狂背后是有意义的。

SVG 图标可以有许多不同的形状。有些很宽，有些很高。通过使用一个共同大小的背景，它可以很容易地调整图标的大小，没有任何问题。它确保所有图标都被正确缩放。

假设你的设计说所有图标都应该是 20px 宽。你使用的第一个图标是宽的，在设计中看起来不错。但是下一个图标的高度大于宽度，现在你遇到了问题。高图标将是 20px 宽，但比其他图标高很多。它的比例会有所不同，看起来会更大。

[![](img/3797f513f139114c4b01229b515f1bce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y2aT8BIm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4tunltatdjfn5wk0t92r.png)

## 2。将文本转换为轮廓

您刚刚为您的副业项目创建了一个新的徽标。它有公司名称，旁边还有一个自定义形状。超级爽。你把它导出为 SVG 并放到你的网站上。但是看起来不太对。你用的全新字体不见了，它看起来像新罗马时代。

这个错误我犯过无数次了。问题是 SVG 使用了文本元素。此文本元素正在使用一种字体。如果您使用标准的 web 字体，就不会出现这个问题。但是，如果你使用自定义字体，而不在网站上导入，那么它就不会工作。

这样做的危险在于，在部署到服务器之前，您可能不会注意到。因为你在自己的机器上设计了徽标，所以你很可能在本地安装了该字体。但是没有安装在服务器上。

有几种方法可以避免这个问题。最简单的一个是在网站上包含字体。这可能会占用很多额外的带宽，如果你不在网站的其他部分使用这种字体，我不推荐这种方法。

在我看来，更好的方法是将文本转换成路径。这可以在 Illustrator 或 Sketch 等任何矢量设计 app 中完成。

只要确保保留一份原始文本的标志副本，以防你不得不调整文本样式或文本本身。

[![](img/4bddd7119e88daff80fc29590e063f2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ur-ywhBi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ur0p4j6fyshi1yygq7z1.png)

## 3。使用同一图标包中的图标

这与其说是技术建议，不如说是设计技巧。过去，我习惯于在 FlatIcon 上单独查找所有图标。这个的问题是都是不同风格的设计。它们在笔画、形状和其他小的设计元素上各不相同。这导致了不一致和混乱的设计。

现在我总是挑选一个图标包，并确保只使用该包中的图标。这意味着有时要妥协寻找一个完美的图标，如果它不在那个包里的话。我觉得这个取舍是值得的。

[![](img/b54c1bea22b2ff6608f9432c6675971a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lSMTBivE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yzicypcnfx8ayzzzvx66.png)

## 结论

这些是我在过去几年中了解到的关于 SVG 的一些小问题。对你来说这似乎是显而易见的，但是在这些事情成为问题之前，我从来没有想过这些事情。