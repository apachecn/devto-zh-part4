# CSS 战#7 -多叶小径

> 原文：<https://dev.to/pheeria/css-battle-7-leafy-trail-45im>

这里是[第七个](https://cssbattle.dev/play/7)。一半的挑战都在后面。这是第一个重复使用前一个任务颜色的任务，准确的说是第五个[。不管怎样，我真的很喜欢战斗中使用的颜色组合，希望有一天颜色会很酷。](https://dev.to/pheeria/css-battle-5-acid-rain-1jf9)

## 1。z 指数

第一种解决方案依赖于`divs`的精确放置。首先，我们用`width`、`height`和`border-radius`来描述一个公共的`div`，然后，我们使用`position: absolute`来允许定位，而不考虑相邻元素。这里的`absolute`可以安全地替换为`fixed`以节省一些字符，如果你喜欢的话。现在我们再次使用`z-index`来堆叠元素。要知道，数字越高，前面的元素就越多。因为我们已经用`body`上的`padding: 75px`定义了内容框，我们唯一的动作就是在左边界附近玩耍。

```
<div id="a"></div>
<div id="b"></div>
<div id="c"></div>

<style>
  body {
    margin: 0;
    background: #0B2429;
    padding: 75px;
  }
  div {
    width: 150px;
    height: 150px;
    border-radius: 100px 0;
    position: absolute;
  }
  #a {
    background: #1A4341;
    z-index: 1;
  }
  #b {
    background: #998235;
    z-index: 2;
    margin-left: 50px;
  }
  #c {
    background: #F3AC3C;
    z-index: 3;
    margin-left: 100px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。方框阴影

如果有人问，我从 CSS 之战中学到了什么，我可能会用“盒子阴影”来回答。这种单一的技术在整个挑战中不断出现，经常导致较短的解决方案。对我个人来说，这些解决方案似乎更符合习惯。
这里，我们再次使用方框阴影。这里唯一的问题是，每个后续的阴影都放置在前一个阴影的后面，所以为了获得想要的外观，我们应该从最右边的叶子开始。
注意一些值是如何用`vh`和`vw`定义的——那是为了保存战斗角色。最终，`50vh`比`150px`少了一个字符，在我们的例子中，重复这个动作 4 次，我们节省了 4 个字符。

```
<div></div>

<style>
* {
    background: #0B2429;
}

div {
    background: #F3AC3C;
    width: 50vh;
    height: 50vh;
    border-radius: 25vw 0;
    margin: 75px 167px;
    box-shadow: -50px 0 #998235, -25vw 0 #1A4341;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

你更喜欢这些解决方案中的哪一个？你还知道其他人吗？