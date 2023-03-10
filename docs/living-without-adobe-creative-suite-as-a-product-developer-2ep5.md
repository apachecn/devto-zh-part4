# 作为产品开发人员，没有 Adobe Creative Suite 的生活

> 原文：<https://dev.to/_rich/living-without-adobe-creative-suite-as-a-product-developer-2ep5>

如果你是一名数码产品开发人员，你可能有 Adobe 软件，如 Photoshop 和 Illustrator。如果你不能没有 Ubuntu 作为你的操作系统，你可能不会。在 Ubuntu 上安装 Adobe 产品是一个困难的过程，因为 Adobe 不生产与 Linux 兼容的软件。

在没有 Adobe 软件套件的情况下，我发现了使用开源软件、免费增值软件和代码创建/修改图形和线框的其他方法。

这里是我用来替换 Adobe 套件的工具的简短集合。这些产品有些是开源的，有些是免费计划的付费订阅。

## Whimsical.com

**流程图、思维导图、线框**。

异想天开是创建线框、流程图和思维导图的神奇工具。异想天开有足够的线框功能来构建您的设计，而不会太复杂。这个应用程序最好的部分是它使用起来非常直观。来自使用 Adobe 产品，异想天开是足够直观的，不需要任何教程就可以立即开始。

除了线框化之外，异想天开还允许你用同样的直观体验创建流程图和思维导图。这个工具是我快速设计应用程序初始原型的重要资源。

**用例:**

*   线框
*   流程图
*   思维导图

## 画板. io

**简单的平面设计。**

Sketchpad.io 是一款不错的网络应用，可以进行基本的图形设计。像异想天开，这是非常直观的使用，虽然有可能是错误的，当使用缩放功能。

总的来说，它有足够的功能来设计快速图形。值得注意的特点包括:易于使用，惊人的字体选择，下降到画板的图像，和容易的梯度覆盖。

**用例**:

*   标志/图形设计
*   图形实验

## Gimp

**高级平面设计**。

对于可以与 photoshop 相媲美的高级图形设计，Gimp 是 Linux 的 goto 软件(或者如果你预算紧张的话)。Gimp 是一个开源软件，它可以做你所期望的专业图形软件所能做的事情。Gimp 的问题是有一个学习曲线。Gimp 适用于所有平台:Linux、Windows 和 OSX。

**用例:**

*   高级平面设计
*   标志设计
*   照片编辑

## PIL Python 图像库

**用代码**进行图像编辑。

对于简单的图像操作或图像自动化任务，有 PIL (Python 图像库)。PIL 可以用来调整大小，过滤和裁剪图像。最近，我需要将白色背景转换成透明背景。对于 PIL，我用下面的代码做到了这一点:

**将颜色改为透明** :

```
from PIL import Image
def color_to_transparent():
    img = Image.open('image.png')
    img = img.convert('RGBA')
    datas = img.getdata()
    newData = []
    for item in datas:
        if item[0] < 100 and item[1] < 100 and item[2] < 100:
            newData.append((0, 0, 0, 0))
        else:
            newData.append(item)
    img.putdata(newData)
    img.save("NEW-image.png", "PNG")
    img.show() 
```

**用例:**

*   创建多个图标版本
*   颜色替换
*   调整大小+裁剪
*   自动化图像编辑任务

## 喷墨打印纸

**矢量图形**

最后，还有 Inkscape。Inkscape 是取代 Adobe Illustrator 的开源软件。界面不像 Adobe Illustrator 那样完美，但是在研究了一段时间后，它变得很容易使用，尤其是如果你已经对 Illustrator 有经验的话。

**用例:**

*   标志设计
*   印刷设计
*   插图

## 结论

在这篇文章中，我们快速地看了一些可以代替 Adobe 使用的工具。Adobe 有一套很棒的产品，但是它们与 Linux 不兼容。然而，并非所有的希望都落空了，有许多开源产品可以帮助你实现你的设计目标。

感谢阅读。