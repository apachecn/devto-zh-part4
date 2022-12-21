# 将 CSS 网格中的重叠内容居中

> 原文：<https://dev.to/bennypowers/centring-overlapping-content-with-css-grid-3hn4>

所以你已经有了一个图像，你想在它上面放置一些内容。

```
<figure id="beshalach">
  <a href="/beshalach-1520/"><img src=img/beshalach-15-20.jpg" alt="Exodus 15:20" /></a>
  <figcaption><a href="/beshalach-1520/">Exodus 15:20</a></figcaption>
</figure> 
```

Enter fullscreen mode Exit fullscreen mode

在过去，你可能会对我们的老朋友绝对定位:

```
figure {
  position: relative;
}

figcaption {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好。`git commit -m "Job's done" && git push`。也许我们可以做得更好一点。使用 CSS grid 我们可以达到同样的效果，但是它给我的印象是更具声明性的语法。

```
figure {
  display: grid;
  place-items: center;
  grid-template-areas: 'image'
}

figure > * {
  grid-area: image;
} 
```

Enter fullscreen mode Exit fullscreen mode

根据这些规则，我们定义了一个 1 乘 1 的网格，其中有一个名为“images”的区域，所有内容都可以放入其中。建立之后，source order 就开始在图像上绘制标题。不需要位置、z 索引等。

这有几个优点:

1.  是**宣示性的**。这些风格描述了预期的结果，而不是开发人员认为应该采取的步骤。
2.  是**可读的**。我不知道你怎么想，但未来的我可能不会看到`top: 50%; left: 50%; transform: translate(-50%, -50%);`并立即想到“集中！”
3.  Flex 和 Grid 有一个更简单的心智模型来处理最黑暗的黑色艺术[堆叠环境](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context)。

所以下次你需要重叠内容的时候，考虑用`grid`代替`position`。