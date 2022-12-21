# 网络工作者-简介

> 原文：<https://dev.to/pmkroeker/web-workers-introduction-4m3c>

一些[预读](https://dassur.ma/things/when-workers/)如果你愿意！

读了上面的帖子后，我开始思考更多关于网络工作者的问题。为什么更多的人不谈论他们。这么多人似乎都在担心绩效，那么为什么员工讨论没有更加普遍呢？

面向工人的 MDN 文档非常广泛，讨论了从 API 规范到用法的各个方面。

我最初不使用它们的理由是，我不认为真的需要它们。二、初始化方法论

```
const worker = new Worker('/path/to/worker.js') 
```

不能很好地适应完整的 TypeScript 环境。要使用 base call 方法，您需要创建一个单独的`worker.ts`文件来传输，并且它需要位于最终应用程序的正确位置(可能与 app src 的位置有很大不同)。我发现这令人沮丧，所以当时我决定不去理会他们。

然后我看到了来自苏尔马的这个帖子，我开始再次思考他们。有了 data vis 仪表板作为我们的主要应用程序，我们肯定可以从将计算从主线程转移到工作线程中受益。

所以我开始查看上面帖子中提到的一些工人包装器([com link](https://github.com/GoogleChromeLabs/comlink)&[Workerize](https://github.com/developit/workerize))。我从 workerize 开始，然后很快决定我想要一个更深入的多方法方法，所以我决定使用 Comlink！使用 webpack，我还找到了 [webpack 工作加载器](https://github.com/webpack-contrib/worker-loader)。

Comlink 做得很好，让你觉得和你的工人一起工作就像和一个班一起工作。方法易于使用，设置也非常简单！如果你对在你的应用程序中添加工人持观望态度，我建议看看 Comlink，它使使用工人的过程更容易，并且将它与工人加载器配对消除了将事物定义为工人的尴尬。

工人系列的下一篇文章将是关于我的实现！