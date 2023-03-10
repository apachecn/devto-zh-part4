# 着色器和点

> 原文：<https://dev.to/lesnitsky/shaders-and-points-3h2c>

## 第二天。简单着色器和三角形

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/8a2abf4d98412e88024769cb04b37b79.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/4f47dc9853f692fb8d8fa51375017962.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

[昨天](https://dev.to/lesnitsky/webgl-month-day-1-19ha)我们学习了 WebGL 的功能——计算可渲染区域内的每个像素颜色。但是它实际上是怎么做到的呢？

WebGL 是一个 API，它可以和你的 GPU 一起渲染东西。虽然 JavaScript 由 v8 在 CPU 上执行，但 GPU 不能执行 JavaScript，但它仍然是可编程的

GPU“理解”的语言之一是 [GLSL](https://en.wikipedia.org/wiki/OpenGL_Shading_Language) ，所以我们不仅要熟悉 WebGL API，还要熟悉这种新语言。

GLSL 是一种类似 C 的编程语言，所以对于 JavaScript 开发者来说，它很容易学习和编写。

但是我们在哪里写 glsl 代码呢？如何传递给 GPU 才能执行？

让我们写一些代码

让我们创建一个新的 js 文件，并获取对 WebGL 渲染上下文的引用

📄index.html

```
 </head>
    <body>
      <canvas></canvas>
-     <script src="./src/canvas2d.js"></script> +     <script src="./src/webgl-hello-world.js"></script>
    </body>
  </html> 
```

📄src/webgl-hello-world.js

```
const canvas = document.querySelector('canvas');
const gl = canvas.getContext('webgl'); 
```

GPU 可执行的程序通过 WebGL 渲染上下文的方法生成

📄src/webgl-hello-world.js

```
 const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
+ 
+ const program = gl.createProgram(); 
```

GPU 程序由两个【函数】组成
这些函数被称为`shaders`
WebGL 支持几种类型的着色器

在这个例子中，我们将使用`vertex`和`fragment`着色器。
两者都可以用`createShader`的方法创造出来

📄src/webgl-hello-world.js

```
 const gl = canvas.getContext('webgl');

  const program = gl.createProgram();
+ 
+ const vertexShader = gl.createShader(gl.VERTEX_SHADER);
+ const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

现在让我们编写尽可能简单的着色器

📄src/webgl-hello-world.js

```
 const vertexShader = gl.createShader(gl.VERTEX_SHADER);
  const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);
+ 
+ const vShaderSource = `
+ void main() {
+     
+ }
+ `; 
```

对于那些有一些 C/C++经验的人来说，这应该看起来很熟悉

不像 C 或 C++ `main`不返回任何东西，而是给一个全局变量`gl_Position`赋值

📄src/webgl-hello-world.js

```
 const vShaderSource = `
  void main() {
- +     gl_Position = vec4(0, 0, 0, 1);
  }
  `; 
```

现在，让我们更仔细地看看分配了什么。

着色器中有许多可用的函数。

`vec4`函数创建 4 个分量的矢量。

`gl_Position = vec4(0, 0, 0, 1);`

看起来很奇怪..我们生活在三维世界中，第四个元素到底是什么？难道是`time`？😕

不见得

[来自 MDN 的报价](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API/WebGL_model_view_projection#Homogeneous_Coordinates)

> 事实证明，这种添加允许许多操纵 3D 数据的好技术。
> 三维点是在典型的笛卡尔坐标系中定义的。添加的第四维将该点变为齐次坐标。它仍然表示 3D 空间中的一个点，并且可以很容易地演示如何通过一对简单的函数来构造这种类型的坐标。

现在我们可以忽略第四个组件，并将其设置为`1.0`,因为

好了，我们有一个着色器变量，着色器源在另一个变量中。我们如何将这两者联系起来？随着

📄src/webgl-hello-world.js

```
 gl_Position = vec4(0, 0, 0, 1);
  }
  `;
+ 
+ gl.shaderSource(vertexShader, vShaderSource); 
```

应该编译 GLSL 着色器才能执行

📄src/webgl-hello-world.js

```
 `;

  gl.shaderSource(vertexShader, vShaderSource);
+ gl.compileShader(vertexShader); 
```

编译结果可以由。这个方法返回一个“编译器”输出。如果是一个空字符串，那么一切都是好的

📄src/webgl-hello-world.js

```
 gl.shaderSource(vertexShader, vShaderSource);
  gl.compileShader(vertexShader);
+ 
+ console.log(gl.getShaderInfoLog(vertexShader)); 
```

我们需要对片段着色器做同样的事情，所以让我们实现一个辅助函数，我们也将用于片段着色器

📄src/webgl-hello-world.js

```
 }
  `;

- gl.shaderSource(vertexShader, vShaderSource);
- gl.compileShader(vertexShader); + function compileShader(shader, source) {
+     gl.shaderSource(shader, source);
+     gl.compileShader(shader); 
- console.log(gl.getShaderInfoLog(vertexShader));
+     const log = gl.getShaderInfoLog(shader);
+ 
+     if (log) {
+         throw new Error(log);
+     }
+ }
+ 
+ compileShader(vertexShader, vShaderSource); 
```

最简单的片段着色器是什么样子的？完全一样

📄src/webgl-hello-world.js

```
 }
  `;

+ const fShaderSource = `
+     void main() {
+         
+     }
+ `;
+ 
  function compileShader(shader, source) {
      gl.shaderSource(shader, source);
      gl.compileShader(shader); 
```

片段着色器的计算结果是颜色，它也是 4 个分量(r，g，b，a)的向量。与 CSS 不同，值在`[0..1]`而不是`[0..255]`的范围内。片段着色器的计算结果应该赋给变量`gl_FragColor`

📄src/webgl-hello-world.js

```
 const fShaderSource = `
      void main() {
- +         gl_FragColor = vec4(1, 0, 0, 1);
      }
  `;

  }

  compileShader(vertexShader, vShaderSource);
+ compileShader(fragmentShader, fShaderSource); 
```

现在我们应该把`program`和我们的着色器连接起来

📄src/webgl-hello-world.js

```
 compileShader(vertexShader, vShaderSource);
  compileShader(fragmentShader, fShaderSource);
+ 
+ gl.attachShader(program, vertexShader);
+ gl.attachShader(program, fragmentShader); 
```

下一步–链接程序。这个阶段需要验证顶点和片段着色器是否相互兼容(我们将在后面获得更多细节)

📄src/webgl-hello-world.js

```
 gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
+ 
+ gl.linkProgram(program); 
```

我们的应用程序可能有几个程序，所以我们应该在发出 draw 调用之前告诉 gpu 我们想要使用哪个程序

📄src/webgl-hello-world.js

```
 gl.attachShader(program, fragmentShader);

  gl.linkProgram(program);
+ 
+ gl.useProgram(program); 
```

好了，我们准备画点东西了

📄src/webgl-hello-world.js

```
 gl.linkProgram(program);

  gl.useProgram(program);
+ 
+ gl.drawArrays(); 
```

WebGL 可以呈现几种类型的“图元”

*   点
*   线
*   三角形的

我们应该传递一个我们想要呈现的原始类型

📄src/webgl-hello-world.js

```
 gl.useProgram(program);

- gl.drawArrays();
+ gl.drawArrays(gl.POINTS); 
```

有一种方法可以将包含图元位置信息的输入数据传递给顶点着色器，因此我们需要传递要渲染的第一个图元的索引

📄src/webgl-hello-world.js

```
 gl.useProgram(program);

- gl.drawArrays(gl.POINTS);
+ gl.drawArrays(gl.POINTS, 0); 
```

和图元计数

📄src/webgl-hello-world.js

```
 gl.useProgram(program);

- gl.drawArrays(gl.POINTS, 0);
+ gl.drawArrays(gl.POINTS, 0, 1); 
```

无渲染😢
怎么了？

实际上，为了渲染点，我们还应该在顶点着色器中指定点的大小

📄src/webgl-hello-world.js

```
 const vShaderSource = `
  void main() {
+     gl_PointSize = 20.0;
      gl_Position = vec4(0, 0, 0, 1);
  }
  `; 
```

惊叹声🎉我们说得有道理！

[![WebGL Point](img/f8045e8fe16752c2a12af4394bc25679.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9bTiwkyg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/webgl-point.png)

因为`gl_Position`是`vec4(0, 0, 0, 1)` = > `x == 0`和`y == 0`
WebGL 坐标系与`canvas2d`不同，所以渲染在画布中央

`canvas2d`

```
0.0
-----------------------→ width (px)
|
|
|
↓
height (px) 
```

`webgl`

```
 (0, 1)
                      ↑
                      |
                      |
                      |
(-1, 0) ------ (0, 0)-·---------> (1, 0)
                      |
                      |
                      |
                      |
                    (0, -1) 
```

现在让我们从 JS 中传递点坐标，而不是在 shader 中硬编码它

顶点着色器的输入数据被称为`attribute`
让我们定义`position`属性

📄src/webgl-hello-world.js

```
 const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);

  const vShaderSource = `
+ attribute vec2 position;
+ 
  void main() {
      gl_PointSize = 20.0;
-     gl_Position = vec4(0, 0, 0, 1); +     gl_Position = vec4(position.x, position.y, 0, 1);
  }
  `; 
```

为了用数据填充属性，我们需要获取属性位置。可以把它看作 javascript 世界中属性的唯一标识符

📄src/webgl-hello-world.js

```
 gl.useProgram(program);

+ const positionPointer = gl.getAttribLocation(program, 'position');
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

GPU 只接受类型化数组作为输入，所以让我们定义一个`Float32Array`作为指针位置的存储

📄src/webgl-hello-world.js

```
 const positionPointer = gl.getAttribLocation(program, 'position');

+ const positionData = new Float32Array([0, 0]);
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

但是这个数组不能原样传递给 GPU，GPU 应该有自己的缓冲区。
在 GPU 世界中有不同种类的“缓冲区”，在这种情况下我们需要`ARRAY_BUFFER`

📄src/webgl-hello-world.js

```
 const positionData = new Float32Array([0, 0]);

+ const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER);
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

要对 GPU 缓冲区进行任何更改，我们需要“绑定”它。buffer 绑定后，作为“当前”处理，任何缓冲区修改操作都将在“当前”缓冲区上执行。

📄src/webgl-hello-world.js

```
 const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

+ gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

为了用一些数据填充缓冲区，我们需要调用`bufferData`方法

📄src/webgl-hello-world.js

```
 const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

  gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
+ gl.bufferData(gl.ARRAY_BUFFER, positionData); 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

为了优化 GPU 端的缓冲区操作(内存管理)，我们应该向 GPU 传递一个“提示”，指示如何使用这个缓冲区。[有几种方法可以使用缓冲器](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/bufferData#Parameters)

*   `gl.STATIC_DRAW`:缓冲区的内容可能会经常使用，不会经常改变。内容被写入缓冲区，但不被读取。
*   `gl.DYNAMIC_DRAW`:缓冲区的内容可能会经常使用，经常改变。内容被写入缓冲区，但不被读取。
*   `gl.STREAM_DRAW`:缓冲区的内容可能不会经常使用。内容被写入缓冲区，但不被读取。

    使用 WebGL 2 上下文时，还可以使用以下值:

*   `gl.STATIC_READ`:缓冲区的内容可能会经常使用，不会经常改变。从缓冲区读取内容，但不写入内容。

*   `gl.DYNAMIC_READ`:缓冲区的内容可能会经常使用，经常改变。从缓冲区读取内容，但不写入内容。

*   `gl.STREAM_READ`:缓冲区的内容可能不会经常使用。从缓冲区读取内容，但不写入内容。

*   `gl.STATIC_COPY`:缓冲区的内容可能会经常使用，不会经常改变。用户既不写也不读内容。

*   `gl.DYNAMIC_COPY`:缓冲区的内容可能会经常使用，经常改变。用户既不写也不读内容。

*   `gl.STREAM_COPY`:缓冲区的内容可能会经常使用，不会经常改变。用户既不写也不读内容。

📄src/webgl-hello-world.js

```
 const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

  gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
- gl.bufferData(gl.ARRAY_BUFFER, positionData); + gl.bufferData(gl.ARRAY_BUFFER, positionData, gl.STATIC_DRAW); 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

现在我们需要告诉 GPU 应该如何从我们的缓冲区读取数据

必需的信息:

属性大小(对于`vec2`为 2，对于`vec3`为 3，以此类推)

📄src/webgl-hello-world.js

```
 gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, positionData, gl.STATIC_DRAW);

+ const attributeSize = 2;
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

缓冲区中的数据类型

📄src/webgl-hello-world.js

```
 gl.bufferData(gl.ARRAY_BUFFER, positionData, gl.STATIC_DRAW);

  const attributeSize = 2;
+ const type = gl.FLOAT; 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

正常化–指示数据值是否应该被限制在某个范围内

对于`gl.BYTE`和`gl.SHORT`，如果为真，则将值箝位到`[-1, 1]`

对于`gl.UNSIGNED_BYTE`和`gl.UNSIGNED_SHORT`，如果为真，则将值箝位到`[0, 1]`

对于类型`gl.FLOAT`和`gl.HALF_FLOAT`，该参数没有影响。

📄src/webgl-hello-world.js

```
 const attributeSize = 2;
  const type = gl.FLOAT;
+ const nomralized = false; 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

这两个我们以后再说😉

📄src/webgl-hello-world.js

```
 const attributeSize = 2;
  const type = gl.FLOAT;
  const nomralized = false;
+ const stride = 0;
+ const offset = 0; 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

现在我们需要调用`vertexAttribPointer`来设置我们的`position`属性

📄src/webgl-hello-world.js

```
 const stride = 0;
  const offset = 0;

+ gl.vertexAttribPointer(positionPointer, attributeSize, type, nomralized, stride, offset);
+ 
  gl.drawArrays(gl.POINTS, 0, 1); 
```

让我们试着改变点的位置

📄src/webgl-hello-world.js

```
 const positionPointer = gl.getAttribLocation(program, 'position');

- const positionData = new Float32Array([0, 0]);
+ const positionData = new Float32Array([1.0, 0.0]); 
  const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER); 
```

什么都没变😢但是为什么呢？

原来——默认情况下所有属性都是禁用的(用 0 填充),所以我们需要`enable`我们在属性中的位置

📄src/webgl-hello-world.js

```
 const stride = 0;
  const offset = 0;

+ gl.enableVertexAttribArray(positionPointer);
  gl.vertexAttribPointer(positionPointer, attributeSize, type, nomralized, stride, offset);

  gl.drawArrays(gl.POINTS, 0, 1); 
```

现在我们可以渲染更多的点！让我们用一个点来标记画布的每一个角落

📄src/webgl-hello-world.js

```
 const positionPointer = gl.getAttribLocation(program, 'position');

- const positionData = new Float32Array([1.0, 0.0]);
+ const positionData = new Float32Array([
+     -1.0, // point 1 x
+     -1.0, // point 1 y
+ 
+     1.0, // point 2 x
+     1.0, // point 2 y
+ 
+     -1.0, // point 3 x
+     1.0, // point 3 y
+ 
+     1.0, // point 4 x
+     -1.0, // point 4 y
+ ]); 
  const positionBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

  gl.enableVertexAttribArray(positionPointer);
  gl.vertexAttribPointer(positionPointer, attributeSize, type, nomralized, stride, offset);

- gl.drawArrays(gl.POINTS, 0, 1);
+ gl.drawArrays(gl.POINTS, 0, positionData.length / 2); 
```

让我们回到我们的着色器

我们不一定需要显式地将`position.x`和`position.y`传递给`vec4`构造函数，有一个`vec4(vec2, float, float)`覆盖

📄src/webgl-hello-world.js

```
 void main() {
      gl_PointSize = 20.0;
-     gl_Position = vec4(position.x, position.y, 0, 1); +     gl_Position = vec4(position, 0, 1);
  }
  `;

  const positionPointer = gl.getAttribLocation(program, 'position');

  const positionData = new Float32Array([
-     -1.0, // point 1 x
-     -1.0, // point 1 y +     -1.0, // top left x
+     -1.0, // top left y 
      1.0, // point 2 x
      1.0, // point 2 y 
```

现在让我们通过将每个位置除以 2.0 来移动所有点更靠近中心

📄src/webgl-hello-world.js

```
 void main() {
      gl_PointSize = 20.0;
-     gl_Position = vec4(position, 0, 1); +     gl_Position = vec4(position / 2.0, 0, 1);
  }
  `; 
```

结果:

[![Result](img/4c01b6560f989f91c42fb1c0518e57df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3THZ6C5E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/4points.png)

### 结论

我们现在对 GPU 和 WebGL 的工作原理有了更好的理解，可以渲染一些非常基本的东西
我们明天将探索更多的原始类型！

### 作业

用点
绘制一个`Math.cos`图提示:你所需要的就是用有效值填充`positionData`

* * *

[![GitHub stars](img/8a2abf4d98412e88024769cb04b37b79.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/4f47dc9853f692fb8d8fa51375017962.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)