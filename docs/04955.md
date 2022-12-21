# 不是另一个待办事项应用程序:第 6 部分

> 原文：<https://dev.to/westbrook/not-another-to-do-app-part-6-an>

#### 用 Open Web 组件推荐弄脏你的手和脚...算是吧。

> *这是 2019 年 2 月 26 日来自 [Medium](https://medium.com/@westbrook/not-another-to-do-app-169c14bb7ef9) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️:如果这是你第一次阅读，欢迎！*

*欢迎来到“不要再做一个应用程序”，这是一篇冗长的综述，讲述了每个开发人员在某个时候都会编写的最小的应用程序之一。如果你是来学习编写应用程序的特定技术的，或者是从以前的安装中走出来的，那么你可能来对地方了，应该继续读下去！如果没有，你可能想[从头开始](https://dev.to/westbrook/not-another-to-do-app-2kj9)，这样你也可以知道[我们所有角色的背景故事...](https://github.com/Westbrook/to-do-app)*

如果你已经做到了这一步，为什么现在就放弃呢？

* * *

# 使其成为可重复使用的零件

<figure>

[![Make it a Reusable Part](img/57ab984d82bff2d0ff61500d957b644e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_2lvu5c7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/st3192c2romspp4qnliq.jpeg)

<figcaption>Photo by [Lacey Williams](https://unsplash.com/@travelwithlace) on [Unsplash](https://unsplash.com/)</figcaption>

</figure>

当使用一个特定的组件模型时，很容易被骗去认为你想要重用的 UI 的每一部分都应该以那个组件模型的形式来构造。在我的应用程序中，我选择使用 web 固有的组件模型，即 web 组件，但是当我不需要/不想在影子 DOM 中封装东西时，或者当在组件/应用程序的生命周期中没有与所讨论的 DOM 特别相关的状态需要管理时，该怎么办呢？在这些情况下，我求助于`lit-html`及其创建模板的功能方法。

```
const reusablePart = () => html`... Your reusable part here ...`; 
```

Enter fullscreen mode Exit fullscreen mode

在无数情况下，这种模式在应用程序开发中很有价值。我们实际上已经看到了，可能还不知道，在上一期中纠正`.map`林挺错误时，我将模板部分移到了 [`/src/to-do-ui.js`](https://github.com/Westbrook/to-do-app/blob/master/src/to-do-ui.js) ，这同时利用了这种模式，同时允许代码在测试过程中重用:

```
import { html } from 'lit-element';

export const renderTodo = todo => html`
    <to-do .todo="${todo}">${todo.todo}</to-do>
`;

export const renderTodos = todos => todos.map(renderTodo); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到我们的待办事项列表(`renderTodos`)是通过将`todos`数组映射到`renderTodo`模板上而生成的。然后在测试过程中利用`renderTodo`模板轻松创建我们运行测试所用的`to-do`夹具。

```
import { renderTodo } from '../src/to-do-ui';

// ...

const newToDo = {
    id: 2,
    todo: 'New To Do'
};
it('is a to do', async () => {
    const el = await fixture(renderTodo(newToDo));
    expect(el.textContent).to.equal(newToDo.todo);
    expect(el.todoId).to.equal(newToDo.id);
}); 
```

Enter fullscreen mode Exit fullscreen mode

> # # Award

这种技术我最喜欢的应用之一是将 SVG 图标注入到我的模板中。你就大功告成了！它非常有用，提出了一系列有趣的可用性、a11y 和代码可重用性问题，以至于我正在研究基于标记模板文字的图标组不仅可以跨项目共享，还可以通过 [`feather-icon-literals`](https://www.npmjs.com/package/feather-icon-literals) 跨框架共享。然而，我将在以后的日子里继续深入探讨这个问题。在我的待办事项应用中，只有两个图标；[`iconPlus`](https://github.com/Westbrook/to-do-app/blob/master/src/icons.js#L3)[`iconMinus`](https://github.com/Westbrook/to-do-app/blob/master/src/icons.js#L19)。两者出来大致如下:

```
export const iconPlus = html`
    
        <line x1="12" y1="5" x2="12" y2="19" />
        <line x1="5" y1="12" x2="19" y2="12" />
    
`; 
```

Enter fullscreen mode Exit fullscreen mode

这绝对是这种技术最简单的实现；每个属性都是固定的，a11y 是从图标所在的按钮租用的，而不是应用于图标，等等。在那里，可以添加许多其他定制，现在，如果您进行了定制，它们将集中在一个地方，以便在整个项目中重用。或者，更进一步，将它分解到自己的包中(或者使用我的包， [PRs welcome](https://github.com/Westbrook/feather-icon-literals) )，使它成为你想要的许多项目的依赖项。

* * *

# 短游戏

正如许多对此类话题有意见的人所投票的那样，一篇 9000 多字的文章是不行的。

> ![WestbrookJ profile image](img/c68b7eeba8c71652bba77a02b2b5f5eb.png)WestbrookJ@ WestbrookJ![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以，我在写博文...我注意到，当我不看的时候，它变得相当长，一次完成可能有 7000 个单词。我想说我会很幸运地把它编辑下来，但是一些技巧我还在提高...我该怎么办？2019 年 2 月 20 日 12 点 22 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1098196160613896192)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1098196160613896192)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1098196160613896192)

所以，怀着对你最深的敬意，我亲爱的读者，我把即将到来的对话分成了微不足道的十个部分。恭喜你，你已经接近第一场比赛的尾声了！如果你到目前为止还很享受，或者是那种会给一部新的情景喜剧加几集就能让它大放异彩的人，这里有一份其他人的名单，你可以把它们放在你的网飞名单上:

*   不要另一个待办应用(也许现在我可以只使用这些可重复使用的部分，再也不用编写另一个待办应用了...)
*   [入门](https://dev.to/westbrook/not-another-to-do-app-3jem)
*   [早考，常考](https://dev.to/westbrook/not-another-to-do-app-2m9a)
*   [测量两次，皮棉一次](https://dev.to/westbrook/not-another-to-do-app-part-4-58cd)
*   [使其成为组件](https://dev.to/westbrook/not-another-to-do-app-part-5-5d7o)
*   使其成为可重复使用的部分(你在这里)
*   你的组件真的需要知道这些吗？
*   [有些抽象并不(仅仅)适用于你的应用](https://dev.to/westbrook/not-another-to-do-app-part-9-10j3)
*   [可重用和可扩展的数据管理/最终...](https://dev.to/westbrook/not-another-to-do-app-part-10-mp6)
*   [查看运行中的应用](https://gifted-lamport-70b774.netlify.com/)

* * *

特别感谢 [Open Web Components](https://open-wc.org/) 的团队，他们提供了大量的工具和建议，以支持不断增长的工程师和公司社区，将高质量的 Web 组件带入行业。[在 GitHub](https://github.com/open-wc/open-wc) 上访问他们，并创建一个问题，提交一份 PR，或签署一份回购协议来参与行动！