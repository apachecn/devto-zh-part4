# 创建交互式 CSS 3D 设备模型

> 原文：<https://dev.to/checkboxoz/creating-interactive-css-3d-device-mockups-1gkn>

[![](img/35785daeffe3daf939033d4246c84bcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d4rhv979--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.ozramos.com/wp-content/uploads/2019/08/mockup-shortcoe-screenshot.gif)

所以前几天我偶然发现了这个叫做 TriDiv 的很棒的 web 应用程序，它可以帮助你通过拖放来创建 CSS 3D 对象🤯因为我正在重新设计我的作品集，这启发了我去创造互动的模型。

## 创建 3D 实体模型

TriDiv 允许您使用立方体、圆柱体、棱柱和金字塔等基本形状构建模型，每个形状都有自己的可配置性，如边数和圆角。因为每一面都由普通的`<div>`组成，所以你可以给每一面指定特定的样式。

为了快速验证我的想法，我制作了一个简单的笔记本电脑，由 3 个立方体组成，其中两个包含圆角。然后，我给其中一个对象取了一个类名“screen ”,并给这个对象取了一个粉红色的默认背景(不过如果你只是一次性的，你也可以使用你自己的图片):

[![](img/77c06a6586fd4e77b3f54c3d0d2c801e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mx75NtOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.ozramos.com/wp-content/uploads/2019/08/laptop-pure-css-01.jpg)

## 添加悬停动画

[![](img/35785daeffe3daf939033d4246c84bcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d4rhv979--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.ozramos.com/wp-content/uploads/2019/08/mockup-shortcoe-screenshot.gif)

为了添加悬停动画，我选择使用[普通版本 Tilt.js](https://micku7zu.github.io/vanilla-tilt.js/index.html) (也有[jQuery 版本](http://gijsroge.github.io/tilt.js/))来获得悬停效果。然后我添加了一个有趣的效果，在上面的粉色`.screen`对象上，屏幕弹出标准 CSS。

[https://codepen.io/labofoz/embed/GVyjNN?height=600&default-tab=result&embed-version=2](https://codepen.io/labofoz/embed/GVyjNN?height=600&default-tab=result&embed-version=2)

## 更进一步

*   单击时，用实际项目的 iFrame 替换图像
*   添加移动和平板设备