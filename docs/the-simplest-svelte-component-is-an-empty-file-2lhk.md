# 最简单的细长组件是一个空文件

> 原文：<https://dev.to/jesseskinner/the-simplest-svelte-component-is-an-empty-file-2lhk>

在重构我的苗条代码时，我发现了一些令我震惊的事情:

> ![Jesse Skinner profile image](img/bb7c5364e8d13762bffee27875b7261f.png)杰西·斯金纳[@杰西·斯金纳](https://dev.to/jesseskinner)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ sveltejs](https://twitter.com/sveltejs)组件可以是空文件。有多少其他组件框架可以这么说？2019 年 7 月 16 日上午 11:13[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1151087576884961280)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1151087576884961280)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1151087576884961280)

能够使用一个空文件作为一个苗条的组件在重构期间很有用，因为我可以为新组件创建一个占位符文件，导入它并开始使用它:

```
<script>
import Empty from './empty.svelte';
</script>

<Empty/> 
```

Enter fullscreen mode Exit fullscreen mode

当然，它什么也没做，但它也没坏。

我认为这是非常具有象征意义的，是什么让苗条变得如此具有开创性和强大。让我们深入挖掘，看看这个小小的好处能教会我们什么是苗条。

## 一个苗条的组件是一个文件

使用 Svelte，组件和文件是一对一的关系。每个文件都是一个组件，文件不能有多个组件。在使用大多数组件框架时，这通常是一个“最佳实践”。也许这种做法来自于在 Java 或 C++等语言中将每个类放在一个单独的文件中的做法。

通过实施这种做法，Svelte 可以做出一些简化代码的假设。这就引出了我的下一个观点。

## 没有样板文件，只是制作一个新文件

在大多数组件框架中，您需要编写一些代码来定义您的组件。使用 React，最简单的组件是一个空函数。在其他框架中，您需要导入一个库并调用一个特殊的函数来定义和创建您的组件。与苗条，你只是创造一个新的。细长的文件。

Svelte 编译器会自动获取每个文件并为其生成一个组件。这给我们带来了另一个重要的观察结果。

## 你不需要苗条才能使用苗条的组件

为了挂载 React 组件，您需要导入`react-dom`。使用 Vue 组件需要 Vue 库。角度应用程序绝对需要加载角度框架。

另一方面，Svelte 是一个编译器。在某种程度上，Svelte 更像是一种编程语言，而不是一个库。当你用 JavaScript 编程时，你不需要导入一些东西来使用一个`for`循环。同样，您不需要在您的 Svelte 代码中导入任何东西来使用 Svelte 的模板语法。你的苗条的文件被编译成 Javascript 和 CSS。这是一种非常不同的方法。

您可能会猜测一个空文件会编译成一个空的 JavaScript 文件，但是每个 Svelte 组件都有一个 API，允许您在 Svelte 之外使用它，并将其装入 DOM。下面是使用编译后的瘦组件的样子:

```
import Empty from './empty.js';

const empty = new Empty({
  target: document.body,
  props: {
    // if we had some, they'd go here
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们编译我们的空组件并将其与苗条的内部捆绑在一起，它最终是 2080 字节未压缩，1043 字节 gzipped。所以使用 Svelte 的开销最终只有一千字节。相比之下，其他框架仅安装一个空组件就需要 10 倍或 100 倍的字节！

## 苗条是一种新的典范

乍一看，能够使用一个空文件作为组件似乎是一个愚蠢的、不切实际的花招。但是更深入地看，我认为它教会了我们很多关于 Svelte 如何不同于它之前的大多数(如果不是全部的话)JavaScript 组件框架。

我想它会激励其他框架开发人员采取类似的方法，并获得一些相同的好处。这是一种永久改变事物的思维转变。Svelte 不仅仅是一个新的框架，而是一个完整的范式转变。

* * *

对苗条和网页开发感兴趣？[订阅《与杰西一起编码》时事通讯！](https://www.codingwithjesse.com/newsletter)