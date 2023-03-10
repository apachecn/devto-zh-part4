# CSS 锥形渐变-令人敬畏的按钮效果！

> 原文：<https://dev.to/wilsmex/css-canonical-gradients-awesome-button-effects-20om>

你知道吗，现在的 CSS 除了支持标准的线性和径向渐变之外，还支持锥形渐变。如果这是你第一次听到“圆锥形”这个词，那么这篇教程就是为你准备的！视觉学习者:观看 youtube 视频教程([订阅频道！](http://www.youtube.com/c/FollowAndrew?sub_confirmation=1))
[https://www.youtube.com/embed/XMEX9_HvW2k](https://www.youtube.com/embed/XMEX9_HvW2k)

## 什么是锥形渐变？

标准渐变因其视觉上创造的“圆锥”形状而得名。这里一张图胜过千言万语。规范渐变环绕区域的外边界，而不是像下面这样穿过中心:
[![Conic Gradient Visual](img/f828943a0792564ea65df06d5af5aa7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J_kJ3m0i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/os23h6qafok8d62uz11m.png)

## 锥形渐变语法

你可以用和线性渐变相似的语法创建锥形渐变，比如:

```
background: conic-gradient(red, orange, yellow, green, blue); 
```

Enter fullscreen mode Exit fullscreen mode

## 计量单位

需要记住的一件重要事情是，因为锥形渐变使用“角度”作为值，所以我们可以利用非典型的 css 单元类型。以下是可用于这些渐变的可用单位列表:

*   **度** -度(360 度)
*   格拉德 -格拉迪安(400 格拉迪安)
*   **弧度** -弧度(2π弧度)
*   **转** -转(1 转)
*   **%** -百分比(100%)

## 几样:

注意，如果你的浏览器不支持圆锥渐变，(咳咳 Firefox)你可以使用[这个很棒的聚合填充。](https://leaverou.github.io/conic-gradient/)

### 经过重复渐变的旭日背景

```
div{
background: #0ac repeating-conic-gradient(hsla(0,0%,100%,.2) 0 15deg, hsla(0,0%,100%,0) 0 30deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Sunburst](img/774f28f0ae8a5774806bb9f04160a70a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJuYpnPv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wooiz6pqxhgo84nhw28g.png)

### 饼状图有人吗？

请注意，这些对于可访问性来说并不太好

```
div{
    background: conic-gradient(
     red 48deg, green 48deg 195deg, blue 195deg);
    border-radius: 50%
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Pie Chart](img/e863b6b3c5c39bb77afa849dd6695823.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dMvgnAoU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1fiaq5ryvjrtgf82sx62.png)

### 棋盘图案(花式‘透明’图层效果)

```
div{
    background:
    conic-gradient(#fff 0.25turn, #bbb 0.25turn 0.5turn, #fff 0.5turn 0.75turn, #bbb 0.75turn)
    top left / 10% 10% repeat;
    outline: 1px solid;
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Checkerboard](img/f88a024690b5a37555fa4442b77537a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nKPPmAAs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xxh8ktoa7fo3g5h0osrd.png)

### 真“锥”-ic 渐变

```
#five{
    background: conic-gradient(from -28deg, white,black 180deg,white);
    border-radius:50%;
    transition:all .5s;
    transform:rotate(0deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

[![Conic](img/5072236b2c9a7de8bf09b0bc1690d42b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mEdUcCBb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t07e13nrj2vt7pswhcka.png)

### 花式裤扣(Ana Tudors)

[https://codepen.io/thebabydino/embed/qdPRpN?height=600&default-tab=js,result&embed-version=2](https://codepen.io/thebabydino/embed/qdPRpN?height=600&default-tab=js,result&embed-version=2)