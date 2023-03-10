# 重建旧学校艺术字教会了我什么关于 CSS

> 原文：<https://dev.to/valeriecodes/what-recreating-old-school-wordart-taught-me-about-css-2200>

几周前，在被纯 CSS 的奇妙效果彻底激发后，我有了尝试用纯 CSS 重新创建微软 Word 艺术字的想法。

[![WordArt options in Microsoft word, Image via WikiHow](img/d4d29de6bd9189e7c0f6a020cc53e056.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SBS9rYEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b6ioxo75nb51v8gyezp4.png)

艺术字是我 2000 年早期文字经验的主要组成部分，对我初中和高中的大脑来说，它就是让任何印刷材料看起来像✨polished✨.的东西

在 Word 的最新版本中，它已经变得低调了，但我想去寻找怀旧因素，重现经典。

[![Grid of WordArt options in Word 2019](img/ae9784efa48fca9afbc0616ae9fffd29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vIy0q5Em--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltq98oknkbly07bqse1a.png)

我不是第一个有这种想法的人。有几个 [网站](https://codepen.io/kathykato/pen/omxPap)可以在网上生成现代艺术字，但我注意到这些网站似乎没有原始艺术字功能的所有选项，所以我把这作为提高 CSS 水平的一个机会，并尝试自己实现它们。

事实证明，这说起来容易做起来难，这些在线艺术字生成器缺少一些经典的东西是有充分理由的。

[![My attempt at the tapered text using varying font sizes](img/2e4f212feaf0f04a7677e264fd9011d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3YtCYyko--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u7x5ulkfkbwannj3wo1g.png)

我发现的第一件事是 CSS 没有一个很好的方法来“扭曲”文本。您可以让文本跟随 SVG 的路径，但是您不能像第一个图像上的第三个示例那样扭曲文本的形状。这可能有合理的可访问性原因，因为它会使文本更不清晰。在 CSS 中以一种健壮的方式实现它也可能是困难的(而且它可能不是一个非常有用或需要的特性)。我试图用不同的字体大小来近似它，结果并不太好。上图。

[![Sparkling WordArt text](img/5b13cdafdb5ba6f9d5f88595540c7b75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2gQjL1H4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3kqjjgr6w5u6osoxe7jz.png)

将图像设置为文本填充也比您想象的要困难。尽管如此，它只在 WebKit 中受支持。这里有个[教程](https://css-tricks.com/image-under-text/)。设置渐变为文本填充需要[一个类似的过程](https://css-tricks.com/snippets/css/gradient-text/)。

[![Curved text along an arc](img/29b9529fca045c467df07f7ecc0e2cbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ytI7xhGG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g15dwyy1m3arhy3lsgam.png)

正如我前面提到的，沿着 SVG 路径换行是可能的，但我认为这很难适应任意长度的文本(例如，根据所使用的文本，有一条变得更长的曲线)。我喜欢的示例艺术字生成器通过使用 [CSS 转换](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)属性而不是 SVG 来解决倾斜文本的问题。

除了这几个挑战之外，我对大多数艺术字可以用 CSS 很好地完成印象深刻。这就是为什么要避免在图片中嵌入文本的原因(除了现有的可访问性)。我所有的 CSS 艺术字作品都可以在[这个 Codepen](https://codepen.io/valeriecodes/pen/dxzGOz) 上找到。