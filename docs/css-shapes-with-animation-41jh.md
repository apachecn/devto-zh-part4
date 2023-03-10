# 带动画的 CSS 形状

> 原文：<https://dev.to/gvinod1991/css-shapes-with-animation-41jh>

在设计领域，你可以用简单的 CSS 属性创造奇迹，例如，你可以创建一个简单的箭头符号或跳动的心脏。因此，让我们学习如何在 CSS 中创建不同的形状，并为创建的形状添加动画属性。

## 圆圈

要创建一个圆形，我们必须首先创建 div 元素并分配宽度和高度 CSS 属性。添加一些背景色，使边界半径为 50%。

```
.circle{
   width:100x;
   height:100px;
   background:#159AC1;
   border-radius:50%; 
} 
```

Enter fullscreen mode Exit fullscreen mode

## 长方形

要创建一个矩形，我们必须创建一个 div 元素并分配宽度和高度 CSS 属性。添加一些背景色，使其更加清晰可见。

```
.rectangle{
   width:100x;
   height:50px;
   background:#159AC1;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/GodtiVinod/embed/PoYBNae?height=600&default-tab=result&embed-version=2](https://codepen.io/GodtiVinod/embed/PoYBNae?height=600&default-tab=result&embed-version=2)

## 三角形

要创建一个三角形，我们必须像使用 position: absolute 一样使用 border 属性。参见下面的 CSS 代码。

```
.triangle-up{
    position: absolute;
    border-right: 20px solid transparent;
    border-left: 20px solid transparent;
    border-bottom: 20px solid tomato;
}

.triangle-down{
    position: absolute;
    border-top:20px solid tomato;
    border-left:20px solid transparent;
    border-right:20px solid transparent;
}

.triangle-right{
    position:absolute;
    border-right:20px solid tomato;
    border-top:20px solid transparent;
    border-bottom:20px solid transparent;
}

.triangle-left{
    position: absolute;
    border-left: 20px solid tomato;
    border-top: 20px solid transparent;
    border-bottom: 20px solid transparent;
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/GodtiVinod/embed/dybjXRe?height=600&default-tab=result&embed-version=2](https://codepen.io/GodtiVinod/embed/dybjXRe?height=600&default-tab=result&embed-version=2)

## 上弦月

要创建四分之一的月亮，首先，创建圆形白色背景，并应用框阴影

```
.quarter-moon {
    position: relative;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: #fff;
    box-shadow: 10px 10px 0 0 pink;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 心

打造心形，要用前后伪类。我们在动画关键帧中使用缩放旋转和缩放变换属性来显示心跳

```
.heart{
        position: relative;
    }

.heart:before {
    content: "";
    position: absolute;
    width: 50px;
    height: 75px;
    left: 0px;
    border-radius: 50px 50px 0 0;
    background: pink;
    transform: rotate(-45deg);
    animation: heart-pump 1s infinite;
}

.heart:after {
    content: "";
    position: absolute;
    width: 50px;
    height: 75px;
    left: 20px;
    border-radius: 50px 50px 0 0;
    background: pink;
    transform: rotate(45deg);
    animation: heart-pump-1 1s infinite;
} 
```

Enter fullscreen mode Exit fullscreen mode

添加心跳动画

```
@keyframes heart-pump {
    to {
        transform: rotate(-45deg) scale(1.2);
    }
}

@keyframes heart-pump-1 {
    to {
        transform: rotate(45deg) scale(1.2);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/GodtiVinod/embed/KKPBMGg?height=600&default-tab=result&embed-version=2](https://codepen.io/GodtiVinod/embed/KKPBMGg?height=600&default-tab=result&embed-version=2)