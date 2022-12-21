# WebGL 月份。第 28 天。点击检测。第二部分

> 原文：<https://dev.to/lesnitsky/webgl-month-day-28-click-detection-part-ii-367e>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/53e332970190dff301660ebbe3179632.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/ce80776d2225c8c24d8d786c967f4a3e.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月

昨天我们已经将 minecraft 地形渲染到一个屏幕外的纹理中，其中每个对象都被编码成一种特定的颜色，并学习了如何将像素颜色从纹理中读取回 JS。现在让我们将这个颜色解码为一个对象索引，并高亮显示选中的立方体

`gl.readPixels`从左下角开始用像素颜色填充`Uint8Array`。我们需要将客户坐标转换为数组中的像素坐标。不要忘记像素比例，因为我们的屏幕外帧缓冲区会考虑它，而事件坐标不会。

📄src/minecraft.js

```
 requestAnimationFrame(render);
  }

- document.body.addEventListener('click', () => {
+ document.body.addEventListener('click', (e) => {
      coloredCubesRenderBuffer.bind(gl);

      renderTerrain(gl, viewMatrix, projectionMatrix, true);

      const pixels = new Uint8Array(canvas.width * canvas.height * 4);
      gl.readPixels(0, 0, canvas.width, canvas.height, gl.RGBA, gl.UNSIGNED_BYTE, pixels);
+ 
+     const x = e.clientX * devicePixelRatio;
+     const y = (canvas.offsetHeight - e.clientY) * devicePixelRatio;
  });

  (async () => { 
```

我们需要跳过`y`行(`y * canvas.width`)乘以 4(每像素 4 个整数)

📄src/minecraft.js

```
 const x = e.clientX * devicePixelRatio;
      const y = (canvas.offsetHeight - e.clientY) * devicePixelRatio;
+ 
+     const rowsToSkip = y * canvas.width * 4;
  });

  (async () => { 
```

横坐标是`x * 4`(坐标乘以每个像素的整数个数)

📄src/minecraft.js

```
 const y = (canvas.offsetHeight - e.clientY) * devicePixelRatio;

      const rowsToSkip = y * canvas.width * 4;
+     const col = x * 4;
  });

  (async () => { 
```

所以 pixel 的最终索引是 rowsToSkip + col

📄src/minecraft.js

```
 const rowsToSkip = y * canvas.width * 4;
      const col = x * 4;
+ 
+     const pixelIndex = rowsToSkip + col;
  });

  (async () => { 
```

现在我们需要读取每个像素的颜色分量

📄src/minecraft.js

```
 const col = x * 4;

      const pixelIndex = rowsToSkip + col;
+ 
+     const r = pixels[pixelIndex];
+     const g = pixels[pixelIndex + 1];
+     const b = pixels[pixelIndex + 2];
+     const a = pixels[pixelIndex + 3];
  });

  (async () => { 
```

现在我们需要从 r g b 转换回整数

📄src/minecraft.js

```
 requestAnimationFrame(render);
  }

+ function rgbToInt(r, g, b) {
+     return b + g * 255 + r * 255 ** 2;
+ }
+ 
  document.body.addEventListener('click', (e) => {
      coloredCubesRenderBuffer.bind(gl); 
```

让我们放弃相机旋转代码，使场景静止

📄src/minecraft.js

```
 function render() {
      offscreenRenderBuffer.clear(gl);

-     mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, -30]);
-     mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
-     mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [0, 0, 30]);
- 
-     mat4.getTranslation(cameraFocusPoint, cameraFocusPointMatrix);
- 
      mat4.lookAt(viewMatrix, cameraPosition, cameraFocusPoint, [0, 1, 0]);

      renderSkybox(gl, viewMatrix, projectionMatrix);
      const g = pixels[pixelIndex + 1];
      const b = pixels[pixelIndex + 2];
      const a = pixels[pixelIndex + 3];
+ 
+     const index = rgbToInt(r, g, b);
+ 
+     console.log(index);
  });

  (async () => { 
```

并且更新初始摄像机位置以更好地看到场景

📄src/minecraft.js

```
 gl.viewport(0, 0, canvas.width, canvas.height);

- const cameraPosition = [0, 5, 0];
- const cameraFocusPoint = vec3.fromValues(0, 0, 30); + const cameraPosition = [0, 10, 0];
+ const cameraFocusPoint = vec3.fromValues(30, 0, 30);
  const cameraFocusPointMatrix = mat4.create();

  mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint); 
```

接下来，让我们将选择的颜色索引作为变化传递到顶点着色器

📄src/shaders/3d-textured . v . glsl

```
 uniform mat4 viewMatrix;
  uniform mat4 projectionMatrix;
+ uniform float selectedObjectIndex; 
  varying vec2 vTexCoord;
  varying vec3 vColor; 
```

如果对象索引与所选对象索引匹配，则乘以对象颜色

📄src/shaders/3d-textured . f . glsl

```
 varying vec3 vColor;

  uniform float renderIndices;
+ varying vec4 vColorMultiplier; 
  void main() {
-     gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)); +     gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)) * vColorMultiplier; 
      if (renderIndices == 1.0) {
          gl_FragColor.rgb = vColor; 
```

📄src/shaders/3d-textured . v . glsl

```
 varying vec2 vTexCoord;
  varying vec3 vColor;
+ varying vec4 vColorMultiplier; 
  vec3 encodeObject(float id) {
      int b = int(mod(id, 255.0));

      vTexCoord = texCoord;
      vColor = encodeObject(index);
+     
+     if (selectedObjectIndex == index) {
+         vColorMultiplier = vec4(1.5, 1.5, 1.5, 1.0);
+     } else {
+         vColorMultiplier = vec4(1.0, 1.0, 1.0, 1.0);
+     }
  } 
```

并反映 js 中的着色器更改

📄src/minecraft-terrain.js

```
 State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.index, 0);
  }

- export function render(gl, viewMatrix, projectionMatrix, renderIndices) {
+ export function render(gl, viewMatrix, projectionMatrix, renderIndices, selectedObjectIndex) {
      gl.useProgram(State.program);

      setupAttributes(gl);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
      gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);

+     gl.uniform1f(State.programInfo.uniformLocations.selectedObjectIndex, selectedObjectIndex);
+ 
      if (renderIndices) {
          gl.uniform1f(State.programInfo.uniformLocations.renderIndices, 1);
      } else { 
```

📄src/minecraft.js

```
 gl.uniform2f(programInfo.uniformLocations.resolution, canvas.width, canvas.height);

+ let selectedObjectIndex = -1;
+ 
  function render() {
      offscreenRenderBuffer.clear(gl);

      mat4.lookAt(viewMatrix, cameraPosition, cameraFocusPoint, [0, 1, 0]);

      renderSkybox(gl, viewMatrix, projectionMatrix);
-     renderTerrain(gl, viewMatrix, projectionMatrix); +     renderTerrain(gl, viewMatrix, projectionMatrix, false, selectedObjectIndex); 
      gl.useProgram(program);

      const index = rgbToInt(r, g, b);

-     console.log(index);
+     selectedObjectIndex = index;
  });

  (async () => { 
```

就是这样！我们现在知道选择的对象索引，这样我们就可以执行 JS 操作以及视觉反馈！

感谢阅读！

* * *

[![GitHub stars](img/53e332970190dff301660ebbe3179632.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/ce80776d2225c8c24d8d786c967f4a3e.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)