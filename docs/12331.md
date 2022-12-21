# 颤振阻滞:处理导航的正确方法

> 原文：<https://dev.to/pedromassango/flutter-bloc-a-better-way-to-handle-navigation-4fig>

如果你一直在使用 Flutter 和 BLoC，也许你有必要导航到你的 BLoc 中的一个新页面，因为我们知道我们没有一个 **`BuildContext`** 来使用 **`Navigator.of()`** 函数进行导航。让我告诉你怎么做...

注意:我在这里假设你已经知道 BLoC，如果不是只关注这些链接:[https://felangel.github.io/bloc/](https://felangel.github.io/bloc/)

为了对一个集团的状态做出反应，我们有时会在我们的小部件之间做一些事情: