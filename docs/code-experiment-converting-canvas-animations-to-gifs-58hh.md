# 代码实验:将画布动画转换为 gif

> 原文：<https://dev.to/melissamcewen/code-experiment-converting-canvas-animations-to-gifs-58hh>

我想我会尝试一个有趣的实验，展示我是如何解决问题的。这里的想法是，你可以看到我是如何工作的，有多乱。在这一过程的每一步，我都会展示我正在处理的代码，即使它很乱。

这些都是小故障“混音”的形式，基本上很容易在每一步创建一个新的副本，所以旧的有我之前在做的。

您会注意到许多代码毫无意义。很多代码被注释掉了。还有很多`console.log`。这基本上是我如何学习我不熟悉的东西。四处打探看看会发生什么。

我的过程基本上是一些混合物:

*   在谷歌、Glitch、Codepen 和 Github 上搜索东西
*   我亲自运行它们，看看它们是如何工作的
*   注释掉代码来分解它，看看什么是重要的，什么是不重要的
*   添加一些东西，看看它是否有效
*   在不同的阶段记录大量的变量，看看它们发生了什么
*   试图结合不同的例子

最近我接手了一个项目，它有一个相当简单的前提。或者至少我是这么认为的。这个想法是在 HTML 画布上制作一个动画，然后把它变成一个很酷的 gif 动画。这样你就可以很容易地在社交媒体上分享它。充分披露我对画布动画一无所知，只知道它的存在。

原来还有很多我不知道的。令人震惊。

[![surprise pikachu](img/78f9c2e683b476ec8091ef9da78001d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lQEfHZh9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e1lfqyg51al07yfdzwne.png)

也证明了浏览器并不真的希望你这样做？我应该记得这篇优秀的文章[GIF 已死。GIF](https://www.popularmechanics.com/technology/a21457/the-gif-is-dead-long-live-the-gif/) 万岁。

关键是网络标准的坚持者已经试图埋葬 gif 很久了。所以他们并不急于在网络浏览器中内置 gif 制作功能。同时，每个人和他们的妈妈都认为网络上的动画= gifs。

但是网页浏览器内置了一些很酷的东西，我想这是为了诱惑我放弃 gif。一个是 [MediaRecorder Web API](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) 。

很多人似乎用它来录制视频，但我找不到很多利用画布动画的例子。我在 GitHub 上找到了一个名为[WebRTC samples Record stream from a canvas](https://webrtc.github.io/samples/src/content/capture/canvas-record/)。

它使用了一个 3d 例子，但我想使用一些 2d 动画，所以我把它移植到 Glitch 并进行测试。

[https://glitch.com/embed/#!/embed/proud-sociology?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/proud-sociology?previewSize=100&path=index.html)

接下来，我在 Github 上偶然发现了这个更简单的 [CanvasRecorder](https://smusamashah.github.io/blog/2018/10/26/CanvasRecorder) 实现。我喜欢它，因为它真的只是这个概念的一个简单易读的实现。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[SMUsamaShah](https://github.com/SMUsamaShah)/[CanvasRecorder](https://github.com/SMUsamaShah/CanvasRecorder)

### 用 JavaScript 录制 HTML5 画布到 webm 视频

<article class="markdown-body entry-content container-lg" itemprop="text">

# 画布记录器. js

[![HitCount](img/4f4d88994951656e1dca17b222beef84.png)](http://hits.dwyl.io/smusamashah/CanvasRecorder)

将画布录制到 webm 视频，而不影响渲染性能。

*注意:只在 Chrome 上测试过，在 Firefox 上应该没问题*

博客文章:[https://smusamashah . github . io/blog/2018/10/26/CanvasRecorder](https://smusamashah.github.io/blog/2018/10/26/CanvasRecorder)

## 如何使用

创建一个记录器

```
const canvas = document.getElementById('animation');
const recorder = new CanvasRecorder(canvas);
```

Enter fullscreen mode Exit fullscreen mode

```
// optional: bits per second for video quality, defaults to 2.5Mbps
const recorder = new CanvasRecorder(canvas, 4500000);
```

Enter fullscreen mode Exit fullscreen mode

开始录音

```
recorder.start();
```

Enter fullscreen mode Exit fullscreen mode

停止录制

```
recorder.stop();
```

Enter fullscreen mode Exit fullscreen mode

保存/下载录像

```
recorder.save();

// Save with given file name
recorder.save('busy_motion.webm');
```

Enter fullscreen mode Exit fullscreen mode

## 它是如何工作的

它基于这个 [WebRTC 样本](https://webrtc.github.io/samples/src/content/capture/canvas-record/)。从画布元素中捕获 [`MediaStream`](https://developer.mozilla.org/en-US/docs/Web/API/MediaStream_Recording_API) ，并用 [`MediaRecorder`](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder) 记录。

</article>

[View on GitHub](https://github.com/SMUsamaShah/CanvasRecorder)

啊，这是一个很好的例子！尽可能简单，这样你就可以很容易地理解它，并且可以随意摆弄它。

效果很好。完美。我受够了。

[https://glitch.com/embed/#!/embed/resolute-hamburger?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/resolute-hamburger?previewSize=100&path=index.html)

开个玩笑。它使 webm 不是 gif。web 标准的人真的很喜欢 webm，但是我不确定是否有人得到了这个备忘录。你不能在推特上分享它们。哦，它在 Safari 中根本不起作用。叹气。

我在这里绕了一点路，探索 Giphy 的 API，应该能够将 webm 转换为 gif，因为该网站是。它还有很好的社交分享功能。但这并没有解决 Safari 或手机的问题。我也是吃了苦头才知道 upload api 不会带 webm。如果我需要记录一个 A 帧的网络虚拟现实场景，我可以从这里开始。

[https://glitch.com/embed/#!/embed/subsequent-legal?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/subsequent-legal?previewSize=100&path=index.html)

以此类推到其他各种“解决方案”。 [Gif.js](https://jnordberg.github.io/gif.js/) 好像还行。但是我想用的很多例子都在 CoffeeScript 中，我真的不太容易读懂。

<figure>

[![I don't know her](img/3c5c3803e10032a468ce6cabebbbc846.png)](https://i.giphy.com/media/wFk7roIpDeXC0/giphy.gif)

<figcaption>Me when I see CoffeeScript</figcaption>

</figure>

正如我说过的，我喜欢简单的例子，并且只用普通的 Javascript。人们更可能使用 Javascript 而不是 CoffeeScript。此外，从普通 Javascript 到 CoffeeScript 通常比反过来更容易。

谢天谢地，我找到了这个 [codepen](https://codepen.io/ford/pen/RKYvNy) ，它包含了一个简单的例子。虽然很搞笑，但这是为了演示 gif.js 的一个问题

[https://codepen.io/ford/embed/RKYvNy?height=600&default-tab=result&embed-version=2](https://codepen.io/ford/embed/RKYvNy?height=600&default-tab=result&embed-version=2)

我把它放在我的旧电脑上，很好

[https://glitch.com/embed/#!/embed/fir-decimal?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/fir-decimal?previewSize=100&path=index.html)

这时，我电脑上的风扇启动了，我担心它会飞走。

[![my laptop getting ready to fly](img/8fad02965f8597fa46ed89ab2618e154.png)](https://i.giphy.com/media/caVdtPeN83dza/giphy.gif)

它说“在浏览器中工作支持:网络工人，文件 API 和类型化数组”嗯。是时候在手机上测试了，这很容易，因为它有故障。

我很震惊，但它确实有效！！也在我的死敌又名狩猎又名新 IE 中工作。太酷了。

下一个候选人。[捕获](https://github.com/spite/ccapture.js/)。但是我看了文档，它使用 gif.js，所以我敢打赌它会使用更多的资源，我的粉丝已经开始锻炼了。我跳过它。

现在我可以绕道生成一个 gif 服务器端，但是我已经在这上面花费了比我计划的更多的时间，所以很遗憾我将离开。另外，我有一些节点“画布”的经验...它和“真正的”画布不一样。

接下来是 [jsgif](https://github.com/antimatter15/jsgif) 但是我浏览了一下文档，然后马上放弃了。

基本上，我得出的结论是所有的解决方案都是不好的，应该被禁止。

看起来 gif.js 是“最好”的解决方案。

我很好奇它能做什么。就像最终的应用程序可能只使用“常规”画布，但我也想过使用 WebGL。我有很多 A-frame 的例子，A-frame 是 Three.js，也就是 WebGL。

可悲的是，它说`TypeError: Argument 1 of CanvasRenderingContext2D.drawImage could not be converted to any of: HTMLImageElement, SVGImageElement, HTMLCanvasElement, HTMLVideoElement, ImageBitmap.`

我花了一些时间，但最终我意识到我在这上面花了太多时间(这就是为什么我在工作时使用计时器，因为我很容易被扯进正题)。

也许是 A-frame 的错。我知道 Glitch 上有一个很好的 [Three.js 示例，所以我会看看它是否有效。](https://glitch.com/~threejs)

同样的错误。但我记得 CCapture 有一些很酷的 3D gifs，所以它可能需要一些东西。现在，我必须指出，对于像这样优先级不高的项目，我不会深入探究并弄清楚它们是如何工作的。如果你正在建造一些你想持续多年的东西，你可能想弄清楚这些东西在更深层次上是如何工作的。我没有。

[https://glitch.com/embed/#!/embed/half-thyme?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/half-thyme?previewSize=100&path=index.html)

在这一点上，我甚至没有考虑 UI。如果你想试试的话。您可以在浏览器控制台中使用 start()和 stop()。有用！

现在我做了一些不好的事情，尝试 A 帧，尽管为什么？？？我尝试的第一个[例子](https://glitch.com/~starter-aframe-fancy)相当复杂，我的电脑现在感觉好像着火了？

[![computer is on fire](img/09921b32d4bb037df08063732d4df938.png)](https://i.giphy.com/media/l4pTjY0qhA2c5abII/giphy.gif)

我不得不强制退出浏览器，然后我用一个简单的例子试了一下，结果成功了！

[https://glitch.com/embed/#!/embed/silly-poison?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/silly-poison?previewSize=100&path=index.html)

很好，但这不是我应该做的。哎呀。

所以我觉得 gif.js 的“解决方案”是最好的。我清理了这个应用程序，这样我以后就可以继续工作了(或者我的同事也可以),或者你可以“重新组合”它，做一些很酷的事情。

<figure>[https://glitch.com/embed/#!/embed/canvas-to-gif?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/canvas-to-gif?previewSize=100&path=index.html)

<figcaption>Canvas example from this [Codepen](https://codepen.io/agar3s/pen/pJpoya)</figcaption>

</figure>

我希望做另一个帖子，在那里我对最终产品的工作，我保证是凉爽的。

由于这是我第一次做这种事情，我很想知道你的提示和技巧！