# 颤振:一切都是一个部件系列-第 2 部分:组成是关键。

> 原文：<https://dev.to/topeomot/flutter-everything-is-a-widget-series-part-2-composition-is-key-3b6h>

在使用 Flutter 时，你很快会想到的一个主题是，一切都是小部件。本系列旨在帮助初学者理解这个简单而强大的概念，并向他们介绍 Flutter 中的基本小部件。

为了在这个旅程中帮助我们，我仅使用 Flutter 自带的内置小部件构建了一个 Twitter 移动克隆应用。你可以在 https://github.com/topeomot2/twitter-ui-app 找到代码

* * *

每个 Flutter 应用程序都是从调用 main()开始的，main()调用 runApp 命令。runApp 命令只是设置应用程序的根小部件。从这里开始，我们将构建一个以根部件为原点的部件树。

> 在 Flutter 中，小部件是其他小部件和 UI 的构建块。

用于此的技术是合成。您使用小部件组成其他小部件，并且您的 UI(可以被视为一个巨大的小部件)也是由小部件组成的。我们将用两个例子来说明这一点。

### Twitter 移动克隆应用 Widget 树

为了说明这一点，下面是 Twitter Mobile Clone 应用程序的部件树的一部分。

[![](img/f8f021478f25da64404989a388547051.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--peXQtAYb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AQRC0NRVmMrDZxWORfEh8pw.jpeg)

请注意以下事项

*   MyApp 小部件是根小部件。这将通过调用 main 函数中的 runApp()来加载。

```
void main() => runApp(MyApp());
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Twitter UI',
      theme: new ThemeData(
          primaryColor: DarkModeBg,
      ),
      home: TwitterHomePage(),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   小部件不仅用于内容，也用于结构。这种小部件的例子有 Column、Row、Padding、SizedBox 等。Flutter 开发中的一切都是小部件，即使它们不包含任何内容，如文本、图像等。一切都延伸到颜色、对齐等方面

*   特定类型的小部件可以根据需要多次重用。

正如我所说的，我们将在本系列中浏览所有这些小部件。

### RaisedButton 小工具

RaisedButton 是 MaterialApp 库中内置的按钮小部件之一。下面是 RaisedButton 的构造函数代码。

```
const RaisedButton({
  Key key,
  @required VoidCallback onPressed,
  ValueChanged<bool> onHighlightChanged,
  ButtonTextTheme textTheme,
  Color textColor,
  Color disabledTextColor,
  Color color,
  Color disabledColor,
  Color highlightColor,
  Color splashColor,
  Brightness colorBrightness,
  double elevation,
  double highlightElevation,
  double disabledElevation,
  EdgeInsetsGeometry padding,
  ShapeBorder shape,
  Clip clipBehavior = Clip.none,
  MaterialTapTargetSize materialTapTargetSize,
  Duration animationDuration,
  Widget child,
}) : assert(elevation == null || elevation >= 0.0),
     assert(highlightElevation == null || highlightElevation >= 0.0),
     assert(disabledElevation == null || disabledElevation >= 0.0),
     super(
       key: key,
       onPressed: onPressed,
       onHighlightChanged: onHighlightChanged,
       textTheme: textTheme,
       textColor: textColor,
       disabledTextColor: disabledTextColor,
       color: color,
       disabledColor: disabledColor,
       highlightColor: highlightColor,
       splashColor: splashColor,
       colorBrightness: colorBrightness,
       elevation: elevation,
       highlightElevation: highlightElevation,
       disabledElevation: disabledElevation,
       padding: padding,
       shape: shape,
       clipBehavior: clipBehavior,
       materialTapTargetSize: materialTapTargetSize,
       animationDuration: animationDuration,
       child: child,
     ); 
```

Enter fullscreen mode Exit fullscreen mode

*   请注意方法参数 ButtonTextTheme。这需要一个小部件，该小部件需要一个 ButtonTheme 小部件，该小部件本身扩展了 inheritedWidget。

*   子参数属于 Widget 类型。这意味着我们可以将一个小部件传递给这个小部件。通常，一个小部件有一个名为“child”或“children”的参数，这取决于小部件本身。列和行小部件有一个“子”参数，而容器有一个“子”小部件。

*   微件包含其他微件的属性原则贯穿于整个 Flutter。

### 面向安卓开发者

作为一个使用 Java 或 Kotlin 的 Android 开发者，Flutter 中的这种组合方法会导致你的 UI 层次结构中有很多层，这是用这些语言开发时所不喜欢的。在用 Java 和 Kotlin 开发 Android ADK 时，我们的目标是浅层次，因为你的 UI 层次越多，应用程序就越慢。颤振不受这种类型的限制。Flutter 是如何做到这一点的？

Flutter 有一个布局算法，它以次线性的性能从窗口小部件树构建 UI。最坏的情况是在初始布局时是线性的 O(N)，在后续的 UI 更新时是次线性的(< O(N))，其中 N 是小部件的数量。

次线性布局算法和其他因素的结合意味着你不应该害怕组成部件。

### 构图很关键

如果你的目标是成为一名更好的 Flutter 开发者，请记住，小部件是构建模块，组合是从模块中创建一个建筑的技术。