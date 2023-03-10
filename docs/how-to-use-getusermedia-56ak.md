# 如何使用 getUserMedia()

> 原文：<https://dev.to/baransel/how-to-use-getusermedia-56ak>

由`navigator.mediaDevices`暴露的`MediaDevices`对象给了我们`getUserMedia`方法。

> 警告:`navigator`对象也公开了一个`getUserMedia()`方法，这个方法可能仍然有效，但是**不赞成使用**。为了保持一致性，API 被移到了`mediaDevices`对象中。

这就是我们如何使用这种方法。

假设我们有一个按钮:

```
<button>Start streaming</button> 
```

Enter fullscreen mode Exit fullscreen mode

我们一直等到用户点击这个按钮，然后我们调用`navigator.mediaDevices.getUserMedia()`方法。

我们传递一个描述我们想要的媒体约束的对象。如果我们想要视频输入，我们将调用

```
navigator.mediaDevices.getUserMedia({
  video: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以非常具体地说明这些约束:

```
navigator.mediaDevices.getUserMedia({
  video: {
    minAspectRatio: 1.333,
    minFrameRate: 60,
    width: 640,
    heigth: 480
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过调用`navigator.mediaDevices.getSupportedConstraints()`获得设备支持的所有约束的列表。

如果我们只是想要音频，我们可以通过`audio: true` :

```
navigator.mediaDevices.getUserMedia({
  audio: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们两者都想要:

```
navigator.mediaDevices.getUserMedia({
  video: true,
  audio: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个方法返回一个承诺，所以我们将使用 async/await 在一个`stream`变量中获得结果:

```
document.querySelector('button').addEventListener('click', async (e) => {
  const stream = await navigator.mediaDevices.getUserMedia({
    video: true
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

单击该按钮将在浏览器中触发一个面板，以允许使用媒体设备。

一旦我们完成了，我们从`getUserMedia()`得到的`stream`对象现在可以用于各种事情。最直接的方法是在页面的`video`元素中显示视频流:

```
<button>Start streaming</button>
<video autoplay>Start streaming</button> 
```

Enter fullscreen mode Exit fullscreen mode

```
document.querySelector('button').addEventListener('click', async (e) => {
  const stream = await navigator.mediaDevices.getUserMedia({
    video: true
  })
  document.querySelector('video').srcObject = stream
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们添加一个按钮来访问摄像机，然后添加一个`video`元素，带有`autoplay`属性。

```
<button id="get-access">Get access to camera</button>
<video autoplay></video> 
```

Enter fullscreen mode Exit fullscreen mode

JS 监听按钮上的点击，然后调用`navigator.mediaDevices.getUserMedia()`请求视频。然后我们通过调用`getUserMedia()`的结果调用`stream.getVideoTracks()`来访问所用摄像机的名称。

流被设置为`video`标签的源对象，这样回放就可以发生:

```
document.querySelector('#get-access').addEventListener('click', async function init(e) {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: true
    })
    document.querySelector('video').srcObject = stream
    document.querySelector('#get-access').setAttribute('hidden', true)
    setTimeout(() => { track.stop() }, 3 * 1000)
  } catch (error) {
    alert(`${error.name}`)
    console.error(error)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

getUserMedia()的参数可以指定对视频流的附加要求:

```
navigator.mediaDevices.getUserMedia({
  video: {
    mandatory: { minAspectRatio: 1.333, maxAspectRatio: 1.334 },
    optional: [
      { minFrameRate: 60 },
      { maxWidth: 640 },
      { maxHeigth: 480 }
    ]
  }
}, successCallback, errorCallback); 
```

Enter fullscreen mode Exit fullscreen mode

要获得音频流，你也需要音频媒体对象，并调用`stream.getAudioTracks()`而不是`stream.getVideoTracks()`。

回放 3 秒钟后，我们通过调用`track.stop()`来停止视频流。