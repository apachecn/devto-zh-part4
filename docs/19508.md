# 让我们建立:与顺风 CSS 响应导航条

> 原文：<https://dev.to/justalever/lets-build-with-tailwind-css-responsive-navbar-16hl>

欢迎来到我的“让我们来构建”系列的另一个补充:使用顺风 CSS 系列。这一期将重点关注在本地开发环境中使用 [PostCSS](https://postcss.org/) 来创建和配置一个完全用 Tailwind CSS 构建的响应式 navbar。

[https://www.youtube.com/embed/025OldWcmbQ](https://www.youtube.com/embed/025OldWcmbQ)

与之前我在 [Codepen](https://codepen.io/webcrunchblog/) 上托管代码的教程不同，本教程将引用来自 [Adam Wathan](https://twitter.com/adamwathan) 的 [playground Github repo](https://github.com/tailwindcss/playground) ，它使用 PostCSS 和一些其他依赖项来完全控制 Tailwind CSS 框架。我们将从默认主题开始，但会在以后的文章中继续扩展。

Post CSS 结合 autoprefixer 允许我们创建一个生成由 Tailwind CSS 框架提供的实用程序类的构建过程。有了这些，我尝试创建一个只支持 CSS 的响应 navbar 来切换移动设备上的状态。我们将利用复选框来创建下拉效果，否则这将更适合 JavaScript。

除了一个超出框架范围的选择器，navbar 完全是用 Tailwind 构建的。总的来说，我对结果非常满意。

[在这里找到最终代码](https://github.com/justalever/tailwind-navbar)。

#### 到目前为止的级数

*   [如何安装顺风 CSS v1.0](https://dev.to/justalever/how-to-install-tailwind-css-v1-0-p42)
*   [让我们建造:顺风 CSS–Tweet](https://dev.to/justalever/let-s-build-with-tailwind-css-tweet-3ejh)
*   [让我们建立:顺风 CSS-Dribbble Shot](https://web-crunch.com/lets-build-tailwind-css-dribbble-shot)
*   [让我们构建:使用顺风 CSS–响应式导航条](https://web-crunch.com/lets-build-tailwind-css-responsive-navbar)

帖子[let Build:With tail wind CSS-Responsive Navbar](https://web-crunch.com/lets-build-tailwind-css-responsive-navbar/)首先出现在 [Web-Crunch](https://web-crunch.com) 上。