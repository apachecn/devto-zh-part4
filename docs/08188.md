# 将水平线变成关闭按钮

> 原文：<https://dev.to/voletiswaroop/turn-horizontal-lines-into-a-close-button-1l0g>

在本文中，我将向您展示如何使用简单的 CSS，用最少的代码将水平线变成关闭按钮。

大多数情况下，我们会使用这些水平线来创建一个移动菜单。我们也可以称水平线为导航线。

HTML 代码如下所示:

```
<span> </span>
<span> </span>
<span> </span> 
```

创建水平线的 CSS 代码:

```
.navlines {
    width: 24px;
    height: 24px;
    position: relative;
}

.navlines span {
    position: absolute;
    width: 100%;
    border: 1.5px solid #582c83;
    border-radius: 9px;
    transition: all 0.5s ease;
}
.navlines span:nth-child(1) {
    top: 0px;
}
.navlines span:nth-child(2) {
    top: 10px;
    width: 18px;
}
.navlines span:nth-child(3) {
    top: 20px;
}

```

水平线准备好了，现在我们需要将水平线转换为关闭按钮。

CSS 代码将导航行转为关闭按钮:

```
.navlines:hover span:nth-child(1) {
    -webkit-transform: rotate(45deg);
    -moz-transform: rotate(45deg);
    -o-transform: rotate(45deg);
    transform: rotate(45deg);
    top: 50%;
}
.navlines:hover span:nth-child(2) {
    width: 0%;
    opacity: 0;
}

.navlines:hover span:nth-child(3) {
    -webkit-transform: rotate(-45deg);
    -moz-transform: rotate(-45deg);
    -o-transform: rotate(-45deg);
    transform: rotate(-45deg);
    top: 50%;
}

```

在此演示:

[![Navigation Interaction](img/5acf63c9438fcc1b5a54fc07ca30910e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ty5974xe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--77UnlMTK--/c_imagga_scale%2Cf_auto%2Cfl_progressive%2Ch_420%2Cq_auto%2Cw_1000/https://thepracticaldev.s3.amazonaws.com/i/0jh4pl7lbj8y99oulmob.gif)