# 超轻量级网站设计

> 原文：<https://dev.to/rfornal/hyper-lightweight-website-designs-1h3d>

## 不尝鲜

我记得我第一次听说渐进式 Web 应用程序。我记得当时完全没有印象...我当然不是这项技术的早期采用者。事实上，在为另一个项目深入研究 Web Workers 之后，过了几年我才发现了 PWA bug。我听说了服务人员，并立即看到了我一直在考虑的几个副业项目的含义。

## 这一切开始汇集在一起...

我构建了两个 pwa，用我能管理的最简单的代码库(嵌入式 CSS、JavaScript、数据和图像)来实现它们。设计和概念很简单，而且**很有效**；实际上，效果很好。

虽然我的想法是用最少的可行代码(没有任何框架)来设计这些网站，但我很快意识到我已经开发了一个强大的基线，可以用于比较:如果这些网站是使用现代框架重新设计的，我就可以很快看到好处在哪里。

然后我打了一个游戏改变者...

### 超轻量网站

[超轻量级网站(克里斯·扎卡里亚斯)](https://www.youtube.com/watch?v=VUwyYhNO63I&t=788s)

在本次演示中，我们将看到...

> 超轻网站是对基于网络的用户界面的一种再认识，其设计目的是推动性能的极限。
> 
> **的目标**是提供一种与真实事物大致一致的体验，可以尽快交付给最终用户**。**
> 
>  **高延迟环境无处不在，甚至到今天也是如此。**

 **### 搭建超轻网站的步骤:

1.  确定网站上最活跃的页面。
2.  从零开始构建页面的新版本，专注于优化。
3.  将所有的 HTML、CSS 和 JavaScript 组合成一个“超轻”的 HMTL 页面。
4.  **SERVE** 采用压缩和 HTTP/2 流水线技术的 CDN 背后的超轻页面。
5.  **测量**原始页面、未缓存页面和超轻页面。
6.  **分析**结果。

### 你想继续做的事情:

*   提供真实的内容，最好是动态的。
*   最低响应布局*，公共断点*。
*   响应图像。
*   在某些情况下是精灵。
*   负责任地优化 SVG 图形*。*
**   可达性。*

 *### 你想停止做的事情:

*   广告和社交媒体追踪器。
*   CMS 集成。
*   JavaScript 库。
*   通过框架的 CSS 布局。
*   懒加载内容，*图片*除外。
*   数据 URIs，*除非绝对确定*。
*   JavaScript 和 CSS 编译。
*   自定义 web 字体。

### 建议(可能时)

*   尽可能在服务器端内联。
*   使用 HTML5 语义元素。
*   使用根 em 大小的相对布局。
*   使用 HTML 模板。
*   使用 CSS :target 选择器进行状态转换。
*   用 HTML 和 CSS 替换 SVG。
*   使用带有片段标识符的 SVG sprite。
*   基于图像的精灵。
*   使用 srcset 的响应图像。

### 测量性能

*   使用 [sitespeed.io](http://sitespeed.io) 。
*   对其他页面重复该过程。
*   建立绩效预算。
*   定期运行现场速度*或类似工具*。

## PWAs(和代码)

遵循超轻设计的渐进式网络应用:

*   [美国许可证页面游戏](http://bit.ly/uslpg)
*   [面试问题&回答申请](http://bit.ly/interviewqa)

设计代码:

*   [代码:美国执照页游戏](http://bit.ly/uslpg-code)
*   [代码:面试问题&答题申请](http://bit.ly/interview-qa-pwa-code)

## 结论

首先，pwa 既有趣又酷。服务人员提供的功能远不止于移动开发。

第二，我在开发一些 pwa 时使用的紧密模式实际上是使用了新的模式...允许我远离框架和 spa，而专注于速度和性能。

此外，我现在真的很高兴我花时间玩了渐进式 Web 应用程序和相关技术；这是一次令人大开眼界的经历。

最后，我不确定老派是否已经再次成为新的，但肯定有一些有用的实现，我们必须克服有限的带宽(我们仍然有...移动设备或特定位置)。***