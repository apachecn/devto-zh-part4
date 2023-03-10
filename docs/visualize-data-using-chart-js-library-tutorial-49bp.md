# 使用 chart.js 库可视化数据(教程)

> 原文：<https://dev.to/saraahmed626/visualize-data-using-chart-js-library-tutorial-49bp>

有时，在我们构建的软件中，我们发现我们必须处理无法清晰查看的数据集，除非我们将其可视化，否则我们没有机会鸟瞰我们项目数据中真正发生的事情，这就是数据可视化的重要性所在，使用图表是可视化数据的方法之一。

“图表可以采用多种形式，但是有一些共同的特征为图表提供了从数据中提取含义的能力。

通常，图表中的数据是用图形表示的，因为人们通常能够从图片中比从文本中更快地推断出含义。文本通常仅用于注释数据。

图表中文本最重要的用途之一是标题。图表的标题通常出现在主图表的上方，并提供图表中数据所指内容的简明描述。

数据中的尺寸通常显示在轴上。如果使用水平轴和垂直轴，它们通常分别称为 x 轴和 y 轴。每个轴都有一个刻度，由周期性刻度表示，通常伴有数字或分类指示。每个轴通常还会在其外部或旁边显示一个标签，简要描述所表示的尺寸。如果标度是数字的，标签通常会以括号中的标度单位为后缀。例如，“行进的距离(m)”是一个典型的 x 轴标签，表示行进的距离(以米为单位)与图表中数据的水平位置有关。"

这个博客是关于使用 JS 库 chart.js 的教程。

## Chart.js

Chart.js 是一个 JavaScript **开源**库，允许你使用 **HTML5 canvas** 元素绘制不同类型的图表。因为它使用 HTML5 canvas，所以您必须包含一个 polyfill 来支持旧的浏览器。
其**反应灵敏**，具有**交互性特征**(鼠标悬停点击)，还使用**图例**也称为一键。图例包含图表中出现的变量列表及其外观示例。该信息允许在图表中识别每个变量的数据。

## 支持的图表类型

折线图
[![Line Chart](img/0211bc06212216bb6404b00db829309c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tBwYpdyd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y1hrsssltvaiae6pg6lv.png)

条形图
[![Bar Chart](img/69acb6095b78e44966501017a78f7945.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FOPQ67FD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fjp2li6ginmq6dtlf7n0.png)

雷达图
[![Radar Chart](img/c94b6fcdfe90cdcae0a0f46c18b5d08a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iLwXfpYn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kub1z9meqsa0h3mypwpl.jpg)

饼状图
[![Doughnut & Pie Chart](img/134681d68a7eaf794ff7dd2bd0161293.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j7Av-KGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/72zui77rbq15rdmvvaa4.png)

极区图
[![Polar Chart](img/23c13c292332ad9d37a49d9b11367053.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gPB7Q2sB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1sumc9zcja83nv75e23f.png)

气泡图
[![Bubble Chart](img/afdee7451bd9102752ef989f348398ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JfPbIi03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pveypm755d3ea5j7lzr9.png)

散布图
[![Scatter Chart](img/8552cb99794f18c803179740e20e165b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eKKAj-rG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kwkpsu39uwvcff21zx8d.png)

面积图
[![Area Chart](img/e4fb3ae318c30696aa36d3e3296850d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t68BUOmo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fmu41ljdp06j89ck7d1r.png)

Mixed:使用 Chart.js，可以创建由两种或多种不同图表类型组合而成的混合图表。

## 如何在你的项目中使用 chart.js

下面是两个例子，一个是通过 JSON 从控制器传递到视图的动态数据。在第二个例子中，数据是固定的，不从任何地方传递。

### 示例(1)从控制器传递的数据

如果我们决定使用图表，那么我们有一些数据集，chart.js 以数据集的形式处理数据。

在开始时，将 chart.js 库包含到您的项目中。在下面的例子中，我们使用 Ajax 和 JSON 列表对象将数据从一个名为 Charts 的控制器传递到我们的视图，由您决定如何将数据发送到画布。或者，如果数据是固定的，那么不需要 JSON 或 Ajax，只需以固定数组/列表的形式添加数据集。使用的图表类型是圆环图。

通常我们会有 3 个数据集，可以用数组或列表来表示:

1.  标签的数据集。
2.  代表每个标签的数量或值的数据集。
3.  代表每个标签的颜色的数据集。

在控制器中，处理来自控制器的数据并将其发送到视图
的方法

```
[AllowCrossSiteJson]
    public ActionResult PieChartData()
    {
      List<string> labelsList = new List<string>();
      List<double> dataList = new List<double>();
      List<string> colorsList = new List<string>();

 //Add the code that process data from Database to be added to the three lists

    return Json(new { labelsList, colorsList, dataList }, JsonRequestBehavior.AllowGet);
        } 
```

在视图中，添加稍后将显示图表的 canvas 标记。

```
<canvas id="doughnut-chart" width="200" height="400"></canvas> 
```

```
<div>
   <canvas id="doughnut-chart" width="200" height="400"></canvas>
   <script type="text/javascript">
      $(document).ready(function () {
      $.ajax({
      type: "get",
      //Charts is the controller
      // doughnutChartData is the Actionresult method that sends the JSON data                     to the view 
      url: "/Charts/doughnutChartData",
      dataType: 'json',
       //When success, data is the JSON object of lists passed from doughnutChartData method
      success: function (data) {
      //Making chart by using new Chart, and the canvas element with the id: doughnut-chart
             new Chart(document.getElementById("doughnut-chart"), {
               //Specify type of chart
                type: 'doughnut',
                data: {
               // First data set that contain labels, its accessed through JSON object data.
                labels: data.labelsList,
                datasets: [{
                label: "",
                //Second data set which contains a list of colors that will represent each label 
                backgroundColor: data.colorsList,
                 // Third data set which contains the value of each labe
                data: data.dataList
                        }]
                    },
                 // using Configuration Options, one can customize the chart, its explained below in a separate paragraph 
                options: {
                legend: {
                    display: false
                         },
                tooltips: {
                    enabled: true
                  },
                 maintainAspectRatio: false,
                 title: {
                    display: true,
                    text: 'Sales/ Item for Current Month'
                         }
                        }
                      });
           }, error: function (error) {
           console.log(error.responseText);
                                }
                                    });
                                });
    </script>
 </div> 
```

在上面的代码中，首先，表示我们的数据的三个数据集被声明，并准备好显示在视图中，但是可以使用配置选项进行调整和定制。

## 配置

该配置用于更改图表的行为方式。有一些属性可以控制样式、字体、图例等。

## 全局配置

这个概念是在 Chart.js 1.0 中引入的，目的是保持配置的干燥，并允许**跨图表类型全局地**改变选项，避免为每个实例指定选项，或者为特定的图表类型指定默认值。

## 配置选项:

#### 动画

提供了许多选项来配置动画的外观和播放时间。

#### 布局

填充。如果该值是一个数字，它将应用于图表的所有边(左、上、右、下)。如果该值是一个对象，则 left 属性定义左填充。同样，也可以指定 right、top 和 bottom 属性。

#### 图例

图表图例显示图表上出现的数据集的相关数据。

#### Title

图表标题定义了要在图表顶部绘制的文本。

#### Tooltip

工具提示是当用户将鼠标悬停在图表上的数据点上时显示的标签。

#### 元素

虽然图表类型提供了配置每个数据集样式的设置，但有时您希望以相同的方式设置所有数据集的样式。一个常见的例子是用相同的颜色描边条形图中的所有条形，但改变每个数据集的填充。可以为四种不同类型的元素配置选项:圆弧、直线、点和矩形。设置后，这些选项适用于该类型的所有对象，除非被附加到数据集的配置特别覆盖。

### 应用上述代码后的结果图表:

应用代码时，将鼠标悬停在每种颜色上会显示每个值的标签。

[![Doughnut Chart ](img/036fe9502dbc5978faacc8dadc944761.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--552MsVWd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mzc4ijjllx4q692wiaue.PNG)

### 例(2)固定数据

```
<html>
<head>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.8.0/Chart.js"></script>

 <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.8.0/Chart.css">
</head>

<body>
<canvas id="myChart" width="400" height="400"></canvas>
<script>
var ctx = document.getElementById('myChart');
var myChart = new Chart(ctx, {
    type: 'bar',
    data: {
        labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
        datasets: [{
            label: '# of Votes',
            data: [12, 19, 3, 5, 2, 3],
            backgroundColor: [
                'rgba(255, 99, 132, 0.2)',
                'rgba(54, 162, 235, 0.2)',
                'rgba(255, 206, 86, 0.2)',
                'rgba(75, 192, 192, 0.2)',
                'rgba(153, 102, 255, 0.2)',
                'rgba(255, 159, 64, 0.2)'
            ],
            borderColor: [
                'rgba(255, 99, 132, 1)',
                'rgba(54, 162, 235, 1)',
                'rgba(255, 206, 86, 1)',
                'rgba(75, 192, 192, 1)',
                'rgba(153, 102, 255, 1)',
                'rgba(255, 159, 64, 1)'
            ],
            borderWidth: 1
        }]
    },
    options: {
        scales: {
            yAxes: [{
                ticks: {
                    beginAtZero: true
                }
            }]
        }
    }
});
</script>

</body>
</html> 
```

只需复制粘贴第二个示例，并在浏览器中查看结果；)

**延伸阅读**
[Chart.js](https://www.chartjs.org/)
[下载 chart.js](https://www.jsdelivr.com/package/npm/chart.js)
[配置选项](https://www.chartjs.org/docs/latest/configuration/animations.html)
[JavaScript 比较图表库](https://en.wikipedia.org/wiki/Comparison_of_JavaScript_charting_libraries)
[图表特性](https://en.wikipedia.org/wiki/Chart#Features_of_a_chart)