# 热门 HTML 面试问题及详细答案🌺🌴😃🤽🏨🏖️第二部分

> 原文：<https://dev.to/theodesp/top-html-interview-questions-with-detailed-answers-part-ii-1j49>

[**此处阅读原文**](https://codethat.today/tutorial/top-html-interview-questions/part-two/)

这是 HTML 面试问题的第二部分。今天，我们回顾一些有趣的话题，如画布，位置 API，文件阅读器 API 等。

我们开始吧💫

### `beforeunload`事件的目的是什么？

就在用户试图导航到另一个页面或关闭窗口之前，`beforeunload`触发。它用于执行一些最终检查，或者提示用户如果用户这样做将会发生的一些副作用。

### 下图中的`location.hash`值是多少:

`http://www.example.com/index.html#part1`

`location.hash`返回 URL 的锚点部分。所以在这种情况下，它将返回`#part1`

### 当`location.hash`发生变化时会触发哪个事件？

当 URL 的片段标识符改变时，触发`hashchange`事件。

### 如果浏览器第一次执行以下命令会怎么样？

`navigator.geolocation.getCurrentPosition(function(pos) {})`

第一次，它将请求用户允许查询设备的当前位置。

### 如何使用 FileReader 对象读取文件的内容？

我们需要一个`input type=file`，用户可以从文件系统或拖放区中选择一个或多个文件。然后我们需要有一个用于传递文件对象列表的`onchange`事件的处理程序。那么要阅读的内容我们需要用到:

```
const reader = new FileReader();
reader.onload = function(e) {
// e.target.result will contain the image data
}
reader.readAsDataURL(file); 
```

Enter fullscreen mode Exit fullscreen mode

### 我们通过将一些文件从桌面拖到指定区域来启动一个拖动事件。我们如何在`drop`事件处理程序中访问这些文件呢？

如果用户拖动文件，这些文件将驻留在 events `dataTransfer`对象中，例如:`e.dataTransfer.files`；

### 服务器发送事件(SSE)和 Websockets 有什么区别？

通过 Websockets，服务器和浏览器建立双向通信(发送和接收)。使用 SSE，只有服务器可以将数据推送到浏览器，反之则不行。

### 我们如何获得 2d 画布的上下文？

我们需要调用画布 getContext API:

`let ctx = canvas.getContext('2d');`

### `<canvas>`元素是否遵循盒子模型？

【T0 号】不使用 CSS 盒子模型。如果你在画布上画东西，它可能会溢出容器。

### 放大 SVG 图像和 JPG 图像会发生什么？

JPG 将像素化，看起来会很模糊。SVG 将保持其清晰度和质量。

### 如何在`<canvas>`元素内直接操纵鼠标指针？

通过使用指针锁定 API，我们可以显示/隐藏指针，将它锁定在一个位置或控制它的移动。

### 我们想在`<video>`元素加载第一帧流时显示一个加载器。我们如何找到这些信息？

由于`<video>`元素是一个`HTMLMediaElement`元素，我们可以使用`networkState`或`readyState`属性来检索关于视频元素状态的信息。

### 一个`<video>`元素中多个`<source>`子元素的用途是什么？

对于每个`<source>`元素，我们指定一个视频，以便浏览器依次尝试每个元素，并使用它支持的第一个视频格式。

### 那两个脚本标签有什么区别？：

```
<script src="app.js" defer></script> <script src="app.js" async></script> 
```

Enter fullscreen mode Exit fullscreen mode

`defer`指定当页面完成解析时执行脚本。另一方面，`async`指定脚本与页面的其余部分异步执行，例如当它加载时，它将被立即执行。

## 进一步资源

*   [指针锁 API](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_Lock_API)
*   [html 媒体元素](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement)