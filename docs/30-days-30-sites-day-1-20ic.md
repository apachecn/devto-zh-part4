# 30 天 30 个站点-第 1 天

> 原文：<https://dev.to/vicradon/30-days-30-sites-day-1-20ic>

第一个挑战是设计一个作品集网站。我以前做过一个，但是那个古怪而且没有反应。所以在第一个挑战中，我用 materialize 制作了一个像样的作品集网站。它在 github 上的[举行。你也可以看看我用它制作的](https://github.com/Vicradon/tdtsc1) [github 页面](https://vicradon.github.io/tdtsc1/)。

# 我学到了什么

首先，我学会了如何用 Materialize 创建一个合适的导航条。我很惊讶我不用写一行 CSS 就能制作一个导航条。仅仅定位元素就产生了一个干净的响应导航条。物化导航条有不同的形式，我的有移动支持。
[![desktop view](img/e7087dac18ffb6996bf385ea76d42e29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e_EHSfV3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fz02ku3d962uvwcxa5la.JPG) 
右边的链接调整大小形成左边的条
[![mobile view](img/02ba0d6d6b86c1e0154823369a8f0052.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Li8VeyD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/khg0c0nt8jht1sdgczge.JPG) 
这是由

1.  将右侧链接的类别设置为“隐藏在中间并向下”
2.  将条形的类设置为 sidenav-trigger，这是一个物化类...除了其他东西

```
<nav>
    <div class="nav-wrapper blue darken-2">
      <a href="#!" class="brand-logo vicradon">Vicradon</a>
      <a href="#" data-target="side-menu" class="sidenav-trigger">
        <i class="material-icons">menu</i>
      </a>
      <ul class="right hide-on-med-and-down ">
        <li><a href="/">HOME</a></li>
        <li><a href="./about.html">ABOUT</a></li>
        <li><a href="./hire.html">HIRE</a></li>
        <li><a href="./add.html">ADD</a></li>
      </ul>
    </div>
  </nav>
  <ul class="sidenav" id="side-menu">
    <li><a href="/">HOME</a></li>
    <li><a href="./about.html">ABOUT</a></li>
    <li><a href="./hire.html">HIRE</a></li>
    <li><a href="./add.html">ADD</a></li>
  </ul> 
```

Enter fullscreen mode Exit fullscreen mode

我想记住这个过程并不重要。重要的是知道它是如何工作的。你总是可以参考文档来获得一个入门 navbar 代码。

# 网格系统

materialize 中使用的网格系统，类似于 Bootstrap，真正抽象了 CSS 网格的使用过程。它基于给定的条件，并相应地调整网格容器的大小。很好地掌握网格系统对布局和设计很有帮助。

# 输入字段物化类

这个类用于为 HTML5 输入元素提供一些顶级的样式。甚至 select 元素也使用输入字段类。一件奇怪的事情是，label 元素被放在 input 元素下面，并被赋予一个与该元素的 id 相匹配的名称，以启用 translate-y 动画。

[上滑动画](https://imgur.com/a/VSUStyS)

所以，我还发现了其他一些微妙的东西，但是已经忘记了。我会记得更新的。