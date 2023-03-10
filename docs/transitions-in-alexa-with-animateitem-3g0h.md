# 带有动画的 Alexa 中的过渡

> 原文：<https://dev.to/arjun_g/transitions-in-alexa-with-animateitem-3g0h>

过渡是简单的动画，提供视觉反馈，并提高用户界面的愉悦度。通过简单地组合不透明度、移动、缩放和旋转，我们可以创建各种类型的过渡。

如果你是转型新手，你可以从 uimovement.com 那里获得一些灵感。由于网站上的动画是为网络或手机制作的，并不是所有的都可以翻译成 alexa 动画，但你可以从中获得一些想法。

注意:一定要确保过渡简短而微妙。

[https://www.youtube.com/embed/DOrp9iRWxLU](https://www.youtube.com/embed/DOrp9iRWxLU)

本文将详细介绍如何使用 [AnimateItem](https://developer.amazon.com/docs/alexa-presentation-language/apl-standard-commands.html#animate_item_command) 创建一些过渡。如果你熟悉 CSS 动画，那么 AnimateItem 的工作方式完全相同，只是有一些限制。

## 淡出

为了创建一个淡入效果，我们所要做的就是在一段特定的时间内逐渐增加组件的不透明度，从 0 到 1。

```
{  "type":  "AnimateItem",  "duration":  1000,  "value":  [  {  "property":  "opacity",  "from":  0,  "to":  1  }  ]  } 
```

[![](img/b8136275dc96a5d5f055fa328276383a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DxTIjbfh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4yq7ir55l2lcgua4dk8g.gif)

## 淡出

淡出类似于淡入，但方向相反。这里我们逐渐减少不透明度从 1 到 0。

```
{  "type":  "AnimateItem",  "duration":  1000,  "value":  [  {  "property":  "opacity",  "from":  1,  "to":  0  }  ]  } 
```

[![](img/4281d0910b14c34d03a855026374f244.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uonIB77l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vkcatenqbo3gy1hnilij.gif)

## FadeInUp

这里不透明度需要从 0 变为 1，组件需要从下往上移动。所以我们操作不透明度和 Y 轴平移

```
{  "type":  "AnimateItem",  "duration":  1000,  "value":  [  {  "property":  "opacity",  "from":  0,  "to":  1  },  {  "property":  "transform",  "from":  [  {  "translateY":  "100%"  }  ],  "to":  [  {  "translateY":  "0"  }  ]  }  ]  } 
```

[![](img/6e163c5bdeb542c3eeee7db4fb2aa676.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IOHc7Mvt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vh9sakncuhn9p1rifc4o.gif)

## zoomindow

对于这个过渡，我们操纵不透明度，在 X 和 Y 轴上缩放，在 Y 轴上平移。这涉及到按顺序执行的多个动画项目。你会在这里看到一个额外的缓动功能，而不是解释它做什么，我会显示一个有和没有缓动效果的并排比较。

```
{  "type":  "Sequential",  "commands":  [  {  "type":  "AnimateItem",  "duration":  600,  "easing":  "cubic-bezier(0.55, 0.055, 0.675, 0.19)",  "value":  [  {  "property":  "opacity",  "from":  0,  "to":  1  },  {  "property":  "transform",  "from":  [  {  "scaleX":  0.1,  "scaleY":  0.1  },  {  "translateY":  "-100vh"  }  ],  "to":  [  {  "scaleX":  0.475,  "scaleY":  0.475  },  {  "translateY":  "60px"  }  ]  }  ]  },  {  "type":  "AnimateItem",  "duration":  400,  "easing":  "cubic-bezier(0.175, 0.885, 0.32, 1)",  "value":  [  {  "property":  "transform",  "from":  [  {  "scaleX":  0.475,  "scaleY":  0.475  },  {  "translateY":  "60px"  }  ],  "to":  [  {  "scaleX":  1,  "scaleY":  1  },  {  "translateY":  "0"  }  ]  }  ]  }  ]  } 
```

[![](img/3b20f30c0abc13cfa1972694729bc7b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PYUJFvda--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f5ainikgz90mf4kia0ng.gif)

添加缓动功能可以增强过渡效果。AnimateItem 支持线性和立方贝兹尔函数。检查[easings.net](https://easings.net)以了解可以用三次贝塞尔曲线生成的缓和类型以及它如何实际影响过渡。

## 结论

类似于上面的例子，你可以创建不同变化的过渡。我创建了一个包含一些转换的报告，你可以用在你的技能中[https://github.com/arjun-g/apl-transitions](https://github.com/arjun-g/apl-transitions)。关于如何使用它的说明可以在回购中找到。淡入淡出！！！！！