# 你说的事件？我听着呢...

> 原文：<https://dev.to/nfairbairn/events-you-say-i-m-listening-5662>

HTML 事件是发生在 HTML 元素上的“事情”。在 HTML 页面中使用 JavaScript 时，JavaScript 可以对这些事件做出“反应”。

这就像一个号召和回应，或者一个行动和反应。

事件侦听器只是对指定动作的指定响应。

您可以将事件侦听器附加到 HTML 元素，以便在必要时执行某些操作。

**公共事件监听器**

*   DOM 内容加载——等待运行脚本，直到页面加载
*   单击时-当用户单击一个元素时执行一个功能
*   提交-当用户提交表单时执行一个功能

事件监听器可以这样写:

```
Element.addEventListener ( event , function )
Element.addEventListener(event, () => {} ) 
```

事件侦听器对于简单的 UI/UX 任务非常有用，比如点击元素、滚动或提交表单！

事件侦听器不仅允许您在单击按钮时更改按钮的颜色，还允许您在幕后处理和执行功能。

例如，当在浏览器游戏中创建一个 JavaScript 时，如何让用户输入来控制任何东西？

您使用事件监听器！当用户点击箭头键时，您可以使用`keyup` `keydown` `keyleft`和`keyright`来执行功能！

```
Element.addEventListener("keyup", () => { jump() }; 
```

仅此一点就为浏览器页面打开了一个全新的交互可能性列表！无需刷新页面！

其他更复杂的事件侦听器可以用于任何事情，从查找光标在页面上的坐标，查看用户是否按下了某个键，到查找您在某个区域单击鼠标的次数。

事件监听器也有很好的特性，比如:

您可以向同一个元素添加许多事件，而无需覆盖现有事件

```
 let box = document.getElementById("test-box");

box.addEventListener("click", () => {
 box.textContent = "I Just Got Clicked!";
});

box.addEventListener("mouseover", () => {
 box.style.color = "red";
}); 
```

你可以在用户调整窗口大小时启动一个函数

```
window.addEventListener("resize", () => {
  document.getElementById("box").textContent = "I'm getting claustrophobic already!";
}); 
```

您可以将参数传递给事件监听器中调用的函数，用于更具体的反应，或者如果动作需要输入

```
function sum(a, b) {
 return a + b;
}

let a = 2
let b = 3

box.addEventListener("submit", () => {
 sum(a,b)
}); 
```

在创建名为 useCapture 的事件侦听器时，还有一个可选的第三个参数。它是一个布尔值，表示同一父元素中元素的事件层次结构。

```
Element.addEventListener(event, function, true);
Element.addEventListener(event, function, useCapture); 
```

默认情况下，这是 false，并使用`bubbling`来声明首先处理最内部元素的事件，然后处理外部元素的事件。

`capturing`相反，首先处理最外层元素的事件，然后处理内层元素的事件

这些事件监听器打开了许多与站点交互的大门。如果没有听众的行动和反应，我们将只是操纵静态页面或在每次你喜欢一篇文章时刷新页面！

JavaScript 超能力！
[![almighty](img/099a7173601d58302065044883622e1a.png)T3】](https://i.giphy.com/media/3fJlTgnz9210s/giphy.gif)