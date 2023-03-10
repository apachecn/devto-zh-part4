# Flutter 的数据可视化部件

> 原文：<https://dev.to/syncfusion/data-visualization-widgets-for-flutter-46l9>

如果你是一名移动应用程序开发人员，并且喜欢使用 Flutter 框架，你会很高兴地知道 Syncfusion Flutter 图表包的测试版可以在 [pub.dev](https://pub.dev/packages/syncfusion_flutter_charts) 上获得。

<figure>

Syncfusion Flutter Charts 包是一个用 [Dart](https://dart.dev/) 原生编写的数据可视化库，用于使用 Flutter 框架为 iOS 和 Android 的高质量移动应用程序用户界面创建美观且高性能的图表。它允许用户绘制数据，并通过无缝交互和响应流畅的动画来呈现定制的图表类型。【T2![Syncfusion Flutter Charts](img/8265eccc33bcc7b5b605f7f088dcd915.png)

<figcaption>Syncfusion Flutter Charts</figcaption>

</figure>

## 什么是颤振？

[Flutter](https://flutter.dev/) 是谷歌的移动应用开发 SDK，它拥有一些小部件和工具，用于从单一代码库创建原生编译的跨平台移动应用。与其他跨平台的移动应用开发框架相比，Flutter 具有以下优势:

**开发周期快:**有状态热重装，不用重新构建就能实时重绘 app。

**单一代码库:**在 iOS 和 Android 中使用单一代码库构建应用。

**灵活的 ui 和小部件:**大量的小部件和工具可用于构建灵活的应用程序。

**原生性能:**该框架提供了完整的原生支持，甚至对滚动、导航、图标和字体也是如此。

**开源:** Flutter 是开源的，可以免费使用或学习。

## 颤振图表特征

Syncfusion Flutter 图表具有丰富的功能，包括用于渲染笛卡尔图表和圆形图表的功能。它们是完全可定制和可扩展的，它们的特性列表将在即将到来的更新中积极扩展。看看下面这些现在的特点。

### 图表类型

图表小部件包括绘制 12 个系列类型的功能:

线

样条

区域

步线

快线

列

条形

气泡

散点

饼图

甜甜圈

径向条形

<figure>

[![Syncfusion Flutter Chart Types](img/5357adea1add56f094e8dffde09eac1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ws7uDec0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.syncfusion.com/blogs/wp-content/uploads/2019/07/Syncfusion-Flutter-Chart-Types.png)

<figcaption>Syncfusion Flutter Chart Types</figcaption>

</figure>

每种图表类型都可以轻松配置内置支持，以创建令人惊叹的视觉效果。您可以在图表中添加任意数量的系列。标记、数据标签、数据标签构建器、动画、渐变填充、破折号、排序和注释等功能易于合并和自定义。

### 轴类型

借助三种轴类型，在图表中绘制任何类型的数据:

数字

类别

日期时间

<figure>

[![Chart Axis Types](img/044c4a5eabaa3c5e532b46e91e3e49c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0gIOxFSq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.syncfusion.com/blogs/wp-content/uploads/2019/07/Chart-Axis-Types.png)

<figcaption>Chart Axis Types</figcaption>

</figure>

标签相交、边缘标签放置、标签旋转、轴反向、反向轴和多轴等轴功能允许用户进一步定制轴元素，以使轴更具可读性。

### 图例

<figure>

借助图例显示有关图表系列的附加信息。图表图例也可用于折叠系列。如果项目超出可用界限，图例可以换行或滚动。[![Syncfusion Flutter Chart with Legend](img/a41698353204fbe9be7ab7e1a59cf8d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ceSQ6oh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.syncfusion.com/blogs/wp-content/uploads/2019/07/Syncfusion-Flutter-Chart-with-Legend-2.jpg)

<figcaption>Syncfusion Flutter Chart with Legend</figcaption>

</figure>

### 用户交互

以下交互功能极大地增强了用户体验:

缩放和平移

十字光标

轨迹球

向下钻取

事件

选择

工具提示

<figure>

[![User Interaction in Syncfusion Flutter Charts](img/aabe4bd7da7866b542c846e79f0f8353.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k7T5WpQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.syncfusion.com/blogs/wp-content/uploads/2019/07/User-Interaction-in-Syncfusion-Flutter-Charts.gif)

<figcaption>User Interaction in Syncfusion Flutter Charts</figcaption>

</figure>

### 动态更新

图表可以用几秒钟内变化的实时数据进行动态更新，如股票价格、温度、速度(即，可以在呈现的图表中动态添加或删除数据点或数据系列)。此外，图表的整个数据源可以被完全替换。

我们计划在未来的版本中添加更多的图表类型，以及更丰富的图表。

## 将颤振图表添加到您的应用程序

本节说明如何创建简单的图表，并演示图表的基本用法。

### 添加依赖关系

将 Syncfusion Flutter 图表依赖项添加到 pubspec.yaml 文件中。

```
dependencies:syncfusion\_flutter\_charts: ^1.0.0-beta.3 
```

### 获取套餐

运行以下命令获取所需的包。

```
$ flutter pub get 
```

### 导入包

在您的 Dart 代码中导入以下包。

```
import 'package:syncfusion\_flutter\_charts/charts.dart'; 
```

### 向 widget 树添加图表

将图表小部件添加为任何小部件的子部件。在这里，将图表小部件添加为容器小部件的子部件。

```
@override
Widget build(BuildContext context) {
  return Scaffold(
    body: Center(
      child: Container(
        child: SfCartesianChart(
        )
      )
    )
  );
} 
```

### 绑定数据源

根据您的数据，初始化适当的轴类型和系列类型。在序列中，您需要映射数据源以及 x 和 y 数据点的字段。因为我们要呈现一个带有分类轴的折线图，所以我已经初始化了它们。

```
@override
Widget build(BuildContext context) {
  return Scaffold(
    body: Center(
      child: Container(
        child: SfCartesianChart(
          primaryXAxis: CategoryAxis(), // Initialize category axis.
          series: <LineSeries<SalesData, String>>[ // Initialize line series.
            LineSeries<SalesData, String>(
              dataSource: [
                SalesData('Jan', 35),
                SalesData('Feb', 28),
                SalesData('Mar', 34),
                SalesData('Apr', 32),
                SalesData('May', 40)
              ],
              xValueMapper: (SalesData sales, _) => sales.year,
              yValueMapper: (SalesData sales, _) => sales.sales
            )
          ]
        )
      )
    )
  );
}

class SalesData {
  SalesData(this.year, this.sales);
  final String year;
  final double sales;
} 
```

### 添加图表元素

最后，添加 Syncfusion Flutter 图表元素，如标题、图例数据标签和工具提示，以显示有关图表中绘制的数据的附加信息。

```
@override
Widget build(BuildContext context) {
  return Scaffold(
    body: Center(
      child: Container(
        child: SfCartesianChart(
          primaryXAxis: CategoryAxis(),
          title: ChartTitle(text: 'Half yearly sales analysis'), //Chart title.
          legend: Legend(isVisible: true), // Enables the legend.
          tooltipBehavior: ChartTooltipBehavior(enable: true), // Enables the tooltip.
          series: <LineSeries<SalesData, String>>[
            LineSeries<SalesData, String>(
              dataSource: [
                SalesData('Jan', 35),
                SalesData('Feb', 28),
                SalesData('Mar', 34),
                SalesData('Apr', 32),
                SalesData('May', 40)
              ],
              xValueMapper: (SalesData sales, _) => sales.year,
              yValueMapper: (SalesData sales, _) => sales.sales,
              dataLabelSettings: DataLabelSettings(isVisible: true) // Enables the data label.
            )
          ]
        )
      )
    )
  );
} 
```

因此，前面的代码呈现了下图中的图表。

[![Simple line chart](img/921c1292db781c9a853302992946bc3d.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--k_mm8PUK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2019/07/simple_line_chart-1.gif)

## 探索颤振图表示例和演示

你可以在这个 [GitHub 位置](https://github.com/syncfusion/flutter-examples)看到 Syncfusion 的 Flutter 应用有很多例子。

另外，看看我们在 [Play Store](https://play.google.com/store/apps/details?id=com.syncfusion.flutter.examples&hl=en) 的演示应用。

如果你想深入了解如何创建一个完整的 Flutter 应用程序，请务必阅读艾德·弗雷塔斯的《简洁的 Flutter》。这是 [Syncfusion 免费技术电子书库](https://www.syncfusion.com/ebooks)的一部分。

## 即将到来的发展计划

接下来，我们计划在 Flutter 中实现调度器和圆形标尺小部件，并在图表小部件中添加图表类型和可用性特性。

我们根据开发人员的需求对我们的开发特性和小部件进行优先排序；因此，如果你想让我们添加一个新的插件，或者你对我们的 Flutter 图表有疑问，请在这篇文章的评论部分告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums?_ga=2.147131827.588310507.1562560519-261840643.1556777789)、[直接跟踪](https://www.syncfusion.com/support/directtrac/incidents)或[反馈门户](https://www.syncfusion.com/feedback/flutter)联系我们。这有助于我们确定要实施的下一组控件和功能的优先级。

## 结论

在这篇博文中，我们介绍了新的 Syncfusion Flutter Charts 小部件，并讨论了我们的未来计划。我们邀请您试用这个小部件，并在它最终发布之前提供您的反馈，以使它更加健壮。一如既往，我们很乐意为您提供帮助！

介绍 Flutter 数据可视化部件的帖子[首先出现在](https://www.syncfusion.com/blogs/post/introducing-data-viz-widgets-for-flutter.aspx) [Syncfusion 博客](https://www.syncfusion.com/blogs)上。