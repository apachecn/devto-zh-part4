# Clojure CLR 中的 OpenGL 游戏(Mono/。Net) -第 3 部分(处理输入)

> 原文：<https://dev.to/metacritical/create-an-opengl-game-in-clojure-clr-mono-net-part3-handling-input-2leo>

## 处理键盘输入和游戏循环。

大多数 3D 应用程序，如游戏，运行在一个游戏循环中，其中有一系列事件修改游戏和 OpenGL 状态，以在屏幕上绘制东西。一个这样的重要事件是用户输入事件。OpenTK `GameWindow`类有
这样一个名为`OnUpdateFrame`的方法，顾名思义，它在每次渲染帧时被触发。

现在它没有做任何事情，因为它在父类中，我们正在处理一个`GameWindow`的实例。

我们真正需要做的是继承`GameWindow`并覆盖`OnUpdateFrame`方法，并在窗口事件发生时捕获它们。

为了做到这一点，Clojure 中主要有 3 种机制来处理继承。`gen-class`、`proxy`和`reify`。但是由于我们处理的是继承类的实例`reify`不能使用。

因为我的目标是保持教程的大部分功能，所以我选择的工具是`proxy`。

因此，让我们修改我们的`core`名称空间，创建一个代理对象并覆盖`OnUpdateFrame`方法，如下所示: