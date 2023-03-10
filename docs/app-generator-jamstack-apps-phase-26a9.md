# 使用工具生成的 JAMstack - 40+开源应用

> 原文：<https://dev.to/sm0ke/app-generator-jamstack-apps-phase-26a9>

各位编码员好，

在这篇文章中，我将介绍一个自动化的工作流程，用于发布 40+ **[JAMstack 开源应用](https://appseed.us/static-site)** ，这些应用是在知名 web 设计机构(Creative-Tim、HTML5Up 和 Pixelarity)制作的现有设计基础上生成的。
我决定建立一个自动化的工作流程，基于我过去两年的自由职业经验，主要基于反馈和客户需求:

*   客户不断要求各种各样的设计可供选择
*   应用程序应该以现代技术交付
*   使用低预算将传统应用迁移到新技术

> 谢谢大家！**内容由 [App 生成器](https://appseed.us)提供**。

* * *

为了解决所有这些问题和挑战，我建立了一个半自动的工作流程:

*   解析平面 HTML 设计，组件提取和翻译到各种模板引擎(Jinja2，PUG，Blade，Php native..等等)

*   将经过处理的设计注入到现有的样板文件中(Php、Python、Javascript、JAMstack、NodeJS)

*   自动执行最小测试集

使用这个工作流，一开始只针对 Php 应用，当我决定构建 JAMstack 应用或 Python 应用时，我发现了巨大的相似性。所有技术的生成过程的初始阶段都是相同的，只有注入阶段略有不同，但差别不大。

> 注意: [HTML 解析器](https://dev.to/sm0ke/html-parser-developer-tools-5d85)工具在之前的[文章](https://dev.to/sm0ke/html-parser-developer-tools-5d85)中有解释。

使用这一过程生成的所有 JAMstack 应用程序都是开源的，发布在 Github 上，7 个应用程序是免费的，35 个是商业的。基于设计提供商的数字如下:

*   在麻省理工学院许可下发布的 4 款应用
*   [HTML5 以上](https://html5up.net/?ref=appseed) - 3 个带 [CCA 3.0](https://html5up.net/license) 许可的应用
*   [像素级](https://pixelarity.com/?ref=appseed)应用- 35 -商业许可

* * *

## 什么是 [JAMstack](https://jamstack.org/)

给新手一个简短的提示， [JAMstack](https://jamstack.org/) 代表 JavaScript、API 和标记。该术语由 Mathias Biilmann 创造，用于描述基于客户端 JavaScript、可重用 API 和预建标记的现代 web 开发架构。

* * *

## [十一软 UI](https://appseed.us/product/eleventy-soft-ui)

[Eleventy Soft UI](https://appseed.us/product/eleventy-soft-ui) 是 **Soft UI 设计系统**(一个基于 Bootstrap 5 的用户友好且美观的设计系统)的 Eleventy 改编版，包括一个使用 Prismic CMS 进行内容管理的博客系统。**软 UI 设计系统**内置超过 70 个前端独立元素，如按钮、输入、导航条、导航标签、卡片或提醒，让你自由选择和组合。所有组件在颜色上都有变化，您可以使用 SASS 文件和类轻松地修改这些变化。

* * *

*   [十一软 UI](https://appseed.us/product/eleventy-soft-ui) -产品页面
*   [十一软 UI](https://eleventy-soft-ui.appseed-srv1.com/) -现场演示

* * *

[![Eleventy Soft UI Design - Open-source SSG starter provided by AppSeed in 11ty on top of Soft UI Design.](img/220c16683b17503f251b464ad5993336.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOPclq5x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/51070104/128602712-4b38f18d-2154-4899-a796-aec0f7435329.png)

* * *

## [JAMstack 纸包](https://jamstack-paper-kit.appseed.us/)

**麻省理工学院许可**，创意设计——蒂姆:[演示](https://jamstack-paper-kit.appseed.us/)，[来源](https://github.com/app-generator/jamstack-paper-kit)

[![JAMstack Paper Kit - Gif animated intro.](img/15d93cbcdfce1722ac96abc17eabcf7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fadP6Muc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-paper-kit-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 氩气设计](https://jamstack-argon-design.appseed.us/)

**MIT License**——开源 app 内置 **JAMstack** 模式:[演示](https://jamstack-argon-design.appseed.us/)，[来源](https://github.com/app-generator/jamstack-argon-design-system)

[![JAMstack Argon Design - Gif animated intro.](img/9cbee6ef31586e77bcfb2561e50152e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wLfh8lrh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-argon-design-intro.gif%3Fraw%3Dtrue)

* * *

## [jam stack big picture](https://jamstack-big-picture.appseed.us/)

**[CCA 3.0 许可](https://html5up.net/license)** ，由 HTML5 设计 Up: [演示](https://jamstack-big-picture.appseed.us/)，[来源](https://github.com/app-generator/jamstack-big-picture)

[![JAMstack BigPicture - Gif animated intro.](img/8a23d05bbd6a36bac6bda5e3bfa31358.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vm4BxUNO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-html5up-bigpicture-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 分形](https://jamstack-fractal.appseed.us/)

**[CCA 3.0 许可](https://html5up.net/license)** ，由 HTML5 设计 Up: [演示](https://jamstack-fractal.appseed.us/)，[来源](https://github.com/app-generator/jamstack-fractal)

[![JAMstack Fractal - Gif animated intro.](img/25ef89b33fbae9b9c6a06ebc16fa358e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PigGoJtS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-html5up-fractal-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 镜头](https://jamstack-lens.appseed.us/)

**商业许可**，像素设计:[演示](https://jamstack-lens.appseed.us/)

[![JAMstack Lens - Gif animated intro.](img/312ebe703d49d4f7c7910440e45ea1d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g8zHj1gB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-lens-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 漂移](https://jamstack-drift.appseed.us/)

**商业许可**，像素设计:[演示](https://jamstack-drift.appseed.us/)

[![JAMstack Drift - Gif animated intro.](img/3117af82a1342992ce7c1408a6be7ca0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RX5BVPpo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-drift-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 并行](https://jamstack-parallelism.appseed.us/)

**商业许可**，像素设计:[演示](https://jamstack-parallelism.appseed.us/)

[![JAMstack Parallelism - Gif animated intro.](img/349583dd40dbe7c4174026bbf3115877.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oJfc_ZWN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-parallelism-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 反射](https://jamstack-reflex.appseed.us/)

**商业许可**，像素设计:[演示](https://jamstack-reflex.appseed.us/)

[![JAMstack Reflex - Gif animated intro.](img/b4aa6d3e6aed525d7e6668b6ca01b63c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--My8LPrXa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/app-generator/static/blob/master/products/jamstack-reflex-intro.gif%3Fraw%3Dtrue)

* * *

## [JAMstack 应用](https://github.com/app-generator/jamstack)

完整的列表可以在这里找到以及每个应用的演示&来源和许可类型的链接

* * *

## [JAMstack](https://jamstack.org/) 资源

*   [JAMstack](https://jamstack.org/) -阅读更多关于 **JAMstack** 模式的信息
*   与 [JAMstack](https://blog.appseed.us/tag/jamstack/) 相关的博客文章
*   生产就绪的管理列表 [JAMstack 应用](https://appseed.us/apps/jamstack)

* * *

感谢您的阅读&快乐编码！