# 不是另一个待办事项应用程序:第 7 部分

> 原文：<https://dev.to/westbrook/not-another-to-do-app-part-7-3cm7>

#### 用 Open Web 组件推荐弄脏你的手和脚...算是吧。

> *这是 2019 年 2 月 26 日来自 [Medium](https://medium.com/@westbrook/not-another-to-do-app-169c14bb7ef9) 的一篇文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️:如果这是你第一次阅读，欢迎！*

*欢迎来到“不要再做一个应用程序”，这是一篇冗长的综述，讲述了每个开发人员在某个时候都会编写的最小的应用程序之一。如果你是来学习编写应用程序的特定技术的，或者是从以前的安装中走出来的，那么你可能来对地方了，应该继续读下去！如果没有，你可能想[从头开始](https://dev.to/westbrook/not-another-to-do-app-2kj9)，这样你也可以知道[我们所有角色的背景故事...](https://github.com/Westbrook/to-do-app)*

如果你已经做到了这一步，为什么现在就放弃呢？

* * *

# 你的组件真的需要知道这些吗？

<figure>

[![Does Your Component Really Need to Know That?](img/f2e9dd63a61aebaeb29d522fa881eaab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4-S58iz4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9ciorrxxhravawi8wy38.jpeg)

<figcaption>Photo by [Mathew Schwartz](https://unsplash.com/@cadop) on [Unsplash](https://unsplash.com/)</figcaption>

</figure>

很难说这是来自我的敏捷培训，还是我对精益的学习，还是我的敏捷培训(大 A，小 A，如果你知道的话...你知道)，或者我自己对[MTU](https://medium.com/@westbrook/may-i-introduce-to-you-the-minimum-testable-unit-79a59772bcc4)的思考，但是我已经变得很喜欢不做那些不必要做的事情。沿着这些思路，我经常与自己进行冗长的对话，讨论应该将控件放在组件的什么位置；它的内容，它的功能，它的造型，一切。我花了很多时间思考这个问题，尤其是与我的应用程序的`to-do`元素的实现相关的问题。

最初，为了与最少工作量的方法保持一致，我觉得我可以将我的待办事项与基于字符串的等式进行比较。除了 to do 的文本字符串之外，没有数据来定义它们，所以我想使用简单的代码:

```
<to-do todo="I've got stuff to do"></to-do> 
```

Enter fullscreen mode Exit fullscreen mode

一个简单的字符串意味着我可以依靠[属性绑定](https://lit-html.polymer-project.org/guide/writing-templates#bind-to-attributes)将必要的数据推送到我的`to-do`元素中，然后我就可以收工了。然而，一个快速的自我 QA(质量保证测试)会告诉你在一个简单的平等世界中这是多么的愚蠢

```
<to-do todo="I've got stuff to do"></to-do>
<to-do todo="I've got stuff to do"></to-do> 
```

Enter fullscreen mode Exit fullscreen mode

当你有两个同名的待办事项时，你也有两个相同的待办事项，这意味着“完成”一个会*意外地*完成两个。一个有很多事要做的人用同样的文字做什么？

首先，我想这样做:

```
<to-do todo='{"todo": "I've got stuff to do"}'></to-do>
<to-do todo='{"todo": "I've got stuff to do"}'></to-do> 
```

Enter fullscreen mode Exit fullscreen mode

感谢 open-wc 的 Starter 应用程序提供的用于构建我的 web 组件的`LitElement`基类，我可以将我的`todo`属性声明为`{type: Object}`，并免费将该属性字符串序列化为实际的`Object`。然后，该对象将在我的单个`to-do`元素之间具有唯一的身份，我可以再次依靠相等检查来“完成”一个要做的事情，而不是另一个，这样一切都好了。

> ## Wrong

依赖于跨应用程序内部的组件边界序列化的数据意味着新的身份可能会在您不希望的时间被创建。特别是，当通过上述绑定序列化进和序列化出一个字符串时，你的对象的外部和内部身份将不会被共享，这就是我如何编写下面的代码的原因:

```
<to-do .todo="${todo}"></to-do> 
```

Enter fullscreen mode Exit fullscreen mode

使用属性绑定意味着我们可以跳过字符串序列化的要求，不仅每个`to-do`元素都有一个具有唯一标识的`todo`，不管显示它们需要做什么工作，而且该标识还将跨组件边界得到维护。web 组件中的丰富数据通信？你没说...

有了这个决定，我花了一点时间使用我希望实现的样式应用程序。我决定改变标准样式方法，而不是依靠`to-do`元素内部(可能借助 CSS 自定义属性)来样式化待办事项文本，我选择通过 light DOM 来应用它，这样父元素就可以控制样式。这是一个很小的差别，但是我的自定义元素却少了一件需要考虑的事情。

```
<to-do todo="${todo}">${todo.todo}</to-do> 
```

Enter fullscreen mode Exit fullscreen mode

我告诉过你什么，一个小小的改变！而且，在内部，这种变化与添加的一个`slot`元素相匹配，以显示从外部投射到您的[阴影 DOM](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM) 中的内容。在这种情况下，看起来像:

```
render() {
    return html`
        <div>
            <slot></slot> <!-- <E<=<- Look, I'm a slot! -->
        </div>
        <button
            @click="${this.completeToDo}"
            title="Complete To Do"
        > ${iconMinus} </button>
    `;
} 
```

Enter fullscreen mode Exit fullscreen mode

有时，您的组件需要知道的比您最初希望的更多，无论是为了在内部保持它们的保真度，还是为了在构造应用程序时跨组件边界做同样的事情。其他时候，您可能能够从组件的肩上卸下一些责任。变得富有哲理并回答这个问题“你的组件真的需要知道这些吗？”无论是交付您现在正在组合的功能，还是减少以后的维护需求，都是非常重要的一步。

* * *

# 短游戏

正如许多对此类话题有意见的人所投票的那样，一篇 9000 多字的文章是不行的。

> ![WestbrookJ profile image](img/c68b7eeba8c71652bba77a02b2b5f5eb.png)WestbrookJ@ WestbrookJ![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)所以，我在写博文...我注意到，当我不看的时候，它变得相当长，一次完成可能有 7000 个单词。我想说我会很幸运地把它编辑下来，但是一些技巧我还在提高...我该怎么办？2019 年 2 月 20 日 12 点 22 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1098196160613896192)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1098196160613896192)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1098196160613896192)

所以，怀着对你最深的敬意，我亲爱的读者，我把即将到来的对话分成了微不足道的十个部分。恭喜你，你已经接近第一场比赛的尾声了！如果你到目前为止还很享受，或者是那种会给一部新的情景喜剧加几集就能让它大放异彩的人，这里有一份其他人的名单，你可以把它们放在你的网飞名单上:

*   [不是另一个待办应用](https://dev.to/westbrook/not-another-to-do-app-2kj9)
*   [开始](https://dev.to/westbrook/not-another-to-do-app-3jem)(记住，哦，很久以前，我们还是孩子..)
*   [早考，常考](https://dev.to/westbrook/not-another-to-do-app-2m9a)
*   [测量两次，皮棉一次](https://dev.to/westbrook/not-another-to-do-app-part-4-58cd)
*   [使其成为组件](https://dev.to/westbrook/not-another-to-do-app-part-5-5d7o)
*   [使其成为可重复使用的零件](https://dev.to/westbrook/not-another-to-do-app-part-6-an)
*   您的组件真的需要知道这些吗？(你在这里)
*   [有些抽象并不(仅仅)适用于你的应用](https://dev.to/westbrook/not-another-to-do-app-part-9-10j3)
*   [可重用和可扩展的数据管理/最终...](https://dev.to/westbrook/not-another-to-do-app-part-10-mp6)
*   [查看运行中的应用](https://gifted-lamport-70b774.netlify.com/)

* * *

特别感谢 [Open Web Components](https://open-wc.org/) 的团队，他们提供了大量的工具和建议，以支持不断增长的工程师和公司社区，将高质量的 Web 组件带入行业。[在 GitHub](https://github.com/open-wc/open-wc) 上访问他们，并创建一个问题，提交一份 PR，或签署一份回购协议来参与行动！