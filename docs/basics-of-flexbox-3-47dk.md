# Flexbox 基础知识— 3

> 原文：<https://dev.to/nabendu82/basics-of-flexbox-3-47dk>

你可以在这里找到这部分[用到的码笔。](https://codepen.io/nabendu82/pen/XqvyVa/)

我们将从 flex 容器属性 align-content 开始。当有额外的空间时，它基本上跨横轴排列项目。

**注意:**只有一行弹性项目时，该属性无效。

使用的基本 HTML 是-

```
<div class=”container”>
 <div class=”box box1">1</div>
 <div class=”box box2">2</div>
 <div class=”box box3">3</div>
 <div class=”box box4">4</div>
 <div class=”box box5">5</div>
 <div class=”box box6">6</div>
 <div class=”box box7">7</div>
 <div class=”box box8">8</div>
 <div class=”box box9">9</div>
 <div class=”box box10">10</div>
 </div> 
```

而使用的基本 CSS 是-

```
/* Some default styles to make each box visible */
.box {
 color:white;
 font-size: 100px;
 text-align: center;
 text-shadow:4px 4px 0 rgba(0,0,0,0.1);
 padding:10px;
}
/* Colours for each box */
.box1 { background:#1abc9c;}
.box2 { background:#3498db;}
.box3 { background:#9b59b6;}
.box4 { background:#34495e;}
.box5 { background:#f1c40f;}
.box6 { background:#e67e22;}
.box7 { background:#e74c3c;}
.box8 { background:#bdc3c7;}
.box9 { background:#2ecc71;}
.box10 { background:#16a085;}
/* We start writing out flexbox here. The above is just page setup */
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
}
.box {
 width:33.333%;
} 
```

这给出如下-

[![The basics](img/a7c4cae863497a9d7f90eb3584c0d33c.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--UednhCsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2864/1%2AQbhGAzGwqKLk-tdhaNV9sg.png) *基础知识*

我们将需要 flex-wrap 属性将它扩展到多行，这是使用 align-content 所必需的。让我们开始吧。

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
} 
```

这导致了-

[![flex-wrap: wrap;](img/1a4e5f773e2a8946293e2aae7602be97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tYqtMMhy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2868/1%2A8ualuFKcKmh62z7slsplWw.png)*flex-wrap:wrap；*

现在，我们准备好使用 align-content 属性了。根据 CSS 技巧[文章](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)，它包含与 justify-content 相似的属性集。我们将一个接一个地讨论它们，这是不言自明的。

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
 align-content: flex-start;
} 
```

[![align-content: flex-start;](img/2ec71825539383f65a34d73994aef214.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bsI_OXuI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2868/1%2A1In_GWrB7NDS5BIiSjlH0Q.png)T3】align-content:flex-start；

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
 align-content: flex-end;
} 
```

[![align-content: flex-end;](img/7fb1a53093280cd578fd5a1c193ae58e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CT-of_N8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2870/1%2AyxYRMGtMTvCuERmUwMdplA.png)*align-content:flex-end；*

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
 align-content: center;
} 
```

[![align-content: center;](img/2ec12f11890702768ebd147f82d7e707.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--0uSEqoZx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2870/1%2Aj40RwQ_wdgaMF110utsGvw.png) *对齐-内容:居中；*

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
 align-content: space-between;
} 
```

[![align-content: space-between;](img/0b1e16c86252be1dc871c594287bf579.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZwE-JLee--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2872/1%2A0daKweS2SHh16qBdLiZO7g.png)*align-content:space-between；*

```
.container {
 display:flex;
 border:10px solid mistyrose;
 height:100vh;
 flex-wrap: wrap;
 align-content: space-around;
} 
```

[![align-content: space-around;](img/588fe19ecae50329a9aafe3d90b58315.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPWk9G7x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2874/1%2AzraTWf3ZR9WzfDU6XTk89Q.png)*align-content:space-around；*