# CSS 战#4 -起伏跌宕

> 原文：<https://dev.to/pheeria/css-battle-4-ups-n-downs-46i5>

这张很漂亮。三次形状都一样，只是方向和位置不同。让我们把它作为第一个解决方案。

## 1。绝对位置

我们创建三个分部，并给它们任意的 id`a`、`b`和`c`。如果你不想这么做，你可以选择`:nth-of-type()`选择器。然后我们塑造我们的形象，不管它叫什么。我们给它一个宽度和高度，一个背景颜色，定位它，并环绕矩形的下侧。之后，对于每个给定的 div，我们给出一个合适的`top`和`left`位置，对于第一个形状，我们也将其旋转 180 度指向上方。

```
<div id="a"></div>
<div id="b"></div>
<div id="c"></div>
<style>
  * {
    background: #62306D;
  }
  div {
    width: 100px;
    height: 100px;
    background: #F7EC7D;
    position: absolute;
    border-radius: 0 0 50px 50px;
  }
  #a {
    top: 50px;
    left: 150px;
    transform: rotate(180deg);
  }
  #b {
    top: 150px;
    left: 50px;
  }
  #c {
    top: 150px;
    left: 250px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。使用边距/填充

Div `o`，代表*外*，在这里作为一个包装器，将所有的空间隔断。这样，我们可以避免绝对定位，从而将[保留在正常的文档流](https://zellwk.com/blog/css-positions/)中。在这个外盒中，我们将三个 div 设计为`inline-block`(注意它们是如何*粘合在一起的*以避免[不需要的空间](https://css-tricks.com/fighting-the-space-between-inline-block-elements/))。最后，通过使用边距来实现它们的定位。

```
<div id="o">
  <div id="a"></div><div id="b"></div><div id="c"></div>
</div>
<style>
  body {
    margin: 0;
    background: #62306D;
  }
  #o {
    width: 300px;
    height: 200px;
    margin: 50px;
  }
  #o > div {
    width: 100px;
    height: 100px;
    background: #F7EC7D;
    display: inline-block;
  }
  #a {
    margin: 0 100px;
    border-radius: 50px 50px 0 0;
  }
  #b, #c {
    border-radius: 0 0 50px 50px;
  }
  #c {
    margin-left: 100px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

同一解决方案还有另一种变化，我们不需要创建一个外部盒子。相反，我们使用`body`元素的`padding`来限制内容区域。通过这种方式，我们实现了与上面代码相同的效果，而不需要创建额外的 div。

```
<div id="a"></div><div id="b"></div><div id="c"></div>
<style>
  body {
    margin: 0;
    padding: 50px;
    background: #62306D;
  }
  div {
    width: 100px;
    height: 100px;
    background: #F7EC7D;
    display: inline-block;
  }
  #a {
    margin: 0 100px;
    border-radius: 50px 50px 0 0;
  }
  #b, #c {
    border-radius: 0 0 50px 50px;
  }
  #c {
    margin-left: 100px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 3。伪元素

这里我们使用了[伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/::after)。你知道，我们有三个数字，每个元素可以有`::before`和`::after`，正好是三个。因此，我们只需要一个分部，我们借助边距来放置它。我们给它的`border-radius`指向上方和背景颜色。它也应该是`inline-block`，这种风格也应该适用于它的伪元素。
然后我们使用 CSS 的*层叠*能力来覆盖伪元素的`border-radius`。值得注意的是，需要设置`content`属性，即使只是作为一个空字符串。

```
<div></div>
<style>
  body {
    background: #62306D;
  }
  div, div::before, div::after {
    margin: 42px 142px;
    width: 100px;
    height: 100px;
    background: #F7EC7D;
    display: inline-block;
    border-radius: 50px 50px 0 0;
  }
  div::before, div::after {
    content: "";
    border-radius: 0 0 50px 50px;
    margin: 100px -100px;
  }
  div::after {
    margin: -200px 100px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode