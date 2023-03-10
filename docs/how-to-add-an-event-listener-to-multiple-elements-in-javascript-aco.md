# 如何在 JavaScript 中向多个元素添加事件监听器

> 原文：<https://dev.to/baransel/how-to-add-an-event-listener-to-multiple-elements-in-javascript-aco>

> 注册我的简讯！。

在 JavaScript 中，使用以下语法向单个元素添加事件监听器:

```
document.querySelector('.my-element').addEventListener('click', event => {
  //handle click
}) 
```

Enter fullscreen mode Exit fullscreen mode

但是如何将同一个事件附加到多个元素上呢？

换句话说，如何同时在多个元素上调用`addEventListener()`？

你可以用两种方法做到这一点。一种是使用循环，另一种是使用事件冒泡。

### 使用循环

从概念上讲，循环是最简单的。

你可以用一个特定的类在所有的元素上调用`querySelectorAll()`，然后使用`forEach()`迭代它们:

```
document.querySelectorAll('.some-class').forEach(item => {
  item.addEventListener('click', event => {
    //handle click
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你的元素没有一个公共类，你可以动态地构建一个数组:

```
[document.querySelector('.a-class'), document.querySelector('.another-class')].forEach(item => {
  item.addEventListener('click', event => {
    //handle click
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 利用事件冒泡

另一种选择是依靠事件冒泡，并在`body`元素上附加事件监听器。

该事件总是由最具体的元素管理，因此您可以立即检查它是否是应该处理该事件的元素之一:

```
const element1 = document.querySelector('.a-class')
const element2 = document.querySelector('.another-class')

body.addEventListener('click', event => {
  if (event.target !== element1 && event.target !== element2) {
    return
  }
  //handle click
} 
```

Enter fullscreen mode Exit fullscreen mode