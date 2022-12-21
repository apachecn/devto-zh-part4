# 带有 face-api.js 的实时头像(如浏览器中的 Animoji)

> 原文：<https://dev.to/pragli/live-avatars-like-animoji-in-the-browser-with-face-api-js-6md>

在这篇文章中，我解释了我们如何在我们的应用程序中添加实时头像，以帮助分布式团队中的队友更好地相互交流。

## 背景

Pragli 是远程工作者的虚拟办公室。在 Pragli 中，我希望有一种方式让队友在工作时感觉在一起，而不侵犯隐私。我们开始尝试“永远在线”视频，但那太令人毛骨悚然了。然后，我们决定尝试类似流式 Animoji 的东西。

最后看起来是这样的:

[![Faces](img/12f55b425010234137274f41197095b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O46YOOm8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k45ywttmh4u8etks7tth.gif)

## 工作原理

真人头像非常容易编码。

我们用 React 和 Electron 构建了 Pragli(桌面版)。为了实现实时头像，我们做了以下工作:

**1)添加头像创建流程**

我们使用了 Pablo Stanley 的 [Avataaars](https://avataaars.com) 库(特别是方盆林的[这个项目](https://github.com/fangpenlin/avataaars))。我们为此评估了几个其他库，但是它具有最大的可配置性/多样性(种族、性别表达等)。

**2)用** [**face-api.js**](https://github.com/justadudewhohacks/face-api.js) 检测人脸

我设置 face-api.js 来检测边界框和表达式属性。我玩了眉毛检测，但不能让它感觉正确。

**3)变换活体头像**

一旦检测到人脸，我们使用边界框来水平平移头像。我们试着放大和缩小头像，但是感觉很奇怪，所以我们取消了它。

我们用表情“开心”作为带着牙齿的微笑(“微笑”)，用“中性”作为正常的微笑(“闪烁”)，用“悲伤”作为更中性/严肃的表情(“严肃”)。

<figure>[![](img/2542deb3a842aef9dfa44f9bcb9feba8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K6fT061J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pragli.com/blog/conteimg/2019/08/faces.png) 

<figcaption>从左到右——快乐、悲伤、中性</figcaption>

</figure>

### 发展提示

当我实际看到 face-api.js 使用的视频和照片时，开发就容易多了。下面的例子:

[![Face Test](img/2bdd2da6caef2fe3c79982cb6a97a1fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MJ29k7D8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/siu7a76lxd9fcagkdvl3.gif)

## 问题

这个系统比我们预期的工作得更好，也就是说因为 face-api.js 做得非常好。仍然有一些问题...

**性能**

使用 face-api.js 进行人脸检测可能需要 10 毫秒到 3-4 秒的时间，这取决于它运行了多少次以及计算机的负载情况。

这意味着，有时候，你原本很快的应用程序会有一瞬间变得迟缓。

我通过不太频繁地运行 face-api.js(大约每 5s 运行一次似乎是正确的)并让它在第一次启动时“预热”来最小化这个问题。第一次检测似乎总是需要最长的时间——更多的工作要做(可能只是我是一个糟糕的开发人员)。

**响应度**

因为 face-api.js 需要时间和 CPU，我们把它压缩到不总是运行。这意味着它没有 Animoji 的反应灵敏、真实的面部体验。反而更被动。然而，对于我们的用例——被动呈现，它工作得非常好。

**设备**

如果你连接了多台设备，管理应用程序正在使用的视频设备可能是一场噩梦，特别是因为 Chrome 可能会忽略你选择的设备。

如果你用这个设备做其他事情(就像我们在视频会议中做的那样)，这尤其会成为一个问题——我们仍然有一些公开的错误。

## 接下来是什么

如果你有兴趣尝试真人头像，[注册 Pragli](https://pragli.com/signin) 并邀请你的队友——这是免费的。你必须通过点击左上角的头像，然后选择“直播头像-视频”来打开直播头像