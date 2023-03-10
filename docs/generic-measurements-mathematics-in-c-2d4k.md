# C#中的一般测量数学

> 原文：<https://dev.to/zacharypatten/generic-measurements-mathematics-in-c-2d4k>

我想分享一个我正在开发的模式，它可以让用 C#进行科学测量变得更容易。

通常当人们对科学主题进行编码时，他们会对所有的值使用“double”。这意味着位置、加速度、速度等都存储为数字标量，它们可以彼此相加(导致错误)，如果不追溯到变量的初始赋值，你就不知道这些值是什么单位。

有多少次你看到有人将角度测量值传入系统 trig 函数(通常以弧度为单位进行角度测量)？这不会引发异常。这将导致在应用程序中很难检测到的糟糕的计算(您通常必须手动调试和观察数学)。

我们能做些什么来帮助用代码进行科学计算？

1.  我们可以使用类型安全来防止测量值的误用。
2.  我们可以跟踪每个值的单位来执行自动的单位转换。

这是我的项目中“角度”类型的当前版本:

[![](img/32f476875844f4a3beeb1d9194dfb3da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zZSl_ihY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/grjff5wq44cfnjdiqsp3.png)

可能的度量单位在 enum 中定义，度量值是一个通用类型，所以您可以使用任何类型:double、float、decimal、BigRational 等。单位之间的转换因子作为枚举值的属性实现，并缓存在静态转换表中，以便快速查找。

但是代码的实际用法是什么样的呢？下面是一个使用示例:

[![](img/5fa9a1eadb508b8746382e5209b7139a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zFlukXhi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oggwlnpshx8e6wx0uegg.png)

定义角度时，需要提供度量单位。从此，数学过程中所有必要的单位转换都将自动处理。在这种情况下，我们将度数测量值添加到转数测量值，从而自动将转数测量值转换为度数[0.5 转= 180 度]，结果为[90 + 180 = 270 度]。

其他测量类型可以实现为它们自己的类型:长度、速度、加速度、力等。这将给你类型安全，这样你就不能误用测量。例如:

[![](img/3ddd8aa9ee3d27aa84ffe627cc005b22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pcKZz6uL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4aq3vqucleh7keyw1qbs.png)

如果我们试图增加一个角度的长度，代码将无法编译。这对你的代码来说是一个很好的额外的有效性层(有助于防止错误)。

有效的度量类型之间的操作呢？简单，特定类型之间定义了运算符。此示例显示了如何直接创建速度值，或者通过将长度值除以时间值来创建速度值:

[![](img/7e14ff1ba74b9bb434ffc172d2bf6e5b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SjuIYzNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htlus15t0h4bx8ajgn5r.png)

速度不同于速度。速度被定义为包括方向和大小。如果你想表示速度，你可以在矢量中用速度。这里有一个例子:

[![](img/bc917eeec4426d18ecc764ea76322b90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_CoI5Cp0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/45opfmfubvdp1881sa75.png)

C#是一种类型安全语言...我是如何对泛型类型进行数学运算的？运行时编译！使用泛型类型的运行时编译允许您打破类型安全性，并假设必要的成员存在。示例:

[![](img/77d074e4e213f193d8122547aefd651d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nKumDfYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hyc4uj2a7kj4hsv22ebx.png)

这只是触及了代码的表面。如果你想了解更多关于它是如何实现的和/或自己尝试一下，请查看这个项目(它是开源的):[https://github.com/ZacharyPatten/Towel](https://github.com/ZacharyPatten/Towel)

谢了。希望你学到了一些东西。