# 内联事件处理程序 vs addEventListener()

> 原文：<https://dev.to/dillionmegida/inline-event-handlers-vs-addeventlistener-f3l>

*这是第一次发表在我的博客上——[迪里昂的博客](https://dillionmegida.com/p/inline-events-vs-add-event-listeners/)*

当你浏览文章、视频教程或书籍时，你有没有感到好奇，遇到过这样的事情:

```
<!-- Code 1 -->

<button onclick="btnClick()">Click Me</button>

<script>
  function btnClick() {
    console.log('Button Clicked');
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

，

```
<!-- Code 2 -->

<button id="myBtn">Click Me</button>

<script>
  var btn = document.getElementById('myBtn');
  btn.onclick = btnClick;
  function btnClick() {
    console.log('Button Clicked');
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

和

```
<!-- Code 3 -->

<button id='myBtn'>Click Me</button>

<script>
  function btnClick() {
    console.log('Button Clicked');
  }

  var btn = document.getElementById('myBtn');
  btn.addEventListener('click', btnClick, false);
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## ？

嗯，我通常很好奇。所以我深入挖掘，想知道为什么一个比另一个更受青睐。在这篇文章中，我将分享我所学到的。

> 注意:我将引用上面代码中的`Code 1`、`Code 2`和`Code 3`。

### 目录

*   内嵌事件
*   addEventListener()
*   内联事件和 addEventListeners 之间的差异

## -内嵌事件

`Code 1`是内联事件的一个例子。这里，事件是用一个函数作为 HTML 标记的属性来指定的，所使用的函数是在 javascript 区域中指定的(可以在脚本标记或外部 javascript 文件中)。

```
<button onclick="btnClick()">Click Me</button> 
```

Enter fullscreen mode Exit fullscreen mode

这里，一旦点击按钮，就会调用`btnClick()`功能。正如在`Code 1`的 javascript 区域中所指定的，“按钮被点击”被记录到控制台。

想象我们有；

```
<button onclick="function1()" onfocus="function2()" ondblclick="function3">Click Me</button> 
```

Enter fullscreen mode Exit fullscreen mode

这可能很难阅读——因为它只是一个按钮😟。

减少标签中的事件或者完全删除标签中的事件(为了可读性，我强烈推荐)的解决方案就是我们在`Code 2`中所拥有的。你所要做的就是给你的元素一个标识(最好是，`id`，或者 class)。

> 你不一定要给你的元素一个标识。目的只是找到一种方法来轻松定位您的目标元素。

例如，通过`id`，您可以添加尽可能多的事件，例如:

```
<button id="myBtn">Click Me</button>

<script>
  var btn = document.querySelector('#myBtn');
  btn.onclick = function1;
  btn.ondblclick = function2;
  //and so on...
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这种内联 javascript 方法相对于内联 HTML 事件的另一个优点是，内联 HTML 要求事件是全局的。不像内联 javascript 那样可以控制范围(甚至闭包),函数的范围在那里是无法控制的。

> `document.querySelector()`是另一种用于选择元素的方法。[点击这里了解更多信息](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)

## - addEventListener()

如`Code 3`中所见；

```
<script>
  function btnClick() {
    console.log('Button Clicked');
  }

  var btn = document.getElementById('myBtn');
  btn.addEventListener('click', btnClick, false);
</script> 
```

Enter fullscreen mode Exit fullscreen mode

上面使用的`addEventListener()`方法指定了一个函数，当给定事件被传递到(或监听)元素时，该函数将被调用。
`Code 3`为 btn 变量创建一个事件监听器，这样当听到一个点击事件时，`btnClick()`功能被激活，将“按钮被点击”记录到控制台。差不多就是我们其他方法做的，对吧？😕

## -差异

> 我们可能已经知道，`Code 1`和`Code 2`做同样的事情。

### 1。内嵌事件可以被覆盖

带有函数的内联事件可以被覆盖，然而，许多函数可以被附加到一个事件，例如使用`addEventListener()`；

```
<button id="btn1">Button 1</button>
<button id="btn2">Button 2</button>

<script>
  function function1() {
    console.log('Function1');
  }
  function function2() {
    console.log('Function2');
  }
  function function3() {
    console.log('Function3');
  }
  function function4() {
    console.log('Function4');
  }

  var btn1 = document.getElementById('btn1');
  var btn2 = document.getElementById('btn2');

  btn1.onclick = function1;
  btn1.onclick = function2;

  btn2.addEventListener('click', function3, false);
  btn2.addEventListener('click', function4, false);

</script> 
```

Enter fullscreen mode Exit fullscreen mode

当按钮 1 和按钮 2 被点击时，我们在控制台得到这些结果；

```
Function2
Function3
Function4 
```

Enter fullscreen mode Exit fullscreen mode

`var btn1 = document.getElementById('bnt1');`已被第二个函数覆盖。

### 2。使用捕获

值`false`(可以替换为 true)是一个用于 useCaptures 的布尔值。这控制了事件如何传播。传播事件可以是冒泡或捕获。这里的传播指的是如何将事件传递给祖先或子元素，以便这些元素继承事件。
[在此阅读更多关于事件传播的信息](https://www.quirksmode.org/js/events_order.html#link4)

这些是主要的区别。

因此，为了确保 HTML 标签的可读性，建议使用 javascript 环境来处理任何事件。此外，如果不止一个函数或表达式将被应用于一个事件，使用 addEventListeners，否则，内联事件。

在本文中了解更多关于 addEventListeners 及其提供的好处-[event target . addevent listener()](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener) 

即使你不好奇(像我一样)，我也希望你能从中学到一些东西😃

请在评论区投稿或提问。