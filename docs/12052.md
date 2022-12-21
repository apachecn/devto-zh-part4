# 电子邮件里有什么？

> 原文：<https://dev.to/kellycodeschaos/what-s-in-an-email-2eee>

制作电子邮件设计系统

创建电子邮件活动时，使用基于组件的系统对于尽可能快速高效地向受众提供优质体验至关重要。

[![](img/31c721d90c6181b3d91da98dbc06fc5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4MF9VaS3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A8L-9TWumvT2BiqYK) 

<figcaption>照片由[网站主办](https://unsplash.com/@webhost?utm_source=medium&utm_medium=referral)于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

不幸的是，由于电子邮件客户端的局限性以及跨浏览器和平台的不一致性，电子邮件的设计和开发具有挑战性。由于所支持的内容，在为电子邮件编码时，现代网络的大多数美妙的灵活性都是不可用的。

也就是说，让我们探索如何创建一个易于配置和实施的系统！

[![](img/993a30bf929a68aa171fc3633640eab7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--faJHftTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A_km9y8PP0fqYgUjo) 

<figcaption>照片由[诺德伍德主题](https://unsplash.com/@nordwood?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

### 品牌基线

确定什么对你的品牌基线至关重要，这很重要。至少，这通常是分配一个调色板和潜在的自定义字体。

请记住，你的品牌所带来的所有怪癖都不需要反映在你的电子邮件活动中！

下面是一个简单的例子，说明如何在模板中使用的 scss 文件中将颜色和版式构造为变量:

```
/***** COLORS ******/

/* raw values -- do NOT reference */
$blue01: #0000ff;

/* role -- reference these */
$primary: $blue01;

/***** TYPOGRAPHY ******/

@function calculateRem($size) {
 $remSize: $size / 16px;
 @return #{$remSize}rem;
}

@mixin fontSize($size) {
 font-size: $size; //Fallback in px
 font-size: calculateRem($size);
}

H1 {
color: $primary;
@include fontSize(32px);
} 
```

我喜欢这种方法，因为如果需要的话，可以很容易地更新$primary 变量的值。如果你想知道为什么我不把一个十六进制的值赋给$primary，从技术上来说我可以这么做——但是很容易忘记我真正引用的是什么，因为这个原始值不是非常具有描述性的。

#### 排版

我从排版开始，因为当你有一个强大的信息层次时，无论你选择什么颜色，你的邮件内容都会闪闪发光。

[![](img/0928131ce0e800426c11d881ee943066.png)](https://res.cloudinary.com/practicaldev/image/fetch/s----Qbfgyi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AFPUvQHcJLPNZKk5o) 

<figcaption>照片由 [J-S 罗密欧](https://unsplash.com/@jromeo?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

但是，如果你想使用你的定制品牌字体，会发生什么呢？自定义字体可能有点棘手，这取决于它们的来源，因为它们可能无法正确地呈现特定平台和电子邮件客户端的组合。如果可能的话，我强烈推荐找一个类似的谷歌字体，因为它们更可靠，而且已经被托管了。有人会注意到吗？

当决定要在模板中包含什么内容时，从基础开始:

*   头球(H1，H2，H3…)
*   正文(p)
*   链接(a)
*   按钮(一个)

#### 颜色

假设你的品牌已经在你的产品/网站中引用了一个基本调色板。厉害！因为电子邮件包含静态图像和文本，所以不需要全部导入。尽量保持简单，用一到两种主要的品牌颜色作为电子邮件的交互部分(链接和按钮)。

[![](img/d53ca906b030b9fd09d11b66021ddd02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DKHCIPGa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Al18H7cPVkne1SqNO) 

<figcaption>照片由[万花筒](https://unsplash.com/@kaleidico?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

### 设置结构

现在你已经了解了品牌，下一步是建立你的模板中实际包含的内容。

#### 组件

组件很重要，因为它们决定了系统的哪些部分是可重用的和可引用的。像常规设计系统一样，你需要确定哪些部分应该尽可能小。

让我们看看一些常见的电子邮件组件和选项:

*   **标题:**文本对齐、边距、版式、颜色
*   **正文:**文本对齐、边距、版式、颜色
*   **按钮:**对齐、尺寸、排版、颜色
*   **分隔线:**全幅、小幅、彩色
*   **图片:**尺寸

使用这些组件，您可以创建预配置的复合组件(原子设计的分子)，这样您就不必在每次需要一组组件时都手动包含相同的组件集。

样品分子:

*   **分子:**表头+正文
*   **分子:**图片+正文

最后但同样重要的是，我喜欢让我的有机体创建我的电子邮件的“部分”:

*   **部分:**介绍(图片+页眉+文本+可选按钮)
*   **节:**三列网格(图片和文字分三列用于桌面)
*   **版块:**头像简介(小图+页眉+正文)

#### 更多关于章节

Sections 值得拥有自己的标注，因为它是电子邮件模板中不可或缺的一部分。这些部分定义了在考虑您正在创建的内容时可以使用的内容。任何时候你偏离一个部分，你都在创造“雪花”代码和设计，这违背了拥有一个系统的目的。

为了成为一个成功的电子邮件模板，定义你要与你的受众分享什么样的内容。是促销吗？时事通讯？更新你的隐私政策？不管是什么，你应该清楚需要支持什么样的内容部分。

一旦你有了自己的部门，下一个关键的部分就是社会化。如果你和一个使用你的模板创建电子邮件的团队一起工作，他们需要知道这些部分叫什么。例如，您可能会收到这样的问题:

*   我如何创建卡片？
*   如何将图像放置在文本的左侧？
*   如何插入页脚？

或者更糟的是，他们只会创建一次性的节类型，而不会使用已经创建的内容！有一个一致的命名约定，并确保在设计(作为符号)和代码(作为注释代码块)中使用什么是显而易见的，这将有助于您的团队通过使用相同的术语更快地工作。

#### 表格布局

是的，现在已经是 2019 年了(截止到这篇文章)，我们仍然不得不依靠桌子来收发电子邮件。不过这没关系，因为这只是意味着我们必须仔细考虑我们的表格单元结构。

设计内容时，您需要考虑左对齐或右对齐以及表格之间的交互方式。通过在表格中嵌套表格，您可以设置内容如何一起流动的规则。

就我个人而言，我更喜欢在移动之前从桌面布局开始。原因是，你的部分通常会去全宽，并堆叠在移动，这是相当直截了当。桌面是一些计划和表格布局成为挑战的地方。将多嵌套表格转换为一系列堆叠表格比反过来更容易。

以下是我如何使用 Salesforce 的 Pardot 电子邮件系统实现章节块(“介绍段落”)的简化示例: