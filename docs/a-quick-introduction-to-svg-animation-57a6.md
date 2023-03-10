# SVG 动画快速介绍

> 原文：<https://dev.to/manuelricci/a-quick-introduction-to-svg-animation-57a6>

在过去的几天里，我和 SVG 做了很多工作。多年来，我使用 Illustrator 制作图形，但我从未接触过我所做的东西的源代码，更不用说动画了。

为了更好地理解这项神奇的技术，我决定更深入地研究这个主题，混乱的路径和坐标变得更加清晰。

这是我在 DEV.to 上的第一篇文章，我想和你分享我关于 SVG 的知识，这样你就可以为你的下一个项目增添趣味。

## 关于 SVG 的小回顾

如果你不知道什么是 SVG，不要害怕，读一下这个简单的解释。

SVG 代表可缩放矢量图形，这是一种能够可视化矢量图形对象的技术，因此，可以从维度上管理可缩放图像。

SVG 是 W3C 标准化的，你可以在这里阅读文档。

## 开始了

既然每个人都清楚 SVG 的定义，那么让我们深入研究一下。

让我们开始讨论 SVG 本身，我们想要制作动画的对象。我认为没有人从头开始用代码创建 SVG，因为你可以使用 Adobe Illustrator 或 Inkscape(有很多软件允许创建 SVG)。

为了这个介绍，我在 Illustrator 上创作了经典的蝙蝠侠标志
[![Classic Batman Logo](img/b875687f6a5baa50b37991759584b81a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PIEkh7l9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/MVJEKka.jpg)

这就是代码

```
<?xml version="1.0" encoding="utf-8"?>
<!-- Generator: Adobe Illustrator 23.0.0, SVG Export Plug-In . SVG Version: 6.00 Build 0)  -->

<style type="text/css">
    .st0{fill:none;stroke:#010101;stroke-miterlimit:10;}
</style>
<path class="st0" d="M340.44,418.45c-2.83,4.36-7.29,8.3-13.24,11.72c-2.98,1.72-6.2,3.22-9.8,4.59c0.98-1.21,1.97-2.47,2.86-3.83
    c1.06-1.61,2.45-4.05,2.33-6.68c-0.11-2.32-1.31-4.54-3.08-5.66c-1.95-1.24-4.85-1.32-7.06-0.2c-2.55,1.3-3.85,4.14-5.06,6.94
    c-0.49-0.64-0.99-1.3-1.51-1.94c-1.05-1.3-2.24-2.6-3.76-3.33c-1.84-0.87-4.23-0.94-6.09-0.16c-2.28,0.96-3.8,3.16-4.98,5.19
    c-1.13,1.93-2.12,3.98-3.2,6.66c-0.96,2.35-1.83,4.8-2.67,7.18c-0.85-2.38-1.73-4.83-2.67-7.18c-1.09-2.67-2.07-4.73-3.2-6.66
    c-1.19-2.03-2.7-4.23-4.98-5.19c-0.87-0.37-1.86-0.55-2.85-0.55c-1.13,0-2.26,0.24-3.24,0.7c-1.52,0.73-2.7,2.03-3.76,3.33
    c-0.52,0.64-1.02,1.3-1.51,1.94c-1.21-2.79-2.5-5.65-5.06-6.94c-2.21-1.12-5.11-1.04-7.06,0.2c-1.77,1.12-2.97,3.34-3.08,5.66
    c-0.12,2.62,1.27,5.06,2.33,6.68c0.89,1.36,1.89,2.62,2.86,3.83c-3.61-1.37-6.82-2.87-9.8-4.59c-5.95-3.43-10.41-7.37-13.24-11.72
    c-3.27-5-4.36-10.5-3.1-15.47c0.73-2.84,2.31-5.89,4.6-8.8c2.94-3.75,6.82-6.99,11.87-9.89c3.3-1.9,6.9-3.56,10.68-4.91
    c2.55-0.91,5.3-1.73,8.39-2.5c-1.2,1.44-2.02,2.49-2.78,3.64c-1.5,2.28-2.35,4.34-2.59,6.31c-0.26,2.08,0.16,4.17,1.19,5.89
    c0.94,1.59,2.42,2.94,4.16,3.81c1.93,0.96,4.09,1.28,6.09,0.9c1.2-0.23,2.34-0.71,3.27-1.4c2-1.48,3.36-4.05,4.16-7.88
    c0.44-2.1,0.62-4.16,0.79-6.17l0.04-0.46c0.21-2.39,0.45-4.85,0.69-7.23l3.88,5.75h7.91l3.88-5.75c0.24,2.38,0.48,4.84,0.69,7.23
    l0.04,0.46c0.17,2.01,0.36,4.07,0.79,6.17c0.8,3.82,2.16,6.4,4.16,7.88c0.93,0.69,2.06,1.18,3.27,1.4c2,0.38,4.16,0.05,6.09-0.9
    c1.75-0.87,3.22-2.23,4.16-3.81c1.02-1.73,1.43-3.81,1.19-5.89c-0.24-1.97-1.09-4.03-2.59-6.31c-0.75-1.14-1.57-2.19-2.78-3.64
    c3.1,0.77,5.85,1.59,8.39,2.5c3.79,1.35,7.38,3.01,10.68,4.91c5.05,2.91,8.94,6.15,11.87,9.89c2.28,2.91,3.87,5.95,4.6,8.8
    C344.81,407.95,343.7,413.44,340.44,418.45z"/>
 
```

Enter fullscreen mode Exit fullscreen mode

## 优化代码

在继续之前，我使用 [SVGOMG](https://jakearchibald.github.io/svgomg/) 来优化从 Illustrator 生成的代码。只需粘贴代码，工具就会完成剩下的工作。默认设置很好，但是你可以随意使用它们来获得更高或更低的优化。

优化后的矿用 SVG 轻了 88%。

单击图标复制优化的代码，并将其粘贴到代码编辑器中的新 HTML 文件中。

## 设置操场

创建一个新的 CSS 文件，只是为了演示的目的，将 SVG 放在视口的中心，怎么做呢？很简单，SVG 元素是一个块元素，所以这里没有魔法

```
svg {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

我想实现的是动画的轮廓，使它看起来像是正在绘制。

首先我们需要定义一个初始状态，为此我们需要两个属性 [`stroke-dasharray`](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-dasharray) 和 [`stroke-dashoffset`](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/stroke-dashoffset) 。这两个属性的作用很简单:

*   `stroke-dasharray`定义形状轮廓中的虚线和间隙的模式。
*   `stroke-dashoffset`定义相关虚线阵列渲染上的偏移。

这两个属性你需要用哪个数字，嗯，你可以猜一下，也可以用 Javascript 找到合适的。

### 让我们找出路径长度

要找出正确的路径长度，您可以使用下面的代码:

```
let path = document.querySelector('path');
let pathLength = path.getTotalLength();
console.log(pathLength) 
```

Enter fullscreen mode Exit fullscreen mode

更高级的解决方案可能是用 Javascript 修改 CSS 变量，但如何理解取决于您:)

上面的代码将在控制台中打印路径长度。这个数字是我们需要给`stroke-dasharray`的数字，也是给`stroke-dashoffset`的相同数值，但为负值。

## 创建动画

为了给轮廓添加动画，我们需要使用我们的老朋友`keyframes`

```
path {
  stroke-dashoffset: -1419;
  stroke-dasharray: 1419;
}

@keyframes draw-logo {
  from {
    stroke-dashoffset: -1419;
  }
  to {
    stroke-dashoffset: 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码是什么意思？我们创建一个初始断点，其中`stroke-dashoffset`被设置为路径长度的负值(在我的例子中是-1419，如果你使用不同的数字，你将会不同),然后我们设置一个最终断点，其中`stroke-dashoffset`被设置为 0。换句话说，我们可以把这个动画翻译成“从隐藏到完全绘制”。

如果您想要反转线条画，您需要将-1419 更改为 1419。

## 应用动画

现在我们已经有了动画，我们可以将它应用到 SVG 路径。为此，我们需要使用`animation-*`属性。

```
path {
  stroke-dashoffset: -1419;
  stroke-dasharray: 1419;
  animation-name: draw-logo;
  animation-duration: 2s;
  animation-iteration-count: 1;
  animation-direction: normal;
  animation-tiiming-function: ease-in;
  animation-fill-mode: forwards;
}

@keyframes draw-logo {
  from {
    stroke-dashoffset: -1419;
  }
  to {
    stroke-dashoffset: 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们分析一下我们写的东西

*   `animation-name`:这是我们在使用`@keyframes`之前创建的动画的名字，非常简单
*   动画需要多长时间完成
*   动画会重复播放多少次
*   `animation-direction`:动画走到哪里，值为`normal`、`reverse`、`alternate`、`alternate-reverse`玩到哪里
*   在他的周期中动画是如何进展的
*   `animation-fill-mode`:动画在执行前后应用了什么风格。

你可以用简写的`animation`，但它不像其他的`font`、`background`等那样真的可以理解。

你可以点击查看最终结果

## 结论

干得好，你已经用 CSS 和一点 JS 创建了你的第一个 SVG 动画。

你怎么想呢?请在评论中告诉我:)