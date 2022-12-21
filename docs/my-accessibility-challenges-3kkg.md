# 我的无障碍挑战

> 原文：<https://dev.to/marsackg86/my-accessibility-challenges-3kkg>

感觉我的投资组合网站完成了，决定使用 [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 进行审计。易访问性和搜索引擎优化是我遭受打击最大的地方。

我在可访问性方面的初始分数是 64 分，老实说，这看起来不好，听起来也不错，总体上让我觉得自己完全是个废物。我戴着眼镜，看不清浅色背景和深色字体，那么我的网站为什么要这样设置呢？好吧，这只是更容易亲吻*保持简单愚蠢*它与白色背景和黑色字体。那为什么我会因为对比度不够而被批评呢？老实说，我真的不确定，但谷歌开发工具的神说话了，因此我必须做一些事情，以确保每个人都可以享受我的网站上的内容。我致力于寻找一些工具来帮助我确保那些对比色是额外可及的。

现在，根据 Colorsafe.co 的说法:“WCAG 指南建议小字体的对比度为 4.5，大字体的对比度为 3，即 24px 或 18px 的粗体。”这个网站让你选择背景颜色，字体颜色，甚至为你生成调色板。但是，如果你喜欢使用我在上一篇文章中提到的 [Adobe Color](https://color.adobe.com/create) 或[Coolors.co](https://coolors.co/)，你可以从上述网站提取十六进制或 RGB 值，然后将它们放入 Colorsafe。在我看来，还有一个工具稍微好一点，尽管它看起来不如 Colorsafe.co 漂亮，那就是 [WebAIM](https://webaim.org/resources/contrastchecker/) 。WebAIM 给出了普通文本的分类:WCAG AA & WCAG AAA，大文本:WCAG AA，WCAG AAA，图形对象和 UI 组件:WCAG AA。如果你想了解一下 AA 和 AAA 的区别，请点击[链接](https://digitalaccessibilitycentre.org/index.php/blog/20-diary/187-the-icing-on-the-cake-the-difference-between-aa-and-aaa-compliance)。在 WebAIM 的所有类别中获得通过并为自己成为一个关心他人的伟大人类而击掌的神奇数字是....7!没错，我的朋友们，我最喜欢的数字是对比度的神奇数字。

接着是代表搜索引擎优化的 SEO。我得了 68 分，这是因为一些锚标签没有 aria 标签，图像没有 alt 描述，我的 meta 没有这个小宝石:meta name="Description" content= " "。 [aria-labels](https://www.w3.org/TR/WCAG20-TECHS/ARIA14.html) “在不能使用可见标签的地方提供了一个不可见的标签。”套用 moz.com[Alt](https://moz.com/learn/seo/alt-text)的话，如果一个图像文件无法加载，文本对于屏幕阅读器来说确实很方便，它可以帮助那些可爱的搜索引擎爬虫索引图像。对于元描述内容，我只是把我的名字作为作者和页面是什么，像“包含项目链接，简历和社交媒体链接的个人网站。”

经过改变，我现在有一个组合网站，根据灯塔有 100 的性能，93 的可访问性，93 的最佳实践和 100 的搜索引擎优化。它在导航方面仍有一些机会，它希望我在导航栏中的社交媒体图标有一个“可识别”的名称。这些是我的下一个目标，在灯塔的所有类别中获得 100 分。