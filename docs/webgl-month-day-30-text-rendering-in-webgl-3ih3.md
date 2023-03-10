# WebGL 月份。第三十天。WebGL 中的文本呈现

> 原文：<https://dev.to/lesnitsky/webgl-month-day-30-text-rendering-in-webgl-3ih3>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/7135120af1222f52e8cbd2d667467057.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/9e598f0325869910a956a73860b171ca.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到 WebGL 月。

在以前的教程中，我们专注于渲染 2d 和 3d 图形，但从未渲染过文本，这是任何应用程序的重要部分。

在这篇文章中，我们将回顾文本渲染的可能方式。

### HTML 覆盖

最明显和简单的解决方案是用 HTML 渲染文本，并把它放在 webgl 画布上，但这只适用于 2D 场景，3D 内容需要一些计算来计算文本位置和 css 转换

### 画布作为纹理

其他技术可以应用于更广泛的情况。这需要几个步骤

1.  创建另一个画布
2.  获取 2d 上下文(`canvas.getContext('2d')`)
3.  用`fillText`或`strokeText`渲染文本
4.  使用此画布作为具有正确纹理坐标的 webgl 纹理

由于纹理是光栅化的图像，当你“靠近”物体时，它会失去质量

### 字形纹理

每个字体实际上是一组“字形”——每个符号都呈现在一个图像中

```
A | B | C | D | E | F | G |
---------------------------
H | I | J | K | L | M | N |
... 
```

每个字母都有自己的“属性”，比如宽度(`i`比`W`细)、高度(`o`比`L`)等等。
这些属性将影响如何构建包含每个字母的矩形

通常，除了纹理之外，你还需要一个 javascript 对象来描述原始纹理图像中的所有属性和坐标

```
const font = {
    textureSize: {
        width: 512,
        height: 512,
    },
    height: 32,
    glyphs: {
        a: { x: 0, y: 0, height: 32, width: 16 },
        b: { x: 16, y: 0, height: 32, width: 14 },
    },
    // ...
}; 
```

为了渲染一些文本，你需要这样的东西

```
function getRects(text, sizeMultiplier) {
    let prevLetterX = 0;

    const rects = text.split('').map((symbol) => {
        const glyph = font.glyphs[symbol];

        return {
            x: prevLetterX,
            y: font.height - glyph.height,
            width: glyph.width * sizeMultiplier,
            height: glyph.height * sizeMultiplier,
            texCoords: glyph,
        };
    });
} 
```

稍后这个“rects”将用于生成属性数据

```
import { createRect } from './gl-helpers';

function generateBuffers(rects) {
    const attributeBuffers = {
        position: [],
        texCoords: [],
    };

    rects.forEach((rect, index) => {
        attributeBuffers.position.push(...createRect(rect.x, rect.y, rect.width, rect.height)),
            attributeBuffers.texCoords.push(
                ...createRect(rect.texCoords.x, rect.texCoords.y, rect.texCoords.width, rect.texCoords.height)
            );
    });

    return attributeBuffers;
} 
```

有一个 [gl-render-text](https://www.npmjs.com/package/gl-render-text) 包可以渲染基于纹理的字体

### 字体三角剖分

由于 webgl 能够绘制三角形，一个更明显的解决方案是将每个字母分成三角形
这似乎是一个非常复杂的任务😢

幸运的是——有一个 [fontpath-gl](https://github.com/mattdesl/fontpath-gl) 包可以做到这一点

### 有符号距离字段字体

在 OpenGL/WebGL 中渲染文本的另一种技术

点击此处查看更多信息

* * *

[![GitHub stars](img/5eea89274fa9a1b31a3f227425518833.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/9e598f0325869910a956a73860b171ca.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)