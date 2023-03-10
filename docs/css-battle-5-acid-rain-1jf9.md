# CSS 战役#5 -酸雨

> 原文：<https://dev.to/pheeria/css-battle-5-acid-rain-1jf9>

嗯，[这个](https://cssbattle.dev/play/5)挺容易的。实际上，我首先用天真的方法解决了这个问题，后来我才明白，我也可以使用`box-shadow`。但是我们已经用过两次`box-shadow`了。所以再补充一点。

## 1。z 指数

最简单的方法是使用外部 div，以边距为中心，或者如果不想污染标记，使用填充来达到相同的目的。放在里面的每个`div`都应该有相同的宽度和高度，有趣的部分来了，`position: absolute;`。这不仅使相对于外部`div`的边缘定位更加容易，而且还使用了 [z-index](https://www.w3schools.com/cssref/pr_pos_z-index.asp) 。我们将任务中的形状堆积起来，并设置`border-radius`。

```
<div id="o">
  <div id="a"></div>
  <div id="b"></div>
  <div id="c"></div>
</div>
<style>
  body {
    margin: 0;
    background: #0B2429;
  }
  #o {
    margin: 30px 80px;
    width: 250px;
    height: 240px;
  }
  #o div {
    width: 120px;
    height: 120px;
    position: absolute;
  }
  #a {
    z-index: 1;
    margin: 0px 120px;
    background: #F3AC3C;
    border-radius: 60px;
  }
  #b {
    z-index: 2;
    margin: 60px 60px;
    background: #998235;
    border-radius: 60px 0px 60px 60px;
  }
  #c {
    z-index: 3;
    margin: 120px 0px;
    background: #F3AC3C;
    border-radius: 60px 0px 60px 60px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。方框阴影

这个灵感来自第一场战斗的最短解决方案。我们做两个分割，其中一个是圆形。第二个在一边有一个方形边框和一个方框阴影。不需要 z-index，因为每个下一个框形阴影都放置在前一个的后面。五个值，两个强制值用于 X 和 Y 偏移、模糊、扩散和颜色。[现在你应该已经意识到你该做什么了](https://www.youtube.com/watch?v=bx1Bh8ZvH84)。这确实是解决方案的主要部分，其他如层叠、覆盖和`nth-of-type()`只不过是试图以一种“智能”的方式减少代码量。

```
<div></div>
<div></div>

<style>
* {
  background: #0B2429;
}

div {
  width: 120px;
  height: 120px;
  margin: 30px 192px;
  border-radius: 60px;
  background: #F3AC3C;
}

div:nth-of-type(2) {
  margin: -30px 72px;
  box-shadow: 60px -60px #998235;
  border-radius: 60px 0 60px 60px;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 3。六十

只是为了尝试一些新的东西，并注意到所有值与 60 之间的关系，我决定为什么不。我们不需要 SASS，我们需要的少得多——[自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)。我们定义一个属性`--sixty`并放置到通配符中，这样它就可以通过层叠来使用。然后我们做一些`calc()`魔法，剩下的和上面一样。

```
<div></div>
<div></div>

<style>
* {
  background: #0B2429;
  --sixty: 60px;
}

div {
  width: calc(2 * var(--sixty));
  height: calc(2 * var(--sixty));
  margin: calc(0.5 * var(--sixty)) calc(3 * var(--sixty) + 0.2 * var(--sixty));
  border-radius: var(--sixty);
  background: #F3AC3C;
}

div:nth-of-type(2) {
  margin: calc(-0.5 * var(--sixty)) calc(var(--sixty) + 0.2 * var(--sixty));
  box-shadow: var(--sixty) calc(-1 * var(--sixty)) #998235;
  border-radius: var(--sixty) 0 var(--sixty) var(--sixty);
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

你更喜欢这些解决方案中的哪一个？你还知道其他人吗？