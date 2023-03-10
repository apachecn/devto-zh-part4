# 让我们用 JavaScript 来构建——使用鼠标事件来激活文本

> 原文：<https://dev.to/justalever/let-s-build-with-javascript-using-mouse-events-to-animate-text-1k82>

在“让我们用 JavaScript 构建”系列的这一期中，我将讨论如何使用鼠标事件来激活文本。

这篇教程很大程度上受到了韦斯·博斯的课程 [JavaScript 30](https://javascript30.com/) 中的一个视频的启发，我强烈推荐这个视频作为 JavaScript 速成课程。

[查看密码本](https://codepen.io/webcrunchblog/pen/aboPYxv)

[https://www.youtube.com/embed/MgF9AajVjUw](https://www.youtube.com/embed/MgF9AajVjUw)

我们将创建一个鼠标悬停时的文本阴影动画。当您将光标移动到包含的 div 上时，由于 JavaScript 事件，文本可以实时显示。我们将针对`mousemove`事件，特别是挂钩到浏览器中定义的相对于光标在屏幕上的位置的坐标。结合一些数学和一些 ES6，我们得到了一个非常酷的小动画效果。

#### 相对链接:

*   [MDN:事件](https://developer.mozilla.org/en-US/docs/Web/Events)
*   [MDN:鼠标移动事件](https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event)
*   [JavaScript30](https://javascript30.com)

#### 到目前为止的级数

*   [让我们构建:使用 JavaScript–DIY 下拉菜单和响应菜单](https://web-crunch.com/lets-build-with-javascript-diy-dropdowns-responsive-menus/)
*   [让我们用 JavaScript 构建——带 Cookies 的广播栏](https://web-crunch.com/lets-build-with-javascript-broadcast-bar-cookies/)
*   [让我们用 JavaScript 构建:粘性导航](https://web-crunch.com/lets-build-with-javascript-sticky-nav/)
*   [让我们构建:用 JavaScript 动态标签](https://web-crunch.com/how-to-create-tabs-with-vanilla-javascript)
*   [让我们构建:用 JavaScript–Modals](https://web-crunch.com/lets-build-with-javascript-how-to-code-modal-vanilla-javascript/)
*   [让我们构建:使用 JavaScript–html 5 视频播放器](https://web-crunch.com/lets-build-with-javascript-html5-video-player/)
*   [让我们构建:用 JavaScript——手风琴](https://web-crunch.com/how-to-create-accordion-vanilla-javascript/)
*   [让我们构建:用 JavaScript 骨架屏幕效果](https://dev.tohow-to-create-skeleton-screen-loading-effect)
*   [如何编写一个非画布菜单——让我们用 JavaScript 来构建吧](https://web-crunch.com/off-canvas-menu)
*   [显示更多–使用 JavaScript 切换显示更少](https://dev.to/justalever/how-to-code-a-show-more--show-less-toggle-with-javascript-3c7m)
*   [如何通过 JavaScript 使用本地存储](https://web-crunch.com/how-to-use-local-storage-with-javascript)
*   [让我们构建:使用 JavaScript–动态复选框](https://web-crunch.com/javascript-dynamic-checkboxes)

帖子[让我们用 JavaScript 构建——用鼠标事件来动画化文本](https://web-crunch.com/javascript-mouse-events-to-animate-text/)最早出现在[的网络社交网站](https://web-crunch.com)上。