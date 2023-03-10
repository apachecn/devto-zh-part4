# 子网格在这里！(差不多)

> 原文：<https://dev.to/ananyaneogi/subgrids-are-here-almost-49eg>

Firefox Nightly 支持子网格(CSS Grid Level 2 规范的一部分)!尽管这还没有在任何浏览器中发布，但是我们可以在夜间版本中测试它。在试用了几个小时后，我真的很兴奋地想到，一旦它被广泛发布，它将带来的所有可能性。

要开始试验它，你需要先在每晚启用该功能-

*   在浏览器中转至`about:config`
*   搜索子网格
*   切换`layout.css.grid-template-subgrid-value.enabled`并将`subgrid`设置为真。

[![](img/ddfb765fcf8e75ea867c11b5e73075d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mxQTHplb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/od1ya45jd63hjqqgs8d4.png)

# 什么是子网格？

> 本身是网格项的网格容器可以将其行和列的定义推迟到其父网格容器，使其成为子网格。在这种情况下，子网格的网格项参与调整父网格容器的网格大小，从而允许两个网格的内容对齐。

这听起来可能有点令人困惑。让我们看一个例子。
这里我们有一个`grid-wrapper`，其中有 3 个直接子元素`elem1`、`elem2`、`elem3`。

```
<div class="grid-wrapper">
  <div class="elem elem1">Element 1</div>
  <div class="elem elem2">Element 2</div>
  <div class="elem elem3">
    <h2>Element 4</h2>
    <p>Element 4 paragraph 1</p>
    <p>Element 4 paragraph 2</p>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们把这些放在网格上。

```
.grid-wrapper {
  display: grid;
  grid-template-columns: 2fr 1fr 4fr;
  gap: 30px;
}

.elem1 {
  grid-column: 1;
  grid-row: 1;
}

.elem2 {
  grid-column: 2 / 4;
  grid-row: 1;
}

.elem3 {
  grid-column: 1 / -1;
  grid-row: 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/ananyaneogi/embed/QXLePy?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/QXLePy?height=600&default-tab=result&embed-version=2)

我们注意到`grid-wrapper`的所有直接子节点都很好地放置在网格上，但是`.elem3`的内部子节点却没有。
如果现在我们希望`p`元素也遵循与其父元素相同的跟踪大小，因为我们不想显式地定位它们，该怎么办？这就是`subgrid`的用武之地。

将现有的`.elem3`替换为以下内容，并观察其运行情况。

```
.elem3 {
  grid-column: 1 / -1;
  grid-row: 2;
  display: grid;
  grid-template-columns: subgrid;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/ananyaneogi/embed/rEBXXz?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/rEBXXz?height=600&default-tab=result&embed-version=2)

简而言之，`subgrid`是`grid-template-columns`和`grid-template-rows`属性的新值。这指示`grid-template-columns` / `grid-template-rows`属性使用在父网格上定义的轨迹作为内部网格使用的轨迹大小和数量，而无需显式声明。

这只是一个基本的例子。通过网格和子网格的结合，我们可以非常容易地创建复杂而有趣的布局。

[https://codepen.io/ananyaneogi/embed/ProoGj?height=600&default-tab=result&embed-version=2](https://codepen.io/ananyaneogi/embed/ProoGj?height=600&default-tab=result&embed-version=2)

# 参考文献

1.  [W3C 规范](https://www.w3.org/TR/css-grid-2/)
2.  [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Subgrid)
3.  Rachel Andrew 在 CSSconf EU 2019 上的精彩演讲:[你好子网格！](https://www.youtube.com/watch?v=vxOj7CaWiPU)