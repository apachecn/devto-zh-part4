# 建立响应材料时间表

> 原文：<https://dev.to/crimsonmed/building-a-responsive-material-timeline-1dd0>

# 简介

我们正在重建一个客户的网站，他们想整合一个简单的响应时间表。我认为这是一个完美的机会来创造一些美观和有用的东西，可以很容易地重复使用。

**桌面版:**

[![Preview](img/2b55e6a29b56586491be1243df677439.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TtNPQwtw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qihgii4u1ostcakom000.gif)

**手机版:**

[![Mobile](img/e7fceb11a1db553d920f5b77ff033630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jQY_yFv5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4wa2k2mmxaxgzyktncc2.png)

**来源:**【http://medericburlet.com/material-timeline/】T2

**GitHub:**https://github.com/crimson-med/Material-Timeline[T3】](https://github.com/crimson-med/Material-Timeline)

# 技术堆栈

对于这个项目，我使用了三种技术: **HTML，CSS，Javascript (JQuery)** 。

*   HTML: 这是用来拥有数据和页面布局的。
*   CSS: 这是用于主题化和从左到右的日期动画。
*   **Javascript (JQuery):** 这用于处理元素何时在用户的视野中。在这个项目中，我使用 JQuery，因为它集成在客户的网站上。

如果需要，您可以用普通的 Javascript 替换 JQuery。

# 理解代码

公平的警告代码可能有点湿(写两遍)，因为它是一个小项目，在我的 JQuery 开始。

## HTML

```
<!DOCTYPE html>
<html><head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" type="text/css" href="css/materialTimeline.css">
</head>
<body>
<div id="timeline"></div>
</body>
<script src="js/jquery.js"></script>
<script src="js/materialTimeline.js"></script>
<script type="text/javascript">
var myDates = [
    {"date": "1959","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "1991","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "1994","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "2000","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "2004","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "2011","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "2013","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."},
    {"date": "2018","content":"Lorem ipsum dolor sit amet, quo ei simul congue exerci, ad nec admodum perfecto mnesarchum, vim ea mazim fierent detracto. Ea quis iuvaret expetendis his, te elit voluptua dignissim per, habeo iusto primis ea eam."}
];
renderTimeline(myDates);
</script>
<script src="js/fadeinTimeline.js"></script>
</html> 
```

我想这不需要太多解释，因为这是基本的 HTML，我们在头部包含了 CSS，然后潜入渲染时间线以及添加 Javascript 文件。

## CSS

CSS 非常简单，我在顶部定义了 CSS 变量(没有使用 SASS，因为它是一个非常小规模的短期项目)。

```
:root {
    --bg-color: #f8b195;
    --text-color:#355c7d;/* Dark Blue #355c7d */
    --timeline-color:#f67280;
    --date-bg-color:#f67280;
    --date-text-color:var(--text-color);
    --line-color:var(--text-color);
    --header-color:var(--text-color);
} 
```

在文件的底部，您有移动响应的媒体查询。
当前断点是:`@media (max-width: 600px)`
然而这很容易改变。

## Javascript

这个项目中有两个 javascript 文件。

**fadeintimeline . js**

这个文件是处理一个元素进入用户视野的实例的主要函数(例如:当用户向下滚动页面时)。

**T1】material timeline . jsT3】**

这个文件是在网页上渲染时间轴的主要函数。

# 结论

这是一个有趣的简单项目，让我发现了 JQuery 和 CSS 动画，如果我必须重做，我会制作一个普通的 javascript 版本，因为这意味着在所有浏览器上都有更好的可用性。我可能会添加一个小生成器，人们可以选择颜色，只需获得 html 代码。

Mederic Burlet