# 如何覆盖你的背景图片

> 原文：<https://dev.to/selbekk/how-to-overlay-your-background-images-59le>

我今天学到了两个不错的小技巧，我想我应该写一篇关于它们的小文章。

## 挑战

通常，我们会在背景图片上放置文字。一个例子可能是一个英雄区，或以上的内容，基本上任何营销网站这些天。

有时，我们需要提高文本和背景图像之间的对比度。当然，我们可以只改变图像本身——但有时这不是一个选项。

## 旧而笨重的方式👴

有几种方法可以解决这个问题，但这是我以前学会的方法。我通常创建以下 HTML 结构:

```
<div class="image-box">
  <div 
    class="image-box__background" 
    style="--image-url: url('some-image.jpg')"
  ></div>
  <div class="image-box__overlay"></div>
  <div class="image-box__content">
    <h1>Buy our product</h1>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后我将使`image-box`相对定位，所有的子元素都绝对定位在里面，并按照我想要的顺序堆叠它们。

> ### 这是什么——语法？
> 
> 注意，我们是通过一个叫做 CSS 自定义属性的东西传入图像 url 的。你可能也知道它们是 CSS 变量。这是在 HTML 和 CSS 之间传递值的一种方式。你可以在 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) 上阅读更多关于 CSS 自定义属性的内容。

对其进行样式化所需的样式如下:

```
/* 
The container box is relative so we can position stuff inside of it 
*/
.image-box {
  position: relative;
}

/*
The background and overlay need to be absolutely positioned
*/
.image-box__background,
.image-box__overlay {
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
}

/* 
The background image div sizes and positions the background itself.
It's also at the bottom-most position in our "div stack" (z-index 1)

We set the image url via a CSS custom property, that's set via the style attribute in our HTML
*/
.image-box__background {
  background: var(--image-url) center center no-repeat;
  background-size: cover;

  z-index: 1
}

/* 
The overlay div is just a colored element with some opacity.
It's above the background image in our stack, so it appears to 
darken the image 
*/
.image-box__overlay {
  background: rgba(0, 0, 0, 0.5);

  z-index: 2;
}

/* 
The content div is at the top of our stack. 
We'd probably add some padding or flexbox properties here as well, 
to place the content appropriately
*/
.image-box__content {
  position: relative;

  z-index: 3;

  /* Finally, style and place the content */
  color: white;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

代码很多，但是运行得很好。下面是实现它的代码笔:

[https://codepen.io/selbekk/embed/ewGYOz?height=600&default-tab=result&embed-version=2](https://codepen.io/selbekk/embed/ewGYOz?height=600&default-tab=result&embed-version=2)

## 新酷的方式！😎

那是很多代码。事实证明，事情不一定是那样的。

让我们把 HTML 修改成这样:

```
<div class="image-box" style="--image-url(some-image.jpg)">
  <h1>Buy our product</h1>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点简单，对不对？让我们也实现 CSS:

```
.image-box {

  /* Here's the trick */
  background: linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)) , var(--image-url) center center;
  background-size: cover;

  /* Here's the same styles we applied to our content-div earlier */
  color: white;
  min-height: 50vh;
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是实现它的代码笔:

[https://codepen.io/selbekk/embed/orGNMa?height=600&default-tab=result&embed-version=2](https://codepen.io/selbekk/embed/orGNMa?height=600&default-tab=result&embed-version=2)

### 这里发生了什么？

您可能已经注意到，我们现在指定了两个背景图像:

```
.image-box {
  background-image: 
    linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), 
    var(--image-url);
} 
```

Enter fullscreen mode Exit fullscreen mode

第一个背景图像是从相同颜色到相同颜色的线性渐变。这种颜色是半透明的黑色，作为你的第二个背景的覆盖。

就这样了。如果你觉得聪明，你也可以把你想要的变暗的数量作为第二个 css 变量来传递，以便进一步定制。或者使用一个实际的梯度，使您的图像更流行一点。

## 利用方框阴影来达到同样的效果

原来，CSS 有几种方法可以将“元内容”放在背景图片上。另一种实现相同目的的方法是使用具有巨大扩展值的`box-shadow`属性和`inset`设置。

```
.image-box {

  /* Here's the trick */
  box-shadow: inset 0 0 0 100vw rgba(0,0,0,0.5);

  /* Basic background styles */
  background: var(--image-url) center center no-repeat;
  background-size: cover;

  /* Here's the same styles we applied to our content-div earlier */
  color: white;
  min-height: 50vh;
  display: flex;
  align-items: center;
  justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个实现代码笔:

[https://codepen.io/selbekk/embed/ydzmEv?height=600&default-tab=result&embed-version=2](https://codepen.io/selbekk/embed/ydzmEv?height=600&default-tab=result&embed-version=2)

这也给了你一些我们可以动画的东西(注意当你悬停图像时会发生什么)，这可能是一个很好的 UX 乐趣。但是，你对渐变没有相同的控制，所以你应该选择哪种技术取决于你的设计环境。人们还注意到，这种技术的性能可能不太好，尤其是在低端设备上。记住，在决定你的技术时，也要考虑到这一点。

感谢您参加我的开发讲座。