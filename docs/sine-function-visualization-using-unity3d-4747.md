# 用 Unity3D 实现正弦函数可视化

> 原文：<https://dev.to/avantcontra/sine-function-visualization-using-unity3d-4747>

**【编码德鲁伊】**系列是我的**横向编程**练习笔记，每一部分都围绕一个主题像数学、物理、电子、图形、声音等。，用几种编程语言实现。

* * *

**编码德鲁伊**
部分:数学
章节:三角函数
部分:Unity3D

*在其他部分，我用 [MaxMSP](https://www.patreon.com/posts/sine-function-28131990/) 、 [Python](https://www.patreon.com/posts/sine-function-28238879) 、 [JavaScript(React)](https://www.patreon.com/posts/sine-function-28178275) :* 可视化了三角函数(正弦和单位圆)

[![trig-sine-visualization-reactjs](img/8205adcc4766cd03829a1ec7a26687ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4pGFZPx6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/op2idw4oo1x2rmrze3jq.gif)

这次我用 **Unity3D** 来实现。

在 Python 版本中，使用了数据可视化库 Matplotlib。用 Unity 我只是根据三角函数算法画出来。

首先，让一个点随正弦函数曲线移动。

在场景中放一个球体，让它的`position.x`匀速增加，然后`position.y = sin(position.x)`。

> vector 3 posSine = sphere . local position；
> posSine.x +=速度；
> posSine.y = Mathf。sin(possine . x)；
> sphere . local position = posSine；

[![sine-unity-sineball](img/5bc9ac5790539564c545118fce32b857.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--isrrWTnF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-unity-sineball.gif)

接下来，画出球体沿单位圆运动的轨迹。

注意，这里把单位圆的半径设为 1，把正弦的周期长度设为 2Pi。也就是说，如前几节所示，单位圆与正弦共享一个坐标系。

画圆的公式:

> x = cos(t)；
> y = sin(t)；

这里的参数`t`，实际上是上一步中正弦的`position.x`。

然后向左移动圆心，使单位圆的旋转起点与正弦的起点一致。

> Vector3 posCircle = posSine
> posCircle.x = Mathf。cos(possine . x)-1f；
> posCircle.y = Mathf。sin(possine . x)；
> circle dot . local position = pos circle；

[![sine-unity-circleball](img/2d3f0a5d2f4ce622087bb200665cc6c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NSxYc1hY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-unity-circleball.gif)

结合正弦和单位圆:

[![sine-unity-sinecircleball](img/ca5b25611654eea20d8d777a627b30c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZzp8lNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-unity-sinecircleball.gif)

在之前的文章中，我把曲线的路径画得更清楚，比如 Python:

[![sine python effect](img/3b66ec077894b88ce6fbf9d98968955f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--364cFgM4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-py-effect-0.gif)

我打算和 Unity 一起偷懒。
将`Trail Renderer`直接添加到两个球体上，让它们自己绘制路径轨迹效果。

[![sine-unity-trail0](img/9990edaa7f45e9a98eb7371edb606e76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L7_LRmf2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-unity-trail0.gif)

嗯，默认的线索有点强...

好的，让我们改变它。在 Unity Assets 商店搜索了`trail`后，我找到了一个免费的[良好的跟踪效果](https://assetstore.unity.com/packages/vfx/particles/spells/particle-ribbon-42866):

[![unity-trail](img/b18060857973a497b5da819d97f3d2a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GvP_14Lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assetstore-cdn-china-v1.unitychina.cn/key-image/6669ea2e-1c95-42fc-b061-51ceb257d996.jpg)

安装后的资产有很多影响。然后根据喜好选择一种材料:

[![sine-unity-trail-material](img/f1b018a8932b5c675b9a817298368731.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2phgCfBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://floatcc.intplusplus.org/sine-unity-trail-material.png)

将材质附加到轨迹渲染器。

**最终效果**:

[![sine-unity-croped](img/fce059b23d5dded140bdd86119ead4e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--egHeYy9p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1lwa8c2fi4f0p4pvcp3n.gif)

*在发布的源代码中，我将`Trail Renderer`添加到一个空的`GameObject`中，并随着球体移动。我没有把轨道直接加到球体上。没有什么特别的原因，只是 Unity 新手的练习。*

参考文章:[构建图形可视化数学](https://catlikecoding.com/unity/tutorials/basics/building-a-graph/)

* * *

空谈是廉价的。给我看看代码！

“编码德鲁伊”系列的这个演示是在这里开源的:
[https://github.com/avantcontra/coding-druid](https://github.com/avantcontra/coding-druid)

你可以在我的网站[floatbug.com](https://www.floatbug.com)找到更多资源。
或者你可以**在我的[店里给我买杯咖啡](https://www.patreon.com/avantcontra)**。你的鼓励是我前进的动力！

干杯~

反政府人员

*   网址:[floatbug.com](https://www.floatbug.com)
*   Github: [avantcontra](https://github.com/avantcontra)
*   Facebook:[avantt entra](https://facebook.com/avantcontra)
*   推特: [avantcontra](https://twitter.com/avantcontra)
*   Instagram: [avantcontra](https://www.instagram.com/avantcontra)
*   patreon _ **给我买个提议** xd): [avantcontra](https://www.patreon.com/avantcontra)