# WebGL 月份。第 26 天。渲染到纹理

> 原文：<https://dev.to/lesnitsky/webgl-month-day-26-rendering-to-texture-4hkp>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/7a62e6572a80bbc8913957ac9042103e.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/a66003765b1d742c03ff459eb1614ef8.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎来到 WebGL 月。

在我们之前的一个教程中，我们已经建立了一些简单的图像过滤器，如“黑白”、“棕褐色”等。我们能否将这种“后期效果”不仅应用于现有图像，还应用于我们正在渲染的整个 3d 场景？

是的，我们可以！然而，我们仍然需要一个纹理来处理，所以我们需要先把场景渲染到纹理上，而不是画布上

从第一个教程中我们知道，canvas 只是一个像素颜色的缓冲区(4 个整数，r，g，b，a)
还有一个深度缓冲区(用于每个像素的 Z 坐标)

所以想法是让 webgl 渲染到一些不同的“缓冲区”而不是画布。

有一种特殊类型的缓冲区，称为`framebuffer`,可以作为渲染目标

为了创建一个帧缓冲区，我们需要调用`gl.createFramebuffer`

📄src/minecraft.js

```
 mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint);

+ const framebuffer = gl.createFramebuffer();
+ 
  function render() {
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360); 
```

Framebuffer 本身不是一个存储器，而是一组对“附件”(颜色、深度)的引用

为了渲染颜色，我们需要一个纹理

📄src/minecraft.js

```
 const framebuffer = gl.createFramebuffer();

+ const texture = gl.createTexture();
+ 
+ gl.bindTexture(gl.TEXTURE_2D, texture);
+ gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, canvas.width, canvas.height, 0, gl.RGBA, gl.UNSIGNED_BYTE, null);
+ 
+ gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
+ gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
+ gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
+ 
  function render() {
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360); 
```

现在我们需要绑定一个帧缓冲区并设置一个颜色附件

📄src/minecraft.js

```
 gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
  gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);

+ gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
+ gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, texture, 0);
+ 
  function render() {
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360); 
```

现在我们的画布是白色的。我们打破了什么吗？不–一切都很好，但是我们的场景现在被渲染到一个纹理而不是画布上

现在我们需要从纹理渲染到画布

顶点着色器非常简单，我们只需要渲染一个画布大小的矩形，所以我们可以从 js 传递顶点位置，而无需任何转换

📄src/shaders/filter.v.glsl

```
attribute vec2 position;

void main() {
    gl_Position = vec4(position, 0, 1);
} 
```

片段着色器需要纹理来读取颜色，还需要分辨率来将像素坐标转换为纹理坐标

📄src/shaders/filter.f.glsl

```
precision mediump float;

uniform sampler2D texture;
uniform vec2 resolution;

void main() {
    gl_FragColor = texture2D(texture, gl_FragCoord.xy / resolution);
} 
```

现在我们需要通过一个程序设置例程

📄src/minecraft.js

```
 import { prepare as prepareSkybox, render as renderSkybox } from './skybox';
  import { prepare as prepareTerrain, render as renderTerrain } from './minecraft-terrain';

+ import vShaderSource from './shaders/filter.v.glsl';
+ import fShaderSource from './shaders/filter.f.glsl';
+ import { setupShaderInput, compileShader } from './gl-helpers';
+ import { GLBuffer } from './GLBuffer';
+ import { createRect } from './shape-helpers';
+ 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
  gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, texture, 0);

+ const vShader = gl.createShader(gl.VERTEX_SHADER);
+ const fShader = gl.createShader(gl.FRAGMENT_SHADER);
+ 
+ compileShader(gl, vShader, vShaderSource);
+ compileShader(gl, fShader, fShaderSource);
+ 
+ const program = gl.createProgram();
+ 
+ gl.attachShader(program, vShader);
+ gl.attachShader(program, fShader);
+ 
+ gl.linkProgram(program);
+ gl.useProgram(program);
+ 
+ const vertexPositionBuffer = new GLBuffer(
+     gl,
+     gl.ARRAY_BUFFER,
+     new Float32Array([...createRect(-1, -1, 2, 2)]),
+     gl.STATIC_DRAW
+ );
+ 
+ const indexBuffer = new GLBuffer(gl, gl.ELEMENT_ARRAY_BUFFER, new Uint8Array([0, 1, 2, 1, 2, 3]), gl.STATIC_DRAW);
+ 
+ const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);
+ 
+ vertexPositionBuffer.bind(gl);
+ gl.vertexAttribPointer(programInfo.attributeLocations.position, 2, gl.FLOAT, false, 0, 0);
+ 
+ gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);
+ 
  function render() {
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360); 
```

在每一帧的开始，我们需要绑定一个 framebuffer 来告诉 webgl 渲染一个纹理

📄src/minecraft.js

```
 gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

  function render() {
+     gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
+ 
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, 30]); 
```

在我们将场景渲染到纹理之后，我们需要使用我们的新程序

📄src/minecraft.js

```
 renderSkybox(gl, viewMatrix, projectionMatrix);
      renderTerrain(gl, viewMatrix, projectionMatrix);

+     gl.useProgram(program);
+ 
      requestAnimationFrame(render);
  } 
```

设置计划属性和统一

📄src/minecraft.js

```
 gl.useProgram(program);

+     vertexPositionBuffer.bind(gl);
+     gl.vertexAttribPointer(programInfo.attributeLocations.position, 2, gl.FLOAT, false, 0, 0);
+ 
+     gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);
+ 
      requestAnimationFrame(render);
  } 
```

绑定空帧缓冲区(这将使 webgl 渲染到画布上)

📄src/minecraft.js

```
 gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

+     gl.bindFramebuffer(gl.FRAMEBUFFER, null);
+ 
      requestAnimationFrame(render);
  } 
```

绑定纹理以将其用作颜色数据的来源

📄src/minecraft.js

```
 gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

      gl.bindFramebuffer(gl.FRAMEBUFFER, null);
+     gl.bindTexture(gl.TEXTURE_2D, texture); 
      requestAnimationFrame(render);
  } 
```

并发出平局呼叫

📄src/minecraft.js

```
 gl.bindFramebuffer(gl.FRAMEBUFFER, null);
      gl.bindTexture(gl.TEXTURE_2D, texture);

+     gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0);
+ 
      requestAnimationFrame(render);
  } 
```

因为我们在渲染地形和天空盒后绑定了不同的纹理，所以我们需要在地形和天空盒程序中重新绑定纹理

📄src/minecraft-terrain.js

```
 await loadImage(textureSource).then((image) => {
          const texture = createTexture(gl);
+         State.texture = texture;
+ 
          setImage(gl, texture, image);

          gl.generateMipmap(gl.TEXTURE_2D);

      setupAttributes(gl);

+     gl.bindTexture(gl.TEXTURE_2D, State.texture);
+ 
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix); 
```

📄src/skybox.js

```
 export function render(gl, viewMatrix, projectionMatrix) {
      gl.useProgram(State.program);

+     gl.bindTexture(gl.TEXTURE_CUBE_MAP, State.texture);
+ 
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix); 
```

我们需要创建一个深度缓冲。深度缓冲区是一个渲染缓冲区(包含来自 fragmnt 着色器输出的数据的对象)

📄src/minecraft.js

```
 gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
  gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, texture, 0);

+ const depthBuffer = gl.createRenderbuffer();
+ gl.bindRenderbuffer(gl.RENDERBUFFER, depthBuffer);
+ 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

并设置 renderbuffer 来存储深度信息

📄src/minecraft.js

```
 const depthBuffer = gl.createRenderbuffer();
  gl.bindRenderbuffer(gl.RENDERBUFFER, depthBuffer);

+ gl.renderbufferStorage(gl.RENDERBUFFER, gl.DEPTH_COMPONENT16, canvas.width, canvas.height);
+ gl.framebufferRenderbuffer(gl.FRAMEBUFFER, gl.DEPTH_ATTACHMENT, gl.RENDERBUFFER, depthBuffer);
+ 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

现在场景看起来更好，但只有一个单一的框架，其他人似乎是在以前的基础上绘制的。发生这种情况是因为纹理在下一次绘制调用之前没有被清除

我们需要调用一个`gl.clear`来清除纹理(清除当前绑定的帧缓冲区)。这个方法接受一个位掩码，告诉 webgl 清除哪个缓冲区。我们需要清除颜色和深度缓冲区，所以蒙版是`gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT`

📄src/minecraft.js

```
 function render() {
      gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);

+     gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
+ 
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, 30]); 
```

> 注意:如果`preserveDrawingBuffer`上下文参数设置为 true，这也应该在渲染到画布之前完成

现在，我们可以重复使用之前教程中的滤镜功能，将整个场景变成黑白的

📄src/shaders/filter.f.glsl

```
 uniform sampler2D texture;
  uniform vec2 resolution;

+ vec4 blackAndWhite(vec4 color) {
+     return vec4(vec3(1.0, 1.0, 1.0) * (color.r + color.g + color.b) / 3.0, color.a);
+ }
+ 
  void main() {
-     gl_FragColor = texture2D(texture, gl_FragCoord.xy / resolution); +     gl_FragColor = blackAndWhite(texture2D(texture, gl_FragCoord.xy / resolution));
  } 
```

就是这样！

屏幕外渲染(渲染到纹理)可用于应用不同的“后期”效果，如模糊、相机上的水等。明天我们将学习屏幕外渲染的另一个有用的用例

感谢阅读！👋

* * *

[![GitHub stars](img/7a62e6572a80bbc8913957ac9042103e.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/a66003765b1d742c03ff459eb1614ef8.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)