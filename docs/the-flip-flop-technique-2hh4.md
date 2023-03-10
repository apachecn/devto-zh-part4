# 触发器技术

> 原文：<https://dev.to/chrisburnell/the-flip-flop-technique-2hh4>

我最近在我网站的页脚实现了一个[配色方案 toggler](%5C%22#color-scheme-toggle%5C%22) ，遵循[安迪·贝尔的](%5C%22https://andy-bell.design%5C%22)指南，[创建一个用户控制的深色或浅色模式](%5C%22https://andy-bell.design/wrote/create-a-user-controlled-dark-or-light-mode%5C%22)，我发现了一个不靠谱但有趣的替代方案来设计我的深色主题，它利用了 CSS 的滤镜属性。

* * *

查看我的网站上的[文章，看看这一切在你的原生浏览器中表现如何。](https://chrisburnell.com/article/the-flip-flop-technique)

* * *

安迪很好地解释了这个解决方案是如何工作的，所以如果你想了解更多信息，我推荐你阅读他的文章。

要点是，我们有一些 JavaScript，它们与页面上显示的一些 CSS 变量挂钩，我们通过改变根元素
的属性，在<q>亮</q>和<q>暗</q>调色板之间来回切换它们的值

```
:root {
    --color-text: black;
}
[data-user-color-scheme="dark"] {
    --color-text: white;
} 
```

## 触发器

我需要花一些时间在我的黑暗主题中使用的颜色上，但是我想要一个临时的解决方案，因为说实话，我很兴奋去实现它！

最终，我意识到，有了一些`filter`的价值观，我们可以想出一个与我的<q>光</q>主题风格完全相反的风格。

#### 图 1

[![](img/fe95e4deee6e8a315c56bd66c1c0d530.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95AJKCiY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wj5phdpt1kir18b0c8hb.png)

**主题:** `unaltered`

#### 图 2

[![](img/25e7d2fb63e9b9fe642745f10cc48f92.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxSl94KS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ywuurgxckfj9moyhyx64.png)

**主题:** `invert(1)`

反转完成。我们已经从浅色背景变成了深色背景。现在我们需要调整颜色的色调——在这种情况下，我们希望我们的棕色是蓝色。

#### 图 3

[![](img/4664f0cc61234576153326ef9f791a8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nYNVtPIi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hsgullzqoz9tx8q1j2wy.png)

**主题:** `invert(1) hue-rotate(180deg)`

现在我们已经设法找回了我们的蓝色，但龙表情符号看起来完全错误。这就是触发器技术的用武之地。

#### 图 4

[![](img/bd42dbc85bda20f0272ea81216aaaf8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bZVKN3Qa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6dz4t6tzq1di1hgsaasn.png)

**主题:** `invert(1) hue-rotate(180deg)`
**表情符号:** `invert(1) hue-rotate(180deg)`

完成了。通过对表情符号再次应用相同的滤镜*，它会翻转回其未改变的外观。*

 *## 色相、饱和度、明度

但是有些事情不太对劲。与第一个例子中未改变的表情符号相比，最后一个例子中的表情符号的颜色似乎不太饱和或不太鲜艳，这在龙的黄色毛发上最为明显。与此演示互动，查看未更改的状态和完全过滤的状态，并亲自查看。

[![](img/6048c80971eec064a46e4016c82eb9fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0rswX-kM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3val5sdhboivfr5sd6xs.png)

更令我困惑的是，这种差异只在我使用默认的*亮*主题时存在——当使用我的*暗*主题时，未改变的表情符号看起来就像最终产品一样不饱和。

## 代码

```
img,
svg,
[role="img"],
embed,
iframe,
object,
video {
    @extend %asset-elements;
}

@mixin theme-dark() {
    @supports (filter: invert(1) hue-rotate(180deg)) {
        &,
        %asset-elements {
            filter: invert(1) hue-rotate(180deg);
        }
    }

    @supports not (filter: invert(1) hue-rotate(180deg)) {
        --color-black: #{$color-white};
        --color-mineshaft: #{$color-alto};
        --color-kaiser: #{$color-dove};
        --color-dove: #{$color-kaiser};
        --color-alto: #{$color-mineshaft};
        --color-white: #{$color-black};
    }
}

@include media("dark") {
    :root {
        --color-scheme: "dark";
    }

    :root:not([data-user-color-scheme]) {
        @include theme-dark;
    }
}

/*:root*/[data-user-color-scheme="dark"] {
    @include theme-dark;
} 
```

也许网络上对颜色或滤镜有所了解的人知道这里发生了什么，但我似乎无法使用任何滤镜组合来尝试将表情符号恢复到原始颜色。

我肯定是少了*点什么*，但是已经*接近*了。*