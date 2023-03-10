# 不是另一个待办事项应用程序

> 原文：<https://dev.to/westbrook/not-another-to-do-app-2kj9>

#### 用 Open Web 组件推荐弄脏你的手和脚...算是:第一部分

> *这是 2019 年 2 月 26 日来自 [Medium](https://medium.com/@westbrook/not-another-to-do-app-762ea0bb960) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️:如果这是你第一次阅读，欢迎！*

# 为什么要做应用？

每个写过待办事项应用的人可能都是因为一个稍微不同的原因而写的。有些出于[无聊](https://dev.to/citizen428/fighting-boredom-with-a-hyperapp-experiment-12a)。另一些人在进行某种[科学探索](http://todomvc.com/)。许多人到[学习一些](https://medium.com/javascript-in-plain-english/i-created-the-exact-same-app-in-react-and-vue-here-are-the-differences-e9a1ae8077fd)新东西。实际上通常是管理你必须做的事情。我敢肯定，还有无数的其他人。本质上，待办事项应用程序是简单性(你通常可以在几分钟内完成一些)和复杂性(它寻求许多重要技术问题的答案)的完美结合。如果你喜欢简单，你的出口在下面:

```
let todos = [];

function render() {
    document.body.innerHTML = ` ${todos.map((todo, index) => `
            <div> ${todo} <button
                  class="complete"
                  data-index="${index}"
                >
                    Complete
                </button>
            </div>`).join('')
        } <div>
            <input />
            <button class="add">Add</button>
        </div>
   `;
}

document.body.addEventListener('click', e => {
    if (e.target.classList.contains('add')) {
        todos.push(document.querySelector('input').value);
        render();
    } else if (e.target.classList.contains('complete')) {
        let index = e.target.dataset.index;
        todos.splice(index, 1);
        render();
    }
});

render(); 
```

Enter fullscreen mode Exit fullscreen mode

是的，这绝对可以更简单和更小...我期待看到证明这是真的帖子！开始富有成效的对话是我写自己编写的代码的主要原因，如果我能在这么早的时候找到一个好的对话，我会非常看好这个系列的其余部分。然而，这是真的，你需要做的就是把上面的内容粘贴到你最喜欢的网站的控制台上，突然它也会变成一个待办事项应用程序！

对于那些处于复杂阵营的人来说(听起来像是错过了一个商业机会)，待办事项应用程序是一扇敞开的大门，通向围绕代码架构、组件边界、事件处理、状态管理、样式、测试等重要问题。这还是在你开始了解除了“作为一个忙碌的人，我希望能够存储我需要完成的任务”和“作为一个高效的人，我希望把我已经完成的任务从列表中删除”这些基本功能之前。如果这个夏令营适合你，欢迎你来参加迎新会！

坦白地说，一开始，深入并学习本系列中所涉及的主题的所有知识就是[搜索](https://www.google.com/search?q=how+to+be+a+javascript+developer&oq=how+to+be+a+javascript&aqs=chrome.0.0j69i57j0l4.5813j1j7&sourceid=chrome&ie=UTF-8)事物的目的。去吧，我等着...(危险主题曲播放..)正因为如此，我并没有试图把自己定位为*的*专家，或者把这篇文章定位为*的*在这些事情上的最终决定权，我是认真的，当我说我更有兴趣参与一场对话，讨论我最近重新考虑编写一个待办事项应用程序的想法时突然想到的问题/解决方案。当你读完之后，在下面的评论中加入对话(他们会在本系列的每一个安装之后等着你)，或者，如果这是你的事情，把对话放到[推特](https://twitter.com/WestbrookJ)上。

# 漫长的游戏

<figure>

[![Gotta catch 'em all!](img/446c8df6526640ca2db98e0430ca109e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ykvm9Ogo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gm50p87j9l921koi57zh.jpeg)

<figcaption>Photo by [Kamil S](https://unsplash.com/@16bitspixelz) on [Unsplash](https://unsplash.com/)</figcaption>

</figure>

“我想成为最好的！从来没有人喜欢过"...等等，我在骗谁呢？我想成为我在推特上关注的工程师。他们[非常关心如何让网页看起来更好](https://twitter.com/argyleink)，[努力帮助网页一起工作](https://twitter.com/stubbornella)，[专注于 a11y](https://twitter.com/SaraSoueidan) ，[从不测试实现细节](https://twitter.com/kentcdodds)，[编写最好的库](https://twitter.com/justinfagnani)，有时也是[设计师](https://twitter.com/Mustafa_x)，等等。那意味着我也必须做所有那些事情，对吗？幸运的是，没有。但是，这并不意味着我不会花太多的时间去尝试。这意味着，虽然我已经写了上面提到的*惊人的* 34 行待办应用程序，以及这个*其他*惊人的[待办应用程序](https://dev.to/westbrook/litelement-to-do-app-4ngn)作为从其他流行前端技术直接到 [LitElement](https://lit-element.polymer-project.org/) 的移植，但我感到一种不可否认的冲动，想做得更多，再做一次，做得更好，然后通过谈论它来烦扰这个世界。

> 不客气！

为此，这一次，我为我的待办事项应用程序列出的待办事项列表如下:

*   基于组件的代码体系结构
*   基于自定义属性的样式 API
*   基于事件的状态管理
*   风格分享
*   单元测试
*   基于 web 组件的用户界面

希望这些领域都是您感兴趣的概念，并且在您评估下一个应用程序的技术决策时，您会发现下面的内容非常有用。如果没有，欢迎在下面的评论中抱怨...我喜欢评论。更重要的是，如果你认为我遗漏了你想建议我在这里添加或在以后的帖子中讨论的任何要点，也请留下评论！

您可能已经注意到，上面遗漏了一项热门的前端工程技术，那就是“风格封装”。我没有说这个是有原因的。除了成为整个社区分歧的温床之外，由于单独决定用 web 组件制作我的 UI(我喜欢用 web 组件使用 [Shadow DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) )，我免费获得了样式封装。这意味着没有必要讨论我是否/如何/何时封装我的风格，它们只是从一开始就有。因为我实际上并没有做任何事情来获得这种封装，所以我把它从我的待办事项应用程序的待办事项列表中省略了...

* * *

# 短游戏

正如许多对此类话题有意见的人所投票的那样，一篇 9000 多字的文章是不行的。

> ![WestbrookJ profile image](img/c68b7eeba8c71652bba77a02b2b5f5eb.png)WestbrookJ@ WestbrookJ![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以，我在写博文...我注意到，当我不看的时候，它变得相当长，一次完成可能有 7000 个单词。我想说我会很幸运地把它编辑下来，但是一些技巧我还在提高...我该怎么办？2019 年 2 月 20 日 12 点 22 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1098196160613896192)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1098196160613896192)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1098196160613896192)

所以，怀着对你最深的敬意，我亲爱的读者，我把即将到来的对话分成了微不足道的十个部分。恭喜你，你已经接近第一场比赛的尾声了！如果你到目前为止还很享受，或者是那种会给一部新的情景喜剧加几集就能让它大放异彩的人，这里有一份其他人的名单，你可以把它们放在你的网飞名单上:

*   不是另一个待办事项应用程序(你在这里)
*   [入门](https://dev.to/westbrook/not-another-to-do-app-3jem)
*   [早考，常考](https://dev.to/westbrook/not-another-to-do-app-2m9a)
*   [测量两次，皮棉一次](https://dev.to/westbrook/not-another-to-do-app-part-4-58cd)
*   [使其成为组件](https://dev.to/westbrook/not-another-to-do-app-part-5-5d7o)
*   [使其成为可重复使用的零件](https://dev.to/westbrook/not-another-to-do-app-part-6-an)
*   你的组件真的需要知道这些吗？
*   [尽早、经常、只在需要的时候把事情分开](https://dev.to/westbrook/not-another-to-do-app-part-8-3lic)(很明显，当我开始把这个系列作为一篇文章来写的时候，我没有遵循这个原则...)
*   [有些抽象并不(仅仅)适用于你的应用](https://dev.to/westbrook/not-another-to-do-app-part-9-10j3)
*   [可重用和可扩展的数据管理/最终...](https://dev.to/westbrook/not-another-to-do-app-part-10-mp6)
*   [查看运行中的应用](https://gifted-lamport-70b774.netlify.com/)

* * *

特别感谢 [Open Web Components](https://open-wc.org/) 的团队，他们提供了大量的工具和建议，以支持不断增长的工程师和公司社区，将高质量的 Web 组件带入行业。[在 GitHub](https://github.com/open-wc/open-wc) 上访问他们，并创建一个问题，提交一份 PR，或签署一份回购协议来参与行动！