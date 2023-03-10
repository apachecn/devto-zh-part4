# 对于一个标准站点，我从 CSS Grid + Flexbox 中学到了 3 件事。

> 原文：<https://dev.to/ryanallmad/i-learned-3-things-using-css-grid-flexbox-for-a-standard-site-1i9c>

我真的很喜欢我的这个小项目，用 CSS Grid 将一个[图形设计网格转换成一个网页，但是用 CSS Grid 构建一个标准网站的想法仍然让我着迷。所以我继续用 CSS Grid 构建了一些基本的和标准的东西。我最初的网格的模板行高是以像素为单位设置的，为了响应而调整高度对我来说太疯狂了，所以我将我的模板行高设置为:](https://dev.to/ryanallmad/translating-the-graphic-design-grid-to-css-grid-3lco)

```

    grid-template-rows: repeat(12, minmax(0px, max-content));

```

现在我有了一个响应就绪的网格。上面的样式将我的网格设置为 12 个灵活的行，如果没有内容，这些行将缩小到 0px。

对我来说，建立一个标准的网站应该从移动开始，再回到桌面。但是因为我有点抓狂，所以先建了桌面版。在我的脑海里，我在想*“我只是想设计一个标题、侧栏、封面图片、主要区域和页脚，然后看到它们全部组合在一起。”这个后来咬了我的屁股。我痴迷于用“最小宽度”的媒体查询首先构建移动设备(*非常疯狂，因为我开始向后*)。毫不奇怪，当我开始为了响应性而改变事物时，我发现 CSS Grid 很笨重(或者说我责怪 CSS Grid)，但是这是一个很大的但是，我开始我的设计是桌面设计，所以从那里向后使用最小宽度的媒体查询将总是更多的工作(*我承认我不应该责怪 CSS Grid 的额外工作，因为它是我的一部分*计划不周。当我“响应”我的图形设计网格时，它也是首先为桌面构建的，我使用了最大宽度的媒体查询，它的工作量少得令人难以置信。*

[https://codepen.io/all-mad-designs/embed/ExYQQLQ?height=600&default-tab=result&embed-version=2](https://codepen.io/all-mad-designs/embed/ExYQQLQ?height=600&default-tab=result&embed-version=2)

## 1.

### 如果你使用 CSS Grid 构建，首先决定你是为移动还是桌面屏幕构建...

如果桌面则使用最大宽度媒体查询
否则使用最小宽度。

## 2.

### 在网格中显示 flex。

当我将它的子元素设置为**“display:flex；”时，布局我的网格的祖先元素要容易得多**我起初试图创建一个子网格，这需要更多的研究来确定。对于一个简单的设计，我发现当我将 header 元素设置为:

```
display:flex; 
flex-flow:row nowrap; 
align-items:flex-end; 

```

header 元素是**的子元素。容器**被设置为**显示:网格；**所以这个标题有设置的**网格子属性**，像**网格列和网格行**。但是可以将标题的 display 属性设置为 flex，这样标题的子标题就可以采用 flex 对齐属性。

在我设计出反应灵敏的设计后，我开始让它变得不那么标准，并加入了我的时髦小元素。我不喜欢听到像*“没人再用方框阴影了”*和*“没人做圆角了”这样的话*好吧，不管怎样，我做了，我觉得这看起来很有趣，起诉我吧(*不，不要真的，我破产了，我还有一张嘴要养活*)。无论如何，我添加了我的小反应触摸，框阴影移动悬停在元素上意味着链接某处，他们也有一个[“涟漪效应”我从本萨博](https://codepen.io/finnhvman/post/pure-css-ripple-with-minimal-effort?fbclid=IwAR10cOpH7_AF8jo6YF1KqtjS2WNr_Wnk6E2UaC4Tx3_UbnFAlY3Og5OS0Ws)

## 3.

### CSS Grid 可以让您更好地控制 JavaScript 滚动事件。

最后，我添加了一些 JavaScript，因为我也在磨练这些技能。当使用带有滚动效果的 CSS Grid 时，JavaScript 可能更容易预测，因为如果您使用许多带有相对位置、绝对位置、粘性等的元素，许多对象属性将受到“定位元素”的影响。，那么你的滚动效果将变得很难预测。有了 CSS Grid，你不需要经常使用定位。我只有两个添加了定位属性的元素，我的 logo 有**位置:absolute**和我的**。content-main** 有**位置:相对；**以便我的滚动功能按照我期望的方式工作。

## 结果

[https://codepen.io/all-mad-designs/embed/rNBJJvG?height=600&default-tab=result&embed-version=2](https://codepen.io/all-mad-designs/embed/rNBJJvG?height=600&default-tab=result&embed-version=2)