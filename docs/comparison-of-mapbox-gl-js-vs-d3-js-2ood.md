# Mapbox GL JS 与 D3.js 的比较

> 原文：<https://dev.to/milafrerichs/comparison-of-mapbox-gl-js-vs-d3-js-2ood>

[![mapbox left, d3 right](img/707d5e91b8aeaa97c5b7a8e968cb9e54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BEedcr5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/civicvision/image/upload/f_auto%2Cq_auto%2Cw_auto%2Cdpr_auto%2Cc_limit/geospatial-d3/marketing/website/mapbox-d3.png)

当你想从你的数据中创建一个在线地图时，你有很多选择。

当我教 d3 或者和人们谈论他们使用什么工具时，经常出现的问题是:**“D3 和 mapboxGL 相比如何？”。**

这就是我想在这个网站上回答的问题。我不仅在概念上，也在代码上比较了这两个库。

让我们从一个更令人困惑的方面开始:

* * *

# 姓名

两个库定义如下:

> Mapbox GL JS 是一个 JavaScript 库，用于交互式、可定制的网络矢量地图。它采用符合 Mapbox 样式规范的地图样式，将它们应用于符合 Mapbox 矢量切片规范的矢量切片，并使用 WebGL 渲染它们。

Mapbox GL JS 是跨平台生态系统 Mapbox GL 的一部分，Mapbox 是一家专注于位置数据的公司。

> D3(或 D3.js)是一个 JavaScript 库，用于使用 web 标准可视化数据。D3 帮助您使用 SVG、Canvas 和 HTML 将数据变得生动。

* * *

# 概念性的

D3.js 和 Mapbox GL JS(或者为了写的少，简称 map box；) )在概念上是非常不同的。Mapbox 是一个创建所谓的“滑动地图”的库，与传单、OpenLayers 和 GoogleMaps 有着良好的合作关系。

> 一般来说，滑动地图是一个指现代网络地图的术语，它可以让你缩放和平移(当你拖动鼠标时，地图会滑动)。

OpenStreetMap 的这段引用很好地解释了这个问题:)

尽管使用这个定义 d3 地图也可以是光滑的地图。您可以为它们添加缩放和平移功能。

> Slippy Maps 的一个核心组成部分是图像应该作为网格上的图块提供。切片图像是浏览大量栅格和矢量地图数据的有效方法，这些数据太大而无法作为单个地图图像进行渲染。

这个来自行星开发者资源“滑滑地图 101”的解释更具技术性，但也更切题。这些地图(用于)从服务器加载图像，并平铺它们以加快加载速度。如今，矢量图像已经取代了图像，但是它们仍然被用作加载和渲染速度更快的图像。

* * *

# 代码

让我们看一些代码。或者准确地说是渲染上述地图的完整代码。Mapbox 地图在左边。右边的 d3 地图。

## 地图框

```
var url = "https://gist.githubusercontent.com/milafrerichs/78ef5702db2dc514fc2bed465d58406b/raw/f1366ee2a83a9afb1dd2427e9cbd4cd3db8d87ca/bundeslaender_simplify200.geojson";
mapboxgl.accessToken = 'YOUR_MAPBOX_TOKEN';
var map = new mapboxgl.Map({
  container: 'map',
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [13.79,53.545], 
    zoom: 5
  });
  map.on('load', function () {
    map.addSource('bb', { type: 'geojson', data: url });
    map.addLayer({
      'id': 'berlin',
      'type': 'fill',
      'source': 'bb',
      'paint': {
        'fill-color': '#088',
        'fill-opacity': 0.8
      }
    });
    map.addLayer({
      'id': 'berlin-stroke',
      'type': 'line',
      'source': 'bb',
      'paint': {
        'line-width': 1,
        'line-color': '#000',
      }
    });
}); 
```

[在 CodeSandbox 中打开](https://codesandbox.io/s/github/milafrerichs/mapping_examples/tree/master/mapbox/simple-map)

## D3

```
var url = "https://gist.githubusercontent.com/milafrerichs/78ef5702db2dc514fc2bed465d58406b/raw/f1366ee2a83a9afb1dd2427e9cbd4cd3db8d87ca/bundeslaender_simplify200.geojson";
d3.json(url).then(function(bb) {
  var bbox = d3.select('#d3-map').node().getBoundingClientRect()
  var width = bbox.width;
  var height = bbox.height;
  var projection = d3.geoEqualEarth();
  projection.fitExtent([[20, 20], [width, height]], bb);
  var geoGenerator = d3.geoPath().projection(projection);
  var svg = d3.select("#d3-map").append('svg')
      .style("width", "100%")
      .style("height", "100%");
  svg.append('g').selectAll('path')
  .data(bb.features)
  .enter()
    .append('path')
    .attr('d', geoGenerator)
    .attr('fill', '#088')
    .attr('stroke', '#000');
}); 
```

[在 CodeSandbox 中打开](https://codesandbox.io/s/github/milafrerichs/mapping_examples/tree/master/d3/simple-map)

* * *

# 代码量

您看到的第一个区别是 mapbox 的代码几乎是 d3 代码的两倍长。这有点令人惊讶。但原因是样式化需要花费很多精力，并且是以可读的方式格式化的。

### 获胜者:D3

* * *

# 易于设置

两者都相对容易设置。你要么下载他们的代码并集成它。或者像我在这个页面上做的那样使用 CDN。

## Mapbox setup:

您需要包含 javascript 代码和用于 mapbox 的 CSS。您需要包含样式表以确保导航元素可见。此外，你需要在 mapbox.com 注册获得一个 API 令牌来使用它。但这是免费的，只要你的电子邮件作为付款。

```
<script src="https://unpkg.com/mapbox-gl@0.49.0" type="text/javascript"></script>
<link href="https://unpkg.com/mapbox-gl@0.49.0/dist/mapbox-gl.css" rel="stylesheet" type="text/css"> 
```

## D3 设置:

```
<script src="https://unpkg.com/d3@5.9.7/dist/d3.min.js" type="text/javascript"></script> 
```

### 获胜者:D3

* * *

# 底图

最明显的区别是缺少 d3 的底图。但这是我故意做的。d3 的强大之处在于它默认不包含底图。您希望数据成为地图的中心，而不是底图。

这是比较重要的部分之一，你可以根据自己的需要选择库，d3 和 mapbox 可以满足不同的需求。当您需要更多信息而不仅仅是数据时，Mapbox 和其他基于底图的库会大放异彩。例如街道位置、更多标签等。

### 获胜者:地图盒子

* * *

# 投影

第二大区别是这些地图的投影。Mapbox 使用**墨卡托**作为默认设置，并且您不能更改投影。

在 D3 里，你自己选择投影。您可以从 14 个不同的投影列表中选择，也可以创建自己的投影。 [D3 投影资源](https://github.com/d3/d3-geo-projection)

```
var projection = d3.geoEqualEarth(); 
```

### 获胜者:D3

* * *

# 造型

地图框样式是通过[地图框样式规范](https://docs.mapbox.com/mapbox-gl-js/style-spec/)完成的，而 D3 样式是通过普通的 SVG 属性和 CSS 完成的。

正如你可能已经注意到的，我们需要在 mapbox 中创建两个图层来设置填充和轮廓的样式。

这是风格规范的诸多限制之一。但是您可以使用样式规范做很多事情，并且不需要考虑如何实现。

在 d3 里，你可以用 SVG 和 CSS 来设计几乎任何东西。而且更方便。在这两种情况下，您都需要学习如何设计元素的样式。

## 地图框:

```
map.addLayer({
      'id': 'berlin',
      'type': 'fill',
      'source': 'bb',
      'paint': {
        'fill-color': '#088',
        'fill-opacity': 0.8
      }
    });
    map.addLayer({
      'id': 'berlin-stroke',
      'type': 'line',
      'source': 'bb',
      'paint': {
        'line-width': 1,
        'line-color': '#000',
      }
    }); 
```

## D3:

在 d3 中，我们只需要两行代码。

```
.attr('fill', '#088')
  .attr('stroke', '#000'); 
```

### 获胜者:D3

* * *

# 居中地图

虽然 Mapbox 依赖于您(创建者)来预先设置缩放、居中等，但是 d3.js 允许您使用您的数据来设置范围。

你可以让 mapbox 计算出最好的缩放和居中，但是这比较困难，我目前没有包括这个。我将来可能会扩展这篇文章。

### 地图框

```
center: [13.79,53.545], 
  zoom: 5 
```

### D3

```
projection.fitExtent([[20, 20], [width, height]], bb); 
```

### 获胜者:D3

* * *

# 你需要学习的概念

要使用 mapbox.js，你需要学习或理解的概念肯定更少。你只需要知道有层和源，调用适当的函数(查看示例和文档:)，阅读 mapbox 样式规范，你就可以开始了。

使用 d3，你应该知道 HTML 的基础，学习一点 SVG，然后知道调用哪个 d3 函数(看例子，文档)。

### 获胜者:地图盒子

* * *

# 结论

正如我在这篇文章的开头提到的，d3 和 mapbox.js 都有不同的用途。但它们确实有相似的特征。

那么它们是如何相互竞争的呢？让我们找出答案。

**地图框** : 2

**D3** : 5

我宣布 D3 是这次比较的获胜者。但是请记住，如果您需要底图，mapbox 可能更容易设置。

* * *

*本帖最初发表于[https://mappingwithd3.com/mapbox-d3](https://mappingwithd3.com/mapbox-d3)T3】*

[![](img/9818d9a15fdba0e8c33b15074b89aa74.png)](https://mappingwithd3.com/?lsc=devto)