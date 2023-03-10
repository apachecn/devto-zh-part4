# WebGL 月份。第二十天。渲染 minecraft 污垢立方体

> 原文：<https://dev.to/lesnitsky/webgl-month-day-20-rendering-a-minecraft-dirt-cube-5ag3>

这是一系列与 WebGL 相关的博客文章。每天都会有新的帖子

[![GitHub stars](img/e42aebac54876f9437aa37d34efaa48e.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/ffb81b9356c02c1da0fb66c696f07f02.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎来到[网络 GL 月](https://github.com/lesnitsky/webgl-month)

今天我们将探索如何给 3d 物体添加纹理。

首先我们需要一个新的切入点

📄index.html

```
 </head>
      <body>
          <canvas></canvas>
-         <script src="./dist/3d.js"></script> +         <script src="./dist/3d-textured.js"></script>
      </body>
  </html> 
```

📄src/3d-textured.js

```
console.log('Hello textures'); 
```

📄web pack . config . js

```
 texture: './src/texture.js',
          'rotating-square': './src/rotating-square.js',
          '3d': './src/3d.js',
+         '3d-textured': './src/3d-textured.js',
      },

      output: { 
```

现在让我们创建简单的着色器，用纯色渲染 3d 对象。[在本教程中了解更多信息](https://dev.to/lesnitsky/webgl-month-day-15-rendering-a-3d-cube-190f)

📄src/shaders/3d-textured . f . glsl

```
precision mediump float;

void main() {
    gl_FragColor = vec4(1, 0, 0, 1);
} 
```

📄src/shaders/3d-textured . v . glsl

```
attribute vec3 position;

uniform mat4 modelMatrix;
uniform mat4 viewMatrix;
uniform mat4 projectionMatrix;

void main() {
    gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);
} 
```

我们需要一个画布，webgl 上下文，并使画布全屏

📄src/3d-textured.js

```
- console.log('Hello textures'); + const canvas = document.querySelector('canvas');
+ const gl = canvas.getContext('webgl');
+ 
+ const width = document.body.offsetWidth;
+ const height = document.body.offsetHeight;
+ 
+ canvas.width = width * devicePixelRatio;
+ canvas.height = height * devicePixelRatio;
+ 
+ canvas.style.width = `${width}px`;
+ canvas.style.height = `${height}px`; 
```

创建和编译着色器。[在此了解更多信息](https://dev.to/lesnitsky/shaders-and-points-3h2c)

📄src/3d-textured.js

```
+ import vShaderSource from './shaders/3d-textured.v.glsl';
+ import fShaderSource from './shaders/3d-textured.f.glsl';
+ import { compileShader } from './gl-helpers';
+ 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  canvas.style.width = `${width}px`;
  canvas.style.height = `${height}px`;
+ 
+ const vShader = gl.createShader(gl.VERTEX_SHADER);
+ const fShader = gl.createShader(gl.FRAGMENT_SHADER);
+ 
+ compileShader(gl, vShader, vShaderSource);
+ compileShader(gl, fShader, fShaderSource); 
```

创建、链接和使用 webgl 程序

📄src/3d-textured.js

```
 compileShader(gl, vShader, vShaderSource);
  compileShader(gl, fShader, fShaderSource);
+ 
+ const program = gl.createProgram();
+ 
+ gl.attachShader(program, vShader);
+ gl.attachShader(program, fShader);
+ 
+ gl.linkProgram(program);
+ gl.useProgram(program); 
```

启用深度测试，因为我们正在渲染 3d。[在此了解更多信息](https://dev.to/lesnitsky/webgl-month-day-16-colorizing-cube-depth-buffer-and-array-uniforms-4nhc)

📄src/3d-textured.js

```
 gl.linkProgram(program);
  gl.useProgram(program);
+ 
+ gl.enable(gl.DEPTH_TEST); 
```

设置着色器输入。[在此了解更多信息](https://dev.to/lesnitsky/webgl-month-day-11-3plb)

📄src/3d-textured.js

```
 import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
- import { compileShader } from './gl-helpers'; + import { compileShader, setupShaderInput } from './gl-helpers'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  gl.useProgram(program);

  gl.enable(gl.DEPTH_TEST);
+ 
+ const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource); 
```

现在让我们去 [Blender](https://www.blender.org/) 创建一个立方体，但是确保选中“生成 uv ”,这样 Blender 可以将立方体顶点映射到一个普通的图像。

[![Blender 1](img/58d600678a101f9f5b78db5d123ca75e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vo-SHIqO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-1.png)

接下来打开“UV 编辑”视图

[![Blender 2](img/66db905f9ef9103423c4707c0c6ada85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1f3Bt-r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-2.png)

进入编辑模式

[![Blender 3](img/c611834ddac2526bdd251e2b89d1b0f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9nRX3w2x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-3.png)

展开的立方体看起来已经很好了，所以我们可以导出 UV 布局

[![Blender 4](img/123205b6618885d5e05669686d51bcf0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BwB5fV-f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-4.png)

现在，如果我们在某个编辑器中打开导出的图像，我们会看到类似这样的内容

[![Photoshop 1](img/509514983ddab0e46ed308366973859e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sqX-KQap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/photoshop-1.png)

酷，现在我们可以用一些内容填充我们的纹理了

让我们渲染一个 minecraft 土块

[![Photoshop 2](img/9dbdaa677b32c9605e9e0aa9bf0815ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LmKPFRJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/photoshop-2.png)

接下来我们需要从 blender 中导出我们的对象，但是不要忘记先对它进行三角剖分

[![Blender 5](img/2f012a726d2856be62a485f023a7913b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fDcDuoen--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-5.png)

最后导出我们的对象

[![Blender 6](img/67f9e10f7910d0a9e12c035692aae754.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MRd4Pixj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/blender-step-6.png)

现在让我们导入我们的立方体并创建一个对象。[在这里了解这个助手类](https://dev.to/lesnitsky/webgl-month-day-19-rendering-multiple-objects-45m7)

📄src/3d-textured.js

```
 import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
  import { compileShader, setupShaderInput } from './gl-helpers';
+ import cubeObj from '../assets/objects/textured-cube.obj';
+ import { Object3D } from './Object3D'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  gl.enable(gl.DEPTH_TEST);

  const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);
+ 
+ const cube = new Object3D(cubeObj, [0, 0, 0], [1, 0, 0]); 
```

如果我们查看对象源，我们会看到类似下面的行

```
vt 0.625000 0.000000
vt 0.375000 0.250000
vt 0.375000 0.000000
vt 0.625000 0.250000
vt 0.375000 0.500000 
```

这些是纹理坐标，由第二个“属性”
中的面引用

```
f 2/1/1 3/2/1 1/3/1

# vertexIndex / textureCoordinateIndex / normalIndex 
```

所以我们需要更新我们的解析器来支持纹理坐标

📄src/gl-helpers.js

```
 export function parseObj(objSource) {
      const _vertices = [];
      const _normals = [];
+     const _texCoords = [];
+ 
      const vertexIndices = [];
      const normalIndices = [];
+     const texCoordIndices = []; 
      objSource.split('\n').forEach(line => {
          if (line.startsWith('v ')) {
              _normals.push(parseVec(line, 'vn '));
          }

+         if (line.startsWith('vt ')) {
+             _texCoords.push(parseVec(line, 'vt '));
+         }
+ 
          if (line.startsWith('f ')) {
              const parsedFace = parseFace(line);

              vertexIndices.push(...parsedFace.map(face => face[0] - 1));
+             texCoordIndices.push(...parsedFace.map(face => face[1] - 1));
              normalIndices.push(...parsedFace.map(face => face[2] - 1));
          }
      });

      const vertices = [];
      const normals = [];
+     const texCoords = []; 
      for (let i = 0; i < vertexIndices.length; i++) {
          const vertexIndex = vertexIndices[i];
          const normalIndex = normalIndices[i];
+         const texCoordIndex = texCoordIndices[i]; 
          const vertex = _vertices[vertexIndex];
          const normal = _normals[normalIndex];
+         const texCoord = _texCoords[texCoordIndex]; 
          vertices.push(...vertex);
          normals.push(...normal);
+ 
+         if (texCoord) {
+             texCoords.push(...texCoord);
+         }
      }

      return { 
          vertices: new Float32Array(vertices), 
-         normals: new Float32Array(normals), +         normals: new Float32Array(normals),
+         texCoords: new Float32Array(texCoords), 
      };
  } 
```

并将该属性添加到 Object3D

📄src/Object3D.js

```
 export class Object3D {
      constructor(source, position, color) {
-         const { vertices, normals } = parseObj(source); +         const { vertices, normals, texCoords } = parseObj(source); 
          this.vertices = vertices;
          this.normals = normals;
          this.position = position;
+         this.texCoords = texCoords; 
          this.modelMatrix = mat4.create();
          mat4.fromTranslation(this.modelMatrix, position); 
```

现在我们需要定义总账缓冲区。[在这里了解更多关于这个助手类的信息](https://dev.to/lesnitsky/webgl-month-day-11-3plb)

📄src/3d-textured.js

```
 import { compileShader, setupShaderInput } from './gl-helpers';
  import cubeObj from '../assets/objects/textured-cube.obj';
  import { Object3D } from './Object3D';
+ import { GLBuffer } from './GLBuffer'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);

  const cube = new Object3D(cubeObj, [0, 0, 0], [1, 0, 0]);
+ 
+ const vertexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.vertices, gl.STATIC_DRAW);
+ const texCoordsBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.texCoords, gl.STATIC_DRAW); 
```

我们还需要定义一个属性来将纹理坐标传递给顶点着色器

📄src/shaders/3d-textured . v . glsl

```
 attribute vec3 position;
+ attribute vec2 texCoord; 
  uniform mat4 modelMatrix;
  uniform mat4 viewMatrix; 
```

并且变化以将纹理坐标传递给片段着色器。[在此了解更多信息](https://dev.to/lesnitsky/shader-varyings-2p0f)

📄src/shaders/3d-textured . f . glsl

```
 precision mediump float;

+ varying vec2 vTexCoord;
+ 
  void main() {
      gl_FragColor = vec4(1, 0, 0, 1);
  } 
```

📄src/shaders/3d-textured . v . glsl

```
 uniform mat4 viewMatrix;
  uniform mat4 projectionMatrix;

+ varying vec2 vTexCoord;
+ 
  void main() {
      gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0);
+ 
+     vTexCoord = texCoord;
  } 
```

让我们设置属性

📄src/3d-textured.js

```
 const vertexBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.vertices, gl.STATIC_DRAW);
  const texCoordsBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, cube.texCoords, gl.STATIC_DRAW);
+ 
+ vertexBuffer.bind(gl);
+ gl.vertexAttribPointer(programInfo.attributeLocations.position, 3, gl.FLOAT, false, 0, 0);
+ 
+ texCoordsBuffer.bind(gl);
+ gl.vertexAttribPointer(programInfo.attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0); 
```

创建和设置视图和投影矩阵。[在此了解更多信息](https://dev.to/lesnitsky/webgl-month-day-15-rendering-a-3d-cube-190f)

📄src/3d-textured.js

```
+ import { mat4 } from 'gl-matrix';
+ 
  import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
  import { compileShader, setupShaderInput } from './gl-helpers';

  texCoordsBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0);
+ 
+ const viewMatrix = mat4.create();
+ const projectionMatrix = mat4.create();
+ 
+ mat4.lookAt(
+     viewMatrix,
+     [0, 0, -7],
+     [0, 0, 0],
+     [0, 1, 0],
+ );
+ 
+ mat4.perspective(
+     projectionMatrix,
+     Math.PI / 360 * 90,
+     canvas.width / canvas.height,
+     0.01,
+     100,
+ ); 
```

通过制服将视图和投影矩阵传递给着色器

📄src/3d-textured.js

```
 0.01,
      100,
  );
+ 
+ gl.uniformMatrix4fv(programInfo.uniformLocations.viewMatrix, false, viewMatrix);
+ gl.uniformMatrix4fv(programInfo.uniformLocations.projectionMatrix, false, projectionMatrix); 
```

Setup viewport

📄src/3d-textured.js

```
 gl.uniformMatrix4fv(programInfo.uniformLocations.viewMatrix, false, viewMatrix);
  gl.uniformMatrix4fv(programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);
+ 
+ gl.viewport(0, 0, canvas.width, canvas.height); 
```

最后渲染我们的立方体

📄src/3d-textured.js

```
 gl.uniformMatrix4fv(programInfo.uniformLocations.projectionMatrix, false, projectionMatrix);

  gl.viewport(0, 0, canvas.width, canvas.height);
+ 
+ function frame() {
+     mat4.rotateY(cube.modelMatrix, cube.modelMatrix, Math.PI / 180);
+ 
+     gl.uniformMatrix4fv(programInfo.uniformLocations.modelMatrix, false, cube.modelMatrix);
+     gl.uniformMatrix4fv(programInfo.uniformLocations.normalMatrix, false, cube.normalMatrix);
+ 
+     gl.drawArrays(gl.TRIANGLES, 0, vertexBuffer.data.length / 3);
+ 
+     requestAnimationFrame(frame);
+ }
+ 
+ frame(); 
```

但是在渲染立方体之前，我们需要加载我们的纹理图像。[点击此处了解更多关于 loadImage helper 的信息](https://dev.to/lesnitsky/webgl-month-day-8-textures-1mk8)

📄src/3d-textured.js

```
 import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
- import { compileShader, setupShaderInput } from './gl-helpers'; + import { compileShader, setupShaderInput, loadImage } from './gl-helpers';
  import cubeObj from '../assets/objects/textured-cube.obj';
  import { Object3D } from './Object3D';
  import { GLBuffer } from './GLBuffer';
+ import textureSource from '../asseimg/cube-texture.png'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
      requestAnimationFrame(frame);
  }

- frame();
+ loadImage(textureSource).then((image) => {
+     frame();
+ }); 
```

📄web pack . config . js

```
 },

              {
-                 test: /\.jpg$/, +                 test: /\.(jpg|png)$/,
                  use: 'url-loader',
              },
          ], 
```

并创建 webgl 纹理。[在此了解更多信息](https://dev.to/lesnitsky/webgl-month-day-10-multiple-textures-gf3)

📄src/3d-textured.js

```
 import vShaderSource from './shaders/3d-textured.v.glsl';
  import fShaderSource from './shaders/3d-textured.f.glsl';
- import { compileShader, setupShaderInput, loadImage } from './gl-helpers'; + import { compileShader, setupShaderInput, loadImage, createTexture, setImage } from './gl-helpers';
  import cubeObj from '../assets/objects/textured-cube.obj';
  import { Object3D } from './Object3D';
  import { GLBuffer } from './GLBuffer';
  }

  loadImage(textureSource).then((image) => {
+     const texture = createTexture(gl);
+     setImage(gl, texture, image);
+ 
      frame();
  }); 
```

并从纹理中读取碎片颜色

📄src/shaders/3d-textured . f . glsl

```
 precision mediump float;
+ uniform sampler2D texture; 
  varying vec2 vTexCoord;

  void main() {
-     gl_FragColor = vec4(1, 0, 0, 1); +     gl_FragColor = texture2D(texture, vTexCoord);
  } 
```

让我们把相机移到顶部一点，看看“草”的一面

📄src/3d-textured.js

```
 mat4.lookAt(
      viewMatrix,
-     [0, 0, -7], +     [0, 4, -7],
      [0, 0, 0],
      [0, 1, 0],
  ); 
```

有些不对劲，上半部分是白色的，但是为什么呢？

结果是图像被 GPU 读取时翻转了，所以我们需要将它翻转回来

结果是图像被 GPU 读取时翻转了，所以我们需要将它翻转回来

📄src/shaders/3d-textured . f . glsl

```
 varying vec2 vTexCoord;

  void main() {
-     gl_FragColor = texture2D(texture, vTexCoord); +     gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1));
  } 
```

[![Minecraft cube](img/863b7431f2d54ded13104810d9b79ab0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ek3mGet4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://git-tutor-assets.s3.eu-west-2.amazonaws.com/minecraft-cube.gif)

酷，我们用 WebGL 渲染了一个 minecraft 立方体🎉

今天就到这里，明天见👋！

* * *

[![GitHub stars](img/e42aebac54876f9437aa37d34efaa48e.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/ffb81b9356c02c1da0fb66c696f07f02.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)