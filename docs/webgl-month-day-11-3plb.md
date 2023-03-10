# WebGL 月份。第 11 天。减少 WebGL 样板文件

> 原文：<https://dev.to/lesnitsky/webgl-month-day-11-3plb>

## 减少样板文件

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/4b13c63f7749e880ea0101298458fa81.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/4c84b80da3e55d8a5b832827716e270f.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

昨天我们已经学习了如何使用多种纹理。这需要修改着色器，以及 javascript，但这种变化可能会部分自动完成

有一个包 [glsl-extract-sync](https://www.npmjs.com/package/glsl-extract-sync) 可以获得关于着色器属性和制服的信息

用
安装这个包

```
npm i glsl-extract-sync 
```

📄package.json

```
 "url-loader": "^2.0.1",
      "webpack": "^4.35.2",
      "webpack-cli": "^3.3.5"
+   },
+   "dependencies": {
+     "glsl-extract-sync": "0.0.0"
    }
  } 
```

现在让我们创建一个助手函数，它将在这个包的帮助下获取所有对属性和统一的引用

📄src/gl-helpers.js

```
+ import extract from 'glsl-extract-sync';
+ 
  export function compileShader(gl, shader, source) {
      gl.shaderSource(shader, source);
      gl.compileShader(shader);
          img,
      );
  }
+ 
+ export function setupShaderInput(gl, program, vShaderSource, fShaderSource) {
+ 
+ } 
```

我们需要提取关于顶点和片段着色器的信息

📄src/gl-helpers.js

```
 }

  export function setupShaderInput(gl, program, vShaderSource, fShaderSource) {
- +     const vShaderInfo = extract(vShaderSource);
+     const fShaderInfo = extract(fShaderSource);
  } 
```

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
- import { compileShader, loadImage, createTexture, setImage } from './gl-helpers'; + import { compileShader, loadImage, createTexture, setImage, setupShaderInput } from './gl-helpers';
  import { createRect } from './shape-helpers';

  import textureImageSrc from '../asseimg/texture.jpg';
  gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertexPosition, gl.STATIC_DRAW);

+ console.log(setupShaderInput(gl, program, vShaderSource, fShaderSource));
+ 
  const attributeLocations = {
      position: gl.getAttribLocation(program, 'position'),
      texCoord: gl.getAttribLocation(program, 'texCoord'), 
```

只有顶点着色器可能有属性，但统一可以在两个着色器中定义

📄src/gl-helpers.js

```
 export function setupShaderInput(gl, program, vShaderSource, fShaderSource) {
      const vShaderInfo = extract(vShaderSource);
      const fShaderInfo = extract(fShaderSource);
+ 
+     const attributes = vShaderInfo.attributes;
+     const uniforms = [
+         ...vShaderInfo.uniforms,
+         ...fShaderInfo.uniforms,
+     ];
  } 
```

现在我们可以得到所有的属性位置

📄src/gl-helpers.js

```
 ...vShaderInfo.uniforms,
          ...fShaderInfo.uniforms,
      ];
+ 
+     const attributeLocations = attributes.reduce((attrsMap, attr) => {
+         attrsMap[attr.name] = gl.getAttribLocation(program, attr.name);
+         return attrsMap;
+     }, {});
  } 
```

并启用所有属性

📄src/gl-helpers.js

```
 attrsMap[attr.name] = gl.getAttribLocation(program, attr.name);
          return attrsMap;
      }, {});
+ 
+     attributes.forEach((attr) => {
+         gl.enableVertexAttribArray(attributeLocations[attr.name]);
+     });
  } 
```

我们还应该得到所有统一的位置

📄src/gl-helpers.js

```
 attributes.forEach((attr) => {
          gl.enableVertexAttribArray(attributeLocations[attr.name]);
      });
+ 
+     const uniformLocations = uniforms.reduce((uniformsMap, uniform) => {
+         uniformsMap[uniform.name] = gl.getUniformLocation(program, uniform.name);
+         return uniformsMap;
+     }, {});
  } 
```

最后返回属性和统一位置

📄src/gl-helpers.js

```
 uniformsMap[uniform.name] = gl.getUniformLocation(program, uniform.name);
          return uniformsMap;
      }, {});
+ 
+     return {
+         attributeLocations,
+         uniformLocations,
+     }
  } 
```

好了，让我们利用一下我们新的甜蜜助手

📄src/texture.js

```
 gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertexPosition, gl.STATIC_DRAW);

- console.log(setupShaderInput(gl, program, vShaderSource, fShaderSource));
+ const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource); 
- const attributeLocations = {
-     position: gl.getAttribLocation(program, 'position'),
-     texCoord: gl.getAttribLocation(program, 'texCoord'),
-     texIndex: gl.getAttribLocation(program, 'texIndex'),
- };
- 
- const uniformLocations = {
-     texture: gl.getUniformLocation(program, 'texture'),
-     otherTexture: gl.getUniformLocation(program, 'otherTexture'),
-     resolution: gl.getUniformLocation(program, 'resolution'),
- };
- 
- gl.enableVertexAttribArray(attributeLocations.position);
- gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0); + gl.vertexAttribPointer(programInfo.attributeLocations.position, 2, gl.FLOAT, false, 0, 0); 
  gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
- 
- gl.enableVertexAttribArray(attributeLocations.texCoord);
- gl.vertexAttribPointer(attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0); + gl.vertexAttribPointer(programInfo.attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0); 
  gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer);
- 
- gl.enableVertexAttribArray(attributeLocations.texIndex);
- gl.vertexAttribPointer(attributeLocations.texIndex, 1, gl.FLOAT, false, 0, 0); + gl.vertexAttribPointer(programInfo.attributeLocations.texIndex, 1, gl.FLOAT, false, 0, 0); 
  const vertexIndices = new Uint8Array([
      // left rect

      gl.activeTexture(gl.TEXTURE0);
      gl.bindTexture(gl.TEXTURE_2D, texture);
-     gl.uniform1i(uniformLocations.texture, 0); +     gl.uniform1i(programInfo.uniformLocations.texture, 0); 
      gl.activeTexture(gl.TEXTURE1);
      gl.bindTexture(gl.TEXTURE_2D, otherTexture);
-     gl.uniform1i(uniformLocations.otherTexture, 1); +     gl.uniform1i(programInfo.uniformLocations.otherTexture, 1); 
-     gl.uniform2fv(uniformLocations.resolution, [canvas.width, canvas.height]);
+     gl.uniform2fv(programInfo.uniformLocations.resolution, [canvas.width, canvas.height]); 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

看起来很像清理😎

我们经常使用的另一个东西是缓冲器。让我们创建一个助手类

📄src/glbuffer . js

```
export class GLBuffer {
    constructor(gl, target, data) {

    }
} 
```

我们需要数据、缓冲区目标和实际的 gl 缓冲区，所以让我们分配从外部传来的所有数据并创建一个 gl 缓冲区。

📄src/glbuffer . js

```
 export class GLBuffer {
      constructor(gl, target, data) {
- +         this.target = target;
+         this.data = data;
+         this.glBuffer = gl.createBuffer();
      }
  } 
```

我们没有给实例分配一个`gl`,因为这可能会导致内存泄漏，所以我们需要从外部传递它

让我们实现一个`gl.bindBuffer`的替代方案

📄src/glbuffer . js

```
 this.data = data;
          this.glBuffer = gl.createBuffer();
      }
+ 
+     bind(gl) {
+         gl.bindBuffer(this.target, this.glBuffer);
+     }
  } 
```

也是设置缓冲区数据的一种便捷方式

📄src/glbuffer . js

```
 bind(gl) {
          gl.bindBuffer(this.target, this.glBuffer);
      }
+ 
+     setData(gl, data, usage) {
+         this.data = data;
+         this.bind(gl);
+         gl.bufferData(this.target, this.data, usage);
+     }
  } 
```

现在，让我们创建一个构造函数的`data`参数，并添加一个`usage`参数，以便能够通过一个构造函数调用完成我们需要的所有事情

📄src/glbuffer . js

```
 export class GLBuffer {
-     constructor(gl, target, data) { +     constructor(gl, target, data, usage) {
          this.target = target;
          this.data = data;
          this.glBuffer = gl.createBuffer();
+ 
+         if (typeof data !== 'undefined') {
+             this.setData(gl, data, usage);
+         }
      }

      bind(gl) { 
```

酷，现在我们可以用我们的瘦包装器替换 texCoords 缓冲区了

📄src/texture.js

```
 import textureImageSrc from '../asseimg/texture.jpg';
  import textureGreenImageSrc from '../asseimg/texture-green.jpg';
+ import { GLBuffer } from './GLBuffer'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  gl.linkProgram(program);
  gl.useProgram(program);

- const texCoords = new Float32Array([
+ const texCoordsBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, new Float32Array([
      ...createRect(0, 0, 1, 1), // left rect
      ...createRect(0, 0, 1, 1), // right rect
- ]);
- const texCoordsBuffer = gl.createBuffer();
- 
- gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
- gl.bufferData(gl.ARRAY_BUFFER, texCoords, gl.STATIC_DRAW); + ]), gl.STATIC_DRAW); 
  const texIndicies = new Float32Array([
      ...Array.from({ length: 4 }).fill(0), // left rect

  gl.vertexAttribPointer(programInfo.attributeLocations.position, 2, gl.FLOAT, false, 0, 0);

- gl.bindBuffer(gl.ARRAY_BUFFER, texCoordsBuffer);
+ texCoordsBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0);

  gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer); 
```

对纹理索引缓冲区进行同样的操作

📄src/texture.js

```
 ...createRect(0, 0, 1, 1), // right rect
  ]), gl.STATIC_DRAW);

- const texIndicies = new Float32Array([
+ const texIndiciesBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, new Float32Array([
      ...Array.from({ length: 4 }).fill(0), // left rect
      ...Array.from({ length: 4 }).fill(1), // right rect
- ]);
- const texIndiciesBuffer = gl.createBuffer();
- 
- gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer);
- gl.bufferData(gl.ARRAY_BUFFER, texIndicies, gl.STATIC_DRAW); + ]), gl.STATIC_DRAW); 
  const vertexPosition = new Float32Array([
      ...createRect(-1, -1, 1, 2), // left rect
  texCoordsBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.texCoord, 2, gl.FLOAT, false, 0, 0);

- gl.bindBuffer(gl.ARRAY_BUFFER, texIndiciesBuffer);
+ texIndiciesBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.texIndex, 1, gl.FLOAT, false, 0, 0);

  const vertexIndices = new Uint8Array([ 
```

顶点位置

📄src/texture.js

```
 ...Array.from({ length: 4 }).fill(1), // right rect
  ]), gl.STATIC_DRAW);

- const vertexPosition = new Float32Array([
+ const vertexPositionBuffer = new GLBuffer(gl, gl.ARRAY_BUFFER, new Float32Array([
      ...createRect(-1, -1, 1, 2), // left rect
      ...createRect(-1, 0, 1, 2), // right rect
- ]);
- const vertexPositionBuffer = gl.createBuffer(); + ]), gl.STATIC_DRAW); 
- gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
- gl.bufferData(gl.ARRAY_BUFFER, vertexPosition, gl.STATIC_DRAW); 
  const programInfo = setupShaderInput(gl, program, vShaderSource, fShaderSource);

+ vertexPositionBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.position, 2, gl.FLOAT, false, 0, 0);

  texCoordsBuffer.bind(gl); 
```

和索引缓冲区

📄src/texture.js

```
 texIndiciesBuffer.bind(gl);
  gl.vertexAttribPointer(programInfo.attributeLocations.texIndex, 1, gl.FLOAT, false, 0, 0);

- const vertexIndices = new Uint8Array([
+ const indexBuffer = new GLBuffer(gl, gl.ELEMENT_ARRAY_BUFFER, new Uint8Array([
      // left rect
      0, 1, 2, 
      1, 2, 3, 
      // right rect
      4, 5, 6, 
      5, 6, 7,
- ]);
- const indexBuffer = gl.createBuffer();
- 
- gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
- gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, vertexIndices, gl.STATIC_DRAW); + ]), gl.STATIC_DRAW); 
  Promise.all([
      loadImage(textureImageSrc),

      gl.uniform2fv(programInfo.uniformLocations.resolution, [canvas.width, canvas.height]);

-     gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
+     gl.drawElements(gl.TRIANGLES, indexBuffer.data.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

现在我们能够用更少的代码更高效地使用着色器了！

明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/4b13c63f7749e880ea0101298458fa81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQC8apdT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/github/stars/lesnitsky/webgl-month.svg%3Fstyle%3Dsocial%26hash%3Dday11)
[![Twitter Follow](img/4c84b80da3e55d8a5b832827716e270f.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--r9zniMWM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/twitter/follow/lesnitsky_a.svg%3Flabel%3DFollow%2520me%26style%3Dsocial%26hash%3Dday11)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)