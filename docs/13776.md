# WebGL 月份。第十二天。高 dpi 显示和 webgl 视口

> 原文：<https://dev.to/lesnitsky/webgl-month-day-12-highdpi-displays-and-webgl-viewport-2cg3>

## 第十二天。高 dpi 显示和 webgl 视口

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/4b13c63f7749e880ea0101298458fa81.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/4c84b80da3e55d8a5b832827716e270f.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

嘿👋欢迎回到网络博客月

所有以前的教程都是在默认大小的画布上完成的，让我们把图片放大吧！

我们需要先调整一点 css 来让 body 充满屏幕

📄index.html

```
 <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <meta http-equiv="X-UA-Compatible" content="ie=edge" />
      WebGL Month
+ 
+     <style>
+     html, body {
+       height: 100%;
+     }
+ 
+     body {
+       margin: 0;
+     }
+     </style>
    </head>
    <body>
      <canvas></canvas> 
```

现在我们可以阅读身体尺寸

📄src/texture.js

```
 const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

+ const width = document.body.offsetWidth;
+ const height = document.body.offsetHeight;
+ 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

并设置画布尺寸

📄src/texture.js

```
 const width = document.body.offsetWidth;
  const height = document.body.offsetHeight;

+ canvas.width = width;
+ canvas.height = height;
+ 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

好的，画布大小改变了，但是我们的图片不是全屏的，为什么？

原来改变画布大小是不够的，我们还需要指定一个 viwport。将视口视为矩形，用作绘图区域，并将其插入到`[-1...1]`剪辑空间

📄src/texture.js

```
 gl.uniform2fv(programInfo.uniformLocations.resolution, [canvas.width, canvas.height]);

+     gl.viewport(0, 0, canvas.width, canvas.height);
+ 
      gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

现在我们的图片填充了整个文档，但是有点模糊。显而易见的原因——我们的纹理不够大，所以它应该被拉伸，失去质量。没错，但是还有一个原因。

现代显示器在物理像素尺寸上适合更高数量的实际像素(苹果称之为视网膜)。有一个全局变量`devicePixelRatio`可能会帮助我们。

📄src/texture.js

```
 const width = document.body.offsetWidth;
  const height = document.body.offsetHeight;

- canvas.width = width;
- canvas.height = height; + canvas.width = width * devicePixelRatio;
+ canvas.height = height * devicePixelRatio; 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

好了，现在我们的画布有了一个合适的尺寸，但是它比视网膜显示器上的身体要大。我们如何解决它？
我们可以用 css `width`和`height`属性将画布缩小到物理尺寸

📄src/texture.js

```
 canvas.width = width * devicePixelRatio;
  canvas.height = height * devicePixelRatio;

+ canvas.style.width = `${width}px`;
+ canvas.style.height = `${height}px`;
+ 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

总结一下，画布的`width`和`height`属性以像素为单位指定实际大小，但是为了使图片在高 dpi 显示上清晰，我们需要在`devicePixelRatio`上乘以宽度和高度，并用 css 缩小画布

现在我们还可以调整画布的大小

📄src/texture.js

```
 gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0);
  });
+ 
+ 
+ window.addEventListener('resize', () => {
+     const width = document.body.offsetWidth;
+     const height = document.body.offsetHeight;
+ 
+     canvas.width = width * devicePixelRatio;
+     canvas.height = height * devicePixelRatio;
+ 
+     canvas.style.width = `${width}px`;
+     canvas.style.height = `${height}px`;
+ 
+     gl.viewport(0, 0, canvas.width, canvas.height);
+ }); 
```

哎呀，调整大小后画布被清除。原来，`width`或`height`属性的修改迫使浏览器清空 canvas(对于`2d`上下文也是如此)，所以我们需要再次发出一个 draw 调用。

📄src/texture.js

```
 canvas.style.height = `${height}px`;

      gl.viewport(0, 0, canvas.width, canvas.height);
+ 
+     gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

今天就到这里，明天见👋

[![GitHub stars](img/4b13c63f7749e880ea0101298458fa81.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/4c84b80da3e55d8a5b832827716e270f.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)