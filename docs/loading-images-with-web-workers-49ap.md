# 使用 Web Workers 加载图像

> 原文：<https://dev.to/trezy/loading-images-with-web-workers-49ap>

网络工作者是神奇的。它们允许 JavaScript 中的多线程，这种语言从一开始就是单线程的。它们的实际应用范围从繁重的数字处理到管理异步数据的检索和传播，再到加载图像(我将在本文中讨论)。

事实上，我正在为即将到来的关于网络工作者的演讲做准备，据我的一个好朋友说...

> 当然，特雷兹的演讲是关于网络工作者的。如果他没有和梅格结婚，他会和一个网络工作者结婚。
> — @xlexious

我是说，我想我有点喜欢他们。IT‽怎么样

## 为什么我们要用 web workers 加载图像？

将图像加载从 UI 线程转移到 worker 对于图像密集型网站来说是一个非常好的机会，也是我最喜欢的 web worker 实现之一。它可以防止图像下载阻塞渲染，并可以大大加快您的网站。

*有趣的事实:* `<img>`标签实际上阻塞了你的应用程序负载。如果你的页面上有 100 张图片，浏览器会在呈现页面之前下载全部 100 张图片。

让我们谈一谈在几种不同的环境中实现 web workers。

### 标准，香草实现

要在普通的 JavaScript 应用程序中启动一个 web worker，需要将它放在自己的文件中。让我们假设我们正在我的网站上工作，【https://trezy.com[。我们将我们的工人文件命名为`image-loader.worker.js`，它将在`https://trezy.com/workers/image-loader.worker.js`可用。](https://trezy.com)

我们将从一个非常简单的 web worker 开始，它将注销它接收到的任何数据:

```
/*
 * image-loader.worker.js
 */

// The `message` event is fired in a web worker any time `worker.postMessage(<data>)` is called.
// `event.data` represents the data being passed into a worker via `worker.postMessage(<data>)`.
self.addEventListener('message', event => {
  console.log('Worker received:', event.data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了在我们的主 JavaScript 文件中使用它，我们将这样做:

```
/*
 * main.js
 */

const ImageLoaderWorker = new Worker('/workers/image-loader.worker.js')

ImageLoaderWorker.postMessage('Hello world!') 
```

Enter fullscreen mode Exit fullscreen mode

如果我们加载所有这些，我们应该在控制台中看到`Hello world!`。
🎉呜！🥳

## 让我们开始吧

### 第一步:更新你的标记

您的 worker 实现已经全部解决了，现在我们可以开始实现我们的图像加载器了。我将从我们打算使用的 HTML 开始:

```
<body>
  <img data-srcimg/image1.png">
  <img data-srcimg/image2.png">
  <img data-srcimg/image3.png">
  <img data-srcimg/image4.png">
  <img data-srcimg/image5.png">
</body> 
```

Enter fullscreen mode Exit fullscreen mode

> 坚持住。那不是`<img>`标签的样子！
> —你，刚才。

很敏锐的观察，你！通常你会使用一个`<img>`元素的`src`属性来告诉它从哪里下载图像文件，但是这里我们使用的是`data-src`。这是因为当浏览器遇到带有`src`属性的`<img>`元素时，它会立即开始下载图片。因为我们想把这项工作卸载给我们的 web worker，所以我们使用`data-src`来防止浏览器在 UI 线程上处理下载。

### 第二步:将图片 URL 传递给我们的 web worker

在我们的`main.js`文件中，我们需要检索所有相关的`<img>`元素，这样我们就可以将它们的 URL 传递给我们的 web worker:

```
/*
 * main.js
 */

// Load up the web worker
const ImageLoaderWorker = new Worker('/workers/image-loader.worker.js')

// Get all of the `<img>` elements that have a `data-src` property
const imgElements = document.querySelectorAll('img[data-src]')

// Loop over the image elements and pass their URLs to the web worker
imgElements.forEach(imageElement => {
  const imageURL = imageElement.getAttribute('data-src')
  ImageLoaderWorker.postMessage(imageURL)
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:下载图片

太棒了。现在我们的 web worker 已经收到了一堆图像 URL，让我们来看看如何处理它们。这对于 web 工作者来说有点复杂，原因有几个:

1.  **你没有访问 DOM API 的权限。**许多非 web worker image downloader 实现创建一个新的图像元素，并在其上设置`src`属性，启动下载，然后用新的替换原来的`<img>`。这对我们不起作用，因为没有办法在 web worker 内部创建 DOM 元素。

2.  **图像没有本地的 JavasScript 格式。**图像是由二进制数据组成的，所以我们需要将这些数据转换成可以在 JavaScript 中使用的东西。

3.  ~~**只能使用字符串与 UI 线程通信。**~~ 我已经改正了。过去是这样，但现在不是了！😁

那么，我们如何下载图像，将其从二进制格式转换成 JavaScript 可以使用的格式，然后传递回 UI 线程呢？这就是`fetch`和`FileReader` API 的用武之地。

#### `fetch`是对不止是 JSON

您可能习惯于看到`fetch`用于从一些 API 获取数据，然后调用`response.json()`以对象的形式获取响应的 JSON 主体。然而，`.json()`并不是这里唯一的选择。还有`.text()`、`.formData()`、`.arrayBuffer()`，以及对我们这个练习很重要的`.blob()`。

一个 [`Blob`](https://developer.mozilla.org/en-US/docs/Web/API/Blob) 可以用来表示几乎任何东西，包括没有本地 JavaScript 格式的数据，比如图像！它们非常适合我们正在做的事情。记住这一点，让我们更新我们的 web worker 来接收图片的 URL，并将它们下载为`Blob` s:

```
/*
 * image-loader.worker.js
 */

// I'm making the event handler `async` to make my life easier. If
// you're not compiling your code, you may want to use the Promise-based
// API of `fetch`
self.addEventListener('message', async event => {
  // Grab the imageURL from the event - we'll use this both to download
  // the image and to identify which image elements to update back in the
  // UI thread
  const imageURL = event.data

  // First, we'll fetch the image file
  const response = await fetch(imageURL)

  // Once the file has been fetched, we'll convert it to a `Blob`
  const fileBlob = await response.blob()
}) 
```

Enter fullscreen mode Exit fullscreen mode

好的，我们有进展了！我们已经更新了我们的图像，因此它们不会自动下载，我们已经获取了它们的 URL 并将其传递给工作人员，并且我们已经将图像下载到浏览器！

### 第四步:将图像数据返回给 UI 线程

既然我们已经获得了 blob 形式的图像，我们需要将它发送回 UI 线程进行渲染。如果我们单独将字符串发送回去，那么 UI 线程将不知道在哪里呈现它。相反，我们将发回一个对象，告诉 UI 线程渲染什么*和*，其中:

```
/*
 * image-loader.worker.js
 */

self.addEventListener('message', async event => {
  const imageURL = event.data

  const response = await fetch(imageURL)
  const blob = await response.blob()

  // Send the image data to the UI thread!
  self.postMessage({
    imageURL: imageURL,
    blob: blob,
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们的工人档案做好了！最后一步是处理我们在 UI 线程中收到的内容。

### 第六步:渲染图片！

我们已经**了，所以**就要完成了！我们需要做的最后一件事是更新 main.js 文件，以接收和处理从 web worker 返回的图像数据。

```
/*
 * main.js
 */

const ImageLoaderWorker = new Worker('/workers/image-loader.worker.js')
const imgElements = document.querySelectorAll('img[data-src]')

// Once again, it's possible that messages could be returned before the
// listener is attached, so we need to attach the listener before we pass
// image URLs to the web worker
ImageLoaderWorker.addEventListener('message', event => {
  // Grab the message data from the event
  const imageData = event.data

  // Get the original element for this image
  const imageElement = document.querySelectorAll(`img[data-src='${imageData.imageURL}']`)

  // We can use the `Blob` as an image source! We just need to convert it
  // to an object URL first
  const objectURL = URL.createObjectURL(imageData.blob)

  // Once the image is loaded, we'll want to do some extra cleanup
  imageElement.onload = () => {
    // Let's remove the original `data-src` attribute to make sure we don't
    // accidentally pass this image to the worker again in the future
    imageElement.removeAttribute(‘data-src’)

    // We'll also revoke the object URL now that it's been used to prevent the
    // browser from maintaining unnecessary references
    URL.revokeObjectURL(objectURL)
  }

  imageElement.setAttribute('src', objectURL)
})

imgElements.forEach(imageElement => {
  const imageURL = imageElement.getAttribute('data-src')
  ImageLoaderWorker.postMessage(imageURL)
}) 
```

Enter fullscreen mode Exit fullscreen mode

查看 [Codepen 演示](https://s.trezy.sh/image-worker-main),一切都在一起工作:

[https://codepen.io/trezy/embed/ZNdbeQ?height=600&default-tab=result&embed-version=2](https://codepen.io/trezy/embed/ZNdbeQ?height=600&default-tab=result&embed-version=2)

## 奖励:用 Webpack 实现 web workers

如果你使用 Webpack 来编译你所有的代码，还有一个很好的选项来加载你的 web workers: [`worker-loader`](https://github.com/webpack-contrib/worker-loader) 。这个加载器允许您将 web worker 导入到一个文件中，并将其初始化，就像它是一个常规类一样。

我觉得这样感觉也更自然一点。在不改变`image-loader.worker.js`内容的情况下，如果您在 Webpack 配置:
中设置了`worker-loader`，这将是一个实现的样子

```
/*
 * main.js
 */

import ImageLoaderWorker from './workers/image-loader.worker.js'

const imageLoader = new ImageLoaderWorker

imageLoader.postMessage('Hello world!') 
```

Enter fullscreen mode Exit fullscreen mode

就像在我们的普通实现中一样，我们应该在控制台中看到`Hello world!`被注销。

## 结论

我们完事了。在使用几种不同的浏览器 API 时，将图像下载卸载给网络工作者是一个很好的练习，但更重要的是，这是一个加速网站渲染的好方法。

请务必在下面的评论中留下你的问题和建议。告诉我你最喜欢的 web 工作者使用的，最重要的是，让我知道我是否在我的例子中错过了一些很棒的东西。

## 更新

*2019 年 11 月 13 日*

*   基于[@单色](https://dev.to/monochromer)的[评论](https://dev.to/monochromer/comment/hlgh)增加了`URL.revokeObjectURL(objectURL)`调用。
*   修正了几个错别字。