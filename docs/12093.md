# requestAnimationFrame(具有高分辨率计时，非常精确)和性能。现在聚合填充

> 原文：<https://dev.to/jalbam/requestanimationframe-with-high-resolution-timing-very-precise-and-performance-now-polyfills-20ka>

两个普通的 JavaScript 聚合填充:

我刚刚修改了 Paul Irish 制作的 window . requestanimationframe poly fill，使其能够自动(在可能的情况下)进行高分辨率计时，并稍微提高了性能。就是这里:[https://gist . github . com/jalbam/5fe 05443270 fa 6d 8136238 EC 72 accbc 0](https://gist.github.com/jalbam/5fe05443270fa6d8136238ec72accbc0)

我还将保罗·爱尔兰人制作的[window . performance . now poly fill 与](https://gist.github.com/paulirish/5438650)[亚伦·莱文的](http://%EF%BB%BFgist.github.com/Aldlevine/3f716f447322edbb3671)进行了混合，并修改了一些代码来改进它。就是这里:[https://gist . github . com/jalbam/cc 805 AC 3c Fe 14004 ecdf 323159 ECF 40 e](https://gist.github.com/jalbam/cc805ac3cfe14004ecdf323159ecf40e)

这两种聚合填充既可以单独使用，也可以一起使用，它们会自动检测 web 客户端是否需要它们。只需将它们加载到您的代码中(使用您最喜欢的方式)，如果客户需要它们，polyfills 将被部署和使用。

如果你想一起使用它们，记得首先加载[窗口的代码。现在 polyfill](https://gist.github.com/jalbam/cc805ac3cfe14004ecdf323159ecf40e) 因为[窗口. requestanimationframe poly fill](https://gist.github.com/jalbam/5fe05443270fa6d8136238ec72accbc0)可能需要它。

欢迎任何评论！