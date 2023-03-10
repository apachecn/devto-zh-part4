# 如何使用 JavaScript 创建折线图

> 原文：<https://dev.to/zingchartinc/how-to-create-a-line-chart-using-javascript-47l3>

折线图或图形是显示由线条连接的数据点的高效方式。折线图有三种不同的类型:简单折线图、三维折线图和垂直折线图。在本文中，我将向您展示如何创建不同类型的折线图，以及如何使用免费的 JavaScript 库“ZingChart”来设计它们的样式。

您将使用以下步骤来创建一个功能齐全的折线图:

1.  引用 ZingChart 库
2.  将图表放在 HTML 页面上
3.  添加数据和呈现图表
4.  图表的基本定制

## 在我开始之前

在开始我的教程之前，我强烈推荐注册 ZingChart 的“Studio”免费网络应用。

这将允许您在 ZingChart 提供的类似沙盒的环境中创建图表。它还为大多数图表类型提供了预制模板，因此您永远不需要从头开始。

查看工作室并开始制图！

## 1。引用 ZingChart 库

如果你对 HTML 布局了解不多，我在下面提供了一个模板，其中引用了 ZingChart 库。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    ZingChart: My Bar Chart
    <script src="<https://cdn.zingchart.com/zingchart.min.js>"></script>
  </head>
  <body>

  </body>
</html> 
```

要引用 ZingChart 库，请在 HTML 的`head`部分包含上面显示的代码片段。

你也可以下载 ZingChart 库或者使用软件包管理器，比如 NPM。

## 2。将图表放在 HTML 页面上

在将任何数据集成到折线图之前，您需要在代码的`body`中引用图表。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    ZingChart: My Bar Chart
    <script src="<https://cdn.zingchart.com/zingchart.min.js>"></script>
  </head>
  <body>
    <div id="myChart"></div>
  </body>
</html> 
```

在 HTML 的`body`中创建一个`div`标签，并给它一个设置为图表名称的`id`。就图表所需的 HTML 而言，您已经完成了！

## 3。添加数据和呈现图表

这就需要一些 JavaScript 知识。

下一步将是包含所有 JavaScript 将存在的`script`标签。当添加`script`标签时，确保它被放置在呈现图表的`div`之后。

```
<script>
  var myConfig = {
     "type": "line",
     "series": [
       {"values": [20,40,25,50,15,45,33,34]},
       {"values": [5,30,21,18,59,50,28,33]},
       {"values": [30,5,18,21,33,41,29,15]} 
     ]
  };

  zingchart.render({ 
    id: 'myChart', 
    data: myConfig, 
    height: "100%", 
    width: "100%" 
  });
</script> 
```

尝试将上述代码添加到 HTML 的`body`中的`script`标记内。如果你在你的浏览器中运行这个，你将会有一个功能齐全的折线图！

点击查看这个[演示。](https://app.zingsoft.com/demos/embed/Z4KY0TKN)

让我们回顾一下上面到底发生了什么。

```
var myConfig = {
  "type": "line",
  "series": [
    {"values": [20,40,25,50,15,45,33,34]},
    {"values": [5,30,21,18,59,50,28,33]},
    {"values": [30,5,18,21,33,41,29,15]}
  ]
}; 
```

我设置了一个名为`myConfig`的变量或`var`。这个对象中的所有属性将告诉 ZingChart 如何呈现图表。

```
var myConfig = {
  "type":"line",    //Tells ZingChart the type of chart to use**
  "series":[
    {"values": [20,40,25,50,15,45,33,34]},
    {"values": [5,30,21,18,59,50,28,33]},
    {"values": [30,5,18,21,33,41,29,15]}
  ]
}; 
```

您创建的对象中的第一个属性应该是`type`属性。ZingChart 有超过 35 种图表类型可用于属性`type`，但是为了构建折线图，您可能希望为类型赋予一个值`line`。上面的参考片段。

```
var myConfig = {
  "type":"line",
  "series":[        //Creates a series of data
    {"values":[20,40,25,50,15,45,33,34]},  //Values for blue line
    {"values":[5,30,21,18,59,50,28,33]},   //Values for red line
    {"values":[30,5,18,21,33,41,29,15]}    //Values for green line
  ]
}; 
```

您想要包含的下一个属性是`series`属性。这个属性将接受一个对象数组作为它的值。当创建自己的`values`时，确保遵循上面显示的格式。只有当您希望在一个图表中包含多条线时，才需要这样做。

您最不想做的事情就是告诉 ZingChart 在页面上呈现您的图表。

```
zingchart.render({    //Render method to show chart on page
  id: 'myChart',     //Reference the id used in your <div> tag
  data: myConfig,    //Reference the myConfig object created before
  height: "100%",    //Sets height for chart
  width: "100%"      //Sets width for chart
}); 
```

这可以通过调用`zingchart.render`方法来完成。该方法作为参数接受的四个主要属性是:

1.  `id`
2.  `data`
3.  `height`
4.  `width`

参考上面代码片段中的注释，了解这些参数对图表的作用。

## 4。折线图的基本定制

现在您已经在 dom 中显示了一个全功能的折线图，我将介绍一些简单的定制，您可以实现这些定制来改变折线图的外观。要深入了解可用的定制，请阅读 [ZingChart 的文档](https://www.zingchart.com/docs/chart-types/line#further-customization-and-styling)。

在前面，我简要地谈到了使用`type`属性并赋予它一个值`line`。你可以给`type`的其他几个值是:

1.  `line3d` -给你绘制的线条一个 3D 外观
2.  `vline` -垂直而非水平旋转图形

```
var myConfig = {
  "type": "line",
  "plot": {
    "aspect": "segmented"
  },
  "series": [
    {"values":[20,40,25,50,15,45,33,34]},
    {"values":[5,30,21,18,59,50,28,33]},
    {"values":[30,5,18,21,33,41,29,15]}
  ]
}; 
```

如果您包含一个名为`plot`的属性，并赋予它一个名为`aspect`的属性，您将进一步赋予您绘制的线条不同的外观。`aspect`可以接受这些价值观:

1.  `segmented` -用相连的直线分隔数据点
2.  `spline` -用相连的曲线分隔数据点
3.  `stepped` -用类似台阶的垂直线和水平线分隔数据点
4.  `jumped` -仅通过阶梯线的水平部分分隔数据点

```
var myConfig = {
  "type": "line",
  "series": [
    {
      "values":[20,40,25,50,15,45,33,34],
      "line-color":"#6666FF",        //Hexadecimal or RGB value
      "line-style":"dashed",         //'solid' | 'dashed'
      "line-width":5                 //In pixels
    }
  ]
}; 
```

为了进一步改变线条本身的样式，您可以直接向持有`values`属性的对象添加属性。参考上面的例子。

ZingChart 提供了大量定制选项。要了解更多关于折线图的信息，请访问 [ZingChart 的文档](https://www.zingchart.com/docs/chart-types/line)。想要从已经制作好的图表中获得一些灵感，请访问[津哈特的画廊](https://www.zingchart.com/gallery)。随意点击“编辑”任何演示，并保存在你的工作室帐户！

* * *

作为数据可视化领域的先驱，ZingChart 是一个强大的 JavaScript 库，以大数据为设计理念。ZingChart 拥有超过 35 种图表类型，并可与您的开发堆栈轻松集成，允许您轻松创建交互式响应图表。