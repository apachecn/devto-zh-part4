# 答:JavaScript 根据星期几改变 div 的 CSS 属性

> 原文：<https://dev.to/plashal/answer-javascript-change-css-property-of-div-depending-on-day-of-the-week-4b3p>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答回复:JavaScript 根据星期几改变 div 的 CSS 属性](https://stackoverflow.com/questions/45354991/javascript-change-css-property-of-div-depending-on-day-of-the-week/45355075#45355075)

Jul 27 '17[![](img/e3f0373ec76330150a340eacd410b600.png)9![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/45354991/javascript-change-css-property-of-div-depending-on-day-of-the-week/45355075#45355075) </header>

最简单的方法是获取当前日期，然后根据给定元素在其容器中的索引来设置该元素的类，如下所示:

```
$('.coloured-box span').eq(new Date().getDay()).addClass('today');
```

```
.coloured-box span.today { color: #C00; }
```

```
<script src="https://ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
<div class="coloured-box">
  <span>Sunday</span>
  <span>Monday</span>
  <span>Tuesday</span>
  <span>Wednesday</span> 
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/45354991/javascript-change-css-property-of-div-depending-on-day-of-the-week/45355075#45355075)</button>