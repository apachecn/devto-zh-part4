# 关于首个商业项目后反应的思考

> 原文：<https://dev.to/metamn/thoughts-on-react-after-the-first-commercial-project-4nkm>

这是未来。它是昂贵的。它不稳定。所有这些都是可以管理的。

## 项目

一个老客户委托我重新设计和重写一个图片组合网站。[需求](https://github.com/metamn/inu-v2-b#requirements)之一是更新旧的堆栈，使其对未来友好。

我选择了 React，并使用了宽松的时间表来充分利用它。

到目前为止，我只是在玩一些小项目来了解生态系统的一部分——我真的很喜欢。认真钻研 React 给我上了一些有趣的课。

## 1。反应是未来

在过去的十年里，我一直在与网络打交道。

我建立了自己的堆栈，学习了各种框架、语言和范例，比如 MVC、Ruby、Ruby on Rails、PHP、WordPress、Yii、BEM、Compass、SASS、Bootstrap、Javascript、jQuery、Foundation、Jekyll、Grunt / Gulp / WebPack、语义 HTML——只要你能想到的。

在所有的反应中，第一个感觉真正*完整*的范例。

通过抛弃 *web* 的概念，转而支持*用户界面*，并把全部赌注押在真正的编程语言上——这是第一次:

1.  我做编程，而不是黑客和调整。
2.  我确实会从视觉上思考设计，而不是使用一些抽象的东西，比如`url`。
3.  我确实用 GraphQL、片段来考虑数据，而不是被像`REST`或`API`这样的东西所约束。
4.  我喜欢主机托管所提供的隔离:所有的东西——数据、风格、逻辑、标记、行为——都在我指尖下的一个屏幕上。
5.  我对自己的代码很有信心。因为它是纯 Javascript(没有`HTML`，没有`CSS`)我确信边缘情况可以被发现，测试并使其工作。
6.  我相信我可以做任何我想做的事情，比以前更容易。
    *   我已经从代码注释中生成了一个需求规范文档。
    *   我将内嵌注释提取到 markdown 文档中，并添加到 Storybook 中，以获得完整的 API 文档。
    *   我已经创建了一个由简单组件驱动的完整的样式指南。
7.  我看到代码是如何与 HTML、CSS 和 Javascript 的最新发展同步的。
    *   项目中最美好的时刻之一是在努力使用滑块/转盘时，经过大量的思考/重构/实践后，最终的解决方案暗示使用了新添加的浏览器功能`CSS scroll snap`技术。
8.  我知道框架/社区/生态系统将为我承担重任，确保自动满足 web 设计和开发的最佳实践；我只需要专注于我的工作，常识是给定的。

总的来说，我觉得所有的垃圾都被删除了，这是我第一次真正做用户界面设计和开发——这可能发生在好的旧网络上。或者其他地方。没关系。

## 2。React 很贵

好事是有代价的。学习和运行 React 是迄今为止框架中最耗时的冒险。

### 2.1 学习

React 以其陡峭的学习曲线而闻名。和争议。难怪——有很多东西要学，其中许多是革命性的。

要进入 React，你必须忘记之前的任何网络经验，学习以下新的范例:

1.  [思考中做出反应](https://reactjs.org/docs/thinking-in-react.html)。
2.  [类型检查](https://reactjs.org/docs/typechecking-with-proptypes.html)。
3.  协同定位数据需求，[数据驱动应用](https://relay.dev/)。
4.  [CSS-in-JS](https://www.styled-components.com/) 。
5.  [挂钩](https://reactjs.org/docs/hooks-intro.html)和异步数据流。

这些只是开始。敢于检查当前的艺术状态，[脸书 2019 年重新设计和重写](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)。

### 2.2 生态系统

每一个新的框架/范式转变都是巨大的，它为解释、偏离和实验留下了空间。这叫围绕新事物的生态系统。

生态系统是混乱的。个人、商店突然出现，提出更好或更差的解决方案、插件、包、模块、风格指南——然后开始宣传他们的方法，让人们坚持下去。

即使有*官方途径*也是被跳过的，很多次，很不幸。

我积极参与了两个这样的生态系统，一个围绕 WordPress，另一个围绕 Ruby 和 Rails。即使这些框架非常固执己见，我也见过许多奇怪的情况，人们认为自己比最初的创造者更聪明。

我听说过类似“我们不能遵循官方指导方针，因为我们公司有特殊需求”的事情，并看到车轮被重新发明，然后结果埋葬了整个团队、产品和运营商。

我一生学到的是:*坚持标准*。

这个转折是有反应的[没有官途](http://metamn.io/react/there-is-no-official-style-guide-for-react/)。事实上它有("[思考反应](https://reactjs.org/docs/thinking-in-react.html)"，"[挂钩规则](https://reactjs.org/docs/hooks-rules.html)")，但它一直称自己为*未独立*。

我不知道背后的动机，但我理解。脸书向世界免费提供了神奇的反应，大力支持它，培育它成为 UI 开发的最佳工具。这应该够了。这已经是一种真正的勇气，我们不能期待更多——对生态系统的所有其他部分都一视同仁。

这种无知/策略(？)导致:

*   使用 Flow 进行类型检查受到如此糟糕的支持~~像~~微软这样的机会主义者用 Typescript / VSCode 取而代之。
*   使用中继的数据驱动应用程序——同样没有得到适当的支持——被 Apollo 简单的“Do GraphQL Right”所取代。
*   CSS-in-JS 被视为继子女，迫使社区接管并归还用户界面设计的乐趣。我试过 6-7 个框架[只有一个单曲幸存](https://www.styled-components.com/)。真是浪费资源。

### 2.3 费用

结合学习和生态系统的困难，我们可以肯定反应将是昂贵的。

为了达到一定程度的自信，我已经把当前的项目重写了三遍。毫无疑问，前两个版本运行得非常完美，但不知何故，到最后我觉得少了点什么，出了点问题。我知道这个结果现在可能会奏效，但从长远来看是不可持续的，它不是以反应方式完成的。

会有很多像我一样的人重新编写一个项目，直到它适合所有人，还会有其他人不会。在后一种情况下，对于支付账单的公司来说，成本将是巨大的。捐助者将得到一些特别的东西，不遵循*标准*，如果碰巧开发团队被改变了，也许整个代码库都必须重写。

无论如何，React 都是关于重写和迁移的。目前在[远离脸书堆栈](https://duckduckgo.com/?q=migrating+code+from+flow+to+typescript&t=canonical&atb=v92-1&ia=web)的方向——但在(可能非常成功的)2019 年脸书重写/重新设计和脸书的[醒来](https://medium.com/flow-type/what-the-flow-team-has-been-up-to-54239c62004f)之后——回到脸书堆栈。

不得不提一下 [Hooks 争议](https://reactjs.org/docs/hooks-intro.html#gradual-adoption-strategy)。五年后，这种语言的核心发生了变化，使得旧的方法——带有样板的类——对于那些学习用新方法反应的人来说无法识别——带有钩子和函数组件。

数十亿行旧代码将被丢弃。

## 3。反应不稳定

再次感觉到系统核心内部有什么不对劲真的很可怕。

上一次我有同样的感觉是在很久以前的 MS-DOS 时代和 BASIC 时代，也许是 Windows 的最初几年。从那以后，PHP、WordPress、Ruby、Rails 和 co 都很顺利。

如果有一个错误信息，它不会在下次突然消失——就像[现在在我当前用官方 Create React 应用程序构建的项目中的](https://github.com/metamn/inu-v2-b/issues/28)。

一条错误信息可以很容易地被谷歌搜索到，或许还能得到解决。现在，不同的浏览器会收到不同的消息，这让修复错误变得有点不现实。

超现实的，就像当前的故事书经历一样，错误来了又去，没有解释。

在成熟的甚至官方的 React 项目中发现了这些 flicks，这使得对 React 的警告更加强烈。好消息是，解决方案可能正在路上，也许被称为[原因](https://reasonml.github.io/)。

## 总结

未来会不会又贵又不稳定？这听起来像当前的全球事务，所以它可能是。

我个人认为恰恰相反。

1.  我在 React 和生态系统中发现了一个真正的用户界面设计和大规模开发工具。
2.  理清思路，坚持即将到来的官方[脸书栈](https://opensource.facebook.com/#frontend) (React，Redux，Relay，Flow，ReasonML)将使代码在项目和团队间可重用。或许也很稳定。
3.  将个人经验浓缩到一个可重用组件的框架中，可以将项目执行时间缩短到正常水平，甚至更短。

## 资源

*   [项目](https://github.com/metamn/inu-v2-b)
*   [旧栈](http://metamn.io/beat/on-old-and-new-stacks/)
*   [2019 年 Facebook.com 重新设计和改写](https://developers.facebook.com/videos/2019/building-the-new-facebookcom-with-react-graphql-and-relay/)
*   【React 没有官方的风格指南
*   [机不可失，时不再来:将 300k LOC 从 Flow 迁移到 Quizlet 的 TypeScript 中](https://medium.com/tech-quizlet/now-or-never-migrating-300k-loc-from-flow-to-typescript-at-quizlet-d3bae5830a1)
*   [流团队一直在做什么](https://medium.com/flow-type/what-the-flow-team-has-been-up-to-54239c62004f)
*   [脸书堆栈](https://opensource.facebook.com/#frontend)