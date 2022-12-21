# 快速眼动的美丽

> 原文：<https://dev.to/valeversa/the-beauty-of-rem-123a>

##### 如何只用两个媒体查询就创建一个完全响应的网页。

在开发我们的 web 应用程序时，我们的团队很快意识到需要一个能够适应各种浏览器大小的高度响应的布局。尽管我们的平台几乎完全在桌面上使用，但我们仍然需要找到一个足够灵活的解决方案，既能在大屏幕上工作，也能在非常小的屏幕上工作(例如，平板电脑、智能手机和非常旧的桌面屏幕)。由于我们布局的复杂性——包括许多表格、按钮和模态——我们很清楚处理媒体查询将是一项艰巨的任务。在寻找替代解决方案时，我们偶然发现了通过根据浏览器视窗宽度缩放网页上的每个组件来创建真正响应的布局的可能性: [vw](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Values_and_units#Numeric_values) 。

```
html { font-size: 4vw; } 
```

这种方法的概念非常简单:为正在处理的 HTML 元素指定一个特定的字体大小(例如，font-size: 4vw)，然后使用 REM 单位定义其中每个元素的大小。

```
.container {
  width: 20rem;
  padding: 2rem;
  margin: 2rem auto;
} 
```

这样，由于容器依赖于父级的字体大小，所以它会根据屏幕宽度放大或缩小。更多例子:你的页面的标题将会有 rem 字体大小，以及填充和边距，你的段落也是如此。这两个元素都根据窗口的宽度来调整大小。

```
h1 {
  font-size: 2rem;
  margin: 0;
  text-align: center;
}

p {
  font-size: .8rem;
} 
```

“随处使用 rem”规则有一个例外:您可以继续对不应该根据屏幕宽度调整大小的元素使用像素。例如，您仍然希望使用像素来定义 div 的边框，因为按比例放大会导致边框或按钮变粗。

```
.container {
  border: 1px solid black;
  border-radius: 16px;
  width: 20rem;
  padding: 2rem;
  margin: 2rem auto;
} 
```

##### 我们还需要媒体查询吗？

答案是肯定的。具体来说，你只需要其中的两个。你需要定义一个断点，在这个断点上你的组件不会变得更小，在另一个断点上你的组件将停止按比例增加，并且无论屏幕变得多大都保持相同的大小。这两个媒体查询只影响 HTML 字体大小元素，但是下面的所有元素都会神奇地缩放。

```
html {
  font-size: 3vw;
  font-family: sans-serif;
}

@media only screen and (max-width: 550px) {
  html { font-size: 12px; }
}

@media only screen and (min-width: 1020px) {
  html { font-size: 24px; }
} 
```

同样，您可以使用像素来确保最小和最大可能的字体大小单位是固定的。

点击[此处](https://codepen.io/versavale/full/VoemoZ)查看完整演示页面。

你有什么评论或者只是给我们发邮件，联系一下[瓦伦蒂娜](https://www.twitter.com/valeversa)或者[汤姆](//mailto:info@tomrothe.de)。