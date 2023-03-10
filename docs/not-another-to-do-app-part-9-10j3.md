# 不是另一个待办事项应用程序:第 9 部分

> 原文：<https://dev.to/westbrook/not-another-to-do-app-part-9-10j3>

#### 用 Open Web 组件推荐弄脏你的手和脚...算是吧。

> *这是 2019 年 2 月 26 日来自 [Medium](https://medium.com/@westbrook/not-another-to-do-app-b5dce4d8931d) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️:如果这是你第一次阅读，欢迎！*

*欢迎来到“不要再做一个应用程序”，这是一篇冗长的综述，讲述了每个开发人员在某个时候都会编写的最小的应用程序之一。如果你是来学习编写应用程序的特定技术的，或者是从以前的安装中走出来的，那么你可能来对地方了，应该继续读下去！如果没有，你可能想[从头开始](https://dev.to/westbrook/not-another-to-do-app-2kj9)，这样你也可以知道[我们所有角色的背景故事...](https://github.com/Westbrook/to-do-app)*

如果你已经做到了这一步，为什么现在就放弃呢？

* * *

# 一些抽象概念并不(仅仅)适用于你的应用程序

<figure>

[![Some Abstractions Aren’t (Just) For Your App](img/df5388ab2b9e218a964c77f0b482f71e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lAI9kRAY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wa8a1lbgh6f604kc9wdm.jpeg)

<figcaption>Photo by [Kira auf der Heide](https://unsplash.com/@kadh) on [Unsplash](https://unsplash.com/)</figcaption>

</figure>

很多时候，我们发现自己正盯着质量抽象的好处。例如，如果您意识到您正在将某些功能烘焙到您制作的每个组件中，那么创建一个中间基类、mixin 或模块导出来抽象这种重复、集中这种功能，并且通常使您能够 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) (不要重复自己)您的代码，这可能是一个好主意。其他时候，做一个可靠的抽象的好处并不明显。如果某个东西在你的应用程序中只被使用一次，那么你可能很难完全推销一个抽象的好处(即使是对你自己，尤其是在我花了很多时间告诉你要*懒惰*并且只在你需要的时候才分开东西之后，在我们一起开始的这个光荣的冒险的前几个安装中)，在这些情况下，不仅考虑你的应用程序可能在哪里使用这个功能，而且考虑你的测试可能在哪里使用这个功能可能也是有用的。(我将把关于你的测试是否是你的应用程序的一部分的争论留给我的自我意识来判断这个系列中的每一句话。或者说，评论，我还是爱评论！)

作为复习，让我们看看一些我们已经花了一些时间的测试代码:

```
it('adds a to do in response to a `todo-new` event', async () => {
    const newTodo = 'New To Do';
    const el = await fixture(html`<open-wc-app></open-wc-app>`);
    expect(el.shadowRoot.querySelectorAll('to-do').length)
        .to.equal(0);
    el.dispatchEvent(new CustomEvent('todo-new', {
        detail: newTodo,
        bubbles: true,
        composed: true
    }));
    await nextFrame();
    expect(el.todos.length).to.equal(1);
    expect(el.todos[0]).to.equal(newTodo);
    expect(el.shadowRoot.querySelectorAll('to-do').length)
        .to.equal(1);    
    expect(el.shadowRoot.querySelectorAll('to-do')[0].textContent)
        .to.equal(newTodo);
}); 
```

Enter fullscreen mode Exit fullscreen mode

孤立来看，没那么疯狂；创建一个 fixture，做一个测试，创建/调度一个定制事件，等一会儿，再做一些测试。现在，为了增加上下文，让我们来看看来自 [`src/to-do-write.js`](https://github.com/Westbrook/to-do-app/blob/master/src/to-do-write.js) :
的一些代码

```
newToDo() {
    if (!this.todo) return;
    this.dispatchEvent(new CustomEvent('todo-new', {
        detail: this.todo,
        bubbles: true,
        composed: true
    }));
    this.todo = '';
} 
```

Enter fullscreen mode Exit fullscreen mode

孤立地看，也不疯狂:检查是否提供了待办事项，创建/调度自定义事件，清理待办事项。即使肩并肩，你也可以很容易地依靠三个 T2 法则作为不去过多考虑这个问题的借口。当然，还有更多...语境。看看 [`src/open-wc-app.js`](https://github.com/Westbrook/to-do-app/blob/master/src/open-wc-app.js) :
中的听者

```
this.addEventListener(
    'todo-new',
    (e) => this.addToDo(e.detail)
);

this.addEventListener(
    'todo-complete',
    (e) => this.completeToDo(e.detail)
); 
```

Enter fullscreen mode Exit fullscreen mode

不，这不是上面代码的第三个实例，但是鹰眼可能已经发现了现在驻留在所有三段代码中的神奇字符串。顺便说一下，你可能也注意到了 [`src/to-do.js`](https://github.com/Westbrook/to-do-app/blob/master/test/to-do.test.js) :
中的这段代码

```
completeToDo() {
    this.dispatchEvent(new CustomEvent('todo-complete', {
        detail: this.todoId,
        bubbles: true,
        composed: true
    }));
} 
```

Enter fullscreen mode Exit fullscreen mode

以及`test/to-do.test.js` :
中这个有些匹配的测试

```
it('removes a to do in response to a `todo-complete` event', async () => {
    const completeToDo = 'New To Do';
    const el = await fixture(
        html`<open-wc-app
            todos='["${completeToDo}"]'
        ></open-wc-app>`
    );
    expect(el.shadowRoot.querySelectorAll('to-do').length))
        .to.equal(1);
    expect(el.shadowRoot.querySelectorAll('to-do')[0])
        .to.equal(completeToDo);
    expect(el.todos[0]).to.equal(completeToDo);
    el.dispatchEvent(new CustomEvent('todo-complete', {
        detail: completeToDo,
        bubbles: true,
        composed: true
    }));
    await nextFrame();
    expect(el.shadowRoot.querySelectorAll('to-do').length))
        .to.equal(0);
}); 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们已经看到了一些神奇字符串的出现，在应用程序代码的隔离中，*可能*已经被接受。然而，当它们被放在它们相关的测试旁边，以及在其中发现字符串的有点神奇的定制事件旁边时，我们的“需要抽象”的钟声应该响起来了。您、您的应用程序和它的测试是一个小家庭，虽然我花了很长时间才意识到这一点，但是抽象并不仅仅是这个家庭的一部分！看看我们如何通过 [`src/to-do-events.js`](https://github.com/Westbrook/to-do-app/blob/master/src/to-do-events.js) :
将这些抽象出来

```
const eventOptions = {
  bubbles: true,
  composed: true,
}

export const toDoEventNames = {
  NEW: 'todo-new',
  COMPLETE: 'todo-complete',
}

const toDoEvent = (todo, event) => {
  return new CustomEvent(event, {
    ...eventOptions,
    detail: todo
  });
}

export const eventCompleteToDo = (todo) => {
  return toDoEvent(todo, toDoEventNames.COMPLETE);
}

export const eventNewToDo = (todo) => {
  return toDoEvent(todo, toDoEventNames.NEW);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了通过`toDoEventNames.COMPLETE`和`toDoEventNames.NEW`枚举的讨厌的神奇字符串，我们的自定义事件创建共享了流程的大部分可操作部分，同时为每个事件公开一个助手来利用该代码。这意味着大量的复杂性可以从上面的例子中去除，我们得到类似
的代码

```
newToDo() {
    if (!this.todo) return;
    this.dispatchEvent(eventNewToDo(this.todo));
    this.todo = '';
} 
```

Enter fullscreen mode Exit fullscreen mode

还有:

```
completeToDo() {
    this.dispatchEvent(eventCompleteToDo(this.todoId));
} 
```

Enter fullscreen mode Exit fullscreen mode

冒泡到:

```
this.addEventListener(
    toDoEventNames.NEW,
    (e) => this.addToDo(e.detail)
);

this.addEventListener(
    toDoEventNames.COMPLETE,
    (e) => this.completeToDo(e.detail)
); 
```

Enter fullscreen mode Exit fullscreen mode

在这一过程中，我们还降低了维护成本(如果我们需要重构)和未来开发成本(如果我们为未来扩展的数据交互添加新的定制事件)。

说到数据和管理数据...你可能有兴趣看看我们长期播放的电视剧的下一个和最后一个条目。

* * *

# 短游戏

正如许多对此类话题有意见的人所投票的那样，一篇 9000 多字的文章是不行的。

> ![WestbrookJ profile image](img/c68b7eeba8c71652bba77a02b2b5f5eb.png)WestbrookJ@ WestbrookJ![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以，我在写博文...我注意到，当我不看的时候，它变得相当长，一次完成可能有 7000 个单词。我想说我会很幸运地把它编辑下来，但是一些技巧我还在提高...我该怎么办？2019 年 2 月 20 日 12 点 22 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1098196160613896192)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1098196160613896192)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1098196160613896192)

所以，怀着对你最深的敬意，我亲爱的读者，我把即将到来的对话分成了微不足道的十个部分。恭喜你，你已经接近第一场比赛的尾声了！如果你到目前为止还很享受，或者是那种会给一部新的情景喜剧加几集就能让它大放异彩的人，这里有一份其他人的名单，你可以把它们放在你的网飞名单上:

*   [不是另一个待办应用](https://dev.to/westbrook/not-another-to-do-app-2kj9)
*   [入门](https://dev.to/westbrook/not-another-to-do-app-3jem)
*   [早考，常考](https://dev.to/westbrook/not-another-to-do-app-2m9a)
*   [测量两次，皮棉一次](https://dev.to/westbrook/not-another-to-do-app-part-4-58cd)
*   [使其成为一个组件](https://dev.to/westbrook/not-another-to-do-app-part-5-5d7o)(这些文章中的介绍/介绍关系可以使用一些组件化...只是通过光线 DOM 应用坏笑话。)
*   [使其成为可重复使用的零件](https://dev.to/westbrook/not-another-to-do-app-part-6-an)
*   你的组件真的需要知道这些吗？
*   [尽早、经常、只在需要的时候把东西分开](https://dev.to/westbrook/not-another-to-do-app-part-8-3lic)
*   有些抽象不(仅仅)是针对你的应用程序的(你在这里)
*   可重复使用和可扩展的数据管理/最终...(即将推出[开发到](https://dev.to))
*   [查看运行中的应用](https://gifted-lamport-70b774.netlify.com/)

* * *

特别感谢 [Open Web Components](https://open-wc.org/) 的团队，他们提供了大量的工具和建议，以支持不断增长的工程师和公司社区，将高质量的 Web 组件带入行业。[在 GitHub](https://github.com/open-wc/open-wc) 上访问他们，并创建一个问题，提交一份 PR，或签署一份回购协议来参与行动！