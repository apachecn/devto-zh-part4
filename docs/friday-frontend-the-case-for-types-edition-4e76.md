# 星期五前端:类型版本的情况

> 原文：<https://dev.to/kball/friday-frontend-the-case-for-types-edition-4e76>

快乐[13 号星期五](https://en.wikipedia.org/wiki/Friday_the_13th)！希望你只有好运气，没有坏运气，你的周末计划完全是极好的。:P

本周我们已经有了一些很棒的文章，但是引起我注意的是关于 TypeScript 3.7 的声明和关于在 JavaScript 中使用类型的相关文章。下一版本的 TypeScript 的 5 个主要特性中有 3 个是直接来自 TC39 的，这一事实和 JavaScript 的发展提醒我这两种语言在许多方面是紧密相连的。随着 JavaScript 的发展，两者之间的差异可能会越来越小。

周末愉快！尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [与 CSS 变量的逻辑运算](https://css-tricks.com/logical-operations-with-css-variables/)

这完全让我大吃一惊。布尔逻辑完全在 CSS 变量中使用 calc，让你建立一些非常非常酷的结构来生成图像&用 CSS 做其他整洁的事情。

##### [CSS 安全漏洞](https://css-tricks.com/css-security-vulnerabilities/)

看看一些与 CSS 漏洞相关的常见问题…主要是展示它们根本不是问题，因为浏览器已经考虑到了它们。有趣的阅读。

##### [《我最喜欢的 CSS hack》](https://dev.to/gajus/my-favorite-css-hack-32g3)

这是一个很好的快速点击的代码片段，可以让你一目了然地看到所有不同的元素在 DOM 中是如何排列的。

##### [在保留边框半径的同时扩展一个方框的各种方法](https://css-tricks.com/various-methods-for-expanding-a-box-while-preserving-the-border-radius)

非常酷的探索不同的方法来扩展一个盒子，以及它们对盒子的属性和盒子里面的东西在扩展过程中的影响。文字会移动吗？边界半径会改变吗？事实证明，这取决于你如何去做...

#### JavaScript

##### [TypeScript 3.7 的 5 大功能及其使用方法](https://dev.to/pimterry/the-5-big-features-of-typescript-3-7-and-how-to-use-them-1bfn)

这真的很有趣，因为被吹捧和解释的 5 个特性中有 3 个实际上是 JavaScript 即将推出的特性。顶级等待、无效合并和可选链接都在 TC39 的第三阶段，很快将在 JS 中正式发布。因此，即使您通常不使用 TypeScript，但是您想走在 JavaScript 的前面，您也可能想看一看。

##### [你已经使用类型](https://www.swyx.io/writing/types-already)

关于类型和 TypeScript 的主题，这是一篇针对类型怀疑论者的优秀的近期文章。介绍我们已经在使用(隐式)类型的一些方法，甚至在动态类型的语言如 JavaScript 中，以及转移到更显式/编译器检查类型如 TypeScript 中的优势。

##### [React 为什么要这样做？](https://gist.github.com/sebmarkbage/a5ef436427437a98408672108df01919)

这是一个脑筋急转弯，但我从中学到了一些东西。React 的核心开发人员之一对 React 正在做出的一些设计选择进行了一次头脑风暴，特别是与 Vue.js 和 Svelte 的对比。

##### [如何用 Svelte 3 构建一个 Unsplash 搜索 App](https://dev.to/ayoisaiah/how-to-build-an-unsplash-search-app-with-svelte-3-4h0e-temp-slug-6430771)

我目前正在玩 Svelte，并且很喜欢它，这个教程足够深入，可以让你切实体会到 Svelte 的味道，同时又不会太让人不知所措。此外，Unsplash 是一个非常酷的免费资源，可以通过令人难以置信的许可获得照片。下一次我需要库存摄影时，我会先尝试一下。

##### [如何学习 D3.js](https://wattenberger.com/blog/d3)

这是 D3 上的杰作，D3 仍然是制作数据可视化的最好的 JavaScript 库之一。D3 一直在我的“学习”列表中，我只是在等待一个项目给我一个好机会…当它出现时，我会回到这篇文章，因为男孩这是一个好机会。

#### 其他牛气

##### [用 Tensorflow.js 为前端开发者提供机器学习](https://www.smashingmagazine.com/2019/09/machine-learning-front-end-developers-tensorflowjs/)

看看如何让 tensorflow 在 web 浏览器中运行，并提示一些可能性。还谈到了在浏览器中而不是在服务器上做这件事的局限性。

##### [原子设计与 UI 组件:理论到实践](https://blog.bitsrc.io/atomic-design-and-ui-components-theory-to-practice-f200db337c24)

对原子设计及其工作原理的全面解释。我认为拥有一种可定义的方法和语言来谈论你的设计和设计系统是非常有价值的，虽然我不是原子设计所使用的特定语言的狂热爱好者，但这种方法是一流的。

##### [can email](https://www.caniemail.com/)

Ooohhhh 这个网站早就应该出现了，它相当于专门针对 HTML 邮件的“caniuse”。天哪，我真希望几年前我在为电子邮件基金会工作的时候也有这个东西。

##### [为什么我在不需要预加载字体的时候还要预加载字体](https://www.alexrp.co.uk/blog/preloading-fonts.html)

我喜欢在现代网络中我们可以使用各种各样的字体；当我们不得不坚持“网络安全”时，我们的设计选择感觉如此陈旧。然而。字体对于第一页的加载性能来说是一个巨大的问题，如果你不小心的话，它们真的会降低你的速度。这篇文章精彩地讲述了一个人寻求快速体验自定义字体的过程。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】