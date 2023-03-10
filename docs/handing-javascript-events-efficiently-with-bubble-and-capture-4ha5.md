# JavaScript 中的事件冒泡使用冒泡和捕获有效处理 JavaScript 事件

> 原文：<https://dev.to/shimphillip/handing-javascript-events-efficiently-with-bubble-and-capture-4ha5>

Javascript 使我们的网络应用程序具有交互性。它可以识别用户产生的事件，例如鼠标点击、鼠标滚轮滚动、按下键盘上的键等...平稳地处理这些类型的用户操作对于良好的用户体验非常重要。今天，我们将以鼠标点击事件为例，学习如何有效地处理 JavaScript 事件。

* * *

## `addEventListener`法

JavaScript 有一个名为`addEventListener`的内置方法，您可以将它附加到 HTML 节点上。它总共接受 3 个参数，如下所示:

1.  事件的名称。
2.  当指定的事件被触发时运行一些代码的回调函数。
3.  可选项:捕获的布尔值。(默认设置为 false)。

```
<div id="div1">I am a div1</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const div1 = document.getElementById("div1");

div1.addEventListener("click", function() {
  console.log("div1 clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

如你所料，点击“我是 div”文本将在控制台上显示“div1 clicked”。让我们在 HTML 中用一个新的 div 包装文本。如果你点击文本，你能猜出输出是什么吗？

```
<div id="div1">
  <div id="div2">I am a div1</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const div1 = document.getElementById("div1");

div1.addEventListener("click", function() {
  console.log("div1 clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

即使我们单击了 id 为“div2”的 div，结果也保持不变，并显示“我是 div1”。

* * *

## 事件冒泡

默认情况下，事件在 JavaScript 中冒泡。事件冒泡是指事件将从最内层的嵌套 HTML 元素开始遍历，并沿着 DOM 层次结构向上移动，直到到达侦听事件的元素。这一举措也就是俗称的**事件传播**或**事件委托**。

在上面的例子中，点击文本‘我是 div 1’等同于点击#div2。因为我们在父元素#div1 上有事件侦听器，所以事件从最内部的子元素#div2 开始冒泡。

这里还有一个例子。让我们在 JavaScript 中将一个事件监听器附加到 div2。

```
<div id="div1">
  <div id="div2">I am a div</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const div1 = document.getElementById("div1");
const div2 = document.getElementById("div2");

div1.addEventListener("click", function() {
  console.log("div1 clicked");
});
div2.addEventListener("click", function() {
  console.log("div2 clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是事件冒泡的结果。

```
div2 clicked
div1 clicked 
```

Enter fullscreen mode Exit fullscreen mode

注意我们也可以添加事件监听器到根级元素，比如 html 和 body，事件会一直冒泡。这是等级制度:

**目标- >正文- > HTML - >文档- >窗口**

* * *

## 停止传播

有时，你不希望事件向一个方向移动，那么你可以使用事件对象的`stopPropagation()`。事件对象作为回调函数中的参数提供。

```
...

div2.addEventListener("click", function(event) {
  event.stopPropagation();
  console.log("div2 clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
div2 clicked 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 事件冒泡的实际运用

假设你正在用纯 JavaScript 制作一个待办事项应用程序，用户可以点击一个待办事项来来回切换它是否完成。向每个待办事项添加单独的事件侦听器是不合理的，因为

1.  这个清单可能会很长。(过程变得繁琐。是的，您可以运行一个循环来添加事件监听器，但是在一个应用程序中有太多的事件监听器会消耗大量的浏览器内存，并且会降低应用程序的速度)
2.  可以动态添加新的待办事项。(无法向它们添加事件侦听器)

我们可以通过在包含列表的父元素上附加一个事件监听器来解决这个问题。仔细看看下面的代码做了什么。

```
<ul class="list">
    <li class="item">Wash dishes</li>
    <li class="item">Walk your dog</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

```
.completed {
    text-decoration: line-through;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
const list = document.querySelector(".list");

list.addEventListener("click", e => {
    e.target.classList.toggle("completed")
}) 
```

Enter fullscreen mode Exit fullscreen mode

点击一个项目会将类别`completed`切换到该特定元素，为文本添加删除线。它还生成一个具有`target`属性的事件对象。使用`e.target`指的是被点击的 DOM，你可以添加`classList`和`toggle`来切换一个类。

* * *

## 目标与当前目标

这是你可能会遇到的常见面试问题。您刚刚了解到目标是指触发事件的 DOM。CurrentTarget 将引用事件侦听器正在侦听的 DOM。让我们在函数中控制台记录`e.target`和`e.currentTarget`。

```
const list = document.querySelector(".list");

list.addEventListener("click", e => {
    console.log(e.target); // <li class="item completed">Walk your dog</li>
    console.log(e.currentTarget); // <ul class="list"></ul>
    e.target.classList.toggle("completed")
}) 
```

Enter fullscreen mode Exit fullscreen mode

*如果父元素有一个事件监听器，但是我们停止了子元素中的事件传播，那么 currentTarget 引用停止传播的 DOM*

* * *

## 事件捕捉

要打开它，将`true`作为第三个参数传递给 addEventListener 方法。

```
Element.addEventListener("click", function(){}, true); 
```

Enter fullscreen mode Exit fullscreen mode

这种类型的传播很少使用。它不是从内向外工作，而是翻转方向，从外向内工作。这是等级制度。

**窗口- >文档- > HTML - >正文- >目标**

因此，如果您想首先获得事件正在监听的父元素，您可以使用这个方法。让我们用前面的一个例子。

```
<div id="div1">
  <div id="div2">I am a div</div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const div1 = document.getElementById("div1");
const div2 = document.getElementById("div2");

div1.addEventListener("click", function() {
  console.log("div1 clicked");
}, true);
div2.addEventListener("click", function() {
  console.log("div2 clicked");
}); 
```

Enter fullscreen mode Exit fullscreen mode

结果

```
div1 clicked
div2 clicked 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 总结

仔细倾听用户交互并正确处理是制作无 bug 应用的第一步。记住冒泡字面上是从内到外冒泡，捕捉是当事件落下来的时候！感谢您的阅读！