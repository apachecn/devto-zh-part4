# 我应该使用什么媒体查询断点？

> 原文：<https://dev.to/rstacruz/what-media-query-breakpoints-should-i-use-292c>

响应式设计需要大量的媒体询问。这是普遍认同的，但是我们应该写*最小宽度*还是*最大宽度*？应该用`768px`还是`780px`？这里有很多的差异，所以我去挖掘，看看是否有一个建立在这个主题上的共识。

```
@media (min-width: /* ??? */) {
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 我们设计是为了什么？

在我们弄清楚使用什么媒体查询断点之前，我们需要看看我们在为什么设备设计。

我研究了一些常见的设备分辨率，大多数都可以归类。我列出了这些类别，以及它们所迎合的分辨率:

*   **移动便携设备**(320 像素至 414 像素)
    适用于 4 英寸至 6.9 英寸屏幕的设备。

*   **移动风景** (568px 到 812px)
    一样，但是风景。

*   **平板电脑便携式** (768px 至 834px)
    适用于 7 英寸至 10 英寸的设备

*   **写字板风景** (1024px 到 1112px)
    同上，也是 12 寸写字板上的肖像

*   **笔记本电脑&台式机显示器** (1200px+)
    变化很大，但一般都是 1200px 及以上

* * *

## 设备

我们来看看几款 iOS 设备。虽然 Android 设备也很重要，但它们有很多差异——大多数手机的设备 DPI 都是可调的。大多数 Android 手机的默认设置与 iOS 相当，所以让我们来看看这些。

以下是一些常见设备的列表，以及它们的[设备无关像素宽度](https://en.wikipedia.org/wiki/Device-independent_pixel):

| 种类 | 宽度 | 设备 |
| --- | --- | --- |
| **手机，人像** | `320px` | iPhone SE |
|  | `375px` | iPhone 6，7，8，X |
|  | `414px` | iPhone 8 Plus |
| **移动，风景** | `568px` | iPhone SE |
|  | `667px` | iPhone 6，7，8 |
|  | `736px` | iPhone 8 Plus |
|  | `812px` | iPhone X |
| **写字板，人像** | `768px` | iPad Air、iPad Mini、iPad Pro 9 " |
|  | `834px` | iPad Pro 10 英寸 |
| **写字板，风景画** | `1024px` | iPad Air、iPad Mini、iPad Pro 9 " |
|  | `1024px` | iPad Pro 12 英寸(纵向) |
|  | `1112px` | iPad Pro 10 英寸 |
| **笔记本电脑显示屏** | `1366px` | 高清笔记本电脑(768p) |
|  | `1366px` | iPad Pro 12 英寸(横向) |
|  | `1440px` | 13 英寸 MacBook Pro(2 倍缩放) |
| **桌面显示器** | `1680px` | 13 英寸 MacBook Pro(1.5 倍缩放) |
|  | `1920px` | 1080p 显示器 |

* * *

## 最常见的断点

我看了一下 2019 年最流行的一些 CSS 框架(还有一些是过去的)，看看他们用了什么断点。他们中的大多数使用相同的点，有一点点的差异。

| 结构 | 小的 | 中等 | 大的 | 超大尺寸 |
| --- | --- | --- | --- | --- |
| 布尔玛 | - | `min: 769px`
(“移动”) | `min: 1024px`
(“桌面”) | `min: 1216px`
("fullhd") |
| 自举 3 | - | `min: 768px` | `min: 992px` | `min: 1200px` |
| 自举 4 | `min: 576px` | `min: 768px` | `min: 992px` | `min: 1200px` |
| 顺风 | `min: 576px` | `min: 768px` | `min: 992px` | `min: 1200px` |
| Zurb 基金会 | - | `min: 640px` | `min: 1024px` | `min: 1200px` |

### 768 像素，992 像素，1200 像素

很多框架使用`768px`、`992px`和`1200px`。这一直是 Bootstrap 3 的默认断点，似乎被认为是足够明智的，已经被其他项目采用。

### 没有小断点？

有些人选择不在 700 像素以下设置断点。这可能来自 Bootstrap 3，它提倡让移动风景视图与肖像视图相同。Bootstrap 4 后来改变了他们的立场，我个人同意这一点——在横向屏幕上看到高的标题是非常讨厌的！

### 576 px 从何而来？

在 Bootstrap 4 增加`576px`断点之前，`480px`是[热门选择](https://github.com/twbs/bootstrap/issues/10203)。最终选择了 576px，因为它大约在 320px 和 768px (+32px)的中间[。我个人觉得选择 576px 而不是 480px 没什么大不了的(或者反过来)；任何一个都应该足以覆盖 iPhone 8 Plus 的 414px 宽度。然而，我更喜欢`480px`，因为 576px 仍然覆盖了 iPhone SE 的横向视图。](https://github.com/twbs/bootstrap/issues/21333)

### 640px

Zurb 的`640px`断点是个有趣的选择。它涵盖了平板电脑和(大部分)移动设备。

* * *

## 那么我该用什么呢？

这主要是主观的，可能取决于你要设计什么样的屏幕。我使用这组断点作为起点，并且我可以为大多数项目推荐它们。

| 断点 | 目的 |
| --- | --- |
| *(默认)* | 手机肖像 |
| 最小宽度:`480px` | 移动风景(和更大的) |
| 最小宽度:`768px` | 平板电脑-纵向(和更大) |
| 最小宽度:`992px` | 平板电脑-风景(和更大) |
| 最小宽度:`1200px` | 笔记本电脑(和兰格) |

### 避免最大宽度

我更喜欢只使用`min-width`，尽可能避免使用`max-width`。混合最小宽度和最大宽度可以使 CSS 代码更短，但更难阅读。

### 偏移你的最大宽度

如果一定要用`max-width`，一定要偏移至少 0.02px，也就是用`max-width: 479.98px`代替`max-width: 480px`，因为后者会和`min-width: 480px`有一点重叠。

### 480px 还是 576px？

我建议使用`480px`而不是`576px`,因为它也可以覆盖更小的手机(例如 iPhone SE)。

### 该不该用 768px？

`min-width: 768px`可能没有大多数人想象的那么有用。如果你正在为平板电脑设计肖像，考虑使用`min-width: 480px`，它在移动风景上看起来也不错。Zurb 基金会似乎也在推广这种手机-景观+平板-便携的协同设计理念，考虑到他们使用`640px`作为一个断点。

### 768 px 的奥秘

在指南中，`min-width: 768px`断点通常被描述为“平板电脑景观”。这是一种误导，因为它也符合平板电脑的纵向模式。如果您需要瞄准平板电脑，请使用`min-width: 992px`。(你也可以用`769px`，但那不会覆盖 iPad Pro 10”。)

## 应该怎么给它们命名？

我个人不喜欢把东西叫做`small`、`medium`和`large`。这些词可能是模棱两可的；一台 iPad Pro 10”算中号还是大号？iPhone SE 会被算作超小型吗？这些词是相对的，它们的主观性会造成一些混乱。

我也不喜欢称他们为`mobile`、`tablet`等等。iPad Pro 12”是一款平板电脑，但为什么你只能用`desktop`媒体查询来针对它？三星 Note 从技术上来说是一部手机，但为什么上面盖着`tablet`？`tablet`占风景还是人像？如今，设备分类之间的界限有点模糊。

相反，我建议用更通用的名字来称呼它。大多数设计师都非常熟悉“700 像素的感觉”，所以我认为这是一个更合适的名字。我喜欢用它们最接近的百分之一来命名它们。

```
@custom-media --viewport-4 (min-width: 480px);
@custom-media --viewport-7 (min-width: 768px);
@custom-media --viewport-9 (min-width: 992px);
@custom-media --viewport-12 (min-width: 1200px); 
```

Enter fullscreen mode Exit fullscreen mode

```
@media (--viewport-4) {
  /* ... */
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 谢谢！

我希望这些对你有帮助。参考资料:

*   [https://bulma.io/documentation/overview/responsiveness/](https://bulma.io/documentation/overview/responsiveness/)
*   [https://getbootstrap.com/docs/3.4/css/](https://getbootstrap.com/docs/3.4/css/)
*   [https://get bootstrap . com/docs/4.3/layout/overview/# responsive-breakpoints](https://getbootstrap.com/docs/4.3/layout/overview/#responsive-breakpoints)
*   [https://foundation.zurb.com/sites/docs/media-queries.html](https://foundation.zurb.com/sites/docs/media-queries.html)
*   [https://tailwindcss.com/docs/responsive-design/](https://tailwindcss.com/docs/responsive-design/)
*   [https://developer . apple . com/library/archive/documentation/device information/Reference/IOs device compatibility/Displays/Displays . html](https://developer.apple.com/library/archive/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Displays/Displays.html)