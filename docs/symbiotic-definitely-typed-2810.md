# 共生明确型

> 原文：<https://dev.to/qaroev/symbiotic-definitely-typed-2810>

我确实考虑过将这篇文章命名为“如何为不是用 TypeScript 编写的 npm 模块提供良好的 TypeScript 开发人员体验”...虽然不太简洁。

明确类型化是一种资源，它允许开发人员将 TypeScript 与现有的 JavaScript 库一起使用，这些库没有自己的类型定义。

DT 开始是作为一种在 JS 和 TS 之间实现互操作的方式。DT 刚开始的时候，npm 上的一切都是 JavaScript。随着时间的推移，用 TypeScript 编写(或重写)库(如 Mobx / Angular)变得越来越普遍。为了发布，它们被编译成 JS，并带有从 TypeScript 生成的完美类型定义以及编译后的 JavaScript。这些库不再需要以明确的类型存在。

随着时间的推移，出现了另一种模式，即类型定义从明确类型中移除，并与它们支持的库一起维护。这方面的一个例子是 MomentJS。

本周，我认为第一次出现了另一种方法。Kent C Dodds 的 react-testing-library 从 MomentJS 方法开始，将类型定义和 JavaScript 源代码放在一起。Alex Krolic 提出了一个 PR，建议从 RTL 回购协议中删除类型定义，以支持社区维护它们的明确类型。

我将直接引用肯特对此动机的解释:

我们收到了很多对 TypeScript 类型化的驱动贡献，很多 pull 请求要么没有得到足够了解 TypeScript 的人的审查，要么被希望贡献者知道他们在做什么的维护者合并。这导致 TypeScript 用户体验不佳，他们可能会经历类型定义混乱和延迟，这也成为项目维护人员的负担(我们大多数人都不太了解 TypeScript)。将类型定义转移到 DefinitelyTyped 会使维护工作变得更有能力。

我必须承认，我一开始对这个想法保持沉默。我喜欢类型与它们支持的包一起发布的想法。这是一个很好的开发者体验；用户安装你的软件包，它可以直接使用 TypeScript。然而，Alex 的公关解决了一个实际问题:当软件包的作者不感兴趣/不具备/没有时间支持 TypeScript 时，你该怎么办？或者不想处理与他们不相关的打字稿相关的 PRs 的噪音。然后呢？

亚历克斯说，我们不要强迫它。让类型和库分开维护。这可以而且已经做得很好了；React 就是一个恰当的例子。React 团队并不致力于 React 的类型定义，这是由一群专门的 React 爱好者在《确定类型》中(出色地)完成的。

这是一个公平的观点。这一举动的悲哀之处在于，开发者体验将会有更多的摩擦。用户必须使用 yarn add-D @ testing-library/react，然后再使用 yarn add-D @ types/testing-library _ _ react 来获取类型。

这两步过程并不是世界末日，但它确实使 TypeScript 用户开始运行起来稍微困难了一些。它降低了开发者的乐趣。顺便提一下，由于@testing-library/react 是一个限定了作用域的包，这变得更加令人讨厌。作用域包的类型有一个古怪的发布约定。一个虚构的@foo/bar 作用域包将作为@types/foo__bar 发布到 npm。这是功能性的，但不明显；很难发现。两步过程而不是一步过程是一个无用的摩擦，最好能消除。

幸运的是，肯特和丹尼尔·金有过这样的时刻:

Kent 建议，在删除库附带的类型定义的同时，我们可以尝试让@ types/testing-library _*react 成为@testing-library/react 的依赖项。这意味着安装@testing-library/react 的人会自动安装@ types/testing-library*_ react。因此，从开发人员的角度来看，就好像类型定义是直接随软件包一起提供的。

长话短说，事情就是这样。如果你使用的是 9.1.2 版的@testing-library/react，你肯定会被隐藏起来。Kent 很好地说明了这一点，他展示了在明确类型化切换之前的 TypeScript 消费体验:

这是之后的样子:

一模一样！也就是说成功了。我承认这是比较之前/之后的比较中最无聊的一个…但是希望你能看到这证明了这正是我们所需要的。

再次引用肯特的话:

通过将类型定义添加到 React 测试库的依赖项中，用户的体验完全没有改变。因此，对于类型定义的维护来说，这是一个巨大的改进，对于那些定义的用户来说，没有任何破坏性的改变。

这显然是一个有用的方法；它增加了价值。如果看到其他不是用 TypeScript 编写的库也采用这种方法，但希望为那些使用 TS 的人提供良好的 TypeScript 体验，那将是非常棒的。