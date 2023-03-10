# 3D 打印简介

> 原文：<https://dev.to/erikaheidi/an-introduction-to-3d-printing-ln1>

## 简介

3D 打印指的是各种各样的过程，其中计算机操作的机器通过连接或固化材料来创建三维物体，通常是一层一层地直到整个物体完成。3D 打印也就是俗称的*增材制造*。

尽管该技术的早期似乎认为 3D 打印是一种昂贵的工艺，只适合美观的原型，但附加 3D 打印背后的技术在过去几年中已经发展到令人印象深刻的规模，降低了壁垒，使其更受最终用户的欢迎和负担得起。

## 为什么要 3D 打印

最初让我进入 3D 打印的是我对[电子学](https://dev.to/erikaheidi/a-primer-on-basic-electronics-and-circuits-n3e)的兴趣。3D 打印为爱好者和制造商打开了一个新世界，因为它降低了创建原始原型的障碍。但是 3D 打印不仅仅用于原型制作；你可以用它来修理家里的东西，用不同的材料制作有用的工具和适配器，进行装饰，为你的孩子制作有趣的原创玩具。

它最令人兴奋的部分是，你可以在像 [MyMiniFactory](http://myminifactory.com) 和 [Thingiverse](http://thingiverse.com) 这样的网站上免费下载成千上万的现有模型，你总是可以用 3D 软件甚至使用代码来创建自己独特的设计！

## 工作原理

有不同的 3D 打印方法，但我们将专注于 FDM 3D 打印，因为这是当今最流行的。

FDM 代表[熔融沉积成型](https://en.wikipedia.org/wiki/Fused_filament_fabrication#Fused_deposition_modeling)，这在实践中意味着连续的熔融材料流(通常是塑料细丝)通过喷嘴挤出，并立即固化，与印刷板上的现有层融合在一起。

这是一个缓慢的过程，但结果可能相当可观。下面的时间推移让我们更好地了解它是如何工作的。这张照片花了大约 20 个小时才完成:

[https://www.youtube.com/embed/GVxVYZv7oM8](https://www.youtube.com/embed/GVxVYZv7oM8)

## 选择 3D 打印机

我不是在给你买哪台打印机的建议。去看看 Youtube 上的视频，阅读一下，考虑一下你打算用它做什么。有许多供应商提供不同价格范围的 3D 打印机，从几百美元到几千美元不等。也就是说，我们有一辆 [Prusa i3 MK3S](https://prusa3d.com) (从 MK3 升级而来)，我们对它非常满意。

### 套件还是组装？

如果你想要的只是“即插即用”(几乎是，因为他们通常仍然需要一些校准)，你应该考虑购买一台已经组装好的打印机。它们更贵，但是你会花很多时间和精力把所有的东西都组装起来——即使有最好的文档，对于初学者来说，把所有的东西都组装好还是很难的。

这绝对是可能的，所以如果你想花点钱，而且你不着急(意思是:你会有时间和耐心仔细组装东西)，你应该去买工具包。

[![Prusa kit](img/b182da89b26899c93c43a05f0df16e03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--StcFSEdL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://heidislab.ams3.digitaloceanspaces.com/3d-printing/kit_assemble.jpg)

我们选择了 Prusa MK3，我很高兴我们选择了套件。组装套件教会了我们很多！这套工具最大的好处是它可以教你很多关于这台机器的知识。从机械角度讲，你会更好地理解它是如何工作的。如果有什么东西坏了，如果有什么东西看起来不对劲，你会更好地理解发生了什么，也许还能解决它。

## 3D 打印灯丝

为了用 3D 打印机打印东西，你需要 3D 打印细丝。这些通常成卷出售，按重量计量(1 千克卷，500 克卷..).有很多不同的品牌和材料，最受欢迎的是解放军。

PLA 是打印最直接的材料。其他材料，如 ABS，可能很难打印，需要特殊的条件，如稳定的室温和非常热的打印床。

另一个很好的选择是 *PETG* ，它易于打印，并且比 PLA 具有更高的电阻。PETG 适用于不易破碎的物品，以及必须承受更高温度或暴露在户外的物品。

其他材料包括柔性细丝、浸铜(以及其他金属)、碳纤维等等。这些通常更难打印，用于特殊目的。

## 3D 打印软件

3D 打印需要特殊的软件来将我们从像 [MyMiniFactory](http://myminifactory.com) 这样的网站下载的 STL 文件转换成你的 3D 打印机可以理解的实际 GCODE。将 STL 转换成 GCODE 的过程被称为**切片**。一个流行的切片程序是 [Slic3r](https://slic3r.org/) ，还有 [PrusaSlicer](https://www.prusa3d.com/prusaslicer/) 供那些拥有 Prusa 打印机的人使用，无论是原装的还是衍生的。

要创建原始模型，你可以使用 3D 建模软件——有很多软件可供选择。下面这段时间展示了我用 Freecad 为我的女儿设计一个简单形状的玩具:

[https://www.youtube.com/embed/pIEu5Qta91w](https://www.youtube.com/embed/pIEu5Qta91w)

更令人兴奋的是使用**代码**创建 3D 模型。你可以用 [OpenScad](https://www.openscad.org/) 来做到这一点，这是一个通过代码创建实体 3D 模型的开源平台。这使您能够创建可定制的 3D 对象！

下面是我用这个工具创建的一个简单名片的预览图:

```
font = "Ubuntu Mono";
letter_size = 60;
padding = 20;

string = "@erikaheidi";
textlen = len(string);

box_width = letter_size*textlen*0.8;
box_height = letter_size + (2*padding);
box_thickness = 20;

start_x = 0 - (box_width / 2) + padding;
start_z = padding;

module text3d(string) {
  linear_extrude(height = box_thickness - 10) {
    text(string, size = letter_size, font = font, halign = "center", valign = "center", $fn = 64);
  }
}

module tag(width, height, thickness) {
    spacing = 20;
    difference() {
        //tag body
        linear_extrude(thickness) {
            square([width, height], center = true);
        }
        //tag hole for lanyard
        linear_extrude(thickness) {
            hole_x = 0 - width / 2 + spacing;
            hole_y = 0 - height / 2 + spacing;
            translate([hole_x, hole_y, 0]) square([15, height-spacing*2]);
        }
    }
}

difference() {
    tag(box_width, box_height, box_thickness);
    translate([0+padding, 0, start_z]) text3d(string);
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是打印结果，以及其他定制打印结果:

[![custom prints](img/d17548f14ce9939f80b50b090bb22f14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ai5Djno9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://heidislab.ams3.digitaloceanspaces.com/3d-printing/custom_prints.jpg)

好玩吧。如果你想在这里看到更多关于 3D 打印的内容，或者特别公开的例子，请在评论中告诉我！

干杯，下次见；-)