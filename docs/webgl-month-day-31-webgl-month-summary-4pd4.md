# WebGL 月份。第 31 天。WebGL 月汇总

> 原文：<https://dev.to/lesnitsky/webgl-month-day-31-webgl-month-summary-4pd4>

[![GitHub stars](img/393e4aeeb2380ad46290ff945a45058b.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/31be46762e6a2b026741aebc85dd37de.png)T6】](https://twitter.com/lesnitsky_a)

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

嘿👋

欢迎来到 WebGL 月的最后一天。这篇文章不会涉及任何新的话题，而是总结过去的 30 天

### 以前的教程:

#### [第一天。简介](https://dev.to/lesnitsky/webgl-month-day-1-19ha)

本文不涉及任何 WebGL 主题，而是解释 WebGL 在幕后做了什么。TL；DR:它计算它必须画的每个像素的颜色

#### [第二天。着色器和点](https://dev.to/lesnitsky/shaders-and-points-3h2c)

介绍 WebGL API 和 GLSL 着色器，具有尽可能简单的原始类型-点

#### [第三天。着色器制服、线条和三角形](https://dev.to/lesnitsky/webgl-month-day-3-shader-uniforms-lines-and-triangles-5dof)

本文介绍了更多将数据传递给着色器的方法，并使用更复杂的图元进行渲染

#### [第四天。着色器变化](https://dev.to/lesnitsky/shader-varyings-2p0f)

将数据从顶点传递到片段着色器

#### [第五天。交错缓冲器](https://dev.to/lesnitsky/webgl-month-day-5-interleaved-buffers-2k9a)

存储顶点数据并将其传递给着色器的替代方法

#### [第六天。索引缓冲器](https://dev.to/lesnitsky/webgl-month-day-6-indexed-buffer-ll6)

一种有助于减少重复顶点数量的技术

#### [第七天。清理和加工](https://dev.to/lesnitsky/webgl-month-day-7-a-bit-of-cleanup-and-tooling-bd4)

WebGL 很有趣，但是当项目增长时，它需要一些工具。幸运的是，我们有 webpack 这样的工具

#### [第八天。纹理](https://dev.to/lesnitsky/webgl-month-day-8-textures-1mk8)

纹理介绍

#### [第九天。图像过滤器](https://dev.to/lesnitsky/webgl-month-day-9-image-filters-5g8e)

利用片段着色器实现简单的图像“过滤器”(反相、黑白、棕褐色)

#### [第十天。多重纹理](https://dev.to/lesnitsky/webgl-month-day-10-multiple-textures-gf3)

如何在一个 webgl 程序中使用多个纹理

#### [第 11 天。减少 WebGL 样板文件](https://dev.to/lesnitsky/webgl-month-day-11-3plb)

实现一些实用类和函数来减少 WebGL 样板文件

#### [第十二天。高 dpi 显示和 WebGL 视口](https://dev.to/lesnitsky/webgl-month-day-12-highdpi-displays-and-webgl-viewport-2cg3)

如何用画布处理 retina 显示并使用 webgl 视口

#### [第十三天。简单动画](https://dev.to/lesnitsky/webgl-month-simple-animation-5hc3)

所有先前的例子都是静态图像，本文将为场景添加一些运动

#### [第十四天。3D 简介](https://dev.to/lesnitsky/webgl-month-day-14-intro-to-3d-2ni2)

三维渲染所需的三维计算理论。无代码

#### [第十五天。渲染一个立方体](https://dev.to/lesnitsky/webgl-month-day-15-rendering-a-3d-cube-190f)

三维理论在实践中的应用

#### [第十六天。深度缓冲。立方体表面颜色](https://dev.to/lesnitsky/webgl-month-day-16-colorizing-cube-depth-buffer-and-array-uniforms-4nhc)

本文包含对前一个示例的修复，并添加了更多颜色

#### [第十七天。OBJ 格式](https://dev.to/lesnitsky/webgl-month-day-17-exploring-obj-format-6fn)

实现简单的 OBJ 格式解析器

#### [第十八天。平面阴影](https://dev.to/lesnitsky/webgl-month-day-18-flat-shading-3nhg)

平面阴影的实现

#### [第十九天。渲染多个对象](https://dev.to/lesnitsky/webgl-month-day-19-rendering-multiple-objects-45m7)

一个典型的 3D 场景由多个物体组成，本教程将教你如何渲染多个物体

#### [第二十天。渲染一个 minecraft 污垢立方体](https://dev.to/lesnitsky/webgl-month-day-20-rendering-a-minecraft-dirt-cube-5ag3)

使用 Blender 和 WebGL 对 3D 对象进行纹理处理

#### [第二十一天。渲染 minecraft 地形](https://dev.to/lesnitsky/webgl-month-day-21-rendering-a-minecraft-terrain-24b5)

我们已经学会了如何渲染多个物体。如何渲染 10000 个物体？

#### [第二十二天。将 webgl 调用次数减少 5000 次](https://dev.to/lesnitsky/webgl-month-day-22-reducing-number-of-webgl-calls-by-5000-times-3a4j)

前面的例子有效，但不是真正的性能。本文解释了*实例化*(一种在渲染大量相同对象时有助于提高性能的技术)

#### [第二十三天。Skynox](https://dev.to/lesnitsky/webgl-month-day-23-skybox-in-webgl-1eig)

向场景中添加“环境”

#### [第二十四天。结合地形和天空盒](https://dev.to/lesnitsky/webgl-month-day-24-combining-terrain-and-skybox-kgo)

如何一起使用多个 WebGL 程序

#### [第二十五天。Mipmaps](https://dev.to/lesnitsky/webgl-month-day-25-mipmaps-33i)

一种提高着色器从纹理读取数据的性能的技术

#### [第二十六天。渲染到纹理](https://dev.to/lesnitsky/webgl-month-day-26-rendering-to-texture-4hkp)

渲染到纹理允许应用一些“后期效果”,并可能用于各种用例

#### [第二十七天。点击检测。第一部分](https://dev.to/lesnitsky/webgl-month-day-27-click-detection-part-i-5920)

#### [第二十八天。点击检测。第二部分](https://dev.to/lesnitsky/webgl-month-day-28-click-detection-part-ii-367e)

检测光标下的对象可能看起来是一项艰巨的任务，但在 JS 中不需要复杂的 3d 数学就可以完成

#### [第二十九天。雾](https://dev.to/lesnitsky/webgl-month-day-29-fog-58od)

用雾改善场景

#### [第三十天。WebGL 中的文本渲染](https://dev.to/lesnitsky/webgl-month-day-30-text-rendering-in-webgl-3ih3)

WebGL 中文本渲染技术综述

### 有用的链接

我一年半前才开始使用 WebGL。我的 WebGL 之旅从一个很棒的资源开始——[https://webglfundamentals.org/](https://webglfundamentals.org/)

还有一件重要的事情需要理解:WebGL 只是 OpenGL 的一个包装器，所以几乎所有来自 OpenGL 教程的东西都可以在 WebGL 中使用:[https://learnopengl.com/](https://learnopengl.com/)

探索更多 glsl 的东西:[https://thebookofshaders.com/](https://thebookofshaders.com/)

着色器的代码笔:[https://www.shadertoy.com/](https://www.shadertoy.com/)

[MDN 上的 WebGL 入门教程](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL)

### 谢谢！

感谢您加入 WebGL 月。希望这篇文章能帮助你学习 WebGL！😉
欢迎向 [github repo](https://github.com/lesnitsky/webgl-month) 提交问题、建议和改进，通过电子邮件或 [twitter](https://twitter.com/lesnitsky_a) 联系我