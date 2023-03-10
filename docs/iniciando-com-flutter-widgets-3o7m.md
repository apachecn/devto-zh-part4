# 从浮点开始:小部件

> 原文：<https://dev.to/flutterfans/iniciando-com-flutter-widgets-3o7m>

## 导言

#### 什么是浮？

[float 是一个用于开发谷歌创建的跨平台应用程序和 web 应用程序的 SDK。](https://dev.to/flutter/o-que-e-o-flutter-e-dart-e25)

我开始学习浮动，从来没有用组件编程，所以几个概念对我来说很陌生，主要是构件的概念和使用。

读取浮点文档时，我遇到了以下构件定义:

> 小部件描述了在给定当前配置和状态的情况下，它们的视图应该是什么样子。当窗口小部件的状态改变时，该窗口小部件重建其描述，该框架与先前的描述不同，以便确定底层呈现树中从一个状态转换到下一个状态所需的最小改变。

在自由翻译中:

> 小部件是描述视图应如何显示的结构，具体取决于您的配置和当前状态。当小部件的状态发生更改时，将重新构建其说明(框架会将其与上一说明的差异进行比较)，以确定从一种状态切换到另一种状态时，对渲染树的最小更改。

代码段包含构件的概念，但不包含有关如何使用它构建浮动应用程序的信息。

本文的目的是明确小部件的概念，无论它们是不变的还是可变的。并通过它们来了解如何构建浮动应用程序。

# 渲染树

首先要理解的是，浮动应用程序中的每个可视项目都是彼此内部的小部件:
如果我想要集中文本，我会将文本小部件放置在中心内。甚至，您的应用程序本身也将成为一个小部件，其中包含其他小部件。

绞俋窒凳璃腔袨怓载蜊奀ㄛ坳蔚笭陔汜伧啭窒凳璃ㄛ捞坳腔伞珋攫﹝。

# 一成不变

调用是一个只构造一次的对象，没有如何重建的信息。所以它不会影响你的渲染树。

不可变或无状态构件的一个示例是文本。它是一个小部件，它接收 string/text 参数并装载可视对象，该值将转换为标签并显示在屏幕上。

```
Text( 'Esse é um texto imutável',); 
```

Enter fullscreen mode Exit fullscreen mode

它本身从不单独更改其文本，如果它是一次构建的，则无法使用新的文本值重建。

# 可变

调用“”是具有 State 对象的对象。此对象表示小部件的当前状态，您可以根据时间或应用程序中发生的事件对其进行修改。此状态知道如何重建，这会影响其中的所有小部件，即其渲染树。

如果我有一个应用程序，单击该应用程序中的按钮可以更改构件的值，则必须使用 setState 方法指示该构件的状态已更改。所以我们强迫物体重新构造，也强迫它里面的所有小部件，包括不变的小部件。如果您想进一步了解 setState 如何影响应用程序性能，请参见“文档”。

你可以通过例子更好地理解:

```
class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}

class _CounterState extends State<Counter> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      child: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.display1,
            ),
            FloatingActionButton(
              onPressed: _incrementCounter,
              tooltip: 'Increment',
              child: Icon(Icons.add),
            )
          ],
        ),
      ),
      color: Colors.white,
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有一个 StatefulWidget，它具有 int 变量、Text 对象和按钮。单击“我的”按钮时，我的编号为 1，表示使用 setState 更改了小部件的状态。然后他用我的新号码强迫我的 Text 对象并在其中重建。

# 结论

最后，构建浮点应用程序是使用 StatefulWidgets 强制重建 statelesswidgets。

*我感谢好友腓力·佩雷拉。*