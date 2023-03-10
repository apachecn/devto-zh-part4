# typescript 和 create-react-app。包封/包围（动词 envelop 的简写）

> 原文：<https://dev.to/plasmo/typescript-and-create-react-app-env-136e>

您可以创建一个扩展 NodeJS 命名空间的类型定义文件，以便为您的 env 配置启用自动完成功能。如果您计划减少查找`.env`文件所花费的时间，这非常有用:

[![Auto Completion for your process.env!](img/5d0f2ed8c40f3065521388d8eb658cd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X8MzSdBO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o60b5slh7kjjbp852694.png)

检查底部的解决方案。现在，让我们往回走一点。当我发现`process.env.NODE_ENV`是有类型的并且有代码完成功能，而另一个没有时，我首先想到了这个问题:

[![NODE_ENV has code completion.](img/6db34533e193690f54fd806796573280.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DwLTme6S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/33j0d4duki9z6n66axa7.png)

自从 2017 年左右我第一次看到这条信息，它就一直在我的脑海中。然而，我从来没有真正深入地研究过它，因为那时我不需要利用客户端环境变量。

在 [Plasmo](//www.plasmo.com) ，我们决定利用客户端环境在准备和生产端点之间进行交换。最初有两个变量，这是可以管理的。但是当这个数字增加到 13 时，查找。env 文件，因为变量名越来越长。这种烦恼可以忍受一段时间，因为我们专注于我们的产品。然而，在我的内心深处，有一种强烈的动力去消灭这个“臭虫”

> 从技术上来说，这不是一个错误，这就是为什么有" "。

使用 TypeScript 的一大优势是重构轻而易举，这是快速移动的关键——从一个文件开始，增长到 300 loc，然后将它分成几个文件，冲洗重复。这也适用于我们的类型定义，因为使用单个`.d`(定义)文件来描述我们的 api 类型和主题类型变得复杂了。所以我们决定最终将我们的打字分成这些整洁的文件:

[![typings](img/24ae3383f0f91aacb06c1228a37fa73e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OXhVioqt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/78a4prz5x09tapw7qemn.png)

最后，我可以跟你说再见了。但令我惊讶的是，当后来恢复 CRA (create-react-app)服务器时，那个文件又复活了。在浏览了一下 CRA 的内部软件包后，我发现 react-scripts(支持 CRA 的主要程序)被编程为生成这个文件。

这时候我的大脑开始注意到这个文件的名字:`react-app-env`。edb 的基本规则说，每个神奇的字符串都有意义(在 Windows 上你可以使用 x64dbg)。生成一个具有这种特定名称的文件意味着它有一些重要的用途，可能是在`${process.env.REACT_APP_AUTHOR}`的组织内部。

用搜索词“react-app-env . d . ts declare global”对[duckduckgo.com](//duckduckgo.com)进行更深入的挖掘，[的第三个结果](https://github.com/facebook/create-react-app/issues/5576)(注意:不是第一或第二，而是第三)给出了一些很棒的见解，特别是这个评论中的[。](https://github.com/facebook/create-react-app/issues/5576#issuecomment-433613637)

[![In case the github link got spike traffic<br>
](img/4d12e901839617513faea44a99d6d810.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w-Iwej3M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/09la585qu012i0okbmgm.png)

github 问题的作者试图解决手头的“bug”，代码片段非常有见地:

```
declare namespace NodeJS {
  interface ProcessEnv {
    NODE_ENV: 'development' | 'production' | 'test'
    PUBLIC_URL: string
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

简而言之，CRA 的类型脚本支持使用 NodeJS 名称空间下的扩展定义。为了在我们的环境中扩展它，只需将上面的代码片段放入您的`react-app-env.d.ts`

# 奖金

您现在知道了如何扩展您的环境类型来实现一些不错的自动完成功能。但是现在你需要处理放置在窗口下的全局 API(比如 Stripe.js)。下面将为您省去一趟鸭子之旅(这是我们`react-app-env.d.ts`文件的简化版:

```
/// <reference types="react-scripts" />
declare namespace NodeJS {
interface ProcessEnv {
    NODE_ENV: 'development' | 'production' | 'test'
    PUBLIC_URL: string
    REACT_APP_HASH: string
    REACT_APP_API_URI: string
    REACT_APP_WS_URI: string
    }
}
interface Window {
    Stripe: any
} 
```

Enter fullscreen mode Exit fullscreen mode