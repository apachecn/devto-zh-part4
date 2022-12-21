# WebGL 月份。第七天。一点清理和工具

> 原文：<https://dev.to/lesnitsky/webgl-month-day-7-a-bit-of-cleanup-and-tooling-bd4>

## 工具化和重构

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/a31ce4a99ae70bc608848d91daf5a2ec.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/21bb8708c7e7eeb23634c1e2c1d9b2f4.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月。

由于我们的代码库在增长，并将变得越来越复杂，我们需要一些工具和清理。

我们需要 webpack，所以让我们创建`package.json`并安装它

📄package.json

```
{  "name":  "webgl-month",  "version":  "1.0.0",  "description":  "Daily WebGL tutorials",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "repository":  {  "type":  "git",  "url":  "git+https://github.com/lesnitsky/webgl-month.git"  },  "author":  "",  "license":  "ISC",  "bugs":  {  "url":  "https://github.com/lesnitsky/webgl-month/issues"  },  "homepage":  "https://github.com/lesnitsky/webgl-month#readme",  "devDependencies":  {  "webpack":  "^4.35.2",  "webpack-cli":  "^3.3.5"  }  } 
```

我们需要一个简单的 webpack 配置

📄web pack . config . js

```
const path = require('path');

module.exports = {
    entry: {
        'week-1': './src/week-1.js',
    },

    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].js',
    },

    mode: 'development',
}; 
```

和更新脚本源

📄index.html

```
 </head>
    <body>
      <canvas></canvas>
-     <script src="./src/webgl-hello-world.js"></script> +     <script src="./dist/week-1.js"></script>
    </body>
  </html> 
```

由于着色器是原始字符串，我们可以将着色器源存储在单独的文件中，并将`raw-loader`用于`webpack`。

📄package.json

```
 },
    "homepage": "https://github.com/lesnitsky/webgl-month#readme",
    "devDependencies": {
+     "raw-loader": "^3.0.0",
      "webpack": "^4.35.2",
      "webpack-cli": "^3.3.5"
    } 
```

📄web pack . config . js

```
 filename: '[name].js',
      },

+     module: {
+         rules: [
+             {
+                 test: /\.glsl$/,
+                 use: 'raw-loader',
+             },
+         ],
+     },
+ 
      mode: 'development',
  }; 
```

让我们实际上移动着色器到单独的文件

📄src/shaders/fragment.glsl

```
precision mediump float;

varying vec4 vColor;

void main() {
    gl_FragColor = vColor / 255.0;
    gl_FragColor.a = 1.0;
} 
```

📄src/shaders/vertex.glsl

```
attribute vec2 position;
attribute vec4 color;
uniform vec2 resolution;

varying vec4 vColor;

#define M_PI 3.1415926535897932384626433832795 
void main() {
    vec2 transformedPosition = position / resolution * 2.0 - 1.0;
    gl_PointSize = 2.0;
    gl_Position = vec4(transformedPosition, 0, 1);

    vColor = color;
} 
```

📄src/week-1.js

```
+ import vShaderSource from './shaders/vertex.glsl';
+ import fShaderSource from './shaders/fragment.glsl';
+ 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  const vertexShader = gl.createShader(gl.VERTEX_SHADER);
  const fragmentShader = gl.createShader(gl.FRAGMENT_SHADER);

- const vShaderSource = `
- attribute vec2 position;
- attribute vec4 color;
- uniform vec2 resolution;
- 
- varying vec4 vColor;
- 
- #define M_PI 3.1415926535897932384626433832795
- 
- void main() {
-     vec2 transformedPosition = position / resolution * 2.0 - 1.0;
-     gl_PointSize = 2.0;
-     gl_Position = vec4(transformedPosition, 0, 1);
- 
-     vColor = color;
- }
- `;
- 
- const fShaderSource = `
-     precision mediump float;
- 
-     varying vec4 vColor;
- 
-     void main() {
-         gl_FragColor = vColor / 255.0;
-         gl_FragColor.a = 1.0;
-     }
- `;
- 
  function compileShader(shader, source) {
      gl.shaderSource(shader, source);
      gl.compileShader(shader); 
```

我们也可以将创建顶点位置的函数移到单独的文件中

📄src/shape-helpers.js

```
export function createRect(top, left, width, height) {
    return [
        left, top, // x1 y1
        left + width, top, // x2 y2
        left, top + height, // x3 y3
        left + width, top + height, // x4 y4
    ];
}

export function createHexagon(centerX, centerY, radius, segmentsCount) {
    const vertexData = [];
    const segmentAngle =  Math.PI * 2 / (segmentsCount - 1);

    for (let i = 0; i < Math.PI * 2; i += segmentAngle) {
        const from = i;
        const to = i + segmentAngle;

        const color = rainbowColors[i / segmentAngle];

        vertexData.push(centerX, centerY);
        vertexData.push(...color);

        vertexData.push(centerX + Math.cos(from) * radius, centerY + Math.sin(from) * radius);
        vertexData.push(...color);

        vertexData.push(centerX + Math.cos(to) * radius, centerY + Math.sin(to) * radius);
        vertexData.push(...color);
    }

    return vertexData;
} 
```

📄src/week-1.js

```
 import vShaderSource from './shaders/vertex.glsl';
  import fShaderSource from './shaders/fragment.glsl';

+ import { createRect } from './shape-helpers';
+ 
+ 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  const triangles = createRect(0, 0, canvas.height, canvas.height);

- function createRect(top, left, width, height) {
-     return [
-         left, top, // x1 y1
-         left + width, top, // x2 y2
-         left, top + height, // x3 y3
-         left + width, top + height, // x4 y4
-     ];
- }
- 
- function createHexagon(centerX, centerY, radius, segmentsCount) {
-     const vertexData = [];
-     const segmentAngle =  Math.PI * 2 / (segmentsCount - 1);
- 
-     for (let i = 0; i < Math.PI * 2; i += segmentAngle) {
-         const from = i;
-         const to = i + segmentAngle;
- 
-         const color = rainbowColors[i / segmentAngle];
- 
-         vertexData.push(centerX, centerY);
-         vertexData.push(...color);
- 
-         vertexData.push(centerX + Math.cos(from) * radius, centerY + Math.sin(from) * radius);
-         vertexData.push(...color);
- 
-         vertexData.push(centerX + Math.cos(to) * radius, centerY + Math.sin(to) * radius);
-         vertexData.push(...color);
-     }
- 
-     return vertexData;
- }
- 
  function fillWithColors(segmentsCount) {
      const colors = []; 
```

因为我们不再使用颜色属性，所以我们可以丢弃任何与之相关的东西

📄src/shaders/fragment.glsl

```
 precision mediump float;

- varying vec4 vColor;
- 
  void main() {
-     gl_FragColor = vColor / 255.0;
-     gl_FragColor.a = 1.0; +     gl_FragColor = vec4(1, 0, 0, 1);
  } 
```

📄src/shaders/vertex.glsl

```
 attribute vec2 position;
- attribute vec4 color;
  uniform vec2 resolution;

- varying vec4 vColor;
- 
  #define M_PI 3.1415926535897932384626433832795

  void main() {
      vec2 transformedPosition = position / resolution * 2.0 - 1.0;
      gl_PointSize = 2.0;
      gl_Position = vec4(transformedPosition, 0, 1);
- 
-     vColor = color;
  } 
```

📄src/week-1.js

```
 import { createRect } from './shape-helpers';

- 
  const canvas = document.querySelector('canvas');
  const gl = canvas.getContext('webgl');

  gl.useProgram(program);

  const positionLocation = gl.getAttribLocation(program, 'position');
- const colorLocation = gl.getAttribLocation(program, 'color');
- 
  const resolutionUniformLocation = gl.getUniformLocation(program, 'resolution');

  gl.uniform2fv(resolutionUniformLocation, [canvas.width, canvas.height]);

- const rainbowColors = [
-     [255, 0.0, 0.0, 255], // red
-     [255, 165, 0.0, 255], // orange
-     [255, 255, 0.0, 255], // yellow
-     [0.0, 255, 0.0, 255], // green
-     [0.0, 101, 255, 255], // skyblue
-     [0.0, 0.0, 255, 255], // blue,
-     [128, 0.0, 128, 255], // purple
- ];
- 
  const triangles = createRect(0, 0, canvas.height, canvas.height);

- function fillWithColors(segmentsCount) {
-     const colors = [];
- 
-     for (let i = 0; i < segmentsCount; i++) {
-         for (let j = 0; j < 3; j++) {
-             colors.push(...rainbowColors[i]);
-         }
-     }
- 
-     return colors;
- }
- 
  const vertexData = new Float32Array(triangles);
  const vertexBuffer = gl.createBuffer(gl.ARRAY_BUFFER);

  gl.enableVertexAttribArray(positionLocation);
  gl.vertexAttribPointer(positionLocation, attributeSize, type, nomralized, stride, offset);

- // gl.enableVertexAttribArray(colorLocation);
- // gl.vertexAttribPointer(colorLocation, 4, type, nomralized, stride, 8);
- 
  gl.drawElements(gl.TRIANGLES, indexData.length, gl.UNSIGNED_BYTE, 0); 
```

Webpack 将有助于我们在未来保持代码库的整洁，但我们目前还不错

明天见👋

* * *

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/a31ce4a99ae70bc608848d91daf5a2ec.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/21bb8708c7e7eeb23634c1e2c1d9b2f4.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)