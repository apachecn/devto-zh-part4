# 用 Scene.js 创建 JavaScript 动态打字动画

> 原文：<https://dev.to/daybrush/create-a-javascript-kinetic-typing-animation-with-scene-js-4jik>

我写一个帖子通知你，我已经在 Scene.js 效果库中添加了一个打字效果。
[https://github.com/daybrush/scenejs-effects](https://github.com/daybrush/scenejs-effects)

在这篇文章中，我想介绍动画打字的原则。

### 打字动画

制作打字动画有三种典型的方法。

1.  JavaScript 动画
2.  CSS 动画(仅限 PC Chrome)
3.  SVG 动画

本文要描述的动画是 JavaScript。因为它比我想象的简单。

CSS 动画还可以使用之前或之后伪选择器和内容属性创建键入动画。不过既然是只支持 PC Chrome 的选择器，用起来就太多了。

如果你的浏览器是 PC Chrome，你会看到下面的动画。
[https://codepen.io/daybrush/embed/rELxqr?height=600&default-tab=result&embed-version=2](https://codepen.io/daybrush/embed/rELxqr?height=600&default-tab=result&embed-version=2)

键入动画是一种将文本按顺序导入 html 的方法，如下所示。

```
const text = "Make a typing effect with Scene.js.";
const length = text.length;
for (let i = 0; i <= length; ++i) {
    element.innerHTML = text.substring(start, i);
} 
```

一旦使用 Scene.js 创建了打字动画，就可以编写如下代码:

```
Scene.typing({ text: "Make a typing effect with Scene.js." }); 
```

播放上面的代码，它看起来像下面的代码笔:
[https://codepen.io/daybrush/embed/ydOVPW?height=600&default-tab=result&embed-version=2](https://codepen.io/daybrush/embed/ydOVPW?height=600&default-tab=result&embed-version=2)

### 动力学动画

除了打字动画之外，它还试图产生一种运动效果。

动力效应将原点向相反方向移动。原点看起来是固定的，但却在移动。

```
.kinetic {
    transform: translate(20px, -40px);
    transform-origin: calc(50% - 20px) calc(50% + 40px);
} 
```

一旦你使用 Scene.js 创建了一个动态动画，你就可以编写如下代码:

```
Scene.kineticFrame({ left: "20px", top: "-40px" }); 
```

播放上面的代码，它看起来像下面的代码笔:
[https://codepen.io/daybrush/embed/NZrVGv?height=600&default-tab=result&embed-version=2](https://codepen.io/daybrush/embed/NZrVGv?height=600&default-tab=result&embed-version=2)

### 动能打字动画

最后，及时添加打字动画，实现动能效果。

```
scene.set({
  "[data-typing='i']": Scene.typing({ text: "I ", duration: 1}),
  "[data-typing='frontend']": {
    1: Scene.typing({ text: "'m Front-End", duration: 1}),
  },
  "[data-typing='engineer']": {
    1.5: Scene.typing({ text: "Engineer", duration: 1}),
  },
  "[data-typing='with']": {
    3.3: Scene.typing({ text: "with", duration: 0.5}),
  },
  // ...
}); 
```

最后，如果把打字效果和动能效果绑在一起，会如下:
[https://codepen.io/daybrush/embed/MMyORm?height=600&default-tab=result&embed-version=2](https://codepen.io/daybrush/embed/MMyORm?height=600&default-tab=result&embed-version=2)

请点击以下链接查看我的项目。
[https://github.com/daybrush/scenejs](https://github.com/daybrush/scenejs)
[https://github.com/daybrush/scenejs-effects](https://github.com/daybrush/scenejs-effects)

喜欢就按星。

如果您有想要的功能或问题，请随时注册问题或写下问题。

谢谢你