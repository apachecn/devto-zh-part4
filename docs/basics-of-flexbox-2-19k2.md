# Flexbox 基础知识— 2

> 原文：<https://dev.to/nabendu82/basics-of-flexbox-2-19k2>

### 对准&对中

我们将在这里学习对齐和居中。

我们将在这里使用这个基本的 CSS 并修改它。

[![Basic CSS to be used](img/c1a03a5d3e7733171b0f0abea3676a2b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--lmG5E_L6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3834/1%2AL2H3WLYqlLGv-sT_37gMlA.png) *要用的基本 CSS*

我们将在这里使用 **justify-content** 。这篇 [CSS 窍门文章](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)是一个很好的参考地方。我们将在这里看到所有选项。我们会有一个边界来把事情说清楚。

[![**justify-content: flex-start;**](img/3b56e1eb084137c3c9ea0a48a118fef2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KPA7jLhC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3830/1%2ANJKa-al992aqCfFs909RXw.png)***justify-content:flex-start；***

[![**justify-content: flex-end;**](img/ae3bb7c0139d29421426a7c1f6046ce5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kz3uUZtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3836/1%2AYdBiKJhdj-6A0usMgRRKrA.png)***justify-content:flex-end；***

[![**justify-content: center;**](img/d44cc9f30e784c9725006b109b8b4472.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--e_7F58MW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3828/1%2Ar4UDPXvir4tpiJNvhf1cpA.png) ** *自圆其说——内容:居中；***

[![**justify-content: space-between;**](img/7eb312e79aadebf971193e15f13dfe57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I49jqXoq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3832/1%2A78P8vNbdwasi-OGhugxqgg.png)***justify-content:space-between；***

[![**justify-content: space-around;**](img/0255d15c71bccde95247677af5c4d7e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xats_0by--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3836/1%2Aet7qPvsepSo2YHCgESTJUg.png)***justify-content:space-round；***

[![**justify-content: space-evenly;**](img/aae8df12542f681eb2770f93e0d88911.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--96agZEHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3838/1%2ApQly0z-CncUFgyYV6Bu5XQ.png)***justify-content:space-equally；***

### 伸缩方向

主轴的方向

弯曲方向是主轴的方向。默认情况下，它是从左到右的 ie 行。让我们保持这个设置，然后我们将改变伸缩方向。

```
.box{
 color: white;
 font-size: 30px;
 text-align: center;
 padding: 10px;
}
.container {
 display: flex;
 justify-content: flex-end;
 border: 5px solid gold;
 flex-direction: column;
 min-height: 100vh;
} 
```

现在，当我们制作 **flex-direction: column** 时，它从上到下改变主轴，我们将得到这个。

现在，当我们将 justify-content 更改为不同的属性时，我们将从上到下获取所有内容。

```
.container {
 display: flex;
 justify-content: flex-start;
 border: 5px solid gold;
 flex-direction: column;
 min-height: 100vh;
} 
```

[![justify-content: flex-start;](img/cf718188b709123e2970cb938149361f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CduBe6xZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3838/1%2A2rXmYskujsrIMfXZ9wkrIQ.png)*justify-content:flex-start；*

```
.container {
 display: flex;
 justify-content: flex-end;
 border: 5px solid gold;
 flex-direction: column;
 min-height: 100vh;
} 
```

[![justify-content: flex-end;](img/e33f7022f62dabddd697407af5ad8862.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1j0wN2NV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3838/1%2AVwqMckRfQaOVHWumFqyEYw.png)*justify-content:flex-end；*

```
.container {
 display: flex;
 justify-content: center;
 border: 5px solid gold;
 flex-direction: column;
 min-height: 100vh;
} 
```

[![justify-content: center; — Nice trick to center the content on the screen](img/0d56dfb1e298301357ba7d5b44299ffc.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--5IEa7SVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3834/1%2Ay1Up7h9wUUUlZrU0i-ji7g.png) *自圆其说——内容:居中；—将内容居中显示在屏幕上的绝妙技巧*

```
.container {
 display: flex;
 justify-content: space-between;
 border: 5px solid gold;
 flex-direction: column;
 min-height: 100vh;
} 
```

[![justify-content: space-between;](img/958a3f36e6349bf44065f72288ace737.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--exxyYx0f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3830/1%2A5DTSd6Ek_-a8bUwqWG8PiQ.png)*justify-content:space-between；*

### 对齐项目

在横轴上对齐项目。它与 justify-content 相反。

我们将以此为基础。

```
.box{
 color: white;
 font-size: 100px;
 text-align: center;
 padding: 10px;
}
.container {
 display: flex;
 border: 5px solid gold;
 height: 100vh;
} 
```

我默认的 align-item 值是 stretch

```
.container {
 display: flex;
 border: 5px solid gold;
 height: 100vh;
 align-items: stretch
} 
```

[![align-items: stretch](img/d4fe6152387dfebe74ce53e334854dc9.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--u3S3NYNh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3830/1%2AbsMIxMUAg8L5Qu1LVlAZ-g.png) *对齐-项目:拉伸*

[![align-items: flex-start](img/21f78815fac828d10cf75b4b4c161349.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--pVk_BB_1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3806/1%2A3WwNIwV62iJUYfYYk4PpUA.png) *对齐-项目:灵活-开始*

[![align-items: flex-end](img/8c347b88f971293dd5270705a25a2510.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I-FrQ_Gk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3832/1%2A0zM2SO5dcOEruzRCTjahag.png)T3】align-items:flex-end

[![align-items: center](img/378d778d27d3bee7d909faad76a2c28d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--FltUGqua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3796/1%2A4WxFLXjKsvBqUmJTFTiRZg.png) *对齐-项目:居中*

属性 **align-items: baseline** 非常有用，因为它对齐所有项目的底部。*在同一层对齐页面中的不同文本时，这非常有用。*

我们会用这个。

```
.container {
 display: flex;
 border: 5px solid gold;
 height: 100vh;
 align-items: baseline;
}
.box1 {font-size: 30px;}
.box2 {font-size: 60px;}
.box3 {font-size: 90px;}
.box4 {font-size: 120px;}
.box5 {font-size: 160px;}
.box6 {font-size: 180px;}
.box7 {font-size: 160px;}
.box8 {font-size: 120px;}
.box9 {font-size: 90px;}
.box10 {font-size: 60px;} 
```

[![**align-items: baseline**](img/11a62593373140b6cbfea92fe36564b5.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--IDEr_LGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2864/1%2A__Rv3qmXAKtHsCj7nJts-Q.png) ** *对齐-项目:基线***