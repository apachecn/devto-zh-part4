# 使用 background-size 将图像置入合适的框中，使用 object-fit 非常简单

> 原文：<https://dev.to/tokuda109/background-size-object-fit-k00>

虽然知道 CSS 的属性中有`object-fit`，但是觉得很方便，所以调查了一下。

在项目以多列布局在一览上的情况下，为了不由于图像尺寸的差异而出现高低差异，大多将显示图像的部分的框的尺寸固定。
对于固定的框，在保持图像的长宽比的同时，框尺寸不会出现间隙，即使出现间隙，也会伸缩显示图像的没有剪切部分。

## 使用后台大小的方法

到目前为止，我们用了以下这样的标注来显示。 如果是用`img`要素一般表示的方法，就必须对用 JavaScript 下载的图像进行尺寸调整。

```
<ul class="items">
  <li class="item">
    <a>
      <img
        class="item__img"
        src="<base64エンコードした1pxの透明なダミー画像>"
        style="background-image: url(<画像 URL>);" />
    </a>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

```
.item__img {
  background-position: center center;
  background-repeat: no-repeat;
  background-size: contain;
  height: 200px;
  width: 200px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，在 HTML 中，在`img`要素的`src`属性中显示以 base64 编码的 1px 的透明虚拟图像，在`style`属性中指定`background-image`。 其次通过在 CSS 中指定`background-size: contain;`，对 img 要素指定的背景图像进行尺寸的伸缩，所以作为手续费太多了。

## 使用 object-fit 的方法

使用`object-fit`的情况下，会变得更简单。

```
<ul class="items">
  <li class="item">
    <a>
      <img
        class="item__img"
        src="<画像 URL>" />
    </a>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

```
.item__img {
  height: 200px;
  object-fit: contain;
  width: 200px;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 img 要素的 CSS 的类中声明`object-fit: contain;`即可。
通过写这样的宣言，可以指定 img 要素的内容(在这种情况下为图像)是如何相对于框架伸缩显示的。

## 总结

虽然属性非常方便，但是不能使用 Internet Explorer，所以如果要采用的话需要确认目标浏览器。

另外，这次使用的代码在[Github](https://github.com/tokuda109/playground/tree/master/object-fit) 上公开。

## Reference

*   [物体拟合](https://developer.mozilla.org/ja/docs/Web/CSS/object-fit)