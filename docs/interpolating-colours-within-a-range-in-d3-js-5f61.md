# 在 D3.js 中的范围内插值颜色

> 原文：<https://dev.to/aurelio/interpolating-colours-within-a-range-in-d3-js-5f61>

在数据可视化中，颜色是一种强大的视觉暗示。不过，一个好的经验法则是少用它们，只在它们给图表增加一些有意义的东西时使用，而不仅仅是因为它们很漂亮。

使用许多不同颜色的一个好的替代方法是在我们的整个数据集上使用一种或两种不同色度的颜色。这将在视觉上突出显示每个数据点的“重量”差异，而不会用无穷无尽的颜色分散用户的注意力。
D3.js 让实现这个目标变得非常容易，允许我们将颜色代码传递给我们的`ranges`。我们只需要传递两种我们想用作极端情况的颜色代码，就可以了。

```
import { scaleLinear } from 'd3-scale';

const ourScale = scaleLinear()
  .domain([0, 100])
  .range([
     '#63a6d6', // <= lower bound of our color scale
     '#124488'  // <= upper bound of our color scale
  ]); 
```

D3.js 将依次对我们传递给 scale 函数的值进行插值，以返回适当的颜色代码。轻松点。下面是一个正在运行的 JsFiddle，来看看它是如何工作的。

[https://jsfiddle.net/webdevem/v8k047yh/embedded/result,js,html,css//dark](https://jsfiddle.net/webdevem/v8k047yh/embedded/result,js,html,css//dark)

封面图片来自 Markus Spiske，点击此处查看他的作品。

*原载于[我的博客](https://nobitagit.github.io/blog/Interpolating-colors-range-d3js/)T3。*