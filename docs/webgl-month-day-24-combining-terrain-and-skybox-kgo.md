# WebGL 月份。第 24 天。结合地形和天空盒

> 原文：<https://dev.to/lesnitsky/webgl-month-day-24-combining-terrain-and-skybox-kgo>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/2f002bd36aac85f6eb3fad10526da225.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/367043e12af3829681d57af89d077b37.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月

在之前的教程中，我们已经渲染了 minecraft 地形和天空盒，但是是在不同的例子中。我们如何将它们结合起来？WebGL 允许使用多个程序，所以我们可以通过轻微的重构将两个例子结合起来。

让我们创建一个新的入口点文件`minecraft.js`，并假设`skybox.js`和`minecraft-terrain.js`导出`prepare`和`render`函数

```
import { prepare as prepareSkybox, render as renderSkybox } from './skybox';
import { prepare as prepareTerrain, render as renderTerrain } from './minecraft-terrain'; 
```

接下来我们需要设置一个画布

```
const canvas = document.querySelector('canvas');
const gl = canvas.getContext('webgl');

const width = document.body.offsetWidth;
const height = document.body.offsetHeight;

canvas.width = width * devicePixelRatio;
canvas.height = height * devicePixelRatio;

canvas.style.width = `${width}px`;
canvas.style.height = `${height}px`; 
```

设置摄像机矩阵

```
const viewMatrix = mat4.create();
const projectionMatrix = mat4.create();

mat4.lookAt(viewMatrix, [0, 0, 0], [0, 0, -1], [0, 1, 0]);

mat4.perspective(projectionMatrix, (Math.PI / 360) * 90, canvas.width / canvas.height, 0.01, 142);

gl.viewport(0, 0, canvas.width, canvas.height);

const cameraPosition = [0, 5, 0];
const cameraFocusPoint = vec3.fromValues(0, 0, 30);
const cameraFocusPointMatrix = mat4.create();

mat4.fromTranslation(cameraFocusPointMatrix, cameraFocusPoint); 
```

定义一个渲染函数

```
function render() {
    renderSkybox(gl, viewMatrix, projectionMatrix);
    renderTerrain(gl, viewMatrix, projectionMatrix);

    requestAnimationFrame(render);
} 
```

并执行“准备”代码

```
(async () => {
    await prepareSkybox(gl);
    await prepareTerrain(gl);

    render();
})(); 
```

现在我们需要实现天空盒和地形的`prepare`和`render`功能

这两个函数都需要访问共享状态，像 WebGL 程序、属性和缓冲区，所以让我们创建一个对象

```
const State = {};

export async function prepare(gl) {
    // initialization code goes here
} 
```

那么什么是“准备”步骤呢？

这是关于创建程序

```
 export async function prepare(gl) {
+     const vShader = gl.createShader(gl.VERTEX_SHADER);
+     const fShader = gl.createShader(gl.FRAGMENT_SHADER); 
+     compileShader(gl, vShader, vShaderSource);
+     compileShader(gl, fShader, fShaderSource); 
+     const program = gl.createProgram();
+     State.program = program; 
+     gl.attachShader(program, vShader);
+     gl.attachShader(program, fShader); 
+     gl.linkProgram(program);
+     gl.useProgram(program); 
+     State.programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);
  } 
```

缓冲器

```
 gl.useProgram(program);

      State.programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);

+     const cube = new Object3D(cubeObj, [0, 0, 0], [0, 0, 0]);
+     State.vertexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.vertices, gl.STATIC_DRAW);
  } 
```

纹理

```
 const cube = new Object3D(cubeObj, [0, 0, 0], [0, 0, 0]);
      State.vertexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.vertices, gl.STATIC_DRAW);

+     await Promise.all([
+         loadImage(rightTexture),
+         loadImage(leftTexture),
+         loadImage(upTexture),
+         loadImage(downTexture),
+         loadImage(backTexture),
+         loadImage(frontTexture),
+     ]).then((images) => {
+         State.texture = gl.createTexture();
+         gl.bindTexture(gl.TEXTURE_CUBE_MAP, State.texture); 
+         gl.texParameteri(gl.TEXTURE_CUBE_MAP, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
+         gl.texParameteri(gl.TEXTURE_CUBE_MAP, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
+         gl.texParameteri(gl.TEXTURE_CUBE_MAP, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
+         gl.texParameteri(gl.TEXTURE_CUBE_MAP, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE); 
+         images.forEach((image, index) => {
+             gl.texImage2D(gl.TEXTURE_CUBE_MAP_POSITIVE_X + index, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
+         });
+     }); } 
```

并设置属性

```
 gl.texImage2D(gl.TEXTURE_CUBE_MAP_POSITIVE_X   index, 0, gl.RGBA, gl.RGBA, gl.UNSIGNED_BYTE, image);
          });
      });
+     setupAttributes(gl); } 
```

我们需要一个单独的函数来设置属性，因为我们也需要在渲染函数中这样做。属性在不同的程序之间共享状态，所以我们需要在每次使用不同的程序时正确地设置它们

`setupAttributes`长得像这样`skybox`

```
function setupAttributes(gl) {
    State.vertexBuffer.bind(gl);
    gl.vertexAttribPointer(State.programInfo.attributeLocations.position, 3, gl.FLOAT, false, 0, 0);
} 
```

现在我们需要一个渲染函数，它将视图和投影矩阵传递给制服，并发出一个绘制调用

```
export function render(gl, viewMatrix, projectionMatrix) {
    gl.useProgram(State.program);

    gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
    gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);

    setupAttributes(gl);

    gl.drawArrays(gl.TRIANGLES, 0, State.vertexBuffer.data.length / 3);
} 
```

这种重构非常简单，因为它只需要将代码片段移动到必要的函数中，所以这些步骤对于`minecraft-terrain`来说看起来是一样的，只有一个例外

我们使用`ANGLE_instanced_arrays`扩展来渲染地形，它设置了`divisorAngle`。由于属性在程序之间共享状态，我们需要“重置”那些除数角度。

```
function resetDivisorAngles() {
    for (let i = 0; i < 4; i++) {
        State.ext.vertexAttribDivisorANGLE(State.programInfo.attributeLocations.modelMatrix + i, 0);
    }
} 
```

并在一次 draw 调用
后调用该函数

```
export function render(gl, viewMatrix, projectionMatrix) {
    gl.useProgram(State.program);

    setupAttributes(gl);

    gl.uniformMatrix4fv(State.programInfo.uniformLocations.viewMatrix, false, viewMatrix);
    gl.uniformMatrix4fv(State.programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);

    State.ext.drawArraysInstancedANGLE(gl.TRIANGLES, 0, State.vertexBuffer.data.length / 3, 100 * 100);

    resetDivisorAngles();
} 
```

生成的代码真的能工作吗？

不幸的是没有😢
问题是我们在比我们的地形小的立方体中渲染天空盒，但是我们可以通过改变天空盒顶点着色器来修复它

```
 attribute vec3 position;
  varying vec3 vTexCoord;

  uniform mat4 projectionMatrix;
  uniform mat4 viewMatrix;

  void main() {
      vTexCoord = position;
-     gl_Position = projectionMatrix * viewMatrix * vec4(position, 1); +     gl_Position = projectionMatrix * viewMatrix * vec4(position, 0.01);
  } 
```

通过改变第四个参数，我们将把天空盒缩放 100 倍(齐次坐标的魔力)。

在这个改变之后，世界看起来还可以，直到我们试着看我们世界立方体的最远的“边缘”。天空盒不会呈现在那里😢

这是因为传递给投影矩阵
的`zFar`参数

```
 const projectionMatrix = mat4.create();

  mat4.lookAt(viewMatrix, [0, 0, 0], [0, 0, -1], [0, 1, 0]);

- mat4.perspective(projectionMatrix, (Math.PI / 360) * 90, canvas.width / canvas.height, 0.01, 100);
+ mat4.perspective(projectionMatrix, (Math.PI / 360) * 90, canvas.width / canvas.height, 0.01, 142); 
  gl.viewport(0, 0, canvas.width, canvas.height); 
```

到最远边缘的距离是`Math.sqrt(size ** 2 + size ** 2)`，也就是`141.4213562373095`，我们正好可以通过`142`

就是这样！

感谢阅读，明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/2f002bd36aac85f6eb3fad10526da225.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/367043e12af3829681d57af89d077b37.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)