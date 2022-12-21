# JavaScript 倒计时，有那么简单吗？

> 原文：<https://dev.to/wanoo21/javascript-countdown-is-it-that-simple-31ge>

[![JavaScript Countdown, is it that simple?](img/154ff51194e70884eb913edb2b63b39f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tL29jTZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1523350703530-161b46e28e24%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

有时候写一个简单的**倒计时器**并没有看起来那么简单。你必须以专家的身份玩“新约会”，并不是每次都会有你喜欢的结果。幸运的是，npm 注册表上有大量的 JavaScript 倒计时插件，现在我想向您展示其中的一个。

我相信 javascript 的未来在 **Web 组件**中，所以现在创建的每个插件都应该支持 Web 组件的标准。话虽如此，我还是选择了这个很酷的[倒计时插件](https://www.webcomponents.org/element/@wanoo21/countdown-time)，是的，它是我做的。

在展示一些很棒的例子之前，我要向你更具体地解释一下安装方法，以及他有哪些属性和方法。所以让我们深入研究一下。

## 将倒计时安装到您的项目中

有两种简单的方法:

1.  通过头文件中的脚本标签——这是最好也是最简单的方法！请注意 <mark>1.2.0</mark> 中的当前版本，此时您应该包括最新版本。

```
<script src='https://unpkg.com/@wanoo21/countdown-time@1.2.0/dist/countdown-time.js'></script> 
```

1.  通过 npm 包——这个解决方案最适合实际的框架，比如 **Vue、Angular 或 React** ,不仅如此，你还可以将它与 **Vanilla JS** 一起使用。

*   运行`npm install @wanoo21/countdown-time`
*   导入它`import “@wanoo21/countdown-time”`

然后在你的模板、JSX、HTML 等任何地方包含`<countdown-time></countdown-time>`元素来显示倒计时。当然，你必须添加一些属性来启动它，关于它们我们将在下一节讨论。

另外，它支持**打字稿**，感谢 [StencilJS](https://stenciljs.com/) 给我这么好的机会！

## 倒计时属性【属性】

它有五个自定义属性，让我们看看它们是什么以及它们代表什么:

1.  `[autostart]` -倒计时准备好时是否启动，如果没有，必须手动启动，默认为`false`。

2.  `[datetime]` -倒计时的日期时间，必须是由**字符串**或**数字**表示的有效日期，例如:`Date.now()`，默认为`Date.now()`。

3.  `[add]` -向当前日期时间添加更多时间，用空格分隔，例如:add="1h 30m" -这将向`datetime`时间添加 1 小时 30 分钟，默认情况下，该属性为空。

[https://codepen.io/wanoo21/embed/RzZRWE/?height=600&default-tab=html,result&embed-version=2](https://codepen.io/wanoo21/embed/RzZRWE/?height=600&default-tab=html,result&embed-version=2)

1.  `[format]` -使用该属性改变显示格式，例如:“{ m }分钟。和{ s }秒。，默认为“{h}:{m}:{s}”。这些占位符可用:
    1.  {w} -周数。
    2.  {d} -天数。
    3.  {h} -小时数。
    4.  {m} -分钟数。
    5.  {s} -秒数。

[https://codepen.io/wanoo21/embed/orexOE/?height=600&default-tab=html,result&embed-version=2](https://codepen.io/wanoo21/embed/orexOE/?height=600&default-tab=html,result&embed-version=2)

1.  `[utc]` -使用这个属性你将把`datetime`时间转换成 UTC 格式，默认是`false`。

[https://codepen.io/wanoo21/embed/QXMEdN/?height=600&default-tab=html,result&embed-version=2](https://codepen.io/wanoo21/embed/QXMEdN/?height=600&default-tab=html,result&embed-version=2)

## 倒计时方法

*   `getCountDownTime() => Promise<ITimeObject>` -获得具有`{ weeks: string; days: string; hours: string; minutes: string; seconds: string; }`属性的`ITimeObject`的承诺。
*   `restart() => Promise<void>` -重新开始倒计时。
*   `setAsExpired() => Promise<void>` -设置为过期，该动作将停止并调用`expire`自定义事件。
*   `start() => Promise<void>` -开始倒计时，`autostart`属性也在做同样的动作。
*   `stop() => Promise<void>` -停止倒计时，该动作将停止倒计时，但不会调用`expired`自定义事件。

## 倒计时事件【客户事件】

有两个自定义事件，`expire`和`ready`:

*   `expire` -倒计时结束时发出。
*   `ready` -倒计时准备开始时发出。两人都返回`CustomEvent<void>`。查看一些示例:

[https://codepen.io/wanoo21/embed/OejRyb/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wanoo21/embed/OejRyb/?height=600&default-tab=js,result&embed-version=2)

## 可用的‘插槽’

有一个选项可以根据当前倒计时的状态隐藏或显示一些内容。在`<countdown-time></countdown-time>`中添加任何内容，并添加以下属性:

*   `[show-on-expired]` -仅在倒计时结束时显示该元素。
*   `[hide-on-expired]` -仅在倒计时时显示该元素，并在倒计时结束时隐藏。

[https://codepen.io/wanoo21/embed/WqEoxL/?height=600&default-tab=html,result&embed-version=2](https://codepen.io/wanoo21/embed/WqEoxL/?height=600&default-tab=html,result&embed-version=2)

## 结论

不错吧。通过一些简单的步骤，你可以在你的网站上拥有一个简单而强大的倒计时器。试着玩玩，告诉我你的想法，和你的想法兼容吗？