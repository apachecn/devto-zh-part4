# 用网格区域制作粘性栏。

> 原文：<https://dev.to/dillionmegida/making-sticky-column-with-grid-area-5eph>

我正在做一个项目，我实现了 CSS 的网格属性。我把屏幕分成两个格子；左侧网格用于导航(用于同一页面内的部分),右侧网格用于内容。

P:S:这不是一个使用网格区域的教程。在此了解更多关于网格区域的信息

这是我的 CSS 代码:

```
.main {
  display:grid;
  grid-template-areas: "nav content";
  grid-template-columns: 2fr 10fr;
  grid-gap:30px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望网页的导航部分不滚动，而内容部分保持可滚动。我对导航部分的 position 属性应用了 fixed 值。

```
.nav {
  position: fixed;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如所料，我的导航部分固定在屏幕上，但是我发现导航部分为自己生成属性。这是属性位置的固定值和绝对值的特征。与从父元素继承一些属性的相对值不同，这些值开发自己的属性。

为了解决这个问题，我应用了粘性值。该值从父元素继承属性，同时仍然拥有自己的小属性；

```
.nav {
  grid-area: nav;
  border-right:5px solid #ddd;
  grid-template-rows: repeat(12, 1fr);
  /* Position of the navigation section */
  position:sticky;
 /* Ensuring its attached to the top of the page */
  top:0;
 /* Ensuring the height of to equal to the viewport's height */
  height:100vh;
} 
```

Enter fullscreen mode Exit fullscreen mode

页（page 的缩写）s:为了使页面能够适应更小的屏幕，需要使用媒体查询进行进一步的调整

粘性价值还有许多其他用途，但是有了这种理解，我相信你可以在设计时灵活运用。

希望这篇文章对某人有所帮助。随意分享。

感谢阅读。