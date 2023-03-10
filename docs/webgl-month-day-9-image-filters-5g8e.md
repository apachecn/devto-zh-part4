# WebGL 月份。第九天。图像过滤器

> 原文：<https://dev.to/lesnitsky/webgl-month-day-9-image-filters-5g8e>

## 图像滤镜

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/89e1d594cccb84fe5f8a93138d1b6fe3.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/d81e7c708149489955f5591d1d4e0f78.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎回到网络博客月

昨天我们已经学习了如何在 webgl 中使用纹理，所以让我们利用这些知识来创建一些有趣的东西。

今天我们将探索如何实现简单的图像过滤器

### 逆

第一个也是最简单的过滤器可能是反转图像的所有颜色。

我们如何反转颜色？

原始值在范围`[0..1]`内

如果我们从每个分量中减去`1`，我们将得到负值，在 glsl 中有一个`abs`函数

你也可以在 glsl 中定义除了`void main`之外的其他函数，就像在 C/C++中一样，所以让我们创建`inverse`函数

📄src/shaders/texture.f.glsl

```
 uniform sampler2D texture;
  uniform vec2 resolution;

+ vec4 inverse(vec4 color) {
+     return abs(vec4(color.rgb - 1.0, color.a));
+ }
+ 
  void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = texture2D(texture, texCoord); 
```

让我们实际使用它

📄src/shaders/texture.f.glsl

```
 void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = texture2D(texture, texCoord);
+ 
+     gl_FragColor = inverse(gl_FragColor);
  } 
```

瞧，我们有一个只有 4 行代码的反向滤波器

[![Inverse](img/6f61dabaa9fbf8c4eec98c3a31daa904.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KmjTLNAE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/inverse-filter.png)

### 黑白分明

我们来想想如何实现黑白滤镜。

白色是`vec4(1, 1, 1, 1)`

黑色是`vec4(0, 0, 0, 1)`

什么是灰色阴影？另外，我们需要给每个颜色分量添加相同的值。

所以基本上我们需要计算每个组件的“亮度”值。在非常简单的实现中，我们可以将所有颜色分量相加，然后除以 3(算术平均值)。

> 注意:这不是最好的方法，因为不同的颜色会给出相同的结果(例如 vec3(0.5，0，0)和 vec3(0，0.5，0)，但实际上这些颜色有不同的“亮度”，我只是试图让这些例子简单易懂)

好，让我们试着实现它

📄src/shaders/texture.f.glsl

```
 return abs(vec4(color.rgb - 1.0, color.a));
  }

+ vec4 blackAndWhite(vec4 color) {
+     return vec4(vec3(1.0, 1.0, 1.0) * (color.r + color.g + color.b) / 3.0, color.a);
+ }
+ 
  void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = texture2D(texture, texCoord);

-     gl_FragColor = inverse(gl_FragColor);
+     gl_FragColor = blackAndWhite(gl_FragColor);
  } 
```

哇哦。看起来不错

[![Black and white](img/bfde37a8d3ddb39c93420c0bcb1e5bc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rqU-q2G3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/black-and-white.png)

### 棕褐色

好了，还有一个更花哨的效果，就是用棕褐色滤镜拍出的“老式”照片。

[棕褐色是红褐色](https://en.wikipedia.org/wiki/Sepia_%28color%29)。RGB 值为`112, 66, 20`

让我们定义`sepia`功能和颜色

📄src/shaders/texture.f.glsl

```
 return vec4(vec3(1.0, 1.0, 1.0) * (color.r + color.g + color.b) / 3.0, color.a);
  }

+ vec4 sepia(vec4 color) {
+     vec3 sepiaColor = vec3(112, 66, 20) / 255.0;
+ }
+ 
  void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = texture2D(texture, texCoord); 
```

一个天真而简单的实现将是通过某个因子用棕褐色来内插原始颜色。为此有一个`mix`函数

📄src/shaders/texture.f.glsl

```
 vec4 sepia(vec4 color) {
      vec3 sepiaColor = vec3(112, 66, 20) / 255.0;
+     return vec4(
+         mix(color.rgb, sepiaColor, 0.4),
+         color.a
+     );
  }

  void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = texture2D(texture, texCoord);

-     gl_FragColor = blackAndWhite(gl_FragColor);
+     gl_FragColor = sepia(gl_FragColor);
  } 
```

结果:

[![Sepia](img/4bec73ca6caca2b408cbe58477245ea0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FkJ5kEMO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/sepia.png)

这应该让你更好地了解在片段着色器中可以做些什么。

尝试实现一些其他过滤器，如饱和度或鲜明度

明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/89e1d594cccb84fe5f8a93138d1b6fe3.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/d81e7c708149489955f5591d1d4e0f78.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)