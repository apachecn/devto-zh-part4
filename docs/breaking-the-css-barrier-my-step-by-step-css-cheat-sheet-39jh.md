# 打破 CSS 污名:我的一步一步的 CSS 备忘单。

> 原文：<https://dev.to/lucashogie/breaking-the-css-barrier-my-step-by-step-css-cheat-sheet-39jh>

*[🔥别忘了在推特上关注我！让我们联合起来。](https://twitter.com/lucashogie)* 如果你喜欢这篇文章，欢迎关注我，我会很快推出更多内容(每周至少一篇)！

CSS 并不难，但有时会令人非常沮丧；通常很难找到“正确的”方法来完成你想做的事情。对我来说，我经常觉得我不应该考虑太多的样式，但是使用特定的方法被证明是非常有用的。我建议遵循以下几个步骤，我相信很多资源会对你有用。

我希望这篇文章能帮助你组织好一步一步写好 CSS 的过程。

## 第一步:准备，身体和:根🌟

不知道`:root`是什么？我也没有。看看 CSS-tricks 的这篇文章，他们解释得很好。
第一步是准备你的 CSS 样式，在`<body>`元素和`:root`选择器中设置你想要的任何东西。你可能想用这些来设计一些东西:

*   背景色(`background-color`)
*   文档宽度填充(`padding`)
*   Fonts ( `font-family` , `font-style` , `font-size` )

注意:在`:root`中定义字体大小允许您设置`em`测量的标准(因此您可以将其设置为 16px，1em 将保证 16px)

## 第二步:容器💭

容器类应该是我存储整个应用程序的 div。在这里，我定义:

*   以`min-height`、`100vh`为例
*   以`max-width`、`730px`为例。
*   app 的宽度，一般是`100%`。
*   我是否要用 flexbox 显示:`display: flex;`(为什么不？:D)
*   默认设置是`flex-direction` : `row`，这对于导航条来说很方便，而`column`则适用于自顶向下的应用
*   如何将**水平对齐** : `align-items`
*   如何将**垂直对齐** : `justify-content`

## 第三步:更多容器💻

在你的父 div 中，为应用程序本身放置更多的 div。想使用一个不伤大脑的网格系统吗？看看布尔玛。

*   如果你想突出它，一个不同的背景颜色
*   容器内的宽度，例如`100%`
*   你的应用程序在容器中可以占据的最大宽度，例如可以用`calc(100% - {{your amount of pixels}} )`来完成！
*   边框和/或阴影(`box-shadow`、`border`、`border-radius`)
*   更多 flexbox:你想如何在容器内部对齐事物？
*   将容器内的内容居中(因为它很可能在现在居中的容器内靠左对齐):`align-items: center;`

## 最后一步，第四步:元素

在这一步之前，它主要是沿着每一步进行的，你可以很容易地设计基础。现在，你需要掌握方向盘。我无法决定你的应用程序中包含哪些元素，但我可以给你一些最佳实践:

*   确保练习非常清晰的造型
*   您的 CSS 可能无法在所有浏览器上运行！！如果你想知道 CSS 能做什么和不能做什么，看看这个神奇的应用程序。
*   ARIA 属性对于有视觉障碍的人(使用屏幕阅读器)来说很好。点击这里查看一篇关于这个[的文章。](https://dev.to/lkopacz/beginning-to-demystify-aria-5bi5)
*   **偷！**使用 CSSscan 对学习 CSS 超级有用。如果你看到一个看起来很神奇的表单，不要害怕去看看 CSS，自己找出怎么做。选择你喜欢的 CSS，修改它，使它成为你自己的，并为你节省了时间而自豪。不要[走极端，尽管](https://medium.com/@zreitano/on-behalf-of-hims-patients-thank-you-for-copying-ro-aba99193d231)(真正有趣的文章，去读吧！)
*   动画是`a w e s o m e`，如果你不过度使用它，它会澄清事情。所以**用吧！**这里有一篇[关于那个](https://dev.to/neshaz/css-animation-how-to-make-things-move-b02)的文章——虽然我也会写一些关于 CSS 动画的，有一些有趣的特殊用途。
*   框架很有趣，但是不要忘记**学习基础知识**！很容易陷入使用像 [Bootstrap](https://getbootstrap.com) 、 [TailwindCSS](https://tailwindcss.com) 、[布尔玛](https://bulma.io/)或任何其他现在正在流行的框架，但是学习如何设计自己是重要的。这些框架是暂时的。造型和设计的实际技能是永恒的，并将永远使你受益。此外，当有人查找您正在使用的框架，结果发现您并没有使用时，您看起来非常酷😎⛱
*   [渐变](https://serfo.com/p/css-gradients/)和[斑点](https://www.blobmaker.app/)
*   [CSSreference](https://cssreference.io/) 和 [HTMLreference](https://htmlreference.io/)