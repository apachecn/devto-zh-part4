# aria-live，无障碍提示♿

> 原文：<https://dev.to/yashints/aria-live-accessibility-tips-2bmp>

上周我在澳洲墨尔本的 [ComponentsConf](https://www.componentsconf.com.au/) 谈论了使用 Tensorflow.js 的 [JavaScript 和 AI。排队是一个杀手，我听到了一些惊人的演讲，其中一个让我着迷。这是关于可访问性，以及我们如何通过在我们这边做很少的事情来变得更具包容性。](https://www.componentsconf.com.au/schedule)

## 背景

我们都创建了网站或应用程序，许多人可以看到我们的页面，但是，我们没有意识到这些也是由那些看不到它们的人使用的，他们使用辅助技术，如屏幕阅读器。

我这里说的不是静态页面，我们处理的是动态页面，由于我们的朋友 JavaScript，页面中的某个区域发生了变化，而无需刷新整个页面。这可以是更新机票的价格列表，或者即时更新搜索结果。

当这种情况发生时，屏幕读者通常不会意识到，除非我们让他们知道页面上有更新，而这正是`aria-live`区域来救援的地方。

## `aria-live`是如何工作的？

`aria-live`让我们将页面的一部分标记为“活动”，这意味着无论用户关注页面的哪个位置，对该区域的更新都应该通知用户，而不是要求用户在更新发生时在该区域。这意味着他们不会错过更新，即使他们目前正在审查页面的其他部分。

例如，如果用户点击了一个按钮，一个动作完成了，作为结果出现的状态消息应该被传达给他们。

最简单的形式是，`aria-live`看起来像这样:

```
<div class="status" aria-live="polite">Thanks for your feedback.</div> 
```

Enter fullscreen mode Exit fullscreen mode

有三个值可以与`aria-live`属性一起使用，`polite`、`assertive`和`off`。

## 没意义？让我们看看它的实际效果

为了演示它是如何工作的，我为上面的每个值创建了一个带有三个区域的 CodePen。

下面是代码:

```
<h1>Polite</h1>

<button onclick="updateContent('msg')">To view the updates, click this button!</button>
<br/> <br/>

<div id="msg" role="region" aria-live="polite" id="msg"></div>

<h1>Assertive</h1>

<button onclick="updateContent('msg2')">To view the updates immediately, click this button!</button>
<br/> <br/>

<div id="msg2" role="region" aria-live="assertive"  id="msg"></div>
<h1>Off</h1>

<button onclick="updateContent('msg3')">No updates will be announced, so don't click this button 😂!</button>
<br/> <br/>

<div id="msg3" role="region" aria-live="off" id="msg"></div> 
```

Enter fullscreen mode Exit fullscreen mode

以及模拟内容变化的简单函数:

```
function updateContent(id) {  
  document.querySelector(`#${id}`).textContent = 'Content changed: ' + Math.random()
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 让我们保持礼貌😊

`aria-live="polite"`告诉辅助技术在完成当前正在做的任何事情时提醒用户这一变化。如果事情很重要但不紧急，并且占了`aria-live`使用的大部分，那么使用它很好。

你可以在下面的视频中看到它是如何工作的，使用的是我在 [CodePen](https://codepen.io/yashints/pen/jONKgMw) 上创建的一个演示。在这个演示中，我用一个随机数来改变每次点击消息框的内容。您可以看到 windows 讲述人在阅读下一条消息之前先阅读了当前消息，尽管该消息已经更改。

[https://www.youtube.com/embed/kcHXxOzGsU8](https://www.youtube.com/embed/kcHXxOzGsU8)

## 霍利·莫利，⚠️发生了急事

告诉辅助技术中断正在发生的任何事情，并立即提醒用户这一变化。这仅适用于重要和紧急的更新，例如类似“服务器出错，您的更改未保存；请刷新页面”，或者作为用户操作的直接结果的输入字段的更新，例如步进器部件上的按钮。

你可以看到，在断言`aria-live`区域，讲述者在下一条信息一出现就阅读它，即使这意味着它切断了前一条信息。

[https://www.youtube.com/embed/upvYxYOIgQQ](https://www.youtube.com/embed/upvYxYOIgQQ)

## 关机节能😁

`aria-live="off"`告诉辅助技术暂时中止`aria-live`中断。如果更新完成了，你一定要把`aria-live`地区设置为关闭。这将有助于辅助技术停止跟踪动态内容的变化。

## 确保`aria-live`区域有效工作

这是一个强大的工具，但你知道这句谚语:

> 权力越大，责任越大。

### 包括从开始的区域

首先要考虑的是，您必须确保该区域在页面加载时是存在的，并且是空的。这不是一个硬性规定，但如果该地区最初不在那里，许多问题可能会发生。

❌不做:

```
<div class="dynamic-content"></div>

<script>
document.querySelector(".dynamic-content").innerHTML = `<div id="msg" aria-live="polite">
    Content changed!
</div>`
<script> 
```

Enter fullscreen mode Exit fullscreen mode

爱因斯坦:
的

```
<div class="dynamic-content">
  <div id="msg" aria-live="polite"></div>
</div>

<script>
  document.querySelector('#msg').textContent = 'Content changed!'
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 想要隐藏文本却得到它的公布😲？

如果你想让屏幕阅读器宣布你的更新，但又想在视觉上隐藏屏幕上的文本，那么像`display: none`或`visibility: hidden`这样的普通 wat 就有点问题了。这两种情况都会导致更新丢失。

另一个传统的方法是将`text-indent: -9999px`添加到你的元素中，但这有一些性能问题，因为浏览器会在你的元素上画一个 9999 像素的方框(在移动设备上更糟糕)。

那么我们该怎么办呢？有两种方法可以做到这一点:

使用`overflow: hidden`和小宽度和高度:

```
.hide-text { 
  position: absolute;
  width: 1px;
  height: 1px;
  overflow: hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

或者使用`overflow: hidden`和`text-indent: 100%` :

```
.hide-text {
  text-indent: 100%;
  white-space: nowrap;
  overflow: hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

这些是在屏幕上隐藏文本的最有效的方法，同时保持检查性能。

## `aria-atomic`

该属性表示在传递更新时，应该将整个区域视为一个整体。想象一个由日、月和年组成的日期小部件，它有`aria-live=assertive`和`aria-atomic=true`。当用户更改日期值时，将再次读取整个日期。

```
<input type="number" value="02" onchange="updateDate('day', event)"/>/
<input type="number" value="12" onchange="updateDate('month', event)"/>/
<input type="number" value="1983" onchange="updateDate('year', event)"/>

<div class="user-date" aria-live="assertive" aria-atomic="true">  
  <span id="day">02</span>/
  <span id="month">12</span>/
  <span id="year">1983</span>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

而对于它的 JavaScript:

```
function updateDate(type, event) {
  if(event && event.target && event.target.value) {
    document.querySelector(`#${type}`).textContent = event.target.value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您更改任何输入中的步进器，应该会读取整个日期。

**⚠️警告**:以上不适用于 windows 讲述人。然而， [ChromeVox](https://chrome.google.com/webstore/detail/chromevox-classic-extensi/kgejglhpjiefppelpmljglcjbhoiplfn) ，一个我用来进行可访问性测试的 Chrome 扩展，工作正常。

## 还有什么？

您可能还想看看其他有用的属性:

*   `aria-relevant`:表示应该向用户呈现什么类型的变更。
*   `aria-busy`:让你通知辅助技术应该暂时忽略一个元素的变化，比如加载东西的时候。

## 总结

到目前为止，我们已经讨论了`aria-live`的内容、方式和原因。我们还看到了如何隐藏文本而不失去与用户交流更新的能力，此外，我们还看到了如何使用`aria-atomic`让整个区域原子地活起来。

希望这有助于你的旅程，让网络成为一个对每个人都更包容的地方，祝你编码愉快。