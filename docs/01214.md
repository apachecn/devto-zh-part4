# 大脑和颜色对比

> 原文：<https://dev.to/dancingkoala/brain-color-contrast-2c65>

下面是一个关于我们人类成为我们感知的奴隶的小故事。

和大多数人一样，我知道我的大脑在解释我的眼睛所看到的东西时会很有趣，但这在我的软件开发中从未发生过。

我为我的一个客户做了一个网站的第一个版本，它包含几个白色背景上的浅灰色横幅，如下所示:

[https://jsfiddle.net/fkmzLsda//embedded/result,html,css//dark](https://jsfiddle.net/fkmzLsda//embedded/result,html,css//dark)

这里没什么疯狂的。对于第二个版本，我的客户让我给背景添加纹理，比如纸的纹理。好吧，我去[透明纹理](https://www.transparenttextures.com/)，抓住“纸米色”的一个，并将其应用到我的身体标签。

**WTF！灰色横幅现在变成白色了！**

为什么会这样？Chrome 是否为了易读性而试图增强对比度？我是不是不小心换了一个属性？有人在我的网站上施了巫术吗？

在花了十几分钟浏览了所有可能有问题的标签的 CSS 属性后，我用 [KDE 颜色选择器](https://kde.org/applications/graphics/org.kde.kcolorchooser)检查了横幅的实际颜色。

正如你所料，它和以前一样，变化只是在我的大脑里。由于纹理的灰色比横幅深得多，**对比度从浅灰色对白色(深对浅)变为浅灰色对深灰色(浅对深)。**

你可以用这个 JSFiddle 体验一下[。这种效果比在全屏页面上要小得多，但你仍然可以体验它(我试图嵌入它，但 dev.to 的灯光主题消除了这种错觉)。](https://jsfiddle.net/ao82hrt4/18/)

吸取的教训:在检查你自己的大脑后才检查你的 CSS)