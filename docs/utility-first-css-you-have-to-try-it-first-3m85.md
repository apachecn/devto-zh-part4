# 实用程序第一 CSS -你必须首先尝试它！

> 原文：<https://dev.to/michi/utility-first-css-you-have-to-try-it-first-3m85>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/utility-first-css)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

几年前，Bootstrap 已经为我们提供了一组实用程序类。例如`text-align: center;`的`text-center`。这些年来，这样的框架开始越来越多的出现。

可能我最喜欢的是间隔工具。`mt-1`代表`margin-top: 2px;`，`px-3`代表`padding-left: 8px; padding-right: 8px;`，`m-auto`代表`margin: auto`等。我不得不为一些抽象的东西发明一个类名的日子已经一去不复返了，这样我就可以给它一些填充。

所以最近发布了[顺风 CSS](https://tailwindcss.com/) 的 1.0 版本。它的方法与传统的 CSS 框架大相径庭。它没有提供高层次的样式和组件，而是保持低层次，只关注实用程序类。

```
<div class="md:flex bg-white hover:bg-gray-200 rounded-lg p-6">
    <img class="h-16 w-16 md:h-24 md:w-24 rounded-full mx-auto md:mx-0 md:mr-6" src="avatar.jpg">
    <div class="text-center md:text-left">
      <h2 class="text-lg">Erin Lindford</h2>
      <div class="text-purple-500">Customer Support</div>
      <div class="text-gray-600">erinlindford@example.com</div>
      <div class="text-gray-600">(555) 765-4321</div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

## 这看起来太可怕了！我的 HTML 就永远毁了！！

这大概是大多数人看到这个的第一反应。嘿，这毕竟打破了语义上的各种最佳实践。

只是感觉不对...直到你亲自尝试！

我再说一遍，你得试出来才能明白！

打开 codepen，尝试从[这里](https://tailwindcss.com/components/#app)重新创建一些东西。

至少对我来说，这种感觉很奇怪**自然**而**富有成效**。不再需要在 HTML 和 CSS 之间切换是一种幸福。想想看，你上一次编辑 HTML 而不同时改变其样式是什么时候？

所以让我们来解决最常见的问题！

## 为什么不直接写内联样式？

*   使用内嵌样式，你会受到很大的限制。没有媒体查询或伪类。

*   有了 tailwind CSS，你可以从一组类中选择，而不是想出一个新的字体大小、颜色、边距、填充等等。每次你添加样式时，你只是从现有的样式集中挑选一些。正如亚当·瓦森所说。这有两大好处。

1.  你将节省大量的时间，不必总是想出新的尺寸和颜色

2.  重复使用相同的尺寸等。自然也会对 UI 提升很多。

### 让我们来看看更多的好处

*   不要再浪费时间发明糟糕的类名，比如`this-wrapper`、`that-body`。

*   断点前缀！我再说一遍，断点前缀！！换句话说，摆脱 99.999%的媒体询问。`xs:p-3`，`md:text-center`...

*   可定制的核心。不喜欢某个楼盘？覆盖配置中的默认值！您甚至可以添加自己的实用程序类，同时仍然利用断点、伪类等。

*   JS 框架不可知。与 vue、react、angular、blade、传统站点等一起开箱即用。这意味着你不必仅仅因为决定使用不同的 JS 框架就去学习一个新的 CSS 框架。

*   发现自己在重复一组课程？没问题，这叫“功利优先”，不是“唯功利”。TailwindCSS 甚至可以帮助你使用现有的工具创建新的类

```
.btn {
  @apply rounded-full text-sm text-white bg-blue-400 shadow-lg;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   你不再需要维护庞大的 CSS/SCSS 文件。毕竟 CSS 发展得非常非常快。

*   这与前面的好处是一致的。有了实用优先的方法，你会更有信心做出改变。

*   没有框架锁定来决定你的按钮等。应该看起来和感觉起来像。这将使你的设计独一无二。

* * *

如果你还有疑问，[看 TailwindCSS 的创作者从零开始做一个网站](https://www.youtube.com/watch?v=0aTRN9CSCY0&list=PL7CcGwsqRpSO3J4YU6BkWqjU0XcVSaPXl)。

TailwindCSS 在[CSS 2019](https://www.freecodecamp.org/news/the-state-of-css-2019-survey-results-are-live/)的状态中也排名极高。

## 结论

> 你知道 GitHub 也在采用这种方法吗？

也许我真的很喜欢它，因为它感觉很清新。也许我忽略了缺点，只关注它提供的好处。这是完全可能的。所以让我们看看一年后我对此的感受。但就目前而言，我真的很享受！

说了这么多，自己尝试一下，希望你会和我一样喜欢！

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。