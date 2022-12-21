# WebGL 月份。第六天。变址缓冲器

> 原文：<https://dev.to/lesnitsky/webgl-month-day-6-indexed-buffer-ll6>

## 第六天。索引缓冲区

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/c8c1367e77180f3b4aa22d1a14d4f92f.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/aae50486f49e914bcab8874d073fc8c8.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎回到 WebGL 月。[昨天](https://dev.to/lesnitsky/webgl-month-day-5-interleaved-buffers-2k9a)我们已经学习了如何使用交错缓冲器。然而，我们的缓冲区包含许多重复的数据，因为一些多边形共享相同的顶点

让我们回到矩形的简单例子

📄src/webgl-hello-world.js

```
 [128, 0.0, 128, 255], // purple
  ];

- const triangles = createHexagon(canvas.width / 2, canvas.height / 2, canvas.height / 2, 7);
+ const triangles = createRect(0, 0, canvas.height, canvas.height); 
  function createHexagon(centerX, centerY, radius, segmentsCount) {
      const vertexData = []; 
```

并用唯一的顶点坐标填充它

📄src/webgl-hello-world.js

```
 const triangles = createRect(0, 0, canvas.height, canvas.height);

+ function createRect(top, left, width, height) {
+     return [
+         left, top, // x1 y1
+         left + width, top, // x2 y2
+         left, top + height, // x3 y3
+         left + width, top + height, // x4 y4
+     ];
+ }
+ 
  function createHexagon(centerX, centerY, radius, segmentsCount) {
      const vertexData = [];
      const segmentAngle =  Math.PI * 2 / (segmentsCount - 1); 
```

让我们现在也禁用颜色属性

📄src/webgl-hello-world.js

```
 const attributeSize = 2;
  const type = gl.FLOAT;
  const nomralized = false;
- const stride = 24; + const stride = 0;
  const offset = 0;

  gl.enableVertexAttribArray(positionLocation);
  gl.vertexAttribPointer(positionLocation, attributeSize, type, nomralized, stride, offset);

- gl.enableVertexAttribArray(colorLocation);
- gl.vertexAttribPointer(colorLocation, 4, type, nomralized, stride, 8); + // gl.enableVertexAttribArray(colorLocation);
+ // gl.vertexAttribPointer(colorLocation, 4, type, nomralized, stride, 8); 
  gl.drawArrays(gl.TRIANGLES, 0, vertexData.length / 6); 
```

好的，我们的缓冲区包含 4 个顶点，但是 webgl 如何用 4 个顶点渲染 2 个三角形呢？有一种特殊类型的缓冲区可以指定如何从顶点缓冲区获取数据并构建图元(在我们的例子中是三角形)

这个缓冲区被称为`index buffer`，它包含顶点缓冲区中顶点数据块的索引。
所以我们需要指定三角形顶点的索引。

📄src/webgl-hello-world.js

```
 const vertexData = new Float32Array(triangles);
  const vertexBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

+ const indexBuffer = gl.createBuffer(gl.ARRAY_BUFFER);
+ 
+ const indexData = new Uint6Array([
+     0, 1, 2, // first triangle
+     1, 2, 3, // second trianlge
+ ]);
+ 
  gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertexData, gl.STATIC_DRAW);
  gl.lineWidth(10); 
```

下一步–将数据上传到 WebGL 缓冲区。
为了告诉 GPU 我们正在使用`index buffer`，我们需要传递`gl.ELEMENT_ARRAY_BUFFER`作为`gl.bindBuffer`和`gl.bufferData`的第一个参数

📄src/webgl-hello-world.js

```
 1, 2, 3, // second trianlge
  ]);

+ gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
+ gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, indexData, gl.STATIC_DRAW);
+ 
  gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertexData, gl.STATIC_DRAW);
  gl.lineWidth(10); 
```

最后一步:为了渲染索引顶点，我们需要调用不同的方法——`drawElements`而不是`drawArrays`

📄src/webgl-hello-world.js

```
 const indexBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

- const indexData = new Uint6Array([
+ const indexData = new Uint8Array([
      0, 1, 2, // first triangle
      1, 2, 3, // second trianlge
  ]);
  // gl.enableVertexAttribArray(colorLocation);
  // gl.vertexAttribPointer(colorLocation, 4, type, nomralized, stride, 8);

- gl.drawArrays(gl.TRIANGLES, 0, vertexData.length / 6);
+ gl.drawElements(gl.TRIANGLES, indexData.length, gl.UNSIGNED_BYTE, 0); 
```

等等，为什么没有渲染？

原因是我们禁用了颜色属性，所以它被填充了零。(0，0，0，0)-透明黑色。让我们来解决这个问题

📄src/webgl-hello-world.js

```
 void main() {
          gl_FragColor = vColor / 255.0;
+         gl_FragColor.a = 1.0;
      }
  `; 
```

### 结论

我们现在知道如何使用索引缓冲区来消除我们需要上传到 gpu 的顶点数量。矩形的例子非常简单(只有 2 个顶点被复制)，另一方面，这是 33%，所以在渲染大量数据时，这可能是一个相当大的性能提升，特别是如果你频繁地更新顶点数据和重新加载缓冲区内容

### 作业

使用索引缓冲区渲染 n 边形

明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/c8c1367e77180f3b4aa22d1a14d4f92f.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/aae50486f49e914bcab8874d073fc8c8.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)