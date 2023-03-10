# 将垂直滚动转移到↔️一侧

> 原文：<https://dev.to/samthor/divert-vertical-scroll-to-the-side-3id0>

今天让我们来试试一篇合并的博客文章:一个技巧是当你有水平滚动区域时帮助你的用户四处走动(为了效果，或者别的什么)，另一个技巧是在网上导航。

## 垂直到水平

如果你有一个非常棒的 UI 功能，只是碰巧可以水平滚动:想想一个很长的产品列表，它会一直持续下去，用户可以使用触控板或滚动条来访问它。

但是如果你的用户有一个普通的只有垂直滚轮的鼠标，他们如何在那个区域导航呢？🤷

我们实际上可以重定向 catch`wheel`事件，只需根据`deltaY`(滚轮的上下运动)修改`scrollLeft`属性(控制*水平*导航)。我们还需要`deltaX`，否则我们会阻止真正的水平滚动。看一看:

```
// handle up/down scrollwheel on the scroller, as most folks don't have horizontal scroll
scrollableElement.addEventListener('wheel', (ev) => {
  ev.preventDefault();  // stop scrolling in another direction
  scrollableElement.scrollLeft += (ev.deltaY + ev.deltaX);
}); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/samthor/embed/jjroEa?height=600&default-tab=result&embed-version=2](https://codepen.io/samthor/embed/jjroEa?height=600&default-tab=result&embed-version=2)

这是我们在 [🧝精灵建造者](https://santatracker.google.com/elfbuilder.html)游戏中用在圣诞老人追踪器上的东西。

## 提示(又名今天我学到的)

如果你在页面上垂直滚动(几乎在任何浏览器中)，但按住 *shift* ，你实际上是水平滚动。

# 谢谢！

今天就到这里吧！

Eighteen