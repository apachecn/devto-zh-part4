# 响应设计的柔性盒布局(下)

> 原文：<https://dev.to/gvinod1991/flex-box-layout-for-responsive-designs-part-2-27o6>

到目前为止，我们已经介绍了 flex-direction、justify-content、align-items 和 flex-wrap 属性，以及它对于设计/开发响应式布局的价值。flex-box 中有更多的属性，比如 flex 项目的顺序和 align-self。

所以让我们开始吧

## 订单

有时，对于开发布局的确切需求，颠倒容器的行和列顺序是不够的。在这种情况下，我们可以使用 order 属性对容器中的项目进行排序。默认情况下，容器中任何项目的顺序都是 0。我们可以使用正整数或负整数作为顺序值。Order 属性只能应用于单个项目。

```
<ul class="flex-box">
    <li class="home">Home</li>
    <li>About</li>
    <li>Portfolio</li>
    <li>Contact</li>
</ul>

.flex-box {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
}
.flex-box :first-child {
    order: 1;
}
.flex-box :last-child {
    order: -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

排序最高的项目比排序最低的项目显示得晚

在上面的例子中,“家”将比其他 3 个显示在最后,“联系人”将首先显示，因为它的顺序比其他人低

具有相同顺序的弹性项目将以与来源顺序相同的顺序显示，例如一个接一个

因此，如果您有 4 件物品，它们的顺序值分别为 3、2、2 和 0 集，它们的显示顺序将是第 4、第 2、第 3，然后是第 1。

第三项显示在第二项之后，因为它具有相同的订单值，并且在来源订单中位于第三项之后。

看看下面的代码笔链接，以获得更清晰的图片。

[https://codepen.io/GodtiVinod/embed/bXgXjB/?height=600&default-tab=result&embed-version=2](https://codepen.io/GodtiVinod/embed/bXgXjB/?height=600&default-tab=result&embed-version=2)

## 自我对齐

该属性也可以应用于单个项目。它将接受与 align-items 相同的值及其特定项的值。考虑下面的 CSS 代码和前面例子的 Html 代码。

```
.flex-box {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
}
.flex-box :first-child {
    align-self: flex-start;
} 
```

Enter fullscreen mode Exit fullscreen mode

“Home”将显示在该行的开头。因为在上面的代码中应用了 align-self 属性和值 flex-start

要更清楚地了解自我校准，请参考下面的代码笔

[https://codepen.io/GodtiVinod/embed/WVpeRM/?height=600&default-tab=result&embed-version=2](https://codepen.io/GodtiVinod/embed/WVpeRM/?height=600&default-tab=result&embed-version=2)