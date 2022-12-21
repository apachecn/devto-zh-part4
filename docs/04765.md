# 纯 CSS 文本动画

> 原文：<https://dev.to/proticm/pure-css-text-animation-3a6e>

> 最初发布于[devinduct.com](https://devinduct.com/blogpost/46/pure-css-text-animation)

## 快速介绍

回到过去，在 CSS3 及其强大功能被释放出来之前，web 动画严重依赖于 JavaScript，没有它，开发人员只能局限于简单的东西，如颜色和边框*“动画”*，没有动画元素上两种状态之间的平滑过渡。最常见的 CSS 效果是，并且可能仍然是，当用户悬停在元素上时，我们改变元素背景颜色的情况。这是由`:hover`伪类实现的。一旦我们需要更复杂的东西，比如淡入淡出或幻灯片效果，我们就必须使用 JavaScript。

更进一步，像文本动画这样的东西用纯 CSS 是不可能做到的，但是幸运的是，我们已经到了这个行业发展的阶段，并且通过这样做用许多新的酷的和有用的特性丰富了 CSS。在这篇文章中，我们将只使用 CSS 动画我们的文本。更准确地说，我们将使用`transition`和`transform`属性。

> 转换使我们能够定义元素的两种状态之间的转换。不同的状态可以使用类似于`:hover`或`:active`的伪类来定义

这很有用。例如，当我们改变元素宽度时，我们可以根据持续时间、定时和延迟来设置宽度的改变方式。

> 转换 CSS 属性让我们可以旋转、缩放、倾斜或平移元素

在我们的例子中，我们将使用`scale`选项，因为我们需要处理一个元素的大小(从左上角开始的行*“移动”*)，信不信由你，这两个属性就是我们所需要的。其他的都是标准的 CSS，比如宽度，位置，不透明度...等等。不同之处在于，我们可以制作它们的动画，并设置状态之间的转换，如上所述。

好了，我想我们已经准备好开始实现了。请随意复制和粘贴代码，并根据您的需要进行修改。尽情享受吧！

## HTML

```
<div class="box">
    <span class="l-top"></span>
    <span class="l-left"></span>
    <div class="box-inner">
      <span class="hover-me">Hover me!</span>
      <span class="hi">Hi!</span>
      <span class="message">Welcome to the No JS Text Animation</span>
    </div>
    <span class="l-right"></span>
    <span class="l-bottom"></span>
</div>
<div class="box box-next">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. 
    Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
    Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
    Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum
</div> 
```

在我们的 HTML 中，第一个框中的每个`span`元素以及整个第二个框都将被动画化。在第一个框中，文本将从左向右滑动，并在此过程中淡入。文本将被动画线条包围，形成一个从左上角开始的方框。每条线都有其动画持续时间和方向。很漂亮，对吧？

第二个框将向下滑动，有一个箭头从原始文本指向它。所有这些**没有**一行 JavaScript 代码。

整个 CSS 实现如下所示:

## CSS

```
.box {
    margin: 0 auto;
    position: relative;
    color: #feb090;
    width: 570px;
    height: 50px;
    font-size: 2rem;
    box-sizing: border-box;
}

.box-inner {
    position: relative;
}

.box-next {
    opacity: 0;
    transition: all 1s ease;
    font-size: inherit
}

.box > div > span, .box > span {
    display: block;
    transition: all 1s ease;
    position: absolute;
    top:0px;
    left: 0;
    width: 100%;
    height: 100%;
}

.box > div > span {
    top:7px;
}

.box > div > span:not(:first-child) {
    opacity: 0;
}

.box > div > span.hover-me {
    transition-duration: .1s;
}

.box > span.l-top, .box > span.l-bottom {
    border-top: 2px solid #b17b64;
    transform: scale(0, 1);
    transform-origin: left;
    transition-duration: .2s
}

.box > span.l-left, .box > span.l-right {
    border-left: 2px solid #b17b64;
    transform: scale(1, 0);
    transform-origin: top;
    transition-duration: .3s
}

.box > span.l-bottom {
    top: 48px;
    transition-delay: .5s
}

.box > span.l-right {
    left: 568px;
    transition-delay: .5s
}

.box:hover > span.l-top, .box:hover > span.l-left, 
.box:hover > span.l-bottom, .box:hover > span.l-right {
    opacity: 1;
    transform: scale(1, 1);
}

.box:hover > div > span.hover-me {
    left: -150px;
    opacity: 0;
}

.box:hover > div > span.hi {
    opacity: 1;
    left: 14px;
}

.box:hover > div > span.message {
    opacity: 1;
    left: 65px;
    transition-delay: .25s;
    transition-duration: .2s;
}

body > div.box:first-child:after {
    content: '\25bc';
    position:absolute;
    left: 47%;
    top: 50px;
    opacity: 0;
    transition: all .2s ease;
}

body > div.box:first-child:hover:after {
    opacity: 1;
}

.box:hover + div.box-next {
    opacity: 1;
    margin-top: 32px
} 
```

## 拨弄

[https://codepen.io/protic_milos/embed/vYBJLrB?height=600&default-tab=result&embed-version=2](https://codepen.io/protic_milos/embed/vYBJLrB?height=600&default-tab=result&embed-version=2)

这一集 CSS 就这么多。如果你喜欢你所看到的，一定要分享出来。此外，你可以随时订阅 devinduct.com 的，或者在 T2 的推特上关注我，了解最新动态。

感谢您的阅读，下一篇文章再见。