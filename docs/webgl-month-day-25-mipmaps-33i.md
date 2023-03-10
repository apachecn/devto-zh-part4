# WebGL 月份。第 25 天。小贴图

> 原文：<https://dev.to/lesnitsky/webgl-month-day-25-mipmaps-33i>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/8806495b741c28f20124c350a1f0c19a.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/29a212e295f7b2984df06dc6b659369d.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月

今天我们将学习另一个 webgl 概念，它可能会提高最终渲染图像的质量

首先，我们需要讨论颜色是如何从纹理中读取的。

假设我们有一个 1024x1024 的图像，但是在画布上只渲染了一个 512x512 的区域。因此，结果图像中的每个像素代表原始纹理中的 4 个像素。

这就是`gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, filter)`发挥作用的地方

关于如何从纹理中读取颜色，有几种算法

*   这个将读取原始图像的 4 个像素并混合 4 个像素的颜色来计算最终的像素颜色

*   `gl.NEARETS`将从原始图像中取最近的像素坐标并使用该颜色。虽然性能更高，但这种方法的质量较低

这两种方法都有其局限性，尤其是当需要绘制纹理的区域比原始纹理小得多时

在处理纹理时，有一种特殊的技术可以提高渲染的质量和性能。这种特殊的纹理被称为[MIP maps]–预先计算的图像序列，其中每个下一个图像的分辨率都逐渐变小。因此，当片段着色器从纹理中读取颜色时，它采用大小最接近的纹理，并从中读取颜色。

在 WebGL 1.0 中，只能为“2 的幂”大小的纹理(256x256、512x512、1024x1024 等)生成 mipmaps。)

这就是我们的污垢立方体的 mipmap 的样子

[![Mipmap](img/6d7f73a745b81e2906d158796e72ed99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iti9rFkE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/mipmap.jpg)

不要担心，你不需要为你所有的纹理生成这样的序列，如果你的纹理大小是 2 的幂，这可以自动完成

📄src/minecraft-terrain.js

```
 const State = {};

+ /**
+  *
+  * @param {WebGLRenderingContext} gl
+  */
  export async function prepare(gl) {
      const vShader = gl.createShader(gl.VERTEX_SHADER);
      const fShader = gl.createShader(gl.FRAGMENT_SHADER);
      await loadImage(textureSource).then((image) => {
          const texture = createTexture(gl);
          setImage(gl, texture, image);
+ 
+         gl.generateMipmap(gl.TEXTURE_2D);
      });

      setupAttributes(gl); 
```

而为了让 GPU 从 mipmap 中读取一个像素颜色，我们需要指定`TEXTURE_MIN_FILTER`。

📄src/minecraft-terrain.js

```
 setImage(gl, texture, image);

          gl.generateMipmap(gl.TEXTURE_2D);
+         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.NEAREST_MIPMAP_LINEAR);
      });

      setupAttributes(gl); 
```

`NEAREST_MIPMAP_LINEAR`将选择最接近尺寸的小中见大贴图，并插入 4 个像素以获得最终颜色

今天到此为止！

感谢阅读，明天见👋

* * *

[![GitHub stars](img/8806495b741c28f20124c350a1f0c19a.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/29a212e295f7b2984df06dc6b659369d.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)