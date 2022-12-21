# 编程交互式(可旋转)3D 立方体

> 原文：<https://dev.to/webdeasy/programming-interactive-rotatable-3d-cube-4575>

*原载于[我的博客](https://webdeasy.de/en/programming-interactive-rotatable-3d-cube/)*

你移动手机，立方体就会转动？是啊！这正是我们今天的计划。借助于通过 [JavaScript](https://webdeasy.de/en/category/javascript-en/) 的设备对齐。

# 演示

下面的演示只适用于移动设备。在桌面版本上，3D 立方体只是简单地刚性显示。

所以拿起你的手机，看看这个奇特的立方体吧！

[https://codepen.io/webdeasy/embed/oRKdwM?height=600&default-tab=result&embed-version=2](https://codepen.io/webdeasy/embed/oRKdwM?height=600&default-tab=result&embed-version=2)

# 立方体

我在这些说明的帮助下创建了这个立方体:

[![mehraas](img/4887ed1ecfdbd097ccbb8b9f4dc97c43.png)](/mehraas) [## CSS 3D 立方体

### ashish Mehra Apr 19 ' 181min read

#html #css #beginners #webdev](/mehraas/css-3d-cube--4lmh)
Nevertheless I would like to explain the implementation in my own words.

# HTML

在 HTML 中，我们有一个外部容器和一个附加容器，立方体的各个页面位于其中。

```
<div class="wrapper">
  <div class="cube">
    <div>Top</div>
    <div>Bottom</div>
    <div>Left</div>
    <div>Right</div>
    <div>Back</div>
    <div>Front</div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

# CSS

这里我们将立方体放在包装器的中心，指定高度和宽度，并使用*透视*和*变换样式*属性使立方体看起来像 3D。

```
.wrapper {
  height: 250px;
  perspective: 700px;
  -webkit-perspective: 700px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  font-family: arial, sans-serif;
}

.cube {
  width: 100px;
  height: 100px;
  transform-style:preserve-3d;
  transform:rotateY(35deg) rotatex(50deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

下面，我们指定各个部分的高度和宽度，并使各个页面上的文本居中。

```
.cube > div {
  position: absolute;
  width: 120px;
  height: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #FFF;
  text-transform: uppercase;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们浏览各个页面，并设置位置和旋转。我们必须将 Z 值设置为宽度的一半: **120px/2 = 60px** 。

```
.cube div:nth-child(1) {
  transform:translateZ(60px);
  background:#237ad4;
}

.cube div:nth-child(2) {
  transform: rotateX(180deg) translateZ(60px);
  background:#2070c3;
}

.cube div:nth-child(3) {
  transform: rotateY(-90deg) translateZ(60px);
  background:#2e84dd;
}

.cube div:nth-child(4) {
  transform:rotateY(90deg) translateZ(60px);
  background:#3f8edf;
}

.cube div:nth-child(5) {
  transform: rotateX(90deg) translateZ(60px);
  background:#2070d3;
}

.cube div:nth-child(6) {
  transform:rotateX(-90deg) translateZ(60px);
  background:#1d67b2;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 检测设备方位

一开始听起来很难的部分，基本上是最简单的。

我们可以通过 JavaScript 事件 **deviceorientation** 拦截对设备方位的识别。

**x 轴**的值是事件变量**伽马**，而 **y 轴**的值是事件变量**贝塔**。

```
window.addEventListener("deviceorientation", (e) => {
  const beta = e.beta;
  const gamma = e.gamma;

  // disable for desktop devices
  if(beta == null || gamma == null) {
    return false;  
  }

  document.getElementsByClassName('cube')[0].style.transform = 'rotateY(' + gamma*3 + 'deg) rotateX(' + beta*3 +'deg)';
}, true); 
```

Enter fullscreen mode Exit fullscreen mode

如果一个变量的值是零，它是一个桌面设备，我们取消事件。

如果是移动设备，我们设置变量为 **x** 和 **y** 并乘以 3 来调整移动设备时的旋转速度。

Docu 到 deviceorientation: [此处](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation)。

# 结论

事件 **deviceorientation** 非常适合这些用途，但也适用于许多其他用途，并且不为许多开发人员所知。您想在哪里使用此功能？欢迎在评论中反馈意见和改进建议！🙂

如果你喜欢这篇文章，我会很高兴你在我的博客上看到更多的文章，并在 T2 的推特和 T4 的脸书上关注我！😍