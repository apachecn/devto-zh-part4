# CSS 中没有引导或网格的列状布局(第 1 部分)

> 原文：<https://dev.to/shamimahossai13/column-like-layout-in-css-without-bootstrap-or-grids-part-1-2hij>

有没有想过如何在 CSS 中创建列，而不使用网格或者迷宫般的 div 标签中那些讨厌的`col-md-sth`？

主要有两种方法可以做到这一点。我将用例子和场景来解释，因为这是我理解得最好的方式。

场景 1:假设你想以报纸的风格写你的文章。如你所知，报纸上大段大段的连续文本是以专栏而不是段落的形式写的。你怎么可能不费吹灰之力就达到这样的效果？原来我们古老而强大的 CSS 有这样的属性。房产

**列数:**

让我们举一个例子
我想写一篇完全像报纸风格的文章。用 html 构造的主体应该是这样的

```
<section>

<h3>Things that don't make sense</h3>

<div class="col">

Lorem ipsum dolor sit amet, consectetur adipiscing elit, 
sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. 
Sollicitudin tempor id eu nisl nunc mi ipsum faucibus vitae. 
Integer vitae justo eget magna fermentum iaculis eu non.
Rhoncus urna neque viverra justo nec ultrices dui. Risus viverra
adipiscing at in tellus integer feugiat scelerisque.
In hendrerit gravida
rutrum quisque non tellus. Consequat semper viverra nam
libero  justo laoreet sit amet.

</div>

</section> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们用一行 CSS 将这部分文本分成列。这将是

```
.col{
  column-count: 3;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后的输出将会是
[https://codepen.io/Silver1/embed/eYmGjKx?height=600&default-tab=result&embed-version=2](https://codepen.io/Silver1/embed/eYmGjKx?height=600&default-tab=result&embed-version=2)
最好的事情是，如果你点击 Codepen 上的 edit 并尝试调整窗口大小，你会看到它根据浏览器大小进行调整。呜哇！

但是等等，还有更多，你也可以定制你的列，让它们有间距，列规则等等。我将把 w3schools 页面的链接留给您，让您可以更多地使用它

[https://www.w3schools.com/css/css3_multiple_columns.asp](https://www.w3schools.com/css/css3_multiple_columns.asp)

虽然我知道这可能没有一般的网格优雅，但它的简单吸引了我的注意力。通常你可能只需要一个简单的列式布局，使用网格对于那些时候来说有点大材小用。

在下面的评论中让我知道你们喜欢和不喜欢这个 CSS 属性。另外，请继续关注第 2 部分。