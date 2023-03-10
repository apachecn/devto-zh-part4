# CSS 战#10 -隐形的灵魂

> 原文：<https://dev.to/pheeria/css-battle-10-cloaked-spirits-4e8e>

孩子们，时代不会变得更好。数字十是一个特殊的数字。这是第一个两位数。这是一个著名的足球球衣号码。事实上，它是如此的著名，以至于土耳其人会把 numara 上的某个东西叫做*，数字 10，以表示钦佩。至于挑战，将会很有趣。*

## 1。Div 方式

让我们从定义我们需要什么开始。三根柱子，挤在一起，上面是圆形。因此，我们将划分为三个部分，每个部分中都有一个部分。为了将它们区分开来，我们称外部 div 为`column`，内部 div 为`circle`。这是标记。

```
<div class="column">
    <div class="circle"></div>
</div>
<div class="column">
    <div class="circle"></div>
</div>
<div class="column">
    <div class="circle"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都需要在紫色背景上。为了给我们的形状创建一个框架，让我们通过样式化`body`标签来限制它们。注意，我使用了零填充和`font-size: 0`。由于声明填充，我们不能使用`*`选择器。

```
body {
  padding: 42px 30px 30px 42px;
  font-size: 0;
  background: #62306d;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们切换到列。一列应该有 100 像素的宽度，150 像素的高度，内联块显示，否则，为什么我们要设置字体大小为零？所有这些都在一个漂亮的黄色背景上，并带有圆圈。

```
.column {
  width: 100px;
  height: 150px;
  display: inline-block;
  background: #f7ec7d;
  border-radius: 50px 50px 0 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码还不能完全工作，因为规则总是有例外。中间的形状几乎就像向我们展示一个中指，它需要一些特殊的处理。有趣的是`:nth-child()`不是零索引的。尽管如此，我们调整了高度，有效地将侧柱向下推。

```
.column:nth-child(2) {
  height: 250px;
} 
```

Enter fullscreen mode Exit fullscreen mode

在每一列中，我们已经定义了一个分割来表示一个圆。我们将宽度和高度设置为相同的 60 个像素，将边框半径设置为 50%，得到一个圆形。我们添加一个 20 像素的边框和背景色。

```
.circle {
  width: 60px;
  height: 60px;
  border: 20px solid #aa445f;
  background: #e38f66;
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们特殊的雪花，中间那一列，又要调整了。这一次我们改变背景和边框颜色。

```
.column:nth-child(2) .circle {
  background: #aa445f;
  border-color: #e38f66;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您不知道的话，如果省略的话，边框颜色和边框阴影默认为元素的`color`属性。所以，我们可以用下面的方式重写我们的颜色。

```
.circle {
  width: 60px;
  height: 60px;
  border: 20px solid;
  border-radius: 50%;
  color: #aa445f;
  background: #e38f66;
}
.column:nth-child(2) .circle {
  color: #e38f66;
  background: #aa445f;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是了。你可以开始玩 CSS，看看如何打破东西导致意想不到的形状。例如，尝试删除列的高度或圆形的边框。是不是很好玩？这是完整的解决方案。

```
<div class="column"><div class="circle"></div></div>
<div class="column"><div class="circle"></div></div>
<div class="column"><div class="circle"></div></div>

<style>
  body {
    padding: 42px 30px 30px 42px;
    font-size: 0;
    background: #62306D;
  }
  .column {
    width: 100px;
    height: 150px;
    display: inline-block;
    background: #F7EC7D;
    border-radius: 50px 50px 0 0;
  }
  .column:nth-child(2) {
    height: 250px;
  }
  .circle {
    width: 60px;
    height: 60px;
    border: 20px solid #AA445F;
    background: #E38F66;
    border-radius: 50%;
  }
  .column:nth-child(2) .circle {
    background: #AA445F;
    border-color: #E38F66;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。伪方式

我们能换一种方式做它吗？当然，我们可以。但在此之前，你知道这个`(_>_)`是什么意思吗？一个漂亮的笑脸，不是吗？如果我在另一种情况下给你看呢？

```
val ordered = seq.sort(_>_) 
```

Enter fullscreen mode Exit fullscreen mode

意思是，按升序排序。对于具有类似 C 语言背景的开发人员来说，Scala 有一些非常不寻常的符号。
你知道这个`* + *`是什么意思吗？显然，这是一只猫头鹰。猫头鹰选择器。在 [Every Layout](https://every-layout.dev/layouts/stack/) 上读到它之后，我被这个简单而强大的 idea - elements 位置吸引住了，它不应该是它自己的问题，而应该是它的父元素。以前，owl 选择器可以用来以一种优雅的方式解决这个问题。现在我们有了一个灵活的盒子。让我们看看我们的解决方案是什么样的。

我们仍然有三栏，但是这次让我们使用段落而不是分割。我知道这在语义上是不正确的，但是你也不应该使用 CSS 来绘制图形。我们选择`p`是因为它的`display: block`属性。对于中间的元素，我们给它一个 id 以区别于后面的元素。请注意，如果属性值由一个单词组成，我们就不需要引号了。这是标记。

```
<p></p>
<p id=a></p>
<p></p> 
```

Enter fullscreen mode Exit fullscreen mode

要设计一个列的样式，我们可以重用之前解决方案中的相同代码。不过，有两个不同之处。我使用视口高度和视口宽度作为测量单位，以更加*软编码*，这是为了允许一些适应性。而且我们也不需要`display: inline-block`。

```
p {
  width: 25vw;
  height: 50vh;
  background: #f7ec7d;
  border-radius: 12.5vw 12.5vw 0 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的中指柱在高度上是重新设计的。我们通过定位一个定义了`id`属性的段落来选择它。

```
p[id] {
  height: 62.5vw;
} 
```

Enter fullscreen mode Exit fullscreen mode

为了重新创建圆圈，这次让我们使用伪类。除了添加了`content: ""`和`display: block`来使我们的调整工作之外，上面的代码都是一样的。当然，我们覆盖了中间列的背景和边框颜色。

```
p:after {
  content: "";
  display: block;
  width: 20vh;
  height: 20vh;
  border: 5vw solid #aa445f;
  background: #e38f66;
  border-radius: 50%;
}
p[id]:after {
  background: #aa445f;
  border-color: #e38f66;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该看到一个白色背景，左边有两列。这不是我们想要的。但这就是我们得到的，因为我们没有放置任何元素。缺失的部分来了。

```
body {
  height: calc(100vh + 8px);
  background: #62306d;
  display: flex;
  justify-content: center;
  align-items: flex-end;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们指定了一个灵活的显示方式，将所有列水平对齐到中央，并将项目与屏幕底部对齐。如果你知道我为什么要在整个高度上增加八个像素，并且这是我目前为止找到的唯一可行的方法，请告诉我。我也很好奇。

所以，整个解决方案来了。

```
<p></p>
<p id=a></p>
<p></p>

<style>
body {
  height: calc(100vh + 8px);
  background: #62306d;
  display: flex;
  justify-content: center;
  align-items: flex-end;
}
p {
  width: 25vw;
  height: 50vh;
  background: #f7ec7d;
  border-radius: 12.5vw 12.5vw 0 0;
}
p[id] {
  height: 62.5vw;
}
p:after {
  content: "";
  display: block;
  width: 20vh;
  height: 20vh;
  border: 5vw solid #aa445f;
  background: #e38f66;
  border-radius: 50%;
}
p[id]:after {
  background: #aa445f;
  border-color: #e38f66;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 3。阴影方式

我已经提到过，如果说我从解决这些挑战中学到了什么，那就是`box-shadow`。我也将尝试在这里应用它。

### 小心！不要试图在生产中重复这一点！

这个想法是将三个 div 相对于整个页面进行绝对定位。div 被命名为 l 代表左边，c 代表中间，r 代表右边。

```
<div id=l></div><div id=c></div><div id=r></div> 
```

Enter fullscreen mode Exit fullscreen mode

这些 div 将代表圆圈。他们有相同的宽度和高度，背景和边界，一切都和上面一样。造成差异的是`position: fixed`和指定的`top: 134px`和`left: 50px`参数。

```
div {
  position: fixed;
  top: 134px;
  left: 50px;
  width: 20vh;
  height: 20vh;
  background: #e38f66;
  border: 5vw solid #aa445f;
  border-radius: 50%;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们稍微调整了一下中间和右边元素的位置和颜色。

```
#c {
  top: 34px;
  left: 150px;
  background: #aa445f;
  border-color: #e38f66;
}
#r {
  left: 250px;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们给我们的`body`赋予颜色。

```
* {
  background: #62306d;
  color: #f7ec7d;
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会奇怪，我们为什么要用`color` property？毕竟，我们这里没有任何文本。`color`属性被作为边框和阴影的默认值，这就是原因。
这是解决方案的瑰宝，强大的`box-shadow`。

```
box-shadow: 0 10px, 0 20px, 0 30px, 0 40px, 0 50px, 0 60px, 0 70px, 0 80px,
  0 90px, 0 100px, 0 105px, 0 110px, 0 120px, 0 130px, 0 135px 0 5px, 0 190px 0
    30px; 
```

Enter fullscreen mode Exit fullscreen mode

一圈又一圈，一圈又一圈，一直堆到底部。最初，十个像素的距离就足够了，但当达到左右圆圈的水平时，我们会看到一条细线，列不接触。所以我们把最后两个圆做得更大来掩盖这个缺陷。

这是整个解决方案。

```
<div id=l></div><div id=c></div><div id=r></div>

<style>
* {
  background: #62306d;
  color: #F7EC7D;
}
div {
  position: fixed;
  top: 134px;
  left: 50px;
  width: 20vh;
  height: 20vh;
  background: #e38f66;
  border: 5vw solid #aa445f;
  border-radius: 50%;
  box-shadow:0 10px, 0 20px, 0 30px, 0 40px, 0 50px, 0 60px, 0 70px, 0 80px, 0 90px, 0 100px, 0 105px, 0 110px, 0 120px, 0 130px, 0 135px 0 5px, 0 190px 0 30px;
}
#c {
  top: 34px;
  left: 150px;
  background: #aa445f;
  border-color: #e38f66;
}
#r {
  left: 250px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

写这个花了很多时间。我希望你喜欢它。祝您愉快！