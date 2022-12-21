# 为什么 d3 的 geoContains 函数不能处理美国县数据。

> 原文：<https://dev.to/andersjr1984/why-d3-s-geocontains-function-does-not-work-with-us-county-data-kck>

这是使用[d3js.org](https://d3js.org/us-10m.v1.json)的美国 json 地形和 d3 版本 5.9.5。我已经发布了对 d3 的 gitHub 回购的解决方案，我想让尽可能多的人知道，这样就没有人会浪费时间去试图解决它。

所以，我有一个问题和一个对我有用的“黑客”解决方案。

我有几个(100)城市，我想根据他们在一个数组中给出的分数来给他们所在的县着色。

我用以下参数调用 geo contains:geo contains(county，points)。在地图上，几个县现在“包含”了纽约市(相当于“铁锈地带”和北部边境)。

我仔细阅读了这里发布的代码，并在与 Json topography 数据进行比较时注意到了以下问题:

geoContains 第 37 行:这是多边形的坐标传递给 polygon contains(object . coordinates)的地方；

polygon 包含第 2 行:这是传入的多边形数组的长度(object.coordinates)。对于上面引用的 Json 文件中的所有多边形，这个数组的长度是 1，它包含另一个数组，该数组包含创建多边形的所有坐标。这就是错误发生的地方。

我通过将正确的坐标数组传递给 polygonContains 函数而不是保存该数组的数组或通过 geoContains 函数来解决这个问题。它非常好用。

我想指出这是一个问题，也给人们这个问题的原因和最终的解决方案。我只是一个新手程序员(不到 6 个月编程，自学，在 d3 工作一周)，所以我不确定我这样做是否正确。我花了很多时间在网上搜索，试图找出问题所在，所以我希望上面的内容能帮助其他人。