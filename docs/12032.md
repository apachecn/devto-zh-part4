# WebGL 月份。第 22 天。将 WebGL 调用次数减少 5000 次

> 原文：<https://dev.to/lesnitsky/webgl-month-day-22-reducing-number-of-webgl-calls-by-5000-times-3a4j>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/166ab629c2d3e15f0c94f06e681dac8a.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/861dc6e39a7b70e1d7d65b6b6c51cb80.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月

[昨天](https://dev.to/lesnitsky/webgl-month-day-21-rendering-a-minecraft-terrain-24b5)我们渲染了 minecraft 的地形，但是效果并不理想。我们必须为每个块发出两个 gl 调用。一个用于更新模型矩阵 uniform，另一个用于发出绘制调用。有一种方法可以通过一次调用来渲染整个场景，这样我们就可以将调用次数减少 5000 倍🤯。

这些技术被称为 WebGL 实例化。我们的立方体共享相同的顶点和纹理坐标数据，唯一的区别是模型矩阵。我们可以定义一个属性，而不是将其作为 uniform 传递

📄src/shaders/3d-textured . v . glsl

```
 attribute vec3 position;
  attribute vec2 texCoord;
+ attribute mat4 modelMatrix; 
- uniform mat4 modelMatrix;
  uniform mat4 viewMatrix;
  uniform mat4 projectionMatrix; 
```

矩阵属性实际上是一些`vec4`属性，所以如果`mat4`属性位置是`0`，我们将有 4 个位置分别为`0`、`1`、`2`、`3`的独立属性。我们的`setupShaderInput`助手不支持这些，所以我们需要手动启用每个属性

📄src/3d-textured.js

```
 const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);

+ for (let i = 0; i < 4; i++) {
+     gl.enableVertexAttribArray(programInfo.attributeLocations.modelMatrix + i);
+ }
+ 
  const cube = new Object3D(cubeObj, [0, 0, 0], [1, 0, 0]);

  const vertexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.vertices, gl.STATIC_DRAW); 
```

现在我们需要为矩阵数据定义一个 float 32 数组。尺寸是`100 * 100`(我们世界的尺寸)`* 4 * 4`(模型矩阵的尺寸)

📄src/3d-textured.js

```
 gl.viewport(0, 0, canvas.width, canvas.height);

- const matrices = [];
+ const matrices = new Float32Array(100 * 100 * 4 * 4);
  const rotationMatrix = mat4.create();

  for (let i = -50; i < 50; i++) { 
```

为了节省资源，我们可以在用数据填充矩阵数组时，对所有立方体使用单一模型矩阵

📄src/3d-textured.js

```
 gl.viewport(0, 0, canvas.width, canvas.height);

  const matrices = new Float32Array(100 * 100 * 4 * 4);
+ const modelMatrix = mat4.create();
  const rotationMatrix = mat4.create();

  for (let i = -50; i < 50; i++) {
      for (let j = -50; j < 50; j++) {
-         const matrix = mat4.create();
- 
          const position = [i * 2, (Math.floor(Math.random() * 2) - 1) * 2, j * 2];
-         mat4.fromTranslation(matrix, position); +         mat4.fromTranslation(modelMatrix, position); 
          mat4.fromRotation(rotationMatrix, Math.PI * Math.round(Math.random() * 4), [0, 1, 0]);
-         mat4.multiply(matrix, matrix, rotationMatrix); +         mat4.multiply(modelMatrix, modelMatrix, rotationMatrix); 
          matrices.push(matrix);
      } 
```

我们还需要一个计数器来知道 matrices Float32Array 的偏移量，以便将数据写入适当的位置

📄src/3d-textured.js

```
 const modelMatrix = mat4.create();
  const rotationMatrix = mat4.create();

+ let cubeIndex = 0;
+ 
  for (let i = -50; i < 50; i++) {
      for (let j = -50; j < 50; j++) {
          const position = [i * 2, (Math.floor(Math.random() * 2) - 1) * 2, j * 2];
          mat4.fromRotation(rotationMatrix, Math.PI * Math.round(Math.random() * 4), [0, 1, 0]);
          mat4.multiply(modelMatrix, modelMatrix, rotationMatrix);

-         matrices.push(matrix);
+         modelMatrix.forEach((value, index) => {
+             matrices[cubeIndex * 4 * 4 + index] = value;
+         });
+ 
+         cubeIndex++;
      }
  } 
```

接下来，我们需要一个矩阵总帐缓冲区

📄src/3d-textured.js

```
 }
  }

+ const matricesBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, matrices, gl.STATIC_DRAW);
+ 
  const cameraPosition = [0, 10, 0];
  const cameraFocusPoint = vec3.fromValues(30, 0, 0);
  const cameraFocusPointMatrix = mat4.create(); 
```

和偏移量设置属性指针，因为我们的缓冲区是交叉存取的。[点击此处了解有关交错缓冲器的更多信息](https://dev.to/lesnitsky/webgl-month-day-5-interleaved-buffers-2k9a)

📄src/3d-textured.js

```
 const matricesBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, matrices, gl.STATIC_DRAW);

+ const offset = 4 * 4; // 4 floats 4 bytes each
+ const stride = offset * 4; // 4 rows of 4 floats
+ 
+ for (let i = 0; i < 4; i++) {
+     gl.vertexAttribPointer(programInfo.attributeLocations.modelMatrix + i, 4, gl.FLOAT, false, stride, i * offset);
+ }
+ 
  const cameraPosition = [0, 10, 0];
  const cameraFocusPoint = vec3.fromValues(30, 0, 0);
  const cameraFocusPointMatrix = mat4.create(); 
```

webgl 1 不支持实例化本身，但是可以通过扩展获得，所以我们需要得到它

📄src/3d-textured.js

```
 const offset = 4 * 4; // 4 floats 4 bytes each
  const stride = offset * 4; // 4 rows of 4 floats

+ const ext = gl.getExtension('ANGLE_instanced_arrays');
+ 
  for (let i = 0; i < 4; i++) {
      gl.vertexAttribPointer(programInfo.attributeLocations.modelMatrix + i, 4, gl.FLOAT, false, stride, i * offset);
  } 
```

基本上，这个扩展的作用是帮助我们避免重复每个立方体的顶点位置和纹理坐标，因为它们是相同的。通过使用实例化，我们告诉 WebGL 渲染对象的 N 个实例，重用每个对象的一些属性数据，并获得其他属性的“唯一”数据。要指定每个对象的哪些属性包含数据，我们需要调用扩展的`vertexAttribDivisorANGLE(location, divisor)`方法。

Divisor 用于确定如何从每个对象的填充有数据的属性中读取数据。

我们的 modelMatrix 属性对每个对象都有一个矩阵，所以 divisor 应该是`1`。
我们可以对对象`0`和`1`使用 modelMarix `A`，对对象`2`和`3`使用`B`——在这种情况下，除数是`2`。

在我们的例子中是`1`。

📄src/3d-textured.js

```
 for (let i = 0; i < 4; i++) {
      gl.vertexAttribPointer(programInfo.attributeLocations.modelMatrix + i, 4, gl.FLOAT, false, stride, i * offset);
+     ext.vertexAttribDivisorANGLE(programInfo.attributeLocations.modelMatrix + i, 1);
  }

  const cameraPosition = [0, 10, 0]; 
```

最后，我们可以读取所有矩阵上的迭代，并使用单个调用。然而，我们应该在扩展实例上调用它，而不是在 gl 本身上。最后一个参数应该是我们想要渲染的实例的数量

📄src/3d-textured.js

```
 mat4.lookAt(viewMatrix, cameraPosition, cameraFocusPoint, [0, 1, 0]);
      gl.uniformMatrix4fv(programInfo.uniformLocations.viewMatrix, false, viewMatrix);

-     matrices.forEach((matrix) => {
-         gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, matrix);
- 
-         gl.drawArrays(gl.TRIANGLES, 0, vertexBuffer.data.length / 3);
-     }); +     ext.drawArraysInstancedANGLE(gl.TRIANGLES, 0, vertexBuffer.data.length / 3, 100 * 100); 
      requestAnimationFrame(frame);
  } 
```

就是这样！我们刚刚将 gl 调用次数减少了 5000 次🎉！

WebGL 实例化扩展得到了广泛支持，所以只要有意义，就不要犹豫使用它。

典型案例——需要渲染许多相同的对象，但具有不同的位置、颜色和其他类型的“属性”

感谢阅读！明天见👋

* * *

[![GitHub stars](img/166ab629c2d3e15f0c94f06e681dac8a.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/861dc6e39a7b70e1d7d65b6b6c51cb80.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)