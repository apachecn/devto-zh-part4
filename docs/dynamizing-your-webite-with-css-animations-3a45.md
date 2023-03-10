# 用 CSS 动画动态化你的网站

> 原文：<https://dev.to/crimsonmed/dynamizing-your-webite-with-css-animations-3a45>

最近，我第二次尝试 CSS 动画。我第一次看到它的时候，它看起来就像一个廉价版的 flash。然而，我发现你可以很容易地做一些简单的动画来吸引你的观众。

## 目录

*   [简介](#introduction)
*   [标志&结果](#logo-and-result)
*   [HTML 布局](#html-layout)
*   [点燃火焰](#building-flame)
*   [构建粒子](#building-particle)
*   [火焰动画](#animation-flame)
*   [粒子动画](#animation-particle)
*   [发光动画](#animation-glow)
*   [结论](#conclusion)

## 简介

在这个博客中，我们将看看如何创建一个简单的动画来帮助网站动态化。运动吸引注意力，因此 CSS 动画非常适合吸引你的听众。

但是请记住不要做太多的动画，我们的目标不是让观众失去兴趣。在这篇博客中，我们将看看如何制作一个动画标志来吸引你的观众。

我们将会看到我在我的临时登陆页面上制作的动画。 [https://火. dev/](https://%E7%81%AB.dev/)

## 标识&结果

下面是这个标志的样子:

[![Kasai Logo](img/d264d89ebf1994c67ca7a4c9e6ab0e02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SEGBFXsK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/90ac2at0er3zvpfzptfz.png)

这是什么标志将看起来像动画

[![Kasai animated](img/74eea7db27665b582918cc0c40c662d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ab3X-T_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ir4gt3joc7ncl0p7jmry.gif)

## HTML 布局

```
<div class="fire">
    <div class="fire-left">
        <div class="main-fire"></div>
        <div class="particle-fire"></div>
    </div>
    <div class="fire-main">
        <div class="main-fire"></div>
        <div class="particle-fire"></div>
    </div>
    <div class="fire-right">
        <div class="main-fire"></div>
        <div class="particle-fire"></div>
    </div>
    <div class="fire-bottom">
        <div class="main-fire"></div>
    </div>
</div> 
```

我们可以看到这里有四个火组件:

*   左边的火焰
*   中间的火焰
*   合适的火焰
*   底部发光

## 建筑火焰

我们将看看中间的火焰，因为左边和右边只是一个较小的版本。

下面是 CSS 代码:

```
.fire-main {
    position: absolute;
    height: 100%;
    width: 100%;
    animation: scaleUpDown 3s ease-out;
    animation-iteration-count: infinite;
    animation-fill-mode: both;
}

.fire-main .main-fire {
    position: absolute;
    width: 100%;
    height: 100%;
    background-image: radial-gradient(farthest-corner at 10px 0, #d43300 0%, #ef5a00 95%);
    transform: scaleX(0.8) rotate(45deg);
    border-radius: 0 40% 60% 40%;
    filter: drop-shadow(0 0 10px #d43322);
} 
```

作为父 div，我们确保它使用父 div 的所有高度和宽度。我们也是在这个潜水点拍摄动画。

我们说动画应该为`3s`和`ease-out`播放，并且应该为`infinite`迭代。

我们复制了火焰。这很简单。使用`border-radius: 0 40% 60% 40%;`我们可以创建一个漂亮的泪滴，然后用`transform: scaleX(0.8) rotate(45deg);`完成定位和缩放

## 构建粒子

为了让火焰更加真实，让我们制作一些漂浮的小粒子。

```
.fire-main .particle-fire {
    position: absolute;
    top: 60%;
    left: 45%;
    width: 10px;
    height: 10px;
    background-color: #ef5a00;
    border-radius: 50%;
    filter: drop-shadow(0 0 10px #d43322);
    animation: particleUp 2s ease-out 0;
    animation-iteration-count: infinite;
    animation-fill-mode: both;
} 
```

由于`width`和`height`具有相同的`10px`值，再加上一个`border-radius: 50%`，这给了我们一个圆。加入一点点`drop-shadow`能够真正给人一种深度感。

## 火焰动画

flam 动画的想法非常简单。我们从正常尺寸开始，然后让火焰长高，然后变小，同时变薄。

```
@keyframes scaleUpDown {
    0%,
    100% {
        transform: scaleY(1) scaleX(1);
    }
    50%,
    90% {
        transform: scaleY(1.2);
    }
    75% {
        transform: scaleY(0.95);
    }
    80% {
        transform: scaleX(0.55);
    }
} 
```

让我们按时间(动画完成的百分比)来看看这个:

*   **0%** 火焰被缩放到`(1,1)` (x，y)
*   **50%** 更高的火焰缩放到`(1, 1.2)`
*   **75%** 较小的火焰缩放到`(1, 0.95)`
*   **80%** 稀薄火焰缩放至`(0.55, 0.95)`
*   **90%** 更高的火焰缩放到`(0.55, 1.2)`
*   **100%** 正常火焰缩放至`(1,1)`

## 粒子动画

粒子动画非常简单，它是一个简单的上升动画，混合了一些不透明度的变化。

```
@keyframes particleUp {
    0% {
        opacity: 0;
    }
    20% {
        opacity: 1;
    }
    80% {
        opacity: 1;
    }
    100% {
        opacity: 0;
        top: -100%;
        transform: scale(0.5);
    }
} 
```

让我们按时间(动画完成的百分比)来看看这个:

*   **0%** 火焰是看不见的`opacity: 0;`
*   **20%** 火焰可见`opacity: 1;`
*   **80%** 火焰可见`opacity: 1;`
*   **100%** 火焰移到顶端`top: -100%;`看不见`opacity: 0;`变小`transform: scale(0.5);`

如果你不习惯 CSS 动画，你可能会想为什么我们要给 **20%** 和 **80%** 同样的线。动画的工作方式和上次的变化一样。因此，如果我没有从 **20%** 到 **100%** 的 **80%** ，它会慢慢地将不透明度变为 0。然而，如果我们想要一个更快的不透明度变化，并且只在最后说 **80%** 的时候说`opacity: 1;`，那么它只会变成在 80%和 100%之间的`0`

## 发光动画

这个火焰动画的最后一点是光晕。为此，我们有一个非常简单的 div，样式如下:

```
.fire-bottom .main-fire {
    position: absolute;
    top: 30%;
    left: 20%;
    width: 75%;
    height: 75%;
    background-color: #ff7800;
    transform: scaleX(0.8) rotate(45deg);
    border-radius: 0 40% 100% 40%;
    filter: blur(10px);
    animation: glow 2s ease-out 0;
    animation-iteration-count: infinite;
    animation-fill-mode: both;
} 
```

这个 css 的大部分和我们在上面看到的简单火焰是一样的，主要的不同来自于`filter: blur(10px);`这将给我们的火焰形状一点模糊。

看一下发光动画:

```
@keyframes glow {
    0%,
    100% {
        background-color: #ef5a00;
    }
    50% {
        background-color: #ff7800;
    }
} 
```

我们简单地看到我们把颜色从深橙色变成浅橙色。

## 结论

我们可以很容易地看到，仅使用 HTML 和 CSS3，我们就可以真正抓住用户对徽标的注意力。这可以很容易地帮助分支。这可以用在一个非常简单的登录页面上，比如一个双面板(logo 在左边，登录表单在右边)或者一个简单的 logo 在登录表单上面。