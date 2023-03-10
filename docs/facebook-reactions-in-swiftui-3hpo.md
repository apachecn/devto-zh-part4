# SwiftUI 中的脸书反应

> 原文：<https://dev.to/fabiogiolito/facebook-reactions-in-swiftui-3hpo>

60 行左右。我从来没想到你能用这么几行字完成所有的工作。那些过渡和可中断的动画？

我花了几周时间跟踪 WWDC 研究 Swift UI。起初我认为这是非常基础的，你不能做太多的定制工作。但是当你从零开始构建组件时，你会发现它是多么的灵活。

这是睡前 1 小时搭建的原型。我醒来时发现它在 Twitter 上爆炸了(至少对于我的平均参与度来说)。

[![Animated gif showing a prototype of a Facebook Reactions implementation over XCode](img/653cb6545e13993b248316e7369bc365.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2t6UWb6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lwov22le0re5wym71lf4.gif)

这里是代码，如果你知道一些关于 SwiftUI 和编程的事情，应该是可以理解的。但是这里有一个概述:

UI 由状态控制。我是不是把手指从喜欢按钮上拽下来了？然后显示/隐藏反应。选择哪个反应？把那个放大一点。

那就只是改变那些状态的问题了。当我开始拖动时，将 isDragging 更改为 true。然后根据我拖动的距离，计算选择了哪个反应。

编码:
[https://gist . github . com/fabiogiolto/424 E1 d 68766 fc 054 ad 4 BF 6 FD 3506 e9 c 3](https://gist.github.com/fabiogiolito/424e1d68766fc054ad4bf6fd3506e9c3)

推特:
[https://twitter.com/fabiogiolito/status/1142226669471748096](https://twitter.com/fabiogiolito/status/1142226669471748096)