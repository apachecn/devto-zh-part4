# WebGL 月份。第八天。口感

> 原文：<https://dev.to/lesnitsky/webgl-month-day-8-textures-1mk8>

## 第八天。口感

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/13752c9d8ea4520a48deffcffe622088.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/936d7bb3cde88ab04a0c4311ba404939.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋欢迎回到 WebGL 月。

我们已经学习了几种将颜色数据传递给着色器的方法，但是还有一种方法非常强大。今天我们将学习纹理

让我们创建简单的着色器

📄src/shaders/texture.f.glsl

```
precision mediump float;

void main() {
    gl_FragColor = vec4(1, 0, 0, 1);
} 
```

📄src/shaders/texture.v.glsl

```
attribute vec2 position;

void main() {
    gl_Position = vec4(position, 0, 1);
} 
```

📄src/texture.js

```
import vShaderSource from './shaders/texture.v.glsl';
import fShaderSource from './shaders/texture.f.glsl'; 
```

获取 webgl 上下文

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
+ 
+ const canvas = document.querySelector('canvas');
+ const gl = canvas.getContext('webgl'); 
```

创建着色器

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
+ import { compileShader } from './gl-helpers'; 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
+ 
+ const vShader = gl.createShader(gl.VERTEX_SHADER);
+ const fShader = gl.createShader(gl.FRAGMENT_SHADER);
+ 
+ compileShader(gl, vShader, vShaderSource);
+ compileShader(gl, fShader, fShaderSource); 
```

和程序

📄src/texture.js

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

创建一个顶点位置缓冲区并用数据填充它

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
  import { compileShader } from './gl-helpers';
+ import { createRect } from './shape-helpers';
+ 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  gl.linkProgram(program);
  gl.useProgram(program);
+ 
+ const vertexPosition = new Float32Array(createRect(-1, -1, 2, 2));
+ const vertexPositionBuffer = gl.createBuffer();
+ 
+ gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
+ gl.bufferData(gl.ARRAY_BUFFER, vertexPosition, gl.STATIC_DRAW); 
```

设置职位属性

📄src/texture.js

```
 gl.bindBuffer(gl.ARRAY_BUFFER, vertexPositionBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertexPosition, gl.STATIC_DRAW);
+ 
+ const attributeLocations = {
+     position: gl.getAttribLocation(program, 'position'),
+ };
+ 
+ gl.enableVertexAttribArray(attributeLocations.position);
+ gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0); 
```

设置索引缓冲区

📄src/texture.js

```
 gl.enableVertexAttribArray(attributeLocations.position);
  gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0);
+ 
+ const vertexIndices = new Uint8Array([0, 1, 2, 1, 2, 3]);
+ const indexBuffer = gl.createBuffer();
+ 
+ gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
+ gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, vertexIndices, gl.STATIC_DRAW); 
```

并发出平局呼叫

📄src/texture.js

```
 gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
  gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, vertexIndices, gl.STATIC_DRAW);
+ 
+ gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

所以现在我们可以继续纹理。

你可以上传图像到一个图形处理器，并用它来计算像素颜色。在一个简单的例子中，当画布大小相同或至少与图像大小成比例时，我们可以逐个像素地渲染图像，读取图像的每个像素颜色，并将其用作`gl_FragColor`

让我们制作一个加载图像的助手

📄src/gl-helpers.js

```
 throw new Error(log);
      }
  }
+ 
+ export async function loadImage(src) {
+     const img = new Image();
+ 
+     let _resolve;
+     const p = new Promise((resolve) => _resolve = resolve);
+ 
+     img.onload = () => {
+         _resolve(img);
+     }
+ 
+     img.src = src;
+ 
+     return p;
+ } 
```

加载图像并创建 webgl 纹理

📄src/texture.js

```
 import vShaderSource from './shaders/texture.v.glsl';
  import fShaderSource from './shaders/texture.f.glsl';
- import { compileShader } from './gl-helpers'; + import { compileShader, loadImage } from './gl-helpers';
  import { createRect } from './shape-helpers';

+ import textureImageSrc from '../asseimg/texture.jpg';

  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');
  gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
  gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, vertexIndices, gl.STATIC_DRAW);

- gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
+ loadImage(textureImageSrc).then((textureImg) => {
+     const texture = gl.createTexture();
+ 
+     gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
+ }); 
```

[GTI}添加图像

📄asseimg/texture.jpg

我们还需要一个合适的 webpack 加载器

📄package.json

```
 "homepage": "https://github.com/lesnitsky/webgl-month#readme",
    "devDependencies": {
      "raw-loader": "^3.0.0",
+     "url-loader": "^2.0.1",
      "webpack": "^4.35.2",
      "webpack-cli": "^3.3.5"
    } 
```

📄web pack . config . js

```
 test: /\.glsl$/,
                  use: 'raw-loader',
              },
+ 
+             {
+                 test: /\.jpg$/,
+                 use: 'url-loader',
+             },
          ],
      }, 
```

为了操作纹理，我们需要做和缓冲区一样的事情——绑定它

📄src/texture.js

```
 loadImage(textureImageSrc).then((textureImg) => {
      const texture = gl.createTexture();

+     gl.bindTexture(gl.TEXTURE_2D, texture);
+ 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

并将图像上传到绑定纹理

📄src/texture.js

```
 gl.bindTexture(gl.TEXTURE_2D, texture);

+     gl.texImage2D(
+         gl.TEXTURE_2D,
+     );
+ 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

让我们暂时忽略第二个论点，我们以后再讨论它

📄src/texture.js

```
 gl.texImage2D(
          gl.TEXTURE_2D,
+         0,
      );

      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

第三和第四个参数指定内部纹理格式和源(图像)格式。对于我们的图像，它是 GL . rgba。[查看此页面了解更多关于格式的细节](https://developer.mozilla.org/en-US/docs/Web/API/WebGLRenderingContext/texImage2D)

📄src/texture.js

```
 gl.texImage2D(
          gl.TEXTURE_2D,
          0,
+         gl.RGBA,
+         gl.RGBA,
      );

      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

下一个参数指定源类型(0..255 是无符号字节)

📄src/texture.js

```
 0,
          gl.RGBA,
          gl.RGBA,
+         gl.UNSIGNED_BYTE,
      );

      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

和图像本身

📄src/texture.js

```
 gl.RGBA,
          gl.RGBA,
          gl.UNSIGNED_BYTE,
+         textureImg,
      );

      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0); 
```

我们还需要指定不同的纹理参数。我们将在接下来的教程中讨论这些参数。

📄src/texture.js

```
 textureImg,
      );

+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
+     gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
+ 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

为了能够在着色器中处理纹理，我们需要指定一个`sampler2D`类型的制服

📄src/shaders/texture.f.glsl

```
 precision mediump float;

+ uniform sampler2D texture;
+ 
  void main() {
      gl_FragColor = vec4(1, 0, 0, 1);
  } 
```

并指定此制服的价值。有一种方法可以使用多个纹理，我们将在接下来的教程中讨论它

📄src/texture.js

```
 position: gl.getAttribLocation(program, 'position'),
  };

+ const uniformLocations = {
+     texture: gl.getUniformLocation(program, 'texture'),
+ };
+ 
  gl.enableVertexAttribArray(attributeLocations.position);
  gl.vertexAttribPointer(attributeLocations.position, 2, gl.FLOAT, false, 0, 0);

      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
      gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);

+     gl.activeTexture(gl.TEXTURE0);
+     gl.uniform1i(uniformLocations.texture, 0);
+ 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

让我们也将画布分辨率传递给着色器

📄src/shaders/texture.f.glsl

```
 precision mediump float;

  uniform sampler2D texture;
+ uniform vec2 resolution; 
  void main() {
      gl_FragColor = vec4(1, 0, 0, 1); 
```

📄src/texture.js

```
 const uniformLocations = {
      texture: gl.getUniformLocation(program, 'texture'),
+     resolution: gl.getUniformLocation(program, 'resolution'),
  };

  gl.enableVertexAttribArray(attributeLocations.position);
      gl.activeTexture(gl.TEXTURE0);
      gl.uniform1i(uniformLocations.texture, 0);

+     gl.uniform2fv(uniformLocations.resolution, [canvas.width, canvas.height]);
+ 
      gl.drawElements(gl.TRIANGLES, vertexIndices.length, gl.UNSIGNED_BYTE, 0);
  }); 
```

有一个特殊的`gl_FragCoord`变量包含每个像素的坐标。与`resolution`制服一起，我们可以得到一个`texture coordinate`(图像中像素的坐标)。纹理坐标在范围`[0..1]`内。

📄src/shaders/texture.f.glsl

```
 uniform vec2 resolution;

  void main() {
+     vec2 texCoord = gl_FragCoord.xy / resolution;
      gl_FragColor = vec4(1, 0, 0, 1);
  } 
```

并使用`texture2D`渲染整个图像。

📄src/shaders/texture.f.glsl

```
 void main() {
      vec2 texCoord = gl_FragCoord.xy / resolution;
-     gl_FragColor = vec4(1, 0, 0, 1); +     gl_FragColor = texture2D(texture, texCoord);
  } 
```

凉爽的😎我们现在可以渲染图像了，但是关于纹理还有很多要学，所以明天见

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/13752c9d8ea4520a48deffcffe622088.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/936d7bb3cde88ab04a0c4311ba404939.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)