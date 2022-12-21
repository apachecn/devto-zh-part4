# 棱角分明:黑暗的命运

> 原文：<https://dev.to/sandeepbalachandran/angular-dark-fate-48dp>

嘿伙计们，

猜猜谁又开始写更多的帖子了？哇呜。我。史上最差的介绍。对吗？

[![wohoo](img/fe6f046ac647070a962f2eed8017b6fd.png)](https://i.giphy.com/media/10I5e2yNnaozOo/giphy.gif)

如果有人记得我参加了 30 天盖茨比挑战赛。自从我开始这个挑战，差不多 60 天过去了。我坚持了 5 天。在我的辩护中，我主要是在工作的前端。所以这段时间我跟不上。“你周末到底在做什么”。对吗？事实上我不知道。

[![wohoo](img/3411e9d12c745c875417a15366f69c37.png)](https://i.giphy.com/media/JPsvjVgxpQQgNDApMd/giphy.gif)

我要把这归咎于`Instant gratification monkey`。如果你不知何故，我们不明白参考见下文。
很好解释的内容。

[https://www.youtube.com/embed/arj7oStGLkU](https://www.youtube.com/embed/arj7oStGLkU)

哦等等。差点忘了`motivate`..
故事时间

> 在 14 世纪天山凌雪云杉林深处的某个地方，在真正的武术“学位”之前的训练期间，有一个特定的学生没有通过大多数测试。他非常失望，问主人为什么他总是失败。主人深吸了一口气，决定让他好受些。他问“你有没有注意到不管怎样，太阳每天都从东方升起？“是的”学生回答道。
> “你注意到了吗，不管发生什么，这条河一直在那边流着呢？”
> “是的我做主人”。回答..
> 大师深深吸了口气，说道。
> 
> “你忙着看这些骰子，而不是练习如何通过考试。停止抱怨，练习，让自己沉浸其中”

# <u>新角度 8 变化</u>

## <u>**差载**</u>

Differential loading is a technique which will automatically make your angular apps more performant now

这是怎么回事？
事实证明，在第 8 版中，当你为生产构建应用时，会创建两个包，一个包用于支持 es6 及更高版本的现代浏览器，另一个单独的包用于仅支持 es5 的旧浏览器。当你的应用在浏览器中打开时，会自动加载正确的包，这意味着对于支持 es6 plus 的新浏览器，它们现在将加载更少的代码和更少的聚合填充 从目前收集到的信息来看，这是一个巨大的性能改进，包的大小减少了 7%到 20%,这取决于再次使用的现代 JavaScript 特性的数量，这是一个不需要您添加任何代码的特性，只需运行`ng build --prod`就可以搞定一切。
所以差异加载有利于性能提升。

## <u>**为懒惰航线动态导入**</u>

## <u>**常春藤渲染引擎**</u>

Translating the templates and components into regular HTML and JS that the browser can understand. Not yet in a stable state. But nothing breaks in the current application.

可能在版本 9 中它会是默认的引擎。
您可以使用`ng new project name --enable-ivy`来启用 ivy

它有可能生成相当小的包，使增量编译更容易，也将成为未来创新的基础。

## <u>**数据库**</u>

基本上是一个像 ivy 这样的构建工具，它在版本 8 中还只是实验性的。

我想就是这样了。

你可能需要`typescript 3.4`和`node > 12`

听起来不错。对吧？如果你对 js 有疑问，请到这里。

这个帖子只是为了保持写作的地位，当然还有建设性的批评。所以如果你有什么就把它放在你的..我是说在评论栏里。

[![wohoo](img/b80544ffd0e7c47bd030f8b3cf8322ae.png)](https://i.giphy.com/media/Rhf0uSWt1P2TFqVMZK/giphy.gif)