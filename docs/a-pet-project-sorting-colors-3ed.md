# 一个宠物项目:排序颜色

> 原文：<https://dev.to/tchaflich/a-pet-project-sorting-colors-3ed>

安娜·克鲁兹通过 unsplash 拍摄的封面照片。

我被告知谈论我最喜欢的项目:

[![yashints](img/0d36c7c282ebcf4aec1143e95bad3d39.png)](/yashints) [## 宠物项目，保持自我更新的秘密

### 亚塞尔·阿德尔·梅赫拉班 6 月 9 日 194 分钟阅读

#productivity #growth #programming #beginners](/yashints/pet-projects-the-secret-to-keep-yourself-updated-51e0)

所以这里什么都没有...

分类颜色！这是做不到的——嗯，做不到完美。颜色是在 3d 空间中表示的，这里的排序类型是线性表示的，无论你如何分割，这看起来都不太对。

[![A cube made of colorful other cubes, representing the RGB color space.](img/adaa5b438a4d6596018bb1a8f6e06491.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--i6IorW6V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/RGB_Cube_Show_lowgamma_cutout_b.png/800px-RGB_Cube_Show_lowgamma_cutout_b.png) * RGB 色彩空间表示，来源于[维基](https://en.wikipedia.org/wiki/HSL_and_HSV)*

[![A cylinder representing the HSV colorspace, with a slice removed to show the inside.](img/0211b8d4f5e4d1191964a7c414244851.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--judTMb84--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/3/33/HSV_color_solid_cylinder_saturation_gray.png/640px-HSV_color_solid_cylinder_saturation_gray.png) * HSV 色彩空间表示，再次来源于[维基](https://en.wikipedia.org/wiki/HSL_and_HSV)*

[![A bicone representing the Hue/Chroma/Lightness model of color. This is getting wacky.](img/029750b21866519bba6d3a304c0e88d3.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--MxLP2nSY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/thumb/b/b3/HSL_color_solid_dblcone_chroma_gray.png/800px-HSL_color_solid_dblcone_chroma_gray.png) *连颜色是什么？如果我们的眼睛不是真实的，颜色怎么可能是真实的？*

我想问的问题是:哪种方式*最不坏*？

[![A screenshot of my color sorting project. It does not look very good. If you are visually impaired, and that's why you're getting this text, you aren't missing much.](img/39b1e8e5fa98b952e03528b4c9d66457.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TiXk-8aj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vmi316fdzc3k2z9uw33n.png)

太棒了，因为除了我，没人关心这个。我可以对我喜欢的颜色着迷。

到目前为止，在设置这个过程中，我已经了解了很多关于现代构建方法的知识，并且知道我是多么不在乎在一个跨越大约 12 个文件的项目中使用它们。它们给项目的堆栈和设置增加了大量的工作和限制，并且对于从 Atom 运行的 shell 文件中的两行代码来说，这是不值得的。

我也给自己加了一个任意的约束，看看我是否能在不包含任何依赖包的情况下完成它。我们都拿`node_modules`开玩笑，对吧？在撰写本文时，外部加载的是一个字体和 jQuery(我知道，我知道，它是`date('Y')`、*嘘*，除此之外，它只是全球安装的 sass 和 minify。我想挑战自己保持这种精益。

我也在 ye olde jQuery 上刷新了自己。有一段时间了。像是又见到了老朋友。自从我最后一次看见它以来，它变了一点儿！

它还没有完成，但如果你碰巧想在它的内脏还在地板上的时候看看这个，这里就是:

[https://tchaflich . github . io/color-sort/](https://tchaflich.github.io/color-sorts/)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[颜色分类](https://github.com/tchaflich/color-sorts)

### 寻找对颜色排序的方法

<article class="markdown-body entry-content container-lg" itemprop="text">

# 颜色分类

寻找对颜色排序的方法

</article>

[View on GitHub](https://github.com/tchaflich/color-sorts)