# 使用 React 钩子的傅立叶级数可视化

> 原文：<https://dev.to/avantcontra/fourier-series-visualization-using-react-hooks-1he9>

**【编码德鲁伊】**系列是我的**横向编程**练习笔记，每一部分都围绕一个主题像数学、物理、电子、图形、声音等。，用几种编程语言实现。

* * *

**编码德鲁伊**
**部分**:数学
**章节**:傅立叶级数
**章节** : Javascript (React)

在前一章，我分别用 [MaxMSP](https://www.floatbug.com/2019/07/04/trigonometric-function-visualization-using-maxmsp%e2%80%8a-%e2%80%8acoding-druid/) 、 [JavaScript (React)](https://dev.to/avantcontra/sine-function-visualization-using-javascript-react-3b85) 、 [Python](https://dev.to/avantcontra/sine-function-visualization-using-python-2ogo) 和 [Unity](https://dev.to/avantcontra/sine-function-visualization-using-unity3d-4747) 可视化了三角函数(正弦和单位圆):

[![sine-unity-croped](img/8ccf41ea7f98bb1baa8623f932ef5fff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EnieUS0K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-unity-croped.gif)

本章我实现**傅立叶级数**曲线的可视化。
和 **JavaScript 和**这部分开始反应。

### 数学

在数学中，傅立叶级数是由谐波相关的正弦曲线组成的周期函数，通过加权求和来组合。
( [维基百科](https://en.wikipedia.org/wiki/Fourier_series))

并且可以解释为将重复曲线表示为正弦曲线的和。([咕噜咕噜系列(喵)和让图像说话](http://bilimneguzellan.net/en/purrier-series-meow-and-making-images-speak/)

例如，假设有一个方波，它可以解释为正弦波:

[![](img/9dd86226ebb8656fcaeea1d1e2102157.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3B0bpZRJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/Fourier_series_and_transform.gif)

正弦波越多，方波就越精确。

[![](img/864515a093802555cfe75d8ff7e0be05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ejqh5XZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://floatcc.intplusplus.org/474px-Fourier_Series.svg.png)

现在我们来看看傅立叶级数展开的过程。

假设`f(x)`是`x∈[−π,π]`的周期函数，其周期为`2π`。
它的表情:

[![Alt Text](img/a3b1cb5dd247879fcb0680a0393829b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uMJmNNGN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u0hziw8rdcvijzph8sr2.png)

那么它的傅里叶级数的三角表达式(正弦-余弦形式)就像:

[![Alt Text](img/b9bca0c613cca710f4470064fc6e4a6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z8jZcDnT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e6ohbxzdnv7vmsz29ydc.png)

并且$f(x)$的傅立叶系数是:

[![Alt Text](img/4305ab24178f2acdab92d09e468bf356.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--REXWq25C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/reorvbf7aqr4bqy5iao1.png)

**最后**:

[![Alt Text](img/0976081d0ea7532d8957c90444e6a155.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--21i_A_7M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/swi9cek92qchwwsba6ws.png)

*以上内容指[《高等数学——(工科)(下册)](https://www.amazon.com/s?k=9787810824088&i=stripbooks&linkCode=qs)*

*而最初的傅立叶级数定义和详细解释将在[维基百科](https://en.wikipedia.org/wiki/Fourier_series)中找到。*

前四项的长度之和，并显示如何逼近方波:

[![Alt Text](img/47ad4aecd0dcd8055f28c4728a4d8ee3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vkh3v8ct--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vcz8wxkobevzgihvla4n.png)

[![](img/471e5d3d9a31f8e56d48c556bde82aef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aevyhYEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/Fourier_series_square_wave_circles_animation.gif)

### 可视化

现在用 React 可视化这条曲线。
我已经分叉并重构了一个[正弦可视化](https://dev.to/avantcontra/sine-function-visualization-using-javascript-react-3b85):

[![sine-visualization-reactjs](img/9c98c9f31b12078c85aac78547310b9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sIeL9Q90--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/sine-visualization-reactjs.gif)

我将在上述代码的基础上做傅立叶的东西。

用 React 写代码真的很清楚:

[![fs-js-carbon-0](img/afbfe66ef69839b0259b3fe13fdfea71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ldtEGOxZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://floatcc.intplusplus.org/fs-js-carbon-0.png)

[![Alt Text](img/12e6e8167c2f96f9f33051f1c9e5dc99.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dpZnc0eW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x7l2n15zfwa6it1vr4k4.png)

[![fs-js-carbon-1](img/487ff1159cba14fd4ac3e18ef2637416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nqi8Sw72--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://floatcc.intplusplus.org/fs-js-carbon-1.png)

仍然使用 **React 钩子**更新与组件绑定的数据:

[![fs-js-carbon-2](img/1942d0020ee747e8f025a2c28024e66c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SBmzH2fs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://floatcc.intplusplus.org/fs-js-carbon-2.png)

**最终曲线**:

[![fs-js-5](img/7bb8d5452426b0a9e9ddaf7924742839.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpn1Oa1J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://floatcc.intplusplus.org/fs-js-5.gif)

随着 n 的增加，即正弦波的增加，合成波形越来越接近方波。

#### 参考文献

*   [维基百科](https://en.wikipedia.org/wiki/Fourier_series)

*   [咕噜咕噜系列(喵)和让图像说话](http://bilimneguzellan.net/en/purrier-series-meow-and-making-images-speak/)

*   [丹尼尔·希夫曼的编码挑战](https://thecodingtrain.com/CodingChallenges/125-fourier-series.html)

*   [高等数学-(工科)高等数学（工科类)](https://www.amazon.com/s?k=9787810824088&i=stripbooks&linkCode=qs)

*   **[信号&系统(第二版)作者艾伦·v·奥本海默](https://www.amazon.com/Signals-Systems-2nd-Alan-Oppenheim/dp/0138147574)T3】**

*   [[dry goods ]---Fourier series](https://zhuanlan.zhihu.com/p/35679341)

*   **[How to understand Fourier transform? What are the principles of beauty and voice change? Teacher Li Yongle told you](https://www.youtube.com/watch?v=0LuyxzqI3Hk)**

* * *

空谈是廉价的。给我看看代码！

这个演示和**编码德鲁伊**系列是开源的这里:【https://github.com/avantcontra/coding-druid】

你可以在我的网站[floatbug.com](https://www.floatbug.com)找到更多资源。

你可以在我的[店里给我买一杯咖啡。那里有许多文章、补丁、源代码和一些**高级的顾客专用内容**。](https://www.patreon.com/avantcontra)

你也可以在 [Gumroad 商店](https://gumroad.com/avantcontra)获得一些免费补丁/代码。

你的鼓励是我前进的动力！

干杯~

反政府人员

*   网址:[floatbug.com](https://www.floatbug.com)
*   Github: [avantcontra](https://github.com/avantcontra)
*   Facebook:[avantt entra](https://facebook.com/avantcontra)
*   推特: [avantcontra](https://twitter.com/avantcontra)
*   Instagram: [avantcontra](https://www.instagram.com/avantcontra)
*   Gumroad(直接购买代码): [avantcontra](https://gumroad.com/avantcontra)
*   Patreon(高级内容): [avantcontra](https://www.patreon.com/avantcontra)