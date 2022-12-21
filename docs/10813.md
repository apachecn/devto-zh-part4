# WebGL 月份。第 29 天。雾

> 原文：<https://dev.to/lesnitsky/webgl-month-day-29-fog-58od>

这是一系列与 WebGL 相关的博文。每天都会有新帖子

[![GitHub stars](img/78a74ae5b8d99f01863eea3ac30fce29.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/a6a6d93c856f039acdfbdc6417297e4f.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)

* * *

嘿👋

欢迎来到网络博客月

今天我们要用雾来改善我们的 3D minecraft 地形场景

基本上我们需要“变亮”远处立方体的颜色(计算相机和立方体顶点之间的距离)

为了计算相机位置和某个点之间的相对距离，我们需要将位置乘以视图和模型矩阵。因为我们还需要同样的结果矩阵和投影矩阵，所以让我们把它提取到一个变量中

📄src/shaders/3d-textured . v . glsl

```
 }

  void main() {
-     gl_Position = projectionMatrix * viewMatrix * modelMatrix * vec4(position, 1.0); +     mat4 modelView = viewMatrix * modelMatrix;
+ 
+     gl_Position = projectionMatrix * modelView * vec4(position, 1.0); 
      vTexCoord = texCoord;
      vColor = encodeObject(index); 
```

因为我们的相机看着 Z 轴的负方向，我们需要得到顶点位置的坐标

📄src/shaders/3d-textured . v . glsl

```
 gl_Position = projectionMatrix * modelView * vec4(position, 1.0);

+     float depth = (modelView * vec4(position, 1.0)).z;
+ 
      vTexCoord = texCoord;
      vColor = encodeObject(index); 
```

但是这个值将会是负的，而我们需要一个正值，所以让我们把它取反

📄src/shaders/3d-textured . v . glsl

```
 gl_Position = projectionMatrix * modelView * vec4(position, 1.0);

-     float depth = (modelView * vec4(position, 1.0)).z;
+     float depth = -(modelView * vec4(position, 1.0)).z; 
      vTexCoord = texCoord;
      vColor = encodeObject(index); 
```

我们不能直接使用`depth`，因为我们需要一个在`[0..1]`范围内的值。同样，有一个平滑的“渐变”就像雾一样也不错。我们可以应用 glsl [smoothstep](https://thebookofshaders.com/glossary/?search=smoothstep) 函数来计算最终的雾量。该功能在`lowerBound`和`upperBound`范围内插值。我们相机的最大深度是`142`

```
mat4.perspective(
    projectionMatrix,
    (Math.PI / 360) * 90,
    canvas.width / canvas.height,
    0.01,
    142 // <- zFar
); 
```

所以`depth`的最大值应该是< 142，才能看到任何雾(比 142 更远的物体根本看不到)。让我们使用`60..100`范围。

还有一点需要考虑的是，我们不希望看到物体*完全*白，所以让我们把最终的量乘以`0.9`

我们需要片段着色器中`fogAmount`的最终值，所以这应该是一个`varying`

📄src/shaders/3d-textured . v . glsl

```
 varying vec2 vTexCoord;
  varying vec3 vColor;
  varying vec4 vColorMultiplier;
+ varying float vFogAmount; 
  vec3 encodeObject(float id) {
      int b = int(mod(id, 255.0));
      gl_Position = projectionMatrix * modelView * vec4(position, 1.0);

      float depth = -(modelView * vec4(position, 1.0)).z;
+     vFogAmount = smoothstep(60.0, 100.0, depth) * 0.9; 
      vTexCoord = texCoord;
      vColor = encodeObject(index); 
```

让我们在片段着色器中定义这种变化

📄src/shaders/3d-textured . f . glsl

```
 uniform float renderIndices;
  varying vec4 vColorMultiplier;
+ varying float vFogAmount; 
  void main() {
      gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)) * vColorMultiplier; 
```

现在让我们定义雾的颜色(白色)。我们也可以将这种颜色传递给制服，但是让事情简单一些

📄src/shaders/3d-textured . f . glsl

```
 void main() {
      gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)) * vColorMultiplier;

+     vec3 fogColor = vec3(1.0, 1.0, 1.0);
+ 
      if (renderIndices == 1.0) {
          gl_FragColor.rgb = vColor;
      } 
```

最后，我们需要混合像素的原始颜色和雾。我们可以使用 glsl [mix](https://thebookofshaders.com/glossary/?search=mix)

📄src/shaders/3d-textured . f . glsl

```
 gl_FragColor = texture2D(texture, vTexCoord * vec2(1, -1) + vec2(0, 1)) * vColorMultiplier;

      vec3 fogColor = vec3(1.0, 1.0, 1.0);
+     gl_FragColor.rgb = mix(gl_FragColor.rgb, fogColor, vFogAmount); 
      if (renderIndices == 1.0) {
          gl_FragColor.rgb = vColor; 
```

就是这样，我们的场景现在是“雾蒙蒙”的。实现同样的效果，但“在晚上”，我们只需要改变雾的颜色为黑色。

感谢阅读！

[![GitHub stars](img/78a74ae5b8d99f01863eea3ac30fce29.png)](https://github.com/lesnitsky/webgl-month)
[![Twitter Follow](img/a6a6d93c856f039acdfbdc6417297e4f.png)T6】](https://twitter.com/lesnitsky_a)

[加入邮件列表](http://eepurl.com/gwiSeH),让新邮件直接进入你的收件箱

[此处提供源代码](https://github.com/lesnitsky/webgl-month)

建造于

[![Git Tutor Logo](img/4a5fcbd1126401b65ca7fbe362404aa0.png)](https://github.com/lesnitsky/git-tutor)