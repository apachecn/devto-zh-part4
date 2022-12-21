# 《着色器之书》中的栅栏问题

> 原文：<https://dev.to/vbaknation/the-fence-problem-from-the-book-of-shaders-22jm>

我正在看 Patricio Gonzalez Vivo 的《阴影之书》(The Book of Shaders)，这是我见过的最好的(有吸引力、有挑战性、有趣的)技术书籍之一。艾伦·唐尼的[思考系列](https://greenteapress.com/wp/)对于 Python 和统计学习就像这本书对于计算机图形学一样。至少感觉是这样的。

我在第五章:

> 这一章可以命名为“宫城先生的篱笆课”...
> 
> ...下面的代码结构将成为我们的栅栏:

```
#ifdef GL_ES
precision mediump float;
#endif

uniform vec2 u_resolution;
uniform vec2 u_mouse;
uniform float u_time;

// Plot a line on Y using a value between 0.0-1.0
float plot(vec2 st, float pct){
  return  smoothstep( pct-0.02, pct, st.y) -
          smoothstep( pct, pct+0.02, st.y);
}

void main() {
    vec2 st = gl_FragCoord.xy/u_resolution;

    float y = st.x;

    vec3 color = vec3(y);

    // Plot a line
    float pct = plot(st,y);
    color = (1.0-pct)*color+pct*vec3(0.0,1.0,0.0);

    gl_FragColor = vec4(color,1.0);
} 
```

我花了第一天的时间盯着这些代码玩。我(立即)没有从中得到太多。

## 分心或脑子消化信息

我走了一段弯路，完成了托比·沙奇曼的工作室的非常简短和紧凑的教程，这些教程令人惊叹。如果你没有尝试过，并且对学习复杂计算机图形的噪声构造感兴趣，你绝对应该去看看他的视频。他的教学法是围绕空间学习而建立的，空间学习对于学习计算机图形学来说是一个深刻的概念，并且无疑影响了我对这个领域的思考。

## 回到栅栏上

在 Shadershop 赢了几场后，我准备接受栅栏代码。我意识到我只是在脑子里试图找出栅栏密码，这就是为什么我碰到了路障，或者说，栅栏。Shadershop 如此有效的原因是它允许视觉学习者使用空间媒介来控制代码并查看结果图形的效果。我选择把这当成建议。

我在围栏代码中添加了一些制服，并使用 [WebGL Fundamentals 的样板文件](https://webglfundamentals.org/webgl/lessons/webgl-boilerplate.html)来设置 WebGL 上下文和滑块，以控制着色器函数制服变量。

下面是我为展示结果而创建的代码笔。移动滑块时，请注意(非常小的)预格式化文本的变化，以帮助您更好地可视化该过程:

[https://codepen.io/vishalbakshi/embed/GVWqmG?height=600&default-tab=result&embed-version=2](https://codepen.io/vishalbakshi/embed/GVWqmG?height=600&default-tab=result&embed-version=2)

我很想就统一变量值和图形结果之间的关系得出一些结论，但是我越是尝试不同的值组合，这些关系就越广泛。所以我还没准备好从这一章开始。

从这次经历中最大的收获是:像素的颜色值和位置是分开处理的。我知道，我知道，着色器函数本身是独立的，所以它们是分开处理的，但我对这个概念感到不舒服。

所以我在片段着色器的脚本中添加了一个`u_count` uniform 变量，它决定了画布上绘制了多少个三角形(总共有 24 个三角形)。更改该计数不会影响画布的颜色。因此，片段着色器正在生成各种颜色的贴图，该贴图应用于画布的所有像素，而不仅仅是顶点着色器中指定的像素。这让我产生了两个概念上的困惑，我以前无法完全想象:动画几乎完全可以用片段着色器来制作——随着时间的推移改变像素的颜色，而不是随着时间的推移“移动”(更像是改变可见性)像素(就像你用手绘动画一样)可以产生类似或相同的结果，着色器脚本由并行处理的 GPU 运行(即所有像素同时运行)。

感谢阅读！

## 跟着我

[YouTube](https://www.youtube.com/channel/UCqvA0CVB3QR3TLpGVzkD35g)
T3】insta gramT5[Twitter](https://twitter.com/vbaknation)