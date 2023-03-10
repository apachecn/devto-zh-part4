# 项目 twist-开发日志#2(设计)

> 原文：<https://dev.to/jamelh/project-twiist-dev-log-2-design-3klg>

**TL；**
博士我决定着手的第一件事，就是网站设计。这多半是因为这是我的强项(而且相对容易)。

**开发环境**
首先:我在设计网站的时候用的是[glitch.com](https://glitch.com/)。Glitch 是一个非常酷的实时开发网站/程序，它允许我编辑我网站的每个组件，然后在一个新窗口中立即查看我的编辑。当然，一旦我开始将真正的功能编码到网站中，我将不得不导出我的网站并自己托管它，但目前 Glitch 已经完美地工作了。

初步平面设计
虽然编码是我的爱好，但我真的很喜欢平面设计。我精通 Adobe Illustrator 和 Adobe Photoshop，如果不是很流利的话。这给了我这个项目的好处，因为它让我可以快速地为网站创建一些样本标志和设计材料。

*主标志:*

[![](img/f2548afd79216f2c4d08813c8d5b6174.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ky5DY4MF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/a80d5193-a105-43cb-8a36-8f7eeb03d145%252Ftwiist%2520logo%25203d.png%3Fv%3D1565327691685)

*无效果主标志:*

[![](img/647c80d43c6b5b295be5b2b6cf1843d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QD02ZfuK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.glitch.com/a80d5193-a105-43cb-8a36-8f7eeb03d145%252Ftwiist.png%3Fv%3D1565327691563)

网站
的基本 CSS，HTML & JS 在我找到网站的颜色，并设计了标志后，我准备开始设计网站。我有点跳过基本网站 HTML“设计”，因为它真的很简单，但我会在下面概述一些细节。

*首页*

[![](img/9dbb12d772ab25961341a3eddbe6920c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jSTj5jsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y1bun7p5i8zgh2vkobk7.jpg)

为了填满主页，我使用了真正的缩略图、标题、频道名称和来自 YouTube 的浏览量。

[![](img/aae5814062c02e2a54dc7d918df01b54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EMa4YySe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zrxeb7p99xk9pbom2n5y.gif)

每个缩略图上有一个简单的 CSS 动画，使其在光标悬停时向上“悬停”。

```
 div.*videothumbnail*:hover {
cursor:pointer;
bottom:5px;
} 

```

这个简单的代码(`bottom:5px;`)使我能够高效而轻松地制作缩略图动画。正如我所说的，我不是专家，我是自学的，但这是我找到的达到我想要的样子的方法。

[![](img/a8f56109164c8ffd0f45200bfcd3d621.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pa0ne4g3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8d6z5qv3vn2dj55tra9n.gif)

网站上有一个导航栏，隐藏在屏幕的左侧。让它发挥作用的 javascript 也很简单:

```
 function openNav() {
  document.getElementById("mySidenav").style.width = "250px";
  document.getElementById("main").style.marginLeft = "250px";
}

function closeNav() {
  document.getElementById("mySidenav").style.width = "0";
  document.getElementById("main").style.marginLeft = "0";
} 

```

然后我使用`div id="main"`作为页面主体的容器。

*视频页面*

[![](img/6d4d09ab16e55a2ba2ce9a0a1957292d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jzz6nKuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.glitch.com/a80d5193-a105-43cb-8a36-8f7eeb03d145%252Fvideo%2520page.gif%3Fv%3D1565639576326)

你还可以在这张 gif 上看到，网站上还有一个自定义滚动条和视频播放器。

*滚动条代码:*

```
 ::-webkit-scrollbar {
  width: 10px;
}

::-webkit-scrollbar-track {
  background: #f1f1f1; 
}

::-webkit-scrollbar-thumb {
  background: #888; 
}

::-webkit-scrollbar-thumb:hover {
  background: #555; 
} 

```