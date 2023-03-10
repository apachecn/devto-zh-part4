# 网络动画 API-生活的幻觉

> 原文：<https://dev.to/brianmontanaweb/web-animation-api-llusion-of-life-2mg6>

# 动画的 12 个基本原则

让我们从弗兰克·托马斯和奥利·约翰斯顿的《生命的幻觉》开始，他们是迪斯尼的动画制作人，他们用 12 个概念定义了动画的基础。

**挤压和拉伸:**
赋予元素以物质性，使运动处于一致的物理状态；现实的还是想象的。推拉视觉效果，有时会反弹。

**预期:**
让用户为视觉状态的变化或呈现方式的大转变做好准备。在更大的行动发生之前通过光的运动发出信号。

**Staging:**
为用户提供正在发生的事情的清晰状态或方向，易于快速识别。对剧院或聚光灯聚焦在组件上的引用。

**摆好姿势或向前直走:**
通过对关键帧之间的过渡进行编程来构建关键帧，使其在产品中感觉自然。

**跟随:**
具有多个部分的组件可以通过继续和当组件进入适当位置时迅速返回来对运动作出反应。

**缓入&缓出:**
缓入或缓出动画，随着时间的推移改变值，以较低的值长时间进入或以较低的值更快进入。可视化为 Y 为时间，X 为数值的图形。

**弧线:**
赋予组件的入口或出口一条曲线路径或从头到尾的位置变化，赋予其个性。

**次要动作:**
对进入或退出做出反应的动作。进入的组件与另一个组件碰撞，里面的内容也移动，但以交错或减少的运动来响应。

**计时:**
当用户需要等待服务器响应时的慢速动作(或相关场景)，当组件/数据被填充时的快速动作。

**夸张:**
使组件的动画比实际大，以引起注意或显示其目的，有时给它比例，额外挤压/拉伸等。

**立体图:**
3D 可以在动画制作时赋予组件重量和突出性。

**吸引力:**
在确定重力、重量、运动、尺寸等时，为组件定义一个反应的物理环境，以吸引用户并引导他们关注产品。使组件看起来有趣。

该视频展示了动画的视觉效果，通过所有规则集赋予立方体个性。

[https://player.vimeo.com/video/93206523](https://player.vimeo.com/video/93206523)
[人生的幻觉](https://vimeo.com/93206523)从 [cento lodigiani](https://vimeo.com/centodesign) 到 [Vimeo](https://vimeo.com) 。

这里有一些 CSS 动画，涵盖了动画的基本原理，粘粘的粘粘的按钮！

[https://codepen.io/brianmontanaweb/embed/wpKYwP?height=600&default-tab=result&embed-version=2](https://codepen.io/brianmontanaweb/embed/wpKYwP?height=600&default-tab=result&embed-version=2)
构建迪士尼工作室构思的动画基本原则

运动，当在视觉上被一致地定义时，将传达给观众/观众的作品的意图。这些概念可以建立在任何包含时间成分的媒体上:视频、动画、动作设计、游戏、网络、应用程序、音乐视频等等。

[![Jamiroquai in a room with moving floor.](img/9329d07e9327a206dab2f7d1d2f72030.png)](https://i.giphy.com/media/VkTB7OIqEqTba/giphy.gif)

## 网页动画 API (WAAPI)

允许网站同步和定时来制作 DOM 元素动画。这个 API 将时间模型和动画模型结合在一起，这样浏览器就有了一个通用的动画语言。

将动画从 CSS 转换到 WAAPI 的一些困难。避免伪元素动画，关注动画的组件/元素级别。通过测试不同的演示和状态，了解动画如何影响它所应用到的元素。应用时保持组件完整性的静态动画不需要使用 WAAPI，如果它不会改变太多，就保留在 CSS 中。WAAPI 需要一个 polyfill 来实现新的特性，但是你可以在 Chrome 和 FireFox 中使用第一个实现。以后的特性在 polyfill 中，非常值得一试。

你想考虑 WAAPI 的原因。如果需要用随机/生成的值动态更新动画，这是完美的。你不需要会产生大量代码的臃肿的 Sass 萨斯混合和函数。构建一个动画时间轴，需要基于用户输入的动态响应。WAAPI 仍然利用 CSS 性能优化的好处。您可以在 JS 中处理动画，而无需在样式表中静态设置动画，没有 JS 或更改/更新样式表就无法更新样式表。活在未来的今天。

## WAAPI 动画方法

您可以开始使用的第一种方法是`Element.animate()`。我们来分解一下关键帧和选项:`element.animate(keyframes, options)`。`keyframes`可以是一个对象或对象键值对数组，用于动画元素样式。

`element.animate([arrayFrames] || {objectFrame}, {options})`方法创建一个新的`Animation`对象实例来构建动画所需的帧。帧根据它们在索引顺序中的位置进行定位`[0%, 100%] || [0%, 33%, 66%, 100%]`您可以在帧中定义`offset`来定位时间轴中的帧。

CSS 动画:

```
.class-thing {
  transform: scale(1, 1);
  animation: 4s infinite squash-stretch; 
}

@keyframes squash-stretch {
  92% {
    transform: scale(1, 1);
  }
  94% {
    transform: scale(1.75, 0.5);
  }
  100% {
    transform: scale(1, 1);
  }
} 
```

网页动画 API 动画:

```
element.animate([{
   transform: ‘scale(1, 1)’
 }, {
   transform: ‘scale(1, 1)’,
   offset: 0.92
 }, {
   transform: ‘scale(1.75, 0.5)’,
   offset: 0.94
 }, {
   transform: ‘scale(1, 1)’,
   offset: 1
 }], {
   duration: 4000,
   iterations: Infinity
 }); 
```

将动画的开始部分从在大型产品中可能无法正确级联的 CSS 部分移到 JavaScript 帮助中，以独特和组件化的方式在 web/应用程序中进行动作设计。需要注意的一件事是，WAAPI 的第一次实现需要使用相同的框架样式，这个问题在 polyfill 和下一次实现中得到解决。

这可能会抛出一个错误！ACK！

```
element.animate([{
   transform: ‘scale(1, 1)’,
   //The backgroundColor isn't in any other frame and will throw an error with WAAPI, careful!
   backgroundColor: 'red'
 }, {
   transform: ‘scale(1, 1)’,
   offset: 0.92
 }, {
   transform: ‘scale(1.75, 0.5)’,
   offset: 0.94
 }, {
   transform: ‘scale(1, 1)’,
   offset: 1
 }], {
   duration: 4000,
   iterations: Infinity
 }); 
```

。动画选项

查看 MDN 或 W3C 规范的`animate`方法选项，版本 1 目前在一些浏览器中可用，但建议使用 polyfill 以获得更好的功能支持。我将指出第一次实现中选项的一些问题，这些问题将在未来的更新和当前的 polyfill 中得到解决。建造后不能使用动画选项，但是你可以使用`element.animate(frames, options).effect.timing`。在 Chrome 和 FF 中，Delay 对`1000`和`'1000'`的评估是相同的，但是 duration 只接受整数。

## 多个动画

使用 JS 动态构建多个动画。您可以在示例中看到，延迟是为了错开动画而构建的，当用户单击图像时，延迟会更新，它会将动画重置为开始。更新动画帧时，它们不会覆盖整个帧，而是覆盖属性。

[https://codepen.io/brianmontanaweb/embed/NAEyKy?height=600&default-tab=result&embed-version=2](https://codepen.io/brianmontanaweb/embed/NAEyKy?height=600&default-tab=result&embed-version=2)

图像从侧面飞来，当动画结束时，一个类被移除，你可以控制图像节点列表上的时间，同时动态地重置或更新选项和帧！

感谢您的阅读，如果有什么我应该更新的，请告诉我。我将探索 WAAPI 更多:D
来源/来源:

蕾切尔·纳伯斯— [爱丽丝在网络动画世界](http://codepen.io/collection/bpEza/)
瓦尔·海德— [迪斯尼的经典动画原理能教给网页设计师什么](https://www.fastcodesign.com/3055811/what-disneys-classic-animation-principles-can-teach-us-about-great-web-design)
丹尼尔·C·威尔逊— [动画简介](http://danielcwilson.com/blog/2015/07/animations-intro/)
网络动画 API — [网络动画-js](https://github.com/web-animations/web-animations-js)
W3C — [网络动画规范](https://drafts.csswg.org/web-animations/)