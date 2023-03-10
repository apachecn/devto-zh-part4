# 带有 CSS 网格的 Web 布局:基础

> 原文：<https://dev.to/ltephanysopez/css-grid-the-basics-4ppp>

# 什么是 CSS 网格？

CSS Grid 是 CSS 中的一项技术，它允许开发人员跨浏览器更轻松、更一致地创建响应性 web 设计布局。使用 CSS Grid，我们可以将我们的元素从这个布局:
[![Standard HTML Layout](img/5454b5b51c1d5dce2f23391bd7fd39e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lAxz4sR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ltephanysopez/intro-to-web-dev/master/doimg/normal-html-layout.png) 
转换到这个:
[![CSS Grid Layout](img/0b6bc5ed507a08631b70527225b26b95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hRu0qmjc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ltephanysopez/intro-to-web-dev/master/doimg/css-grid-layout.png)

一个好的网站和一个坏的网站的区别在于以最容易被用户接受的方式组织网站内容的能力。你的网站应该是干净的，有一个易于跟随的导航系统，以有助于一个可用的网页布局。

一个易于遵循的布局会让网站的访问者很容易访问到你网站上有价值和重要的信息。当网页上很难找到内容时，访问者会变得焦躁不安，选择离开网站，并有可能不再回来。

* * *

# 入门

我们开始吧！要创建一个网格，我们首先需要创建一个类名为 grid 的`<div>`元素。标签被用作一个容器，将 HTML 文档分成几个部分。我们使用`<div>`元素将 HTML 元素组合在一起，同时将 CSS 样式应用于许多元素。

```
<div class="grid">

</div> 
```

太棒了。这个`<div>`元素表示在我们的网页上，我们有一个指定的名为 grid 的部分。现在，该是我们在网格中添加项目的时候了，我们将使用这些项目来设计样式。我们将通过添加带有`<div>`元素的较小部分并给它们单独的 id 来做到这一点。

```
<div class="grid">
   <div id="item-1"> 1 </div>
   <div id="item-2"> 2 </div>
   <div id="item-3"> 3 </div>
   <div id="item-4"> 4 </div>
</div> 
```

此时，我们有一个大容器，里面装满了小容器，它们组成了一个网格。让我们通过在 CSS 中修改容器来把它变成一个网格。

```
.grid {
    display: grid;
} 
```

现在，让我们看看我们的网格是什么样子的:
[![No change to our elements](img/5454b5b51c1d5dce2f23391bd7fd39e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lAxz4sR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ltephanysopez/intro-to-web-dev/master/doimg/normal-html-layout.png)

因为我们还没有在网格中定义任何行或列，所以这些元素将保持堆叠在一起。让我们通过向网格类添加两个新属性来改变这一点:`grid-template-columns`和`grid-template-rows`。

```
.grid {
     display: grid;
     grid-template-columns: 100px 100px;
     grid-template-rows: 50px 50px;
} 
```

提供给`grid-template-columns`和`grid-template-rows`属性的每个值决定了我们想要的列宽(100px)和行高(50px)。现在，我们的网格看起来像这样:

[![Adding rows and columns to our grid in CSS](img/85f900ec144ce11f0a19cbdb6c3ffe0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aI0S26LF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ltephanysopez/intro-to-web-dev/blob/master/doimg/grid-one.png%3Fraw%3Dtrue)

太棒了。让我们通过添加`grid-gap`属性:
在网格中的元素之间创建一个小间隙

```
.grid {
     display: grid;
     grid-template-columns: 100px 100px;
     grid-template-rows: 50px 50px;
     grid-gap: 10px;
} 
```

通过添加`grid-gap`属性，我们得到如下结果:
[![CSS Grid with rows, columns, and a gap between all elements](img/f8dba4bc56f928735ed6c0480700e56f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ww9lbMO---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ltephanysopez/intro-to-web-dev/blob/master/doimg/grid-two.png%3Fraw%3Dtrue)

* * *

# 定位和调整大小

除了网格行和网格列，我们还有网格线来标识列的开始和结束。
[![Grid Lines](img/a82d4f6974308e6d85476021f747c3df.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--PM0ls2cf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ltephanysopez/intro-to-web-dev/blob/master/doimg/2d-grid-layout.png%3Fraw%3Dtrue)

从上图中，我们可以看到，虽然我们有 3 列，但我们有 4 条网格线，其中:

*   第 1 列从第 1 行开始，到第 2 行结束
*   第 2 列从第 2 行开始，到第 3 行结束
*   第 3 列从第 3 行开始，到第 4 行结束

* * *

要创建多维布局，我们需要做的就是修改网格中每个元素的开始和结束位置。让我们修改我们的布局，使网格中的第一项跨越所有三列。

在我的 HTML 文件中，我将创建一个具有类名 grid 的`<div>`元素，该元素包含 9 个具有匹配 id 的独特元素:

```
<div class="grid">
     <div id=“item-1”> 1 </div>
     <div id=“item-2”> 2 </div>
     <div id=“item-3”> 3 </div>
     <div id=“item-4”> 4 </div>
     <div id=“item-5”> 5 </div>
     <div id=“item-6”> 6 </div>
     <div id=“item-7”> 7 </div>
     <div id=“item-8”> 8 </div>
     <div id=“item-9”> 9 </div>
</div> 
```

现在我们已经在 HTML 文件中创建了所有的元素，让我们通过添加 3 列 300 像素宽和 3 行 300 像素高来设置网格类的样式。我还指定了网格中的第一个元素应该跨越 3 个完整的列，方法是用两个新属性指定该列应该在哪些行上开始和结束:`grid-column-start`和`grid-column-end`。

```
.grid {
   display: grid;
   grid-template-columns:300px 300px 300px;
   grid-template-rows:300px 300px 300px;
   grid-gap:15px;
}

#item-1 {
   grid-column-start:1;
   grid-column-end:4;
} 
```

让我们看看上面的样式如何影响我们的网格:
[![Multi-dimensional CSS Grid Layout](img/ed49e2e334b4d3e8b8c8ea347ad02df2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4hHDHTFP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ltephanysopez/intro-to-web-dev/blob/master/doimg/multi-dimensional-layout.png%3Fraw%3Dtrue)

因为我们已经将网格中的第一个元素设置为跨越所有 3 列，所以网格中的所有其他元素都被推到下一个可用列。

指定一个元素应该跨越哪些网格线的另一种方法是使用简写的`grid-column`属性，它有两个值:起始网格线和结束网格线。

```
#item-1 {
   grid-column: 1/4;
} 
```

* * *

这就是 CSS 网格的基础！使用我们已经讨论过的属性，您现在可以创建最适合您的网站的多维布局。让我们看另一个例子，它的元素数量与上一个例子相同，但是 CSS 样式不同:

```
.grid {
    display: grid;
    grid-gap: 10px;
    grid-template-columns: 200px 100px 100px;
    grid-template-rows: repeat(5, 100px);
}

#item-1 {
    grid-column:1/3;
}

#item-4 {
    grid-column:2/4;
}

#item-5 {
    grid-column:1/4;
}

#item-9 {
    grid-column:1/4;
} 
```

上面的样式生成了以下网格:

[![Multi-dimensional Layout Example with CSS Grid](img/a7e756bebcf0056a30f23a538c5f5adb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I7LrzuMR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/ltephanysopez/intro-to-web-dev/blob/master/doimg/multid-layout-example.png%3Fraw%3Dtrue)

* * *

感谢阅读本教程！如果您想了解更多关于 CSS Grid 的知识，请查看这些优秀的资源:

*   网格生物:[https://www.gridcritters.com/](https://www.gridcritters.com/)
*   w3 学校:[https://www.w3schools.com/css/css_grid.asp](https://www.w3schools.com/css/css_grid.asp)
*   CSS 招数:[https://css-tricks.com/snippets/css/complete-guide-grid/](https://css-tricks.com/snippets/css/complete-guide-grid/)
*   网格示例:[https://gridbyexample.com/examples/](https://gridbyexample.com/examples/)
*   免费学习 CSS 网格:[https://scrimba.com/g/gR8PTE](https://scrimba.com/g/gR8PTE)