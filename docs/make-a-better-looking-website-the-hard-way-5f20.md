# 努力做一个更好看的网站

> 原文：<https://dev.to/spencerlindemuth/make-a-better-looking-website-the-hard-way-5f20>

在第一部分中，我们介绍了设计一个更好的网站的基本概念和一些工具。现在你知道了如何制作一致的配色方案，并且知道页面上的所有东西都渴望统一，*我们如何让它们都统一呢？*

#### 条块分割

这更多的是一个概念，一个具体的想法，也适用于所有的编码，而不仅仅是网页设计，但原则是重要的，我会覆盖它来重申其重要性。让页面上的每个按钮看起来都一样的最简单的方法是*在每个页面上使用*相同的按钮。它可以在每个页面上有不同的动作，但在 React 中使用相同的组件，或者在网站上的每个类似按钮上使用 vanilla JS 中的相同 CSS 类，这本质上使它统一，因为在相同的代码中没有变化的空间。由于这是一个基于 CSS 和设计的系列，我将跳过 React 组件方法，专注于 CSS 类。
CSS 的一般经验是页面上的每个元素都有一个唯一的 ID 标签，但是类有助于在页面上的许多不同元素中传播样式。要在 HTML 中给元素一个类，只需说:

```
<input type="submit" class="generic-button" value="Submit"/>
<button class="generic-button"/>Prev</button>
<button class="generic-button"/>Next</button> 
```

要在 CSS 中设置这些按钮的样式，请使用“.”标识符后跟类名:

```
.generic-button {
  color: white;
  background-color: red;
  width: fit-content;
  border-radius: 10px;
} 
```

这产生了这些通用按钮:

[![buttons](img/a7a8371edf82ae725949dc24ccb91c7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BamGFDVH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/D39aOTz.jpg)

所以现在我们页面上的任何按钮仍然可以用一个唯一的 ID 挑选出来进行特殊的操作，但是这个类现在不需要任何额外的努力，在第一次呈现时就被设计成与页面上的每个按钮相同的样式，不管功能如何。

但是，当我们希望 submit 按钮具有与其他两个按钮不同的属性，同时仍然保持现有的样式时，会发生什么呢？我们可以将代码复制并粘贴到不同的类中，*或*我们可以用空格将类链接在一起...让我们看看它的作用:

```
<input type="submit" class="generic-button submit" value="Submit"/>
<button class="generic-button"/>Prev</button>
<button class="generic-button"/>Next</button> 
```

我们现在所做的就是在类字段中添加一个空格，所以现在它是“通用按钮提交”。如果我们在 css 中添加另一个类样式:

```
.generic-button {
  color: white;
  background-color: red;
  width: fit-content;
  border-radius: 10px;
}

.submit {
  background-color: green;
} 
```

我们得到了结果:

[![buttons 2](img/c9ea0a0551003f13752cf7b655b2cb1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lqN3dh1Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/fn4Jfzm.jpg)

按钮变绿*和*是如何保持边框半径和字体颜色的？！？！

答案就在语言的名称中。是一个*层叠*样式表(CSS)。这意味着我们从顶部开始应用一个规则，然后当我们进一步阅读文件时，我们应用更多的规则并覆盖现有的规则(也使用特殊性规则)。因此，现在我们可以向单个元素添加多个类，以设置基本样式，然后在顶部添加额外的内容，以添加一些独特的小细节来匹配功能并改善流程。
这里的要点是*编写更少的代码行*，通过划分 CSS 中的类来保持一致性，以应用尽可能多的样式来保持它在尽可能多的元素上可用，而无需重写相同的代码。

#### 动态间距

这一部分是每一个 web 开发者存在的祸根。如何在成千上万屏幕分辨率的世界中保持网站在每种设备上的外观一致？在做出任何造型决定之前，先确定你要和什么样的客户打交道，因为现在是一个移动的世界。在 javascript 中，你可以通过使用类似下面的函数找到客户端类型:

```
 let getDeviceType = () => {
    if(navigator.userAgent.match(/mobile/i)) {
        return 'Mobile';
    } else if (navigator.userAgent.match(/iPad|Android|Touch/i)) {
        return 'Tablet';
    } else {
        return 'Desktop';
    }
} 
```

使用 CSS 你可以使用@media 查询比如屏幕宽度来确定正在使用的设备[(点击这里查看更多关于移动格式)](https://medium.com/@marukohao/responsive-web-design-229350f36844)

```
@media only screen and (max-width: 600px) {
  /*Mobile styles here for devices less than 600px wide*/
} 
```

既然我们已经确定了设备类型，我们就可以开始制作可以在网络上所有设备间正确缩放的布局，但是怎么做呢？

使用动态间距！这意味着设置你的菜单栏的高度为 200 像素是不行的。因为 200 像素在 MacBook pro 显示屏上所占的比例与在 Nexus 4 手机上所占的比例有很大不同。但是这很容易解决，因为 CSS 有一个方便的百分比单元来解决这个问题！CSS 实际上有 *15* 个不同的单位用来确定事物的大小。6 个绝对单位(不随设备变化或缩放的单位)

| 单位 | 财产 | 转换策略 |
| --- | --- | --- |
| 厘米 | 厘米 |  |
| 毫米 | 毫米 |  |
| 在 | 英寸 | 1 英寸= 96px = 2.54cm 厘米 |
| 像素 | 像素 | 1px = 1/96 英寸 |
| 元素铂的符号 | 点 | 1pt = 1/72 英寸 |
| 个人计算机 | 皮卡萨 | 1pc = 12pt |

和 9 个相对单位

| 单位 | 财产 |
| --- | --- |
| **em** | 相对于元素的字体大小(2em 表示当前字体大小的 2 倍) |
| 前夫;前妻;前男友;前女友 | 相对于当前字体的 x 高度(很少使用) |
| 荣誉勋爵 | 相对于“0”(零)的宽度 |
| 雷姆 | 相对于根元素的字体大小 |
| **大众** | 相对于视窗宽度的 1% * |
| **vh** | 相对于视口高度的 1% * |
| 虚拟机(VMM) | 相对于视窗*较小尺寸的 1% |
| vmax | 相对于视窗*较大尺寸的 1% |
| **%** | 相对于父元素 |

这些单元根据屏幕大小(视口)使元素具有不同的大小，并向下传播以改变依赖于父元素大小的子元素的大小(百分比%)，因此现在在每台设备上，元素都将完全适合，并随着您使用手机在 pc 上更改分辨率而缩放。

(先不说缩放比例。视力有障碍的人依靠缩放功能来更好地查看您的站点并阅读其文本，因此不要将字体大小等设置为视口高度，因为无论缩放与否，它们都将始终保持相同的大小)。

现在，您必须使用工具来构建在不同设备上均匀显示的网站，同时保持一致的样式和功能！剩下你要做的就是想出一个好主意并实现它！轻松点。

资源: [w3 学校 CSS 单位](https://www.w3schools.com/cssref/css_units.asp)