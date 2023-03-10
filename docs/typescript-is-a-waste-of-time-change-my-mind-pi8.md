# 打字稿是浪费时间。改变我的想法。

> 原文：<https://dev.to/bettercodingacademy/typescript-is-a-waste-of-time-change-my-mind-pi8>

我认为打字稿是浪费时间。

然而，考虑到 TypeScript 的支持水平，我知道我肯定会在这里触怒一些打字者，但是我所要求的是你听我说完。

我将提出我认为是对我的立场的理性辩护，然后如果你愿意，你可以在下面的评论中反驳我的立场，我们可以进行富有成效的讨论。🙂

# 等待...先看这部分！！！

就我个人而言，在过去一年左右的时间里，我在各种本地和生产环境中使用过 TypeScript。在此之前，我还同时开发了一个全栈 JavaScript 应用，包括 60000 多行流类型的 Node.js 代码和 40000 多行流类型的 React 代码。

然而，和所有的事情一样，背景是非常重要的。

我相信在某些情况下应该使用 TypeScript 吗？

绝对的！如果你的团队已经在使用 TypeScript，如果你想为其他可能使用 TypeScript 的开发人员编写代码，这真的取决于上下文。但是，我认为，在您的公司项目/您的个人项目/任何一种闭源、非库项目中使用 TypeScript 可能会比不使用 TypeScript 花费更多的时间，考虑到开发时间、错误修复时间、识别和输入库所花费的时间等等。

关于这件事，我有几个要点:

1.  专业人士并不是真正的专业人士。
2.  打字的 JS 又长又难读。
3.  **键入的 JS 仍然是...无类型。**
4.  **非类型化的 JS 有什么不好？**

# 1 -职业选手并不是真正的职业选手。

TypeScript(以及当今几乎所有流行的技术产品)的优点通常都包括这样的陈述:**大公司正在使用它**。

现在，如果你学习打字是为了在一家公司获得一个特定的职位，那么祝你好运——我不是在这里争论它是否能让你被雇佣。

然而，如果你认真想想，这个论点就站不住脚了:

大公司用 TypeScript，所以我应该用 TypeScript。

大公司也使用传统系统，因此我应该使用传统系统。

但是等等，你说，TypeScript 被大公司使用的事实意味着它是经过充分研究和支持的！但是，同样的论点也适用于 Flow(脸书的静态类型库)、React、Angular、Vue、Ember、jQuery、Bootstrap...等等，几乎所有受欢迎的图书馆都有！

《人物》列出的其他优点包括:

*   **现在可以访问未来的功能了** -不需要打字稿，你可以直接使用 Babel。
*   使用旧浏览器 - polyfills 或 Babel。
*   **静态类型和类型推断**——见下文。

# 2 型 JS 又长又难读。

[Kyle Simpson](https://github.com/getify) ，精通 JavaScripter 并且是[你不知道 JS](https://github.com/getify/You-Dont-Know-JS) 的作者，不喜欢使用箭头函数。

*为什么？*因为，我引用:

> 我真的不觉得它们在大多数情况下更具可读性。

这里有一篇他写的文章对此进行了更详细的解释:[https://davidwalsh.name/i-dont-hate-arrow-functions](https://davidwalsh.name/i-dont-hate-arrow-functions)。

现在，我不是试图诉诸权威谬论，但我认为

```
import React from 'react';
import ApolloClient from 'apollo-client';

export interface ApolloContextValue {
  client?: ApolloClient<object>;
  renderPromises?: Record<any, any>;
}

let apolloContext: React.Context<ApolloContextValue>;

export function getApolloContext() {
  if (!apolloContext) {
    apolloContext = React.createContext<ApolloContextValue>({});
  }
  return apolloContext;
}

export function resetApolloContext() {
  apolloContext = React.createContext<ApolloContextValue>({});
} 
```

Enter fullscreen mode Exit fullscreen mode

*代码来源于[https://github . com/apollographql/react-Apollo/blob/master/packages/common/src/context/Apollo context . ts](https://github.com/apollographql/react-apollo/blob/master/packages/common/src/context/ApolloContext.ts)T3】*

可读性远不如这个:

```
import React from 'react';
import ApolloClient from 'apollo-client';

let apolloContext;

export function getApolloContext() {
  if (!apolloContext) {
    apolloContext = React.createContext({});
  }
  return apolloContext;
}

export function resetApolloContext() {
  apolloContext = React.createContext({});
} 
```

Enter fullscreen mode Exit fullscreen mode

一定要看看 Kyle 关于为什么他不喜欢箭头函数的文章...关于我对打字稿的批评，他在那里提出的许多观点听起来也是正确的。

# 3 型 JS 尚...无类型。

在 C 和 C++等真正静态类型的语言中，不同的变量类型在内存中的存储方式是不同的。这意味着在变量中意外存储错误类型的数据是非常困难的，几乎是不可能的。

但是，因为 TypeScript 向下编译成 JavaScript，所以不管您的类型设计得多么仔细，总有可能不同的值类型混入 JavaScript 变量中。这是不可避免的——JavaScript 仍然是非类型化的。

事实是，即使你以各种可能的方式熟练地使用和应用了 TypeScript(或 Flow ),要完全类型化你所有的变量、对象、接口等也是非常困难的。妥妥的。我在一个有 60000 多行代码的 Node.js 项目中使用 TypeScript，我发现我的 Sequelize 数据库模型的接口文件本身就有 5000 多行！我发现这个文件大部分是多余的，告诉我我已经知道的东西，但是我写它的原因是因为 VSCode 一直用红色的曲线提醒我应该键入我的模型。

# 4 -非类型化的 JS 有什么不好？

不，但是说真的-有什么不好的？

[![Untyped JS Bad Meme](img/18b8c0c58e8a8bad50692437145d2a05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqBQLJ2C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tozsm33jq6if3fk7qy0w.jpg)

如果有**硬统计数据**表明使用非类型化的 JavaScript 与类型化脚本相比，生产率会显著下降(如果有的话)，那么我不会介意类型化脚本的冗长。

然而，没有这样的证据，坦率地说，我个人确信非类型化的 JavaScript 更有效率。这就是为什么我认为这是真的。

1.  根据定义，TypeScript 比非类型化的 JavaScript 长。因此，开发时间更长(假设其他条件相同)。
2.  TypeScript 声称的好处(更好的错误处理、类型推断)都不是银弹解决方案。你仍然需要测试，你仍然需要正确地命名你的函数和变量。添加接口或类型并不能解决这些问题。
3.  尽管 TypeScript 在开发中确实捕捉到了一些 bug，但是它们通常是相当小的(在函数需要字符串的地方使用数字),并且通常可以被有经验的程序员捕捉到。关于更复杂的问题(例如，竞争条件、内存泄漏等。)，TypeScript 完全没用。
4.  因此，由于您仍然需要编写相同的测试，花费相同(或非常相似)的时间调试和测试您的代码，同时编写更多的代码(参见前提 1)，因此使用非类型化 JavaScript 比 TypeScript 更有效率。

# 5 -更重要的是，看设计模式，学会辨别代码气味。

这里有一个额外的提示——不要担心是使用类型化脚本、流还是无类型化 JavaScript，要学会通过设计模式和避免代码味道(代码中的结构问题)来编写更好的代码。学会发现这些问题，也要学会重构你的代码，让它写得更好。这种东西是跨语言的，是真正能把你的编程技能提升到一个新的水平，把你从初级开发人员变成高级开发人员的东西。🙂

# 感谢阅读！

对于那些花时间通读我的文章的人，谢谢！真希望我的内容已经很有见地了。

一如既往，我来自一个充满爱和谦逊的地方，如果你在评论中留下非严厉的讨论或批评，我将非常感激。我很想了解更多这方面的知识，并形成自己的观点。

编码快乐！