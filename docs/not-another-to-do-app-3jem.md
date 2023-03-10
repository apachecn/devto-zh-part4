# 不是另一个待办事项应用程序:第 2 部分

> 原文：<https://dev.to/westbrook/not-another-to-do-app-3jem>

#### 用 Open Web 组件推荐弄脏你的手和脚...算是吧。

> *这是 2019 年 2 月 26 日来自 [Medium](https://medium.com/@westbrook/not-another-to-do-app-762ea0bb960) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️:如果这是你第一次阅读，欢迎！*

*欢迎来到“不要再做一个应用程序”，这是一篇冗长的综述，讲述了每个开发人员在某个时候都会编写的最小的应用程序之一。如果你是来学习编写应用程序的特定技术的，或者是从以前的安装中走出来的，那么你可能来对地方了，应该继续读下去！如果没有，你可能想[从头开始](https://dev.to/westbrook/not-another-to-do-app-2kj9)，这样你也可以知道[我们所有角色的背景故事...](https://github.com/Westbrook/to-do-app)*

如果你已经做到了这一步，为什么现在就放弃呢？

* * *

# 入门

<figure>

[![Alt Text](img/63248002e7bf377efce7d1bb4a7667f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vmEQLtKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/py0ff6a6pi7wflvr1ysa.jpeg)

<figcaption>Photo by [Tim Gouw](https://unsplash.com/@punttim) on [Unsplash](https://unsplash.com/)</figcaption>

</figure>

我为自己设定了所有这些崇高的目标，我该从哪里开始呢？

空白页通常是任何创造性努力中最可怕的部分，克服它的最好方法是跳过它！

有越来越多的工具可以帮助您开始(在您开始之前)基于 web 组件的应用程序。我过去经常使用的 OG 工具是[聚合物 CLI](https://www.npmjs.com/package/polymer-cli) 。一个简单的`polymer init`经常会给你一种感觉，你已经跳过了大约 50%的工作。然而，去年 Polymer 3 向 ES 模块和 NPM 生态系统的转移给了开发者比以往更多的选择。作为`polymer init`的精神继承者，Polymer 团队最近宣布了 [PWA 初学者工具包](https://github.com/polymer/pwa-starter-kit)，它除了让你开始使用基于 web 组件的应用程序之外，还提供了一些很好的基线[模板选项](https://pwa-starter-kit.polymer-project.org/overview#other-templates)，以及关于将 [Redux](https://redux.js.org/) 绑定到你的项目中或者用 [TypeScript](https://www.typescriptlang.org/) 编写它的建议。如果你是一个基于 Redux 的状态管理的爱好者，或者有兴趣第一次熟悉它，这是一个很好的项目，可以学习如何将许多强大的 javascript 工具捆绑在一起，使其成为你下一个(要做的)应用程序的基础。然而，为了划掉我的一个研究待办事项，对于我的新待办事项应用程序，我将检查一个有趣的名为 [Open Web Components](https://dev.to/thepassle/announcing-open-web-components-5h7) 的新社区项目。他们的哲学始于一种强烈的:

> 开放 web 组件的目标是为开发人员提供强大且久经考验的工具来创建和共享开源 Web 组件。

这似乎是你包里的一套不错的工具，对吧？我强烈推荐的一个例子是他们的 LitElement 的[示例使用列表，当你第一次开始使用这个非常有前途的基类开发 web 组件时，以及当你的记忆无法引导你找到你想要应用的设计模式时，这是一个惊人的资源。更适合我当前构建待办应用程序目标的是一套](https://open-wc.org/developing/#examples)[生成器](https://github.com/open-wc/open-wc/tree/master/packages/generator-open-wc)，它可以让你快速入门，或者以最好的方式加速已经开始的 web 组件开发过程。从他们的一些[子发电机](https://open-wc.org/developing/generator.html#sub-generators)建造，最新的开放式厕所产品之一是[启动器应用](https://github.com/open-wc/open-wc-starter-app)。默认情况下，它获得了一个扩展的 webpack 配置，该配置为开发和生产构建而构建，由[漂亮的](https://prettier.io/)和 [ES Lint](https://eslint.org/) 支持的现代林挺工具，以及一个预先构建的 [Karma](https://karma-runner.github.io/latest/index.html) 测试工具。在我开始之前，我已经开始检查这个项目的两个目标:基于 web 组件的 UI 和单元测试。林挺是免费的(在所有合适的地方都很严格)，这就更好了！

我做了什么...

```
git clone https://github.com/open-wc/open-wc-starter-app.git
cd open-wc-starter-app

yarn install yarn start 
```

Enter fullscreen mode Exit fullscreen mode

你能做什么(因为已经过了几个星期，这就像是一整年的前端开发，所以事情变得更容易了！)...

```
$ npm init open-wc
npx: installed 14 in 4.378s
What would you like to do today?
    > Scaffold a new project
What would you like to scaffold?
Note: Content will be written in a new folder.
    > Starter App
Give it a tag name (min two words separated by dashes) to-do-app 
```

Enter fullscreen mode Exit fullscreen mode

我很懒，稍后你会看到更多，所以我强烈建议第二种方法，因为它可以在一个命令中处理你的新目录、starter app 和`npm install`。它甚至在最后提醒你接下来要做什么来运行生成的代码:

```
You are all set up now!
All you need to do is run:

cd to-do-app
npm run start 
```

Enter fullscreen mode Exit fullscreen mode

但是，然后呢？

***免责声明:**除此之外还有一些代码。这些代码可能并不总是与我在应用程序中最终得到的完全一样，原因比这里值得深入讨论的还要多。然而，该代码在概念上代表了本系列中讨论的各种技术，以及我遇到应用这些技术的需求/愿望的开发过程的一部分。这意味着一个部分中的代码可能无法很好地与另一个部分中的代码一起工作或运行。在这种认识中，不要绝望，因为绝望只属于那些毫无疑问地看到终点的人。我在 GitHub 上有[完整的项目，如果你想阅读、克隆或定制你想要的。](https://github.com/Westbrook/to-do-app)[现场代码](https://gifted-lamport-70b774.netlify.com/)也是 Netlify 的，给喜欢先看电影的。*

* * *

# 短游戏

正如许多对此类话题有意见的人所投票的那样，一篇 9000 多字的文章是不行的。

> ![WestbrookJ profile image](img/c68b7eeba8c71652bba77a02b2b5f5eb.png)WestbrookJ@ WestbrookJ![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以，我在写博文...我注意到，当我不看的时候，它变得相当长，一次完成可能有 7000 个单词。我想说我会很幸运地把它编辑下来，但是一些技巧我还在提高...我该怎么办？2019 年 2 月 20 日 12 点 22 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1098196160613896192)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1098196160613896192)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1098196160613896192)

所以，怀着对你最深的敬意，我亲爱的读者，我把即将到来的对话分成了微不足道的十个部分。恭喜你，你已经接近第一场比赛的尾声了！如果你到目前为止还很享受，或者是那种会给一部新的情景喜剧加几集就能让它大放异彩的人，这里有一份其他人的名单，你可以把它们放在你的网飞名单上:

*   [不是另一个待办应用](https://dev.to/westbrook/not-another-to-do-app-2kj9)
*   开始(你在这里)
*   [早考，常考](https://dev.to/westbrook/not-another-to-do-app-2m9a)
*   [测量两次，皮棉一次](https://dev.to/westbrook/not-another-to-do-app-part-4-58cd)
*   [使其成为组件](https://dev.to/westbrook/not-another-to-do-app-part-5-5d7o)
*   [使其成为可重复使用的零件](https://dev.to/westbrook/not-another-to-do-app-part-6-an)
*   你的组件真的需要知道这些吗？
*   [尽早、经常、只在需要的时候把东西分开](https://dev.to/westbrook/not-another-to-do-app-part-8-3lic)
*   [有些抽象并不(仅仅)适用于你的应用](https://dev.to/westbrook/not-another-to-do-app-part-9-10j3)(将本系列抽象成独立的文章是为你的读者准备的，我希望你真的喜欢它。)
*   [可重用和可扩展的数据管理/最终...](https://dev.to/westbrook/not-another-to-do-app-part-10-mp6)
*   [查看运行中的应用](https://gifted-lamport-70b774.netlify.com/)

* * *

特别感谢 [Open Web Components](https://open-wc.org/) 的团队，他们提供了大量的工具和建议，以支持不断增长的工程师和公司社区，将高质量的 Web 组件带入行业。[在 GitHub](https://github.com/open-wc/open-wc) 上访问他们，并创建一个问题，提交一份 PR，或签署一份回购协议来参与行动！