# WebGL 月份。第十天。多重纹理

> 原文：<https://dev.to/lesnitsky/webgl-month-day-10-multiple-textures-gf3>

## 多个纹理

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/433354a9395fe83c4661db01a546c8c3.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/3e130d395765fae6559a50ae2501dc92.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎回到 WebGL 月。我们已经知道如何使用单幅图像作为纹理，但是如果我们想要渲染多幅图像呢？

我们今天将学习如何做到这一点。

首先，我们需要在片段着色器中定义另一个`sampler2D`

📄src/shaders/texture.f.glsl

```
 precision mediump float;

  uniform sampler2D texture;
+ uniform sampler2D otherTexture;
  uniform vec2 resolution;

  vec4 inverse(vec4 color) { 
```

并呈现两个矩形而不是一个。左边的矩形将使用已经存在的纹理，右边的是新的。

📄src/texture.js

```
 gl.linkProgram(program);
  gl.useProgram(program);

- const vertexPosition = new Float32Array(createRect(-1, -1, 2, 2));
+ const vertexPosition = new Float32Array([
+     ...createRect(-1, -1, 1, 2), // left rect
+     ...createRect(-1, 0, 1, 2), // right rect
+ ]);
  const vertexPositionBuffer = gl.createBuffer();

  gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
  gl.enableVertexAttribArray(attributeLocations.position);
  gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0);

- const vertexIndices = new Uint8Array([0, 1, 2, 1, 2, 3]);
+ const vertexIndices = new Uint8Array([
+     // left rect
+     0, 1, 2, 
+     1, 2, 3, 
+     
+     // right rect
+     4, 5, 6, 
+     5, 6, 7,
+ ]);
  const indexBuffer = gl.createBuffer();

  gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer); 
```

我们还需要一种方法来指定每个矩形的纹理坐标，因为我们不能再使用`gl_FragCoord`，所以我们需要定义另一个属性(`texCoord`

📄src/shaders/texture.v.glsl

```
 attribute vec2 position;
+ attribute vec2 texCoord; 
  void main() {
      gl_Position = vec4(position, 0, 1); 
```

这个属性的内容应该是两个矩形的坐标。左上为`0,0`，宽度和高度为`1.0`

📄src/texture.js

```
 gl.linkProgram(program);
  gl.useProgram(program);

+ const texCoords = new Float32Array([
+     ...createRect(0, 0, 1, 1), // left rect
+     ...createRect(0, 0, 1, 1), // right rect
+ ]);
+ const texCoordsBuffer = gl.createBuffer();
+ 
+ gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
+ gl.bufferData(gl.ARRAY_BUFFER, texCoords, gl.STATIC_DRAW);
+ 
  const vertexPosition = new Float32Array([
      ...createRect(-1, -1, 1, 2), // left rect
      ...createRect(-1, 0, 1, 2), // right rect 
```

我们还需要在 JS 中设置 texCoord 属性

📄src/texture.js

```
 const attributeLocations = {
      position: gl.getAttribLocation(program, 'position'),
+     texCoord: gl.getAttribLocation(program, 'texCoord'),
  };

  const uniformLocations = {
  gl.enableVertexAttribArray(attributeLocations.position);
  gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0);

+ gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
+ 
+ gl.enableVertexAttribArray(attributeLocations.texCoord);
+ gl.vertexAttribPointer(attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0);
+ 
  const vertexIndices = new Uint8Array([
      // left rect
      0, 1, 2, 
```

并将该数据传递给片段着色器

📄src/shaders/texture.f.glsl

```
 );
  }

+ varying vec2 vTexCoord;
+ 
  void main() {
-     vec2 texCoord = gl_FragCoord.xy / resolution; +     vec2 texCoord = vTexCoord;
      gl_FragColor = texture2D(texture, texCoord);

      gl_FragColor = sepia(gl_FragColor); 
```

📄src/shaders/texture.v.glsl

```
 attribute vec2 position;
  attribute vec2 texCoord;

+ varying vec2 vTexCoord;
+ 
  void main() {
      gl_Position = vec4(position, 0, 1);
+ 
+     vTexCoord = texCoord;
  } 
```

好的，我们渲染了两个矩形，但是它们使用了相同的纹理。让我们再添加一个属性，它将指定使用哪个纹理，并通过另一个变量将该数据传递给片段着色器

📄src/shaders/texture.v.glsl

```
 attribute vec2 position;
  attribute vec2 texCoord;
+ attribute float texIndex; 
  varying vec2 vTexCoord;
+ varying float vTexIndex; 
  void main() {
      gl_Position = vec4(position, 0, 1);

      vTexCoord = texCoord;
+     vTexIndex = texIndex;
  } 
```

所以现在片段着色器将知道使用哪个纹理

> 披露者:这不是在片段着色器中使用多个纹理的完美方式，而是如何实现这一点的一个例子

📄src/shaders/texture.f.glsl

```
 }

  varying vec2 vTexCoord;
+ varying float vTexIndex; 
  void main() {
      vec2 texCoord = vTexCoord;
-     gl_FragColor = texture2D(texture, texCoord); 
-     gl_FragColor = sepia(gl_FragColor);
+     if (vTexIndex == 0.0) {
+         gl_FragColor = texture2D(texture, texCoord);
+     } else {
+         gl_FragColor = texture2D(otherTexture, texCoord);
+     }
  } 
```

左矩形的纹理索引为 0，右矩形的纹理索引为 1

📄src/texture.js

```
 gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, texCoords, gl.STATIC_DRAW);

+ const texIndicies = new Float32Array([
+     ...Array.from({ length: 4 }).fill(0), // left rect
+     ...Array.from({ length: 4 }).fill(1), // right rect
+ ]);
+ const texIndiciesBuffer = gl.createBuffer();
+ 
+ gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer);
+ gl.bufferData(gl.ARRAY_BUFFER, texIndicies, gl.STATIC_DRAW);
+ 
  const vertexPosition = new Float32Array([
      ...createRect(-1, -1, 1, 2), // left rect
      ...createRect(-1, 0, 1, 2), // right rect 
```

同样，我们需要设置顶点属性

📄src/texture.js

```
 const attributeLocations = {
      position: gl.getAttribLocation(program, 'position'),
      texCoord: gl.getAttribLocation(program, 'texCoord'),
+     texIndex: gl.getAttribLocation(program, 'texIndex'),
  };

  const uniformLocations = {
  gl.enableVertexAttribArray(attributeLocations.texCoord);
  gl.vertexAttribPointer(attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0);

+ gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer);
+ 
+ gl.enableVertexAttribArray(attributeLocations.texIndex);
+ gl.vertexAttribPointer(attributeLocations.texIndex, 1, gl.FLOAT, false, 0, 0);
+ 
  const vertexIndices = new Uint8Array([
      // left rect
      0, 1, 2, 
```

现在让我们加载我们的第二个纹理图像

📄src/texture.js

```
 import { createRect } from './shape-helpers';

  import textureImageSrc from '../asseimg/texture.jpg';
+ import textureGreenImageSrc from '../asseimg/texture-green.jpg'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
  gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, vertexIndices, gl.STATIC_DRAW);

- loadImage(textureImageSrc).then((textureImg) => {
+ Promise.all([
+     loadImage(textureImageSrc),
+     loadImage(textureGreenImageSrc),
+ ]).then(([textureImg, textureGreenImg]) => {
      const texture = gl.createTexture();

      gl.bindTexture(gl.TEXTURE_2D, texture); 
```

因为我们必须创建另一个纹理——我们需要提取一些公共代码来分离辅助函数

📄src/gl-helpers.js

```
 return p;
  }
+ 
+ export function createTexture(gl) {
+     const texture = gl.createTexture();
+     
+     gl.bindTexture(gl.TEXTURE_2D, texture);
+     
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
+ 
+     return texture;
+ }
+ 
+ export function setImage(gl, texture, img) {
+     gl.bindTexture(gl.TEXTURE_2D, texture);
+ 
+     gl.texImage2D(
+         gl.TEXTURE_2D,
+         0,
+         gl.RGBA,
+         gl.RGBA,
+         gl.UNSIGNED_BYTE,
+         img,
+     );
+ } 
```

📄src/texture.js

```
 loadImage(textureImageSrc),
      loadImage(textureGreenImageSrc),
  ]).then(([textureImg, textureGreenImg]) => {
-     const texture = gl.createTexture();
- 
-     gl.bindTexture(gl.TEXTURE_2D, texture);
- 
-     gl.texImage2D(
-         gl.TEXTURE_2D,
-         0,
-         gl.RGBA,
-         gl.RGBA,
-         gl.UNSIGNED_BYTE,
-         textureImg,
-     );
- 
-     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
-     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
-     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
-     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR); + 
      gl.activeTexture(gl.TEXTURE0);
      gl.uniform1i(uniformLocations.texture, 0); 
```

现在让我们使用新创建的助手

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
- import { compileShader, loadImage } from './gl-helpers'; + import { compileShader, loadImage, createTexture, setImage } from './gl-helpers';
  import { createRect } from './shape-helpers';

  import textureImageSrc from '../asseimg/texture.jpg';
      loadImage(textureImageSrc),
      loadImage(textureGreenImageSrc),
  ]).then(([textureImg, textureGreenImg]) => {
+     const texture = createTexture(gl);
+     setImage(gl, texture, textureImg); 
+     const otherTexture = createTexture(gl);
+     setImage(gl, otherTexture, textureGreenImg); 
      gl.activeTexture(gl.TEXTURE0);
      gl.uniform1i(uniformLocations.texture, 0); 
```

获得统一的位置

📄src/texture.js

```
 const uniformLocations = {
      texture: gl.getUniformLocation(program, 'texture'),
+     otherTexture: gl.getUniformLocation(program, 'otherTexture'),
      resolution: gl.getUniformLocation(program, 'resolution'),
  }; 
```

并为必要的制服设置必要的纹理

要将纹理设置为统一的，您应该指定

*   范围`[gl.TEXTURE0..gl.TEXTURE31]`中的活动纹理单元(纹理单元的数量取决于 GPU，可以用`gl.getParameter`检索)
*   将纹理绑定到纹理单元
*   将纹理单元“索引”设置为`sampler2D`统一

📄src/texture.js

```
 setImage(gl, otherTexture, textureGreenImg);

      gl.activeTexture(gl.TEXTURE0);
+     gl.bindTexture(gl.TEXTURE_2D, texture);
      gl.uniform1i(uniformLocations.texture, 0);

+     gl.activeTexture(gl.TEXTURE1);
+     gl.bindTexture(gl.TEXTURE_2D, otherTexture);
+     gl.uniform1i(uniformLocations.otherTexture, 1);
+ 
      gl.uniform2fv(uniformLocations.resolution, [canvas.width, canvas.height]);

      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

就这样，我们现在可以渲染多个纹理了

明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/433354a9395fe83c4661db01a546c8c3.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/3e130d395765fae6559a50ae2501dc92.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)