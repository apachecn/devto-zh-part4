# 使用类名计算子元素数量的普通 JS 等价物

> 原文：<https://dev.to/paramharrison/vanilla-js-equivalent-for-counting-number-of-child-elements-using-classname-4gko>

有时候，我们学到了很多更大的东西，却错过了一些小知识。我想在 vanilla JS 上创建小帖子，在简单的页面上做没有库或框架的事情。

使用 jquery 计算元素数量非常容易。首先我们将在 jQuery 中看到它，然后在 vanilla JS 中看到它。

```
<ul>
  <li class="li-node">Children 1</li>
  <li class="li-node">Children 2</li>
  <li class="li-node">Children 3</li>
  <li class="li-node">Children 4</li>
  <li class="li-node">Children 5</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

### 使用 jQuery 计数

```
$('.li-node').length; 
```

Enter fullscreen mode Exit fullscreen mode

### 使用普通 JS 计数

```
document.querySelectorAll('.li-node').length; 
```

Enter fullscreen mode Exit fullscreen mode

`document.querySelectorAll`选择所有具有类名的元素并返回一个 html 元素的数组，然后我们可以使用 array `length`属性来查找计数。

[https://codepen.io/Param-Harrison/embed/ZgqpWd?height=600&default-tab=result&embed-version=2](https://codepen.io/Param-Harrison/embed/ZgqpWd?height=600&default-tab=result&embed-version=2)

希望你在香草 JS 中学到了一个简单的技巧😎