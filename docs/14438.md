# Deno 是什么，和 Node.js 有什么不同？

> 原文：<https://dev.to/bnevilleoneill/what-s-deno-and-how-is-it-different-from-node-js-366g>

[![](img/dfd0cb76ed38e919b4254a0d9ec44b08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oTYnaQTW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qusi5oco6yetoeh0am8e.jpg)

Node.js 的创始人 Ryan Dahl 在过去的一年半时间里一直致力于开发一款新的 JavaScript 运行时软件 [Deno](https://deno.land/) ，该软件有望解决 Node 的所有固有问题。

不要误解我的意思，Node 本身就是一个很棒的服务器端 JavaScript 运行时，这主要归功于它庞大的生态系统和 JavaScript 的使用。然而，Dahl 承认有几件事他应该考虑得更多——安全性、模块和依赖性等等。

在他的辩护中，他无法想象这个平台会在如此短的时间内增长多少。此外，早在 2009 年，JavaScript 还是这种人人取笑的奇怪的小语言，它的许多功能还没有出现。

## 什么是 Deno，它的主要特征是什么？

Deno 是一个安全的 Typescript 运行时，构建于 V8 之上，V8 是用于 JavaScript 的 Google 运行时引擎。

它由以下材料制成:

*   Rust (Deno 的内核用 Rust 编写，Node 的用 C++)
*   Tokio(用 Rust 编写的事件循环)
*   TypeScript (Deno 支持 JavaScript 和现成的 TypeScript)
*   V8(谷歌在 Chrome 和 Node 等平台上使用的 JavaScript 运行时)

所以我们来看看 Deno 提供了哪些功能。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 安全(权限)

Deno 最重要的特性之一是它对安全性的关注。

与 Node 相反，Deno 默认情况下在沙箱中执行代码，这意味着运行时无权访问:

*   文件系统
*   网络
*   其他脚本的执行
*   环境变量

让我们来看看许可系统是如何工作的。

```
(async () => {
 const encoder = new TextEncoder();
 const data = encoder.encode('Hello world\n');

 await Deno.writeFile('hello.txt', data);
 await Deno.writeFile('hello2.txt', data);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

该脚本创建了两个名为`hello.txt`和`hello2.txt`的文本文件，其中包含一条`Hello world`消息。代码是在沙箱中执行的，所以它不能访问文件系统。

还要注意，我们使用的是 Deno 名称空间，而不是 fs 模块，就像在 Node 中一样。Deno 名称空间提供了许多基本的助手函数。通过使用名称空间，我们失去了浏览器兼容性，这将在后面讨论。

当我们通过执行:
来运行它时

```
deno run write-hello.ts 
```

Enter fullscreen mode Exit fullscreen mode

我们会收到以下提示:

```
⚠Deno requests write access to "/Users/user/folder/hello.txt". Grant? [a/y/n/d (a = allow always, y = allow once, n = deny once, d = deny always)] 
```

Enter fullscreen mode Exit fullscreen mode

我们实际上被提示了两次，因为来自沙箱的每个调用都必须请求许可。当然，如果我们选择`allow always`选项，我们只会被问一次。

如果我们选择`deny`选项，将会抛出`PermissionDenied`错误，并且由于我们没有任何错误处理逻辑，流程将会终止。

如果我们用下面的命令执行脚本:

```
deno run --allow-write write-hello.ts 
```

Enter fullscreen mode Exit fullscreen mode

没有提示，两个文件都被创建。

除了文件系统的`--allow-write`标志之外，还有`--allow-net`、`--allow-env`和`--allow-run`标志，分别用于启用网络请求、访问环境和运行子进程。

### 模块

Deno 就像浏览器一样，通过 URL 加载模块。当许多人在服务器端看到带有 URL 的 import 语句时，一开始会感到困惑，但它实际上是有意义的——请耐心等待:

```
import { assertEquals } from "https://deno.land/std/testing/asserts.ts"; 
```

Enter fullscreen mode Exit fullscreen mode

你可能会问，通过 URL 导入包有什么大不了的？答案很简单:通过使用 URL，Deno 包可以在没有像`npm`这样的集中注册中心的情况下分发，最近这个注册中心出现了很多问题，所有问题都在这里[解释](https://www.youtube.com/watch?v=MO8hZlgK5zc)。

通过 URL 导入代码，我们使得包创建者可以在他们认为合适的地方托管他们的代码——这是去中心化的最好体现。**不再有`package.json`和`node_modules`。**

当我们启动应用程序时，Deno 下载所有导入的模块并缓存它们。一旦它们被缓存，Deno 将不会再次下载它们，直到我们用`--reload`标志明确要求它。

这里有几个重要的问题要问:

#### 网站宕机怎么办？

由于它不是一个集中的注册中心，托管该模块的网站可能会因为许多原因而关闭。依赖于它在开发期间——或者更糟，在生产期间——是有风险的。

正如我们之前提到的，Deno 缓存下载的模块。由于缓存存储在我们的本地磁盘上，Deno 的创建者建议在我们的版本控制系统(即 g it)中检查它，并将其保存在存储库中。这样，即使当网站关闭时，所有的开发者仍然可以访问下载的版本。

Deno 将缓存存储在环境变量`$DENO_DIR`下指定的目录中。如果我们不自己设置变量，它将被设置为系统默认的缓存目录。我们可以在本地存储库中的某个地方设置`$DENO_DIR`,并将其签入版本控制系统。

#### 我要一直按网址导入吗？

不断地输入 URL 会非常乏味。谢天谢地，Deno 为我们提供了两个选择来避免这样做。

第一个选项是从本地文件中重新导出导入的模块，就像这样:

```
export { test, assertEquals } from "https://deno.land/std/testing/mod.ts"; 
```

Enter fullscreen mode Exit fullscreen mode

假设上面的文件叫做`local-test-utils.ts`。现在，如果我们想再次使用`test`或`assertEquals`函数，我们可以像这样引用它:

```
import { test, assertEquals } from './local-test-utils.ts'; 
```

Enter fullscreen mode Exit fullscreen mode

所以它是否从 URL 加载并不重要。

第二个选项是创建一个导入映射，我们在一个 JSON 文件中指定它:

```
{
   "imports": {
      "http/": "https://deno.land/std/http/"
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样导入:

```
import { serve } from "http/server.ts"; 
```

Enter fullscreen mode Exit fullscreen mode

为了让它工作，我们必须通过包含`--importmap`标志:
来告诉 Deno 关于导入映射的信息

```
deno run --importmap=import\_map.json hello\_server.ts 
```

Enter fullscreen mode Exit fullscreen mode

#### 包版本控制呢？

版本控制必须得到包提供者的支持，但是从客户端来看，它只是在 URL 中设置版本号，就像这样:`https://unpkg.com/liltest@0.0.5/dist/liltest.js`。

### 浏览器兼容性

Deno 的目标是兼容浏览器。从技术上讲，当使用 ES 模块时，我们不必使用任何像 webpack 这样的构建工具来使我们的应用程序准备好在浏览器中使用。

然而，像 Babel 这样的工具会将代码转换到 JavaScript 的 ES5 版本，因此，代码甚至可以在不支持该语言所有最新特性的旧浏览器中运行。但这也是以在最终文件中包含大量不必要的代码和输出文件膨胀为代价的。

由我们来决定我们的主要目标是什么，并做出相应的选择。

### 开箱即用的打字稿支持

Deno 使得使用 TypeScript 变得容易，不需要任何配置文件。尽管如此，用普通的 JavaScript 编写程序并毫无困难地用 Deno 执行它们是可能的。

## 总结

Deno 是 TypeScript 和 JavaScript 的新运行时，是一个有趣的项目，已经稳步发展了相当长一段时间。但在被认为可以生产之前，它还有很长的路要走。

通过它的分散方法，它采取了必要的步骤，将 JavaScript 生态系统从 npm 的集中包注册中心中解放出来。

Dahl 说他希望在夏天结束前发布 1.0 版本，所以如果你对 Deno 的未来发展感兴趣，就开始关注它的知识库吧。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://logrocket.com/signup/)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Deno 是什么，和 Node.js 有什么不同？](https://blog.logrocket.com/what-is-deno/)最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。