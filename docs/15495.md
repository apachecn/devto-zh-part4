# 在没有 WebSockets 的浏览器选项卡之间传递事件

> 原文：<https://dev.to/igor_alexandrov/passing-events-between-browser-tabs-without-websockets-3epl>

如果需要将一些事件从一个浏览器选项卡传递到另一个选项卡，该怎么办？我们没有网络插座...

结果是 localStorage 引发了事件😍

也就是说，当在另一个上下文中添加、删除或更改一个项目时，就会触发事件。实质上，这意味着当您在一个选项卡中更改 localStorage 时，其他选项卡可以通过侦听全局窗口对象的 Storage 事件来发现这一点。例如:

```
 window.addEventListener('storage', function (event) {
    console.log(event.key, event.newValue);
  }); 
```

当然，在使用上有一些限制(你可以在这里读到它们[https://www.w3.org/TR/webstorage/](https://www.w3.org/TR/webstorage/))，但是对于简单的情况，它是完美的匹配。