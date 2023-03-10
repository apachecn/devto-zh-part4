# TIL:标题侧控制表格标题的位置

> 原文：<https://dev.to/stefanjudis/til-caption-side-controls-the-position-of-a-table-caption-2l9d>

今天我看到[发了一条 CSS-Tricks](https://twitter.com/css/status/1163538653353861124) 的推文。它共享 CSS 属性`caption-side`的行为。以前没见过`caption-side`。

当您处理 HTML 表格时，可以使用属性。表格可能包含[和`caption`元素](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/caption)。关于`caption`元素的线索是它们**必须是表**中的第一个元素。

该规则使它们通常出现在表格的顶部区域，并且没有给出不同位置的选项。

```
<table>
  <caption>Populations of cities</caption>
  <thead>
    <tr>
      <th>City</th>
      <th>Population</th>
    </tr>
  </thead>
  <tr>
    <td>Berlin</td>
    <td>1</td>
  </tr>
  <tr>
    <td>New York City</td>
    <td>2</td>
  </tr>
</table> 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，您可以使用`caption-side` CSS 属性将`caption`元素的位置移动到其他地方。不幸的是，今天您可以安全使用的值只有`top`和`bottom`。`right`、`left`等少数可用，但不支持跨浏览器。

下面几行 CSS 可以将`caption`元素的位置改变到底部，即使它是表格中的第一个元素！🎉

```
table {
  /* moves the caption to the bottom */
  caption-side: bottom;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Visualisation of the caption CSS property including two tables: one with the caption in the top region (caption-side: top) and one with the caption in the bottom region (caption-side: bottom)](img/92df0c12c516889100a8708ac003f163.png)](//images.ctfassets.net/f20lfrunubsq/2spIrSxYxKPf1yAgxnfjq8/ebc0960919cff2e0aabcdebcb896357e/Screenshot_2019-08-29_at_19.37.11.png)

如果你想了解更多关于`caption-side`财产的信息，请浏览[至 MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/caption-side) 或 [CSS-Tricks](https://css-tricks.com/almanac/properties/c/caption-side/) 。

此外，如果你想看它的运行，你可以看看这个代码笔。