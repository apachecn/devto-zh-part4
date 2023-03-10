# WebGL 月份。第 27 天。点击检测。第一部分

> 原文：<https://dev.to/lesnitsky/webgl-month-day-27-click-detection-part-i-5920>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/3ff55948c21c6468c418e8ea7b5806ff.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/048d0e5c2caabc2543f4d2b7477db9df.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

昨天我们已经学习了如何渲染纹理。这是一个很好的能力，可以在场景完全渲染后产生一些很好的效果，但是我们可以利用屏幕外渲染来做其他事情。

交互式 3D 中一个重要的事情是点击检测。虽然它可能是用 javascript 完成的，但它涉及一些复杂的数学。相反，我们可以:

*   为每个对象指定唯一的纯色
*   将场景渲染到纹理
*   读取光标下的像素颜色
*   将颜色与对象匹配

因为我们需要另一个帧缓冲区，所以让我们创建一个助手类

src/彩现缓冲区. js

```
export class RenderBuffer {
    constructor(gl) {
        this.framebuffer = gl.createFramebuffer();
        this.texture = gl.createTexture();
    }
} 
```

设置帧缓冲区和颜色纹理

src/彩现缓冲区. js

```
 constructor(gl) {
          this.framebuffer = gl.createFramebuffer();
          this.texture = gl.createTexture();
+ 
+         gl.bindTexture(gl.TEXTURE_2D, this.texture);
+         gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, gl.canvas.width, gl.canvas.height, 0, gl.RGBA, gl.UNSIGNED_BYTE, null);
+ 
+         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
+         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
+         gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
+ 
+         gl.bindFramebuffer(gl.FRAMEBUFFER, this.framebuffer);
+         gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, this.texture, 0);
      }
  } 
```

设置深度缓冲器

src/彩现缓冲区. js

```
 gl.bindFramebuffer(gl.FRAMEBUFFER, this.framebuffer);
          gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, this.texture, 0);
+ 
+         this.depthBuffer = gl.createRenderbuffer();
+         gl.bindRenderbuffer(gl.RENDERBUFFER, this.depthBuffer);
+ 
+         gl.renderbufferStorage(gl.RENDERBUFFER, gl.DEPTH_COMPONENT16, gl.canvas.width, gl.canvas.height);
+         gl.framebufferRenderbuffer(gl.FRAMEBUFFER, gl.DEPTH_ATTACHMENT, gl.RENDERBUFFER, this.depthBuffer);
      }
  } 
```

实现绑定方法

src/彩现缓冲区. js

```
 gl.renderbufferStorage(gl.RENDERBUFFER, gl.DEPTH_COMPONENT16, gl.canvas.width, gl.canvas.height);
          gl.framebufferRenderbuffer(gl.FRAMEBUFFER, gl.DEPTH_ATTACHMENT, gl.RENDERBUFFER, this.depthBuffer);
      }
+ 
+     bind(gl) {
+         gl.bindFramebuffer(gl.FRAMEBUFFER, this.framebuffer);
+     }
  } 
```

清晰

src/彩现缓冲区. js

```
 bind(gl) {
          gl.bindFramebuffer(gl.FRAMEBUFFER, this.framebuffer);
      }
+ 
+     clear(gl) {
+         this.bind(gl);
+         gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
+     }
  } 
```

使用新的助手类

📄src/minecraft.js

```
 import { setupShaderInput, compileShader } from './gl-helpers';
  import { GLBuffer } from './GLBuffer';
  import { createRect } from './shape-helpers';
+ import { RenderBuffer } from './RenderBuffer'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint);

- const framebuffer = gl.createFramebuffer();
- 
- const texture = gl.createTexture();
- 
- gl.bindTexture(gl.TEXTURE_2D, texture);
- gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, canvas.width, canvas.height, 0, gl.RGBA, gl.UNSIGNED_BYTE, null);
- 
- gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
- gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
- gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
- 
- gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
- gl.framebufferTexture2D(gl.FRAMEBUFFER, gl.COLOR_ATTACHMENT0, gl.TEXTURE_2D, texture, 0);
- 
- const depthBuffer = gl.createRenderbuffer();
- gl.bindRenderbuffer(gl.RENDERBUFFER, depthBuffer);
- 
- gl.renderbufferStorage(gl.RENDERBUFFER, gl.DEPTH_COMPONENT16, canvas.width, canvas.height);
- gl.framebufferRenderbuffer(gl.FRAMEBUFFER, gl.DEPTH_ATTACHMENT, gl.RENDERBUFFER, depthBuffer); + const offscreenRenderBuffer = new RenderBuffer(gl); 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER);
  gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

  function render() {
-     gl.bindFramebuffer(gl.FRAMEBUFFER, framebuffer);
- 
-     gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT); +     offscreenRenderBuffer.clear(gl); 
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
      mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
      gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

      gl.bindFramebuffer(gl.FRAMEBUFFER, null);
-     gl.bindTexture(gl.TEXTURE_2D, texture); +     gl.bindTexture(gl.TEXTURE_2D, offscreenRenderBuffer.texture); 
      gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0); 
```

我们可以只传递对象索引，而不是传递对象的整个唯一颜色，这是一个 vec3

📄src/shaders/3d-textured . v . glsl

```
 attribute vec3 position;
  attribute vec2 texCoord;
  attribute mat4 modelMatrix;
+ attribute float index; 
  uniform mat4 viewMatrix;
  uniform mat4 projectionMatrix; 
```

并将该浮点转换为着色器中的颜色

📄src/shaders/3d-textured . v . glsl

```
 varying vec2 vTexCoord;

+ vec3 encodeObject(float id) {
+     int b = int(mod(id, 255.0));
+     int r = int(id) / 255 / 255;
+     int g = (int(id) - b - r * 255 * 255) / 255;
+     return vec3(r, g, b) / 255.0;
+ }
+ 
  void main() {
      gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0); 
```

现在我们需要将颜色通过变量传递给片段着色器

📄src/shaders/3d-textured . f . glsl

```
 uniform sampler2D texture;

  varying vec2 vTexCoord;
+ varying vec3 vColor; 
  void main() {
      gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)); 
```

📄src/shaders/3d-textured . v . glsl

```
 uniform mat4 projectionMatrix;

  varying vec2 vTexCoord;
+ varying vec3 vColor; 
  vec3 encodeObject(float id) {
      int b = int(mod(id, 255.0));
      gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);

      vTexCoord = texCoord;
+     vColor = encodeObject(index);
  } 
```

我们还需要指定我们想要渲染什么:有纹理的物体还是有颜色的，所以让我们为它使用统一的颜色

📄src/shaders/3d-textured . f . glsl

```
 varying vec2 vTexCoord;
  varying vec3 vColor;

+ uniform float renderIndices;
+ 
  void main() {
      gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1));
+ 
+     if (renderIndices == 1.0) {
+         gl_FragColor.rgb = vColor;
+     }
  } 
```

现在让我们创建索引数组

📄src/minecraft-terrain.js

```
 State.modelMatrix = mat4.create();
      State.rotationMatrix = mat4.create();

+     const indices = new Float32Array(100 * 100);
+ 
      let cubeIndex = 0;

      for (let i = -50; i < 50; i++) { 
```

用数据填充它并设置一个 GLBuffer

📄src/minecraft-terrain.js

```
 matrices[cubeIndex * 4 * 4 + index] = value;
              });

+             indices[cubeIndex] = cubeIndex;
+ 
              cubeIndex++;
          }
      }

      State.matricesBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, matrices, gl.STATIC_DRAW);
+     State.indexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, indices, gl.STATIC_DRAW); 
      State.offset = 4 * 4; // 4 floats 4 bytes each
      State.stride = State.offset * 4; // 4 rows of 4 floats 
```

由于我们有了一个新属性，我们需要更新 setupAttribute 和 resetDivisorAngles 函数

📄src/minecraft-terrain.js

```
 State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.modelMatrix + i, 1);
      }
+ 
+     State.indexBuffer.bind(gl);
+     gl.vertexAttribPointer(State.programInfo.attributeLocations.index, 1, gl.FLOAT, false, 0, 0);
+     State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.index, 1);
  }

  function resetDivisorAngles() {
      for (let i = 0; i < 4; i++) {
          State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.modelMatrix + i, 0);
      }
+ 
+     State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.index, 0);
  }

  export function render(gl, viewMatrix, projectionMatrix) { 
```

最后，我们需要渲染函数的另一个参数来区分“渲染模式”(纹理立方体或彩色)

📄src/minecraft-terrain.js

```
 State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.index, 0);
  }

- export function render(gl, viewMatrix, projectionMatrix) {
+ export function render(gl, viewMatrix, projectionMatrix, renderIndices) {
      gl.useProgram(State.program);

      setupAttributes(gl);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);

+     if (renderIndices) {
+         gl.uniform1f(State.programInfo.uniformLocations.renderIndices, 1);
+     } else {
+         gl.uniform1f(State.programInfo.uniformLocations.renderIndices, 0);
+     }
+ 
      State.ext.drawArraysInstancedANGLE(gl.TRIANGLES, 0, State.vertexBuffer.data.length / 3, 100 * 100);

      resetDivisorAngles(); 
```

现在我们需要另一个渲染缓冲区来渲染彩色立方体

📄src/minecraft.js

```
 mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint);

  const offscreenRenderBuffer = new RenderBuffer(gl);
+ const coloredCubesRenderBuffer = new RenderBuffer(gl); 
  const vShader = gl.createShader(gl.VERTEX_SHADER);
  const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
```

现在让我们添加一个点击监听器

📄src/minecraft.js

```
 requestAnimationFrame(render);
  }

+ document.body.addEventListener('click', () => {
+     coloredCubesRenderBuffer.bind(gl);
+ });
+ 
  (async () => {
      await prepareSkybox(gl);
      await prepareTerrain(gl); 
```

并在每次用户点击画布时将彩色立方体渲染到纹理中

📄src/minecraft.js

```
 document.body.addEventListener('click', () => {
      coloredCubesRenderBuffer.bind(gl);
+ 
+     renderTerrain(gl, viewMatrix, projectionMatrix, true);
  });

  (async () => { 
```

现在我们需要一个存储器来读取像素颜色

📄src/minecraft.js

```
 coloredCubesRenderBuffer.bind(gl);

      renderTerrain(gl, viewMatrix, projectionMatrix, true);
+ 
+     const pixels = new Uint8Array(canvas.width * canvas.height * 4);
  });

  (async () => { 
```

并实际读取像素颜色

📄src/minecraft.js

```
 renderTerrain(gl, viewMatrix, projectionMatrix, true);

      const pixels = new Uint8Array(canvas.width * canvas.height * 4);
+     gl.readPixels(0, 0, canvas.width, canvas.height, gl.RGBA, gl.UNSIGNED_BYTE, pixels);
  });

  (async () => { 
```

就这样，我们现在将整个场景渲染到一个屏幕外的纹理中，每个物体都有一个独特的颜色。我们明天将继续点击检测

感谢阅读！👋

* * *

[![GitHub stars](img/3ff55948c21c6468c418e8ea7b5806ff.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/048d0e5c2caabc2543f4d2b7477db9df.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)