# WebGL 月份。第 21 天。渲染 minecraft 地形

> 原文：<https://dev.to/lesnitsky/webgl-month-day-21-rendering-a-minecraft-terrain-24b5>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/980dd16c836e2ac32fcd1bd1d14cb6d6.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/c49d0e722a1b89fc3e3188b60a1f86c9.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到 WebGL 月。

昨天我们渲染了一个单独的 minecraft 泥土立方体，今天让我们来渲染一个地形吧！

我们需要将每个块的位置存储在单独的变换矩阵中

📄src/3d-textured.js

```
 gl.viewport(0, 0, canvas.width, canvas.height);

+ const matrices = [];
+ 
  function frame() {
      mat4.rotateY(cube.modelMatrix, cube.modelMatrix, Math.PI / 180); 
```

现在让我们在 x 和 z 轴上从-50 到 50 创建 10k 块迭代

📄src/3d-textured.js

```
 const matrices = [];

+ for (let i = -50; i < 50; i++) {
+     for (let j = -50; j < 50; j++) {
+         const matrix = mat4.create();
+     }
+ }
+ 
  function frame() {
      mat4.rotateY(cube.modelMatrix, cube.modelMatrix, Math.PI / 180); 
```

每个块的大小为 2(顶点坐标为[-1..1] range)所以位置应该能被 2 整除

📄src/3d-textured.js

```
 for (let i = -50; i < 50; i++) {
      for (let j = -50; j < 50; j++) {
          const matrix = mat4.create();
+ 
+         const position = [i * 2, (Math.floor(Math.random() * 2) - 1) * 2, j * 2];
      }
  } 
```

现在我们需要创建一个变换矩阵。让我们用`ma4.fromTranslation`

📄src/3d-textured.js

```
 const matrix = mat4.create();

          const position = [i * 2, (Math.floor(Math.random() * 2) - 1) * 2, j * 2];
+         mat4.fromTranslation(matrix, position);
      }
  } 
```

让我们也围绕 Y 轴旋转每个块，使地形看起来更随机

📄src/3d-textured.js

```
 gl.viewport(0, 0, canvas.width, canvas.height);

  const matrices = [];
+ const rotationMatrix = mat4.create(); 
  for (let i = -50; i < 50; i++) {
      for (let j = -50; j < 50; j++) {

          const position = [i * 2, (Math.floor(Math.random() * 2) - 1) * 2, j * 2];
          mat4.fromTranslation(matrix, position);
+ 
+         mat4.fromRotation(rotationMatrix, Math.PI * Math.round(Math.random() * 4), [0, 1, 0]);
+         mat4.multiply(matrix, matrix, rotationMatrix);
      }
  } 
```

最后将每个块矩阵推送到矩阵集合中

📄src/3d-textured.js

```
 mat4.fromRotation(rotationMatrix, Math.PI * Math.round(Math.random() * 4), [0, 1, 0]);
          mat4.multiply(matrix, matrix, rotationMatrix);
+ 
+         matrices.push(matrix);
      }
  } 
```

由于我们的块是静态的，我们不需要在每一帧旋转变换

📄src/3d-textured.js

```
 }

  function frame() {
-     mat4.rotateY(cube.modelMatrix, cube.modelMatrix, Math.PI / 180);
- 
      gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, cube.modelMatrix);
      gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix); 
```

现在，我们需要迭代矩阵集合，并为每个立方体发出一个 draw 调用，将其转换矩阵传递给 uniform

📄src/3d-textured.js

```
 }

  function frame() {
-     gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, cube.modelMatrix);
-     gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix); +     matrices.forEach((matrix) => {
+         gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, matrix);
+         gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix); 
-     gl.drawArrays(gl.TRIANGLES, 0, vertexBuffer.data.length / 3);
+         gl.drawArrays(gl.TRIANGLES, 0, vertexBuffer.data.length / 3);
+     }); 
      requestAnimationFrame(frame);
  } 
```

现在让我们创建一个旋转摄像机的动画。摄像机有一个位置和一个指向的点。因此，要实现这一点，我们需要围绕相机的位置旋转焦点。让我们首先摆脱静态视图矩阵

📄src/3d-textured.js

```
 const viewMatrix = mat4.create();
  const projectionMatrix = mat4.create();

- mat4.lookAt(viewMatrix, [0, 4, -7], [0, 0, 0], [0, 1, 0]);
- 
  mat4.perspective(projectionMatrix, (Math.PI / 360) * 90, canvas.width / canvas.height, 0.01, 100);

  gl.uniformMatrix4fv(programInfo.uniformLocations.viewMatrix, false, viewMatrix); 
```

定义相机位置、相机焦点向量和焦点变换矩阵

📄src/3d-textured.js

```
- import { mat4 } from 'gl-matrix'; + import { mat4, vec3 } from 'gl-matrix'; 
  import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
      }
  }

+ const cameraPosition = [0, 10, 0];
+ const cameraFocusPoint = vec3.fromValues(30, 0, 0);
+ const cameraFocusPointMatrix = mat4.create();
+ 
+ mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint);
+ 
  function frame() {
      matrices.forEach((matrix) => {
          gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, matrix); 
```

我们的相机位于 0.0.0，所以我们需要将相机焦点平移到 0.0.0，旋转它，然后平移回原始位置

📄src/3d-textured.js

```
 mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint);

  function frame() {
+     mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [-30, 0, 0]);
+     mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
+     mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [30, 0, 0]);
+ 
      matrices.forEach((matrix) => {
          gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, matrix);
          gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix); 
```

最后一步–统一更新视图矩阵

📄src/3d-textured.js

```
 mat4.rotateY(cameraFocusPointMatrix, cameraFocusPointMatrix, Math.PI / 360);
      mat4.translate(cameraFocusPointMatrix, cameraFocusPointMatrix, [30, 0, 0]);

+     mat4.getTranslation(cameraFocusPoint, cameraFocusPointMatrix);
+ 
+     mat4.lookAt(viewMatrix, cameraPosition, cameraFocusPoint, [0, 1, 0]);
+     gl.uniformMatrix4fv(programInfo.uniformLocations.viewMatrix, false, viewMatrix);
+ 
      matrices.forEach((matrix) => {
          gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, matrix);
-         gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix); 
          gl.drawArrays(gl.TRIANGLES, 0, vertexBuffer.data.length / 3);
      }); 
```

就是这样！

不过这种方法的性能不是很好，因为我们为每个对象发出 2 个 gl 调用，所以每帧有 20k 个 gl 调用。GL 电话很贵，所以我们需要减少这个数字。明天我们将学习一项伟大的技术！

* * *

[![GitHub stars](img/980dd16c836e2ac32fcd1bd1d14cb6d6.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/c49d0e722a1b89fc3e3188b60a1f86c9.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)