# 为什么您应该在 PWA 中使用 Web Share API

> 原文：<https://dev.to/worsnupd/why-you-should-be-using-the-web-share-api-in-your-pwa-14n2>

*交叉发布自[我网站的博客](https://www.danielworsnup.com/blog/why-you-should-be-using-the-web-share-api-in-your-pwa/)。*

渐进式网络应用程序(PWAs)是这些天的所有宣传，并有很好的理由！与传统的 web 应用程序和网站相比，PWAs 提供了显著的用户体验优势。如果你不熟悉渐进式网络应用的概念，我建议你看一看[这篇关于谷歌开发者](https://developers.google.com/web/progressive-web-apps/)的文章。

PWAs 的主要目标是模糊 web 应用程序和原生应用程序(使用原生技术构建的针对特定操作系统的应用程序，如 iOS 和 Android)的 UI/UX 之间的界限。PWA 可以模糊这一界限的方法之一是提供与原生应用相同的原生感觉功能。幸运的是，对于我们这些试图构建伟大的 pwa 的人来说，浏览器供应商一直在努力为我们解锁这些功能，包括将[添加到主屏幕](https://developers.google.com/web/fundamentals/app-install-banners/)、[重新加入推送通知](https://developers.google.com/web/fundamentals/push-notifications/)，甚至[连接到蓝牙设备](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API)！

> 向所有伟大的浏览器供应商大声疾呼，感谢他们继续推动网络平台向前发展！

这篇文章将关注一小部分功能，当正确实现时，将增强应用程序的原生感觉，并有助于增强用户体验:共享。

## 共享

在当今世界，让您的用户能够轻松共享您的内容比以往任何时候都更加重要。但这不仅仅增强了他们的体验——当用户能够在各种平台上轻松分享你的内容时，最终的结果是你的内容的可见性增加了。*你*也一样受益！

> 如果你想扩大你的受众和影响范围，你的内容*需要*易于分享。

传统上，我们 web 开发人员一直负责在我们的 web 应用程序中构建自定义的共享 ui，或者手动，或者利用第三方库或服务。例如， [my website 的博客](https://www.danielworsnup.com/blog/)使用了几个[的 react-share](https://github.com/nygardk/react-share) 按钮和图标作为其自定义的分享 UI，如以下视频所示:

[https://www.youtube.com/embed/Q2CNno4JuJM](https://www.youtube.com/embed/Q2CNno4JuJM)

在没有替代方案的情况下，自定义共享 UI 方法非常好！但是，它有几个主要缺点:

1.  我们无法知道单个用户需要哪些目标份额。不同的用户有不同的(通常是行业/领域特定的)分享需求，所以我们不得不猜测最常需要的分享目标，如脸书、Twitter、Reddit 等。作为后备，我们有时允许用户将 URL 复制到他们的剪贴板。
2.  不同网站上的共享用户界面看起来不一样，迫使用户花点时间去理解他们当前正在浏览的网站上的共享用户界面的功能。此外，对于习惯于设备的原生共享 UI 的用户来说，这是一种令人沮丧的脱节。
3.  我们能够支持的份额目标数量有限。例如，我们不能创建一个按钮，让用户可以直接在 Facebook Messenger 对话中分享内容。
4.  我们必须自己构建 UI，或者依赖第三方库或服务。

有没有一种方法可以同时解决所有这些问题？有！请欢迎 Web 共享 API 上台！

## Web 共享 API

2016 年，Chrome 团队首次在 Android 版 Chrome 61 中推出了 [Web Share API](https://developers.google.com/web/updates/2016/09/navigator-share) 。从那以后，它被更多的浏览器采用(更多的将会出现)。Web Share API 释放了设备的原生共享 UI 的力量，并使其可用于 Web 应用程序。

> 不是 web 应用程序*本身*负责共享 UI，而是 web 共享 API 使 Web 应用程序能够指示*浏览器*显示特定可共享内容的*原生*共享 UI。

这种显示共享 ui 的方法解决了上面提到的所有问题:

1.  浏览器将共享 UI 的呈现交给操作系统(OS)，操作系统知道所有可以作为共享目标的已安装应用程序。然后，操作系统可以根据用户的共享习惯、偏好以及共享内容的具体类型，对某些目标进行优先排序。太神奇了！
2.  向用户呈现他们习惯于在其设备上的本地环境中看到的相同的共享 UI。
3.  所有可以作为共享目标的已安装应用程序都在原生共享 UI 中显示为选项。
4.  我们不需要手动构建任何东西，也不需要依赖任何第三方。浏览器和操作系统负责一切！

这是它在我网站的博客上的样子:

[https://www.youtube.com/embed/CpYLXl0Rm74](https://www.youtube.com/embed/CpYLXl0Rm74)

### 浏览器支持

在撰写本文时，Web Share API 已经在一些移动浏览器中实现，但还没有在桌面和移动设备上得到广泛采用。查看 [Web Share API 我可以使用页面](https://caniuse.com/#feat=web-share)以获取最新的浏览器支持信息。

由于浏览器支持较差，最好的做法是在 Web 共享 API 可用时使用它，当它不可用时回退到自定义共享 UI。我在我的网站上采用了这种方法。在上面的视频中，请注意，如果不支持 Web 共享，本地用户界面是如何被触发自定义用户界面的同一个按钮触发的。开发社区也采用了这种方法。

因为 Web Share API 非常易于使用，所以对于浏览器支持 Web Share 的用户来说，将它添加到您的 Web 应用程序中可以非常迅速地赢得 UX 式的胜利。让我们来看看刚才的*有多简单*吧。

### 使用 Web 共享 API

因为构建和显示共享用户界面的所有艰苦工作已经由浏览器和操作系统负责，所以我们只需做很少的工作就可以让我们的用户通过 Web Share 共享内容。只涉及两个步骤:

1.  验证当前浏览器是否支持 Web 共享
2.  如果是，告诉它分享什么！

当支持 Web 共享时，浏览器在全局`window.navigator`对象上公开一个`share`函数。 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/share)很好地描述了这个功能。看一看！

我们需要做的就是检查 Web 共享是否存在这个函数:

```
if (navigator.share) {
  // Web Share is supported
} else {
  // Web Share is not supported
} 
```

为了减少滥用，支持 Web 共享的浏览器需要调用`navigator.share`来响应用户的手势，比如点击一个共享按钮:

```
myShareButton.addEventListener('click', () => {
  if (navigator.share) {
    // We can call navigator.share here!
  }
})

// We can't call it here 
```

`navigator.share`函数希望你告诉它用户想要分享什么。您用几个可选键将该信息指定为一个对象:`url`、`text`和`title`。尽管您的需求可能会因应用程序的性质而有所不同，但大多数情况下，您希望用户能够共享他们当前正在查看的页面。要做到这一点，你只需要定义`url`和`title`键，我们在浏览器环境中很容易访问到它们:

```
myShareButton.addEventListener('click', () => {
  if (navigator.share) {
    navigator.share({
      url: location.href,
      title: document.title
    })
  } else {
    // Show custom share UI
  }
}) 
```

#### 规范的网址

如果你的应用程序使用[规范的 URL](https://en.wikipedia.org/wiki/Canonical_link_element)，你可以在文档中查询一个规范的 URL，并让这个 URL 优先于`location.href` :

```
const canonicalLink = document.querySelector('link[rel=canonical]')
const url = canonicalLink ? canonicalLink.href : location.href

navigator.share({
  url,
  title: document.title
}) 
```

#### 对份额做出反应

如果你的应用需要对分享动作做出反应，`navigator.share`会为你的方便返回一个承诺。下面的例子使用`await`来处理承诺，但是您也可以轻松地将通常的`.then()`和`.catch()`调用链接起来。

```
try {
  await navigator.share({ url, title })
  console.log('Thanks for sharing!')
} catch (e) {
  console.error('Could not share!', e)
} 
```

差不多就是这么多了！

## 总结

网络共享 API 是网络共享的未来。无论您是否认为您的 web 应用程序是 PWA，它都应该使用可用的 Web 共享 API。检查浏览器支持和有条件地调用 API 只需要几行代码！

### 超越网页分享

构建一个被用户认为提供了与原生应用同等或可比的体验的 web 应用需要大量的工作。[谷歌的这份清单](https://developers.google.com/web/progressive-web-apps/checklist)列举了 PWA 功能的详尽列表，分为基线和示例性功能类别。

你们中的一些人可能已经在构建新的 pwa 或将现有的 web 应用程序迁移到 pwa 的过程中。如果是你，不要让自己被工作量吓倒。不要把注意力放在整个任务的艰巨性上，而是根据预期的影响对功能进行优先排序，一次只关注一个功能，并识别和执行快速成功(例如 Web Share！)才能以放大的速度把你的 app 往正确的方向移动。不要忘记重新评估你的优先事项。

感谢阅读和快乐编码！

#### 喜欢这个帖子？

在 Twitter 上关注我，我在那里发关于前端的消息: [@worsnupd](https://twitter.com/worsnupd)