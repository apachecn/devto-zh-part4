# CSS 战#12 -摆动的小胡子

> 原文：<https://dev.to/pheeria/css-battle-12-wiggly-moustache-5b1k>

说到 CSS，我还是回到 00 后。你看过 4 月 1 日的 Stack Overflow 吗？还是你还记得 MySpace？闪光和有毒的颜色？嗯，那是我的水平。如果我有那么老，我甚至会用`<blink></blink>`。所以，当我发现 CSS Battle 的时候，我觉得应该把它带到战斗之外。
我应该从根本上提高 CSS 的水平。基于引导的页面已发布。新的 JS 框架出现了。
但我没有。还没有。

## 1。初始解

这就是我如何让最后一战达到 100%的。这里的主要思想是使用伪类来产生圆头。

```
<div id="l"></div>
<div id="c"></div>
<div id="r"></div>
<style>
  body{
    background: #F5D6B4;
    margin: 0;
    position: relative;
  }
  #l, #r, #c{
    width: 60px;
    height: 30px;
    border: 20px solid #D86F45;
    position: absolute;
    top: 50%;
    left: 50%;
  }
  #l, #r{
    border-bottom-left-radius: 50px;
    border-bottom-right-radius: 50px;
    border-top: 0;
  }
  #l{
    margin: 0 0 0 -130px;
  }
  #r{
    margin: 0 0 0 30px;
  }
  #c{
    margin: -50px 0 0 -50px;
    border-top-left-radius: 50px;
    border-top-right-radius: 50px;
    border-bottom: 0;
  }
  #l:before, #r:after{
    content: "";
    width: 20px;
    height: 10px;
    position: absolute;
    background: #D86F45;
    border-top-right-radius: 20px;
    border-top-left-radius: 20px;
    top: -10px;
  }
  #l:before{
    left: -20px;
  }
  #r:after{
    left: 60px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。边界

让我们用四个值将所有的边界统一为一个`border-radius`。

```
border-bottom-left-radius: 50px;
border-bottom-right-radius: 50px; 
```

Enter fullscreen mode Exit fullscreen mode

这变成了一个单一的值。

```
border-radius: 0 0 50px 50px; 
```

Enter fullscreen mode Exit fullscreen mode

## 3。配置

我们可以将所有内容定位为固定的，删除拥有 50%的`top`和`left`属性，并用更精确的边距替换它们。

```
<div id="l"></div>
<div id="c"></div>
<div id="r"></div>
<style>
  *{
    background: #F5D6B4;
    position: fixed;
  }
  #l, #r, #c{
    width: 60px;
    height: 30px;
    border: 20px solid #D86F45;
  }
  #l, #r{
    border-radius: 0 0 50px 50px;
    border-top: 0;
  }
  #l{
    margin: 142px 0 0 62px;
  }
  #r{
    margin: 142px 0 0 222px;
  }
  #c{
    margin: 92px 0 0 142px;
    border-radius: 50px 50px 0 0;
    border-bottom: 0;
  }
  #l:before, #r:after{
    content: "";
    width: 20px;
    height: 10px;
    position: absolute;
    background: #D86F45;
    border-radius: 20px 20px 0 0;
    top: -10px;
  }
  #l:before{
    left: -20px;
  }
  #r:after{
    left: 60px;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我可以把它留给你。你知道该怎么做。我希望你和我一样喜欢。我学习了大量的 CSS，并在日常工作中使用。重点是一起学习。由于我在网上找到的内容通常只是皮毛，我决定贡献自己的一份力量。
说到它的竞技部分，你可以[问别人](https://spectrum.chat/css-battle?tab=posts)， [lint 你的代码](https://stylelint.io/user-guide/rules/declaration-block-trailing-semicolon/)和 [minify](https://www.minifier.org/) 它。但是我更推荐看一个[定位教程](https://www.youtube.com/playlist?list=PL4cUxeGkcC9hudKGi5o5UiWuTAGbxiLTh)。当然，永远不要忘记- [少即是多](http://www.folklore.org/StoryView.py?story=Negative_2000_Lines_Of_Code.txt)。

这就是了。目前我是 21780 名玩家中的第 72 名。我解决了 44 场战斗中的 37 场。我的句柄是 [@pheeria](https://dev.to/pheeria) 。我想，待会儿见？