# 转向 Web 开发视角:我希望已经掌握的两个概念

> 原文：<https://dev.to/ashc0des/shifting-to-a-web-dev-perspective-two-concepts-i-wish-i-d-mastered-24e2>

在我的上一篇博文中，我谈到了为什么我要深入研究 web 基础知识。通过学术和专业经验，我发现很容易陷入跟随教程和依赖 StackOverflow 来工作的陷阱。你可以在这里查看。

我的最终目标是成为一名更有能力的开发者，用#a11y 和有目的的设计来编码。我将讨论我在这次旅程开始时(早在三月份)的误解，以及我现在所理解的。

#### 误解:React 中组件之间的关系与 Java 中类之间的层次关系相同。

一点也不。事实上，经典继承是解决方案的例子很少。我错误地将父组件和子组件等同于父类和子类。传统继承的缺点是以代码可重用性的名义在类之间形成的紧密耦合。帮助我意识到负面影响的是大猩猩香蕉问题。

> “你想要的是一根香蕉，你得到的是一只拿着香蕉的大猩猩，还有整个丛林。”

所以这大致可以解释为孩子从父母那里继承了他们不一定需要的行为。将代码重构为小的功能单元或对象组合比继承更有意义。一个组件理论上应该[只负责一件事。](https://en.wikipedia.org/wiki/Single_responsibility_principle)如果职责增加，可能是时候重构为子组件了。组件相互嵌套，因此称为“组合”。React 中的道具就是这样在组件间传递的。

> ![Aishwarya Borkar 🎧👩🏻‍💻 profile image](img/1382b4640ca93cec9af43d56e843982d.png)艾什瓦尔卡🎧👩🏻‍💻[@ ashc 0 des](https://dev.to/ashc0des)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)跟进:经典继承在 [#JavaScript](https://twitter.com/hashtag/JavaScript) 中不存在。相反，你有原型模式&构造器模式。
> 
> 现在，构造器模式确实类似于 Java 中的继承&这是 JavaScript 的创造者想让 JavaScript 更有市场。🙃04:10am-2019 年 3 月 22 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1108944031172550660)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1108944031172550660)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1108944031172550660)0

如你所知，我一直在思考继承，它与 JavaScript 的关系，以及它与 Java 的区别。但是它非常吸引人，并且帮助我形成了对网络基础的看法，在我看来，这是作为一个开发者进步的标志。

推荐阅读:
📖[作文 vs 传承](https://reactjs.org/docs/composition-vs-inheritance.html)

#### 误区:CSS 不需要掌握，只需要工作知识。

CSS 并不像看起来那么简单。我现在还记得有多少次我试图让一个附带的项目进行下去，并认为我会通过从 StackOverflow 中不经意地复制粘贴 CSS 片段或依靠 CSS 选择器来实现最漂亮的 ui。奇怪的是，这些代码片段只有 10 次中的 3 次有效。10 次中有 7 次我会得到 ragequit，在这个过程中抛弃了另一个副业项目。这一次，我使用方便的开发工具来调试 CSS，我注意到我的 CSS 样式被一种叫做用户代理样式表的东西覆盖了。我的第一印象是谁是用户代理，这是联邦调查局真的在监视我们的一举一动的证据吗？当然是开玩笑。我第一次理解了 **CSS 级联算法**，以及 CSS 规则的三个潜在起源(**用户代理、用户和作者**)是如何相互竞争的，胜利者决定了网页的风格。不完全是。来源是最**加权的**因素，影响最终呈现的样式的结果。

推荐阅读:
📖[CSS 如何工作:理解级联](https://blog.logrocket.com/how-css-works-understanding-the-cascade-d181cd89a4d8/)

请记住，在开始我作为企业物联网应用程序前端开发人员的工作之前，我确定了我应该关注的这两个概念。在一个故事接一个故事的基础上，我相对容易地为代码库做出贡献，但是当任务涉及应用程序中的许多视图时，我很难将这些片段放在一起，以及为什么我们的代码是这样构造的。在精神上，我把 Java 概念投射到 React 应用程序上，低估了 CSS。

向前看，我想解决以下问题:
⚛️道具与状态
⚛️状态管理的 redux
⚛️CSS 特异性
⚛️ DOM 概念
⚛️纯函数
⚛️反应钩子

感谢收听！请告诉我是否有任何我应该添加到我的关注列表中的内容:)