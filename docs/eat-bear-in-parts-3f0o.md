# 分块吃熊

> 原文：<https://dev.to/thekashey/eat-bear-in-parts-3f0o>

> 分部分吃熊=== **一步一个脚印**

我开始写这篇文章已经有一段时间了，出于偶然——只是想分享一个简单的概念。我把《新生的文章》称为“让 Redux 再次变得伟大”，它仍然有一部分是关于它的——让一些东西变得更好，就像它应该的那样。

我正在写这篇文章，这里的一件事，或者那里的另一件事，需要一些“澄清”。通过解释细微差别和使用的术语，或者提供一些例子，我使这篇文章比我曾经计划的要大得多。我创造了一个大的🐻熊🐻，即使当时的想法是~打~吃。

这篇文章以简单的建议开始，但它背后的主要思想仍然是如何保持事情简单的建议。不是简单的大建议，而是由简单的建议构成的。

你知道，你解决不了大问题，大问题太大了，无法解决。也许你应该解决较小的问题——分而治之，解决较大的问题，或者应用被称为"[贪婪](https://en.wikipedia.org/wiki/Greedy_algorithm)"的模式。

> 贪婪算法是一种算法范例，它遵循在每个阶段做出局部最优选择的问题解决启发，目的是找到全局最优...但是尽管如此，贪婪试探法可以在合理的时间内产生近似全局最优解的局部最优解。(谢谢维基百科)

换句话说-🐻**分份吃熊**🐻。

* * *

Redux...我不知道这对你来说是不是一个好词，或者，你知道，“r 字”...Redux 是一个伟大的东西，Redux 做了一些革命，Redux 被认为是有害的，你不需要 Redux。强烈的观点，微弱的支持。

人们经常抱怨——Redux 带来的是痛苦，而不是快乐。他们后悔曾经使用它，并准备尽快抛弃它。

...但是让我们仔细检查一些事情。

> 这篇文章不是关于 Redux，而是关于“Redux 之道”。你必须**思考**的方式，让我们称之为，对它感到满意。

## 选择器

没有选择器的 Redux 非常慢，有了选择器也不会好到哪里去，只要我们的选择器并不总是被正确定义(对自己诚实)。创建完美的选择器并不容易，尤其是使用我们现有的库和模式。

如果没有合适的选择器，就无法达到合适的记忆级别，Redux 会导致错误的肯定更新传播。这就是它的设计工作方式，这不是一个重复的问题——任何“单一来源事件传播”的东西(我看着你，反应上下文)都是受害者。

问题不在于`memoization`本身，而在于“你”——你的期望(你希望它开箱即用)，以及你必须自己匹配的期望(Redux 用户会以适当的方式使用 Redux 库)。

> Redux 犯了一点小错误。它应该调用两次`mapStateToProps`并比较结果。如果结果不匹配，那么`mapStateToProps`就不像它预期的那样纯净，那就是你做错了。JFYI React 对钩子做了完全相同的事情，*保护*你。

通常——这是重要的一部分——检查记忆的质量(或存在)，嗯...，跳过。或者开发人员未能修复一些边缘情况，并放弃了它。结果并不令人敬畏——你和你的用户都不高兴。

常见的记忆问题有:

*   单一高速缓存行。几乎所有的记忆库都只存储最后的结果，这(即使是正确的决定)与“组件方法”不太兼容。
*   API。例如，`reselect`的一个强大特性——记忆级联——要求**所有的**选择器拥有相同的接口。嗯——这让一切都变得肮脏...破碎了。在这种情况下，唯一的公共接口是`props`——你必须传递一个填充了 *everything* 的对象作为第二个参数，而它**应该是一个类型为 number 的`todoId`。这不仅是某种抽象的泄漏，而且在没有改变属性比较函数的情况下(默认是 shallow equal)，默认情况下是行不通的。你可以在 Mark Erikson 的[惯用的 Redux:使用重选选择器实现封装和性能](https://blog.isquaredsoftware.com/2017/12/idiomatic-redux-using-reselect-selectors/)中读到更多，但是，不幸的是，那里没有答案。**

但是有十几种其他的方法可以“修复”记忆，你应该知道:

*   [redux-views](https://github.com/josepot/redux-views) 可以在几行中以“重新选择”的方式修复您的重新选择。在这里阅读更多细节- [我们需要更好的冗余选择器](https://medium.com/@josepot/we-need-better-redux-selectors-457a1ed10732)
*   美丽-反应-还原或[反应-反应-还原](https://github.com/dai-shi/reactive-react-redux)根本不需要任何选择器，两者都使用相同的基于代理的跟踪。
*   随意阅读更多关于记忆问题的文章[记忆遗忘炸弹](https://dev.to/thekashey/memoization-forget-me-bomb-34kh)

但是你应该明白关于`selectors`的一件事——它们不仅仅是为了记忆，记忆在这里只是一个实现细节。选择器在这里从数据中抽象出用户界面。在 [redux-starter-kit](https://github.com/reduxjs/redux-starter-kit) 或 [Dan Abramov 的“惯用 redux”视频系列](https://egghead.io/lessons/javascript-redux-colocating-selectors-with-reducers)中，你甚至可以期望*将*减速器和选择器放在*片*中。

选择器是为了保持你的 UI 组件简单，即使你的数据并不简单。

> UI-连接-存储。像 1-2-3 一样简单。

但是人们忘记了这件事。

## 数据

关于选择器的第二个重要时刻是**它们返回的是什么**—`data`，并且`data`是内存化问题的来源——你必须总是返回相同的结果。

我们刚刚就记忆化进行了一次对话，这次我想问另一个问题——“你需要那些数据吗”？

一个简单的例子——`Todo List`——它需要什么作为输入？todos 数组？数组什么的？只是数组的长度？

“视情况而定”(有史以来最好的回答，btw):

*   在现实中，许多“容器”只需要`length`来决定它们要渲染什么- `ListOfElements` + `Pagination`，或者什么都不用做。
*   比 TodoList 小两倍的`ListOfElements`，可能需要`ids`个元素来渲染，基本上是用它们作为`keys`来提高渲染效率。
*   有什么东西需要数组`Todos`？没有，什么都没有。除了`Todo`本身。

几乎每个人都犯过这样的错误——提供复杂的对象，在不需要的时候泄露底层结构(和抽象)给 UI。

这里有一个需要考虑的代码

```
const TodoHeader = () => (
 <section>
   <button>Add todo</button>  <section>
);

const TodoFooter = ({count}) => (
 <section>
   There is {count} todos found, have a great day!
 <section>
);

const TodoList = ({todos}) => (
 <>
  <TodoHeader />
  {todos.map(todo => <Todo key={todo.id} action={todo.action} />)}
  <TodoFooter count={todos.length} />
 </>  );

<TodoList todos={[allMyTodos]} /> 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错，绝对标准。我想这就是你的零件的样子。

但是如果我们只改变一行呢？

```
const TodoList = ({count, children}) => (
 <>
  <TodoHeader />
  {children} // this line
  <TodoFooter count={count} />
 </>  );

<TodoList todoCount={allMyTodos.length}>
 {allMyTodos.map(todo => <Todo key={todo.id} action={todo.action} />)} </TodoList> 
```

Enter fullscreen mode Exit fullscreen mode

这样更简单吗？好点了吗？我已经从一个组件创建了两个组件，我可能应该解释一下自己——“为什么我要把简单代码变得更复杂？”。

> 这实际上是“控制反转”(IoT)，在这里你是*“将控制权交还给你的组件的用户，以他们认为合适的方式实现和定制它”*。想想看——`children`里面的`TodoList`被定义在了绝对不同的地方。您不必执行**道具钻孔**来为另一个组件内的组件提供额外的道具，只要该组件🙀**是外面的**🙀。找到了[这里](https://medium.com/the-non-traditional-developer/your-react-components-are-doing-too-much-16e65968f419)，你可以阅读更多关于[支柱钻孔这里](https://kentcdodds.com/blog/prop-drilling)。

这里正确的问题应该是[何时将一个组件分解成多个组件](https://kentcdodds.com/blog/when-to-break-up-a-component-into-multiple-components)。我指的是肯特·c·多兹的文章，同样，这篇文章没有提供任何*可行的*答案。

打破组件是一个非常固执己见的过程-有时更容易理解粗糙的组件，但有时更多的“声音和尖锐”会更方便。这与个人喜好无关，这与你脑海中的“图片”的大小有关。嗯，人类的大脑对此并不擅长。

但我至少有 3 个好答案给你:

*   **第一个**是一个分裂，然后它是一个以上的组件。如何检测？嗯，多个返回点是一个甜蜜点。

> ![Dinesh profile image](img/aac4da05efbfdaee5f51c9a1b778b55c.png)Dinesh[@ flexdinesh](https://dev.to/flexdinesh)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)React Today—
> 
> 如果你不能把所有的 jsx 放在一起，就把它们解压成单独的组件。不要编写有条件地返回不同 JSX 的方法。这让调试变成了一场噩梦。2019 年 7 月 24 日上午 00:38[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1153826744920489985)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1153826744920489985)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1153826744920489985)

> 然而，这个建议并不完全适用于类组件，在类组件中，你可能有许多类方法，做它们的小工作来为一个大的`render`函数组装结果。同样的多个组件——它们甚至看起来是同样的小功能——只能够使用一个`this.props`和一个为所有人共享的`this.state`。

*   第二个，也是最棒的一个——**勾手**。钩子有一个简单的规则- **钩子规则** -当你不能改变它们的执行顺序时。用一种*顺向逻辑和*顺向逻辑你能创造多少？很清楚-不多。钩子鼓励你把事情保持在一个合理的界限内，一个安全的地方，在那里你不会绕过它们的法则。基于钩子的组件几乎有天生的复杂性限制。

*   最后一个，**第三个**，答案是**数据**——如果你的组件的不同部分需要不同的数据，或者他们需要的数据是从另一个数据派生出来的，因此他们实际上需要不同的东西——那么可能是几个不同的组件合并在一起。为什么

让我们回忆一下我们的代码:

```
// That's not a "list" - it has Menu at the Header
const TodoContainer = ({count, children}) => (
 <>
  <TodoHeader />
  {children}
  <TodoFooter count={count} />
 </>  );

const TodoList = ({todos}) => (
 todos.map(todo => <Todo key={todo.id} action={todo.action} /> )

// we are "deriving" `todoCount` from `allMyTodos.length`
const Todos = ({ todos }) => (
  <TodoContainer todoCount={todos.length}>
   <TodoList todos={todos} />
  </TodoContainer >  ) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们进行下一步，稍微重构一下这段代码:

```
// just add one more prop - `displayCount`
// "derived" props are "derived" elsewhere, not here.
- const Todos = ({ todos }) => ( + const Todos = ({ displayCount, todos }) => (
  <TodoContainer todoCount={displayCount}>
    <TodoList todos={todos} />
  </TodoContainer > 
);

export default connect(
 state => ({
   displayCount: state.todos.length, // imagine a selector
   todos: state.todos, // imagine a selector
}))(Todos) 
```

Enter fullscreen mode Exit fullscreen mode

现在每个部分只消耗它所需要的东西，我们不再混合东西，并对我们的用户界面有更多的控制。

可能不太清楚为什么`displayCount`应该作为一个单独的道具提取，所以可以回答一个简单的问题——如果`todo.length`达到 10000 会发生什么？我希望-分页。你将如何实现分页，或者更具体地说，在哪里——在`Todos`级还是`TodoList`级？在第二种情况下，`todo.length`仍然会反映“正确”的价值，但是在第一种情况下，它不会。

你会选择哪种情况？抱歉，但我不相信任何遥远的未来预测，因此另一个问题——哪种情况更能证明未来*的不可预测性？Well - where `displayCount`没有绑定到`todos`。这种情况下更**优化的变化**。*

> 如果您将在服务器端实现分页会怎样？你将从一个端点得到 N 个 todos，比如`/todos/get/:page`，从类似`/todos/stat`的地方得到总数。😉“为变化而优化”

但是让我们挑选另一个例子，从一个稍微不同的角度来尝试这个问题——让我们**获取一些关于组件安装的数据**。

```
const Component = ({page, pageData}) => {
  useEffect(() => 
    dispatchDataLoading(page.id);
  }, [page.id]

  return <Page>{pageData}</Page> }

// connect this Component somehow 
```

Enter fullscreen mode Exit fullscreen mode

这里我们用*对`page.id`值的*作出反应，分派一些事件来获取我们需要的数据，并最终用获取的数据呈现这个组件。
和**两次**我们都在做两倍于我们需要的事情——试图渲染不再存在的数据，或者试图加载我们还不需要的东西。

简而言之——这是两个不同的组件——一个视图组件和一个“兄弟效应”组件。

```
const SiblingEffectComponent = ({page}) => {
  useEffect(() => 
    dispatchDataLoading(page.id);
  }, [page.id]

}

const ViewComponent = ({pageData}) => {
  return <Page>{pageData}</Page> }

const Component = ({page, pageData}) => (
  <>
   <SiblingEffectComponent page={page}/>
   <ViewComponent data={data} />
  </> );
// connect this Component somehow 
```

Enter fullscreen mode Exit fullscreen mode

这种重构还有另一个有趣的效果——现在你可以在渲染视图之前在兄弟**中执行效果——它们不再相互绑定。然而，它仍然是相同的“渲染周期”。
数据更新传播也是分开的，现在您可以用`React.memo`包装两个组件，这次**可以工作**，因为长数据更新也是分开的。**

将一些副作用*放到一边*——这是一个简单且非常强大的解决性能问题的模式，让应用程序的不同部分只响应它们需要的东西。兄弟姐妹组成了家庭。

> 保持 UI 简单——保持数据简单。这可能需要创建两次以上的选择器，但是...但是这有帮助吗？

## 故事书

很多次听到关于`Redux`和`Storybook`的抱怨。就像很难给`story`提供合适的嘲讽`Store`。或者，如果不提供，那么维持。真实故事！

虽然有大的“端到端”的故事，下面有一个真实的状态，但这是很好的——你不必在任何事情上都使用(好吧，你不能使用)这种故事。问题不在于`redux`或`components`——问题在于*的复杂性*——有太多移动的棋子。

让我们想象你做的每件事都是对的。我们会对故事书有什么问题吗？

*   将 UI 组件开发为 UI 组件。你懂的——**无状态哑组件**。而且不仅仅是无状态，而是“简单”。
*   使用`selectors`将这些 UI 组件连接到底层的`Store`，这将扮演一个经典 3 层系统的抽象层的角色。

> 👨‍🔬UI-连接-存储。像 1-2-3 一样简单。

*   如果底层(商店)发生变化，你只需要改变选择器。
*   如果以后用户界面发生了变化，你只需要改变选择器。
*   请保持层次分离，这将有助于你很多。

然后——提供正确的状态会更容易，只要你的 UI 组件是完全抽象和简单的。

但是，你知道，你的*故事里的第一个`connected component`*是个扫兴的人。它将尝试连接到商店，从中读取一些内容，最终会失败。或者，至少，不会代表您需要的“状态”。因为在“静态”故事书中没有真正的存储，为不同的故事提供正确的状态只是一个尚未解决的问题(一般来说)。

> 想象一个选择器。所有 UI 组件消耗简单类型，控制选择器(谁说嘲讽？)你可以控制一切。你可以控制选择器，[嘲讽在故事书](https://medium.com/@antonkorzunov/rewrite-a-storybook-ae4682176cc3)里有，但这是正确的方式吗？

无论如何——故事书仍然是个问题，上面的任何东西都不能解决这个问题。
如果从另一个角度处理这个问题会更好**会怎么样？**

 **## 依赖注入

尚未解决的问题是“保持组件简单”，以及“有太多的移动部件”。我的意思是——你可以通过简化来简化你的用户界面，随着用户界面的成长——所有的简单性都会消失。事情可能很简单，但只是在它们很小的时候——大而简单的事情甚至比大而复杂的事情更糟糕。

解决方案-用更大的部件制造更小的部件。就像我们对待办事项列表所做的一样。

但是我们创造的解决方案太，姑且称之为，- *纠结*。让我们解开它！

*   这是我们最初的托多斯。这是一个单一的组成部分。

```
const Todos = ({ displayCount, todos }) => (
  <TodoContainer todoCount={displayCount}>
    <TodoList todos={todos} />
  </TodoContainer >  );

export default connect(
 state => ({
   displayCount: state.todos.length, // imagine a selector
   todos: state.todos, // imagine a selector
}))(Todos) 
```

Enter fullscreen mode Exit fullscreen mode

*   这就是魔法...它仍然是单一的成分，但是*在空间中分散*。

```
// removing "tangled" TodoList from here, replacing it by {todoList}
const Todos = ({ displayCount, todoList }) => (
  <TodoContainer todoCount={displayCount}>
    {todoList} // or just "children"
  </TodoContainer >  );

export default connect(
// map state to props
 state => ({
   displayCount: state.todos.length, // imagine a selector
   todos: state.todos, // imagine a selector
}), 
// map dispatch to props
null,
// merge props
(stateProps) => ({
  displayCount: stateProps.displayCount, // keep this prop
  todoList: <TodoList todos={todos} />, // WHAT???
});
)(Todos) 
```

Enter fullscreen mode Exit fullscreen mode

*魔法*——我们刚刚从`connect`开始给我们的 TodoContainer 注入了内部构件。这是一种纯粹的依赖注入模式，但是以一种*组件*的形式。

你可能会问——好吧，那么这改变了什么？(除了我们之前谈过的控制反转)

## 有限构件

这改变了你控制组件的能力。这使得它们是“有限的”。只要那些*无限的*内部构件被提供作为道具，任何组件都不会在其中呈现某些东西，“直到最后”。

每个“下一步”都是作为`slot`(儿童)道具提供的。每一个没有提供`slots`的组件都是*有限的*——它将什么都不呈现。这将是紧凑和简单的，因为它一直被期待。

[![thekashey](img/638fcfb14e5c451b9d2dc31d89e5b034.png)](/thekashey) [## 有限战争

### 安东·科尔祖诺夫 1 月 1 日 196 分钟阅读

#react #testing #javascript](/thekashey/infinite-war-995)

这使得(单元)测试更简单，开发更简单，故事书更简单，给你完全不同的可重用性级别，你需要的一切——就是正确地连接组件。

> PS:还有想想 BEM 架构。从现在开始听起来可能会很有趣。**绝对独立块**意味着什么(BEM 技术的第一个名字)

#### 是否治愈故事书？

想象一下你有一个故事给 ComponentA。出于故事书的目的，你提供了所有你需要的东西作为道具，甚至用`Provider`包装你的组件，提供了“正确的”状态。

> 你有你的`StorybookedComponentA`。

现在假设你有一个 ComponentB 的故事，它在里面的某个地方使用了 ComponentA。

*   在*真实的*应用程序中，你将*从连接函数中提供*作为`componentA`插槽，就像上面的例子一样。
*   在故事书中，你可以为组件 A 提供一个*故事，即`StorybookedComponentA`，而不是一个“真实组件”，这样你就可以从另一个故事中**构建一个故事🤓。***

再说一次——故事是一个“情境”,你可以从一个小的情境中控制一个大的情境，

新的[故事书组件格式](https://medium.com/storybookjs/component-story-format-66f4c32366df)将帮助你:

只要分块吃熊，熊就会被吃掉！

## 火线

现在我们正接近结论——Smart/Dump**关注点分离**，这是当今真正的“s 字”。

关注点的分离为您提供了:

*   并行开发特性的能力。一类开发人员(UI)会创建 UI，而另一类开发人员会完成状态管理。相信我，这些任务需要**不同的**技能，所以最好使用不同专业的开发人员来获得更好的结果。
*   更改 UI 和底层状态管理的更简单的方法——它们并不相互绑定。
*   更好的划分和创建组件的规则，更好的全局状态和局部状态的边界。如果给你一个“创建 UI”的任务，你会清楚地知道哪些变量应该包含在你的解决方案中，哪些应该是外部的。
*   错误率更低。即使出现错误，也更容易处理。一切都是小的、分离的、孤立的，就像...喜欢`hooks`。

只要我们说的是`Redux`——我们说的是`connect`功能，并不“花哨”。而`reselect`并不“花哨”。只有`Hooks`是花里胡哨的！

钩子能拯救世界吗？

## v7

Redux v7 是游戏改变者。它给你钩子。老实说——这是我第一次反对——我希望在我的“反应”组件中尽可能少地使用逻辑。但是后来，你知道，我习惯了。

`useCallback`、`useMemo`——还有其他你用不到的东西——太美了！

```
const Todos = ({ displayCount, todoList }) => (
  <TodoContainer todoCount={displayCount}>
    {todoList} // or just "children"
  </TodoContainer >  );

// the same `connect`, this time with `useMemo`
// and, the main, in a "common" form.
const TodoConnected = () => {
  const displayCount = useSelector(state => state.todos.length);
  const todos = useSelector(state => state.todos);
  const todoList = useMemo(() => <TodoList todos={todos} />, [todos]) 
  return <Todos displayCount={displayCount} todoList={todoList} /> } 
```

Enter fullscreen mode Exit fullscreen mode

但是有一个问题——**reselect**不是钩子兼容的。任何记忆级联，没有这种模式重选都不会好，都不符合钩子定律——只要它们可能对一个选择器的不同部分进行不同的记忆(它们可能有记忆，这已经足够了)——它们就会违反钩子的**规则**。

> 没有这种模式，任何记忆级联和重选都是不符合钩子定律的

有很多方法可以缓解这个问题，比如 [redux-views](https://github.com/josepot/redux-views) 或者[userselect](https://github.com/theKashey/useReselect)，但是同样的，他们在实现上存在争议。

更简单的解决方案是*比你想象的更简单的*——只需替换 v7 的`useSelector`buy[useTrackedSelector](https://github.com/dai-shi/reactive-react-redux#useselector)，这将让你**跳过任何可能的记忆**问题。

老实说——我不推荐使用基于[钩子的记忆选择器](https://react-redux.js.org/next/api/hooks#using-memoizing-selectors)作为它提出的 react-redux——它又一次受到了重新选择的限制。

使用追踪有其自身的局限性。

### 第 4 个答案一个组件拆分

使用追踪有其自身的局限性。它只是跟踪你从状态中读取的变量，不知道为什么。它使用了一些非常符合逻辑的假设，这些假设是...他们只有一个我们有的:

*   如果你阅读`props.a` -你需要`props.a`
*   如果你读`props.a.b`——你需要`props.a.b`，但你不“需要”`props.a`——它是你需要的实变量的一个“持有者”。像`state`只是一个“持有者”。假设是——你只需要最深层的“真实”变量，而不需要中间变量。
*   所以如果你真的需要同时得到`props.a`和`props.a.b` **的值**——你必须**分离**组件，或者**隔离**选择器中的这些读取。

在下面的例子中，如果`state.todos.length`保持旧值，则`state.todos`改变时不会触发更新。

```
const Component = () => {
  const state = useTrackedState(context);

  return (
    <div>
     I have {state.todos.length} things todo:
     <TodoList todos={state.todos} />
    </div>
  ) 
```

Enter fullscreen mode Exit fullscreen mode

解决方案:拆分组件，隔离读取，或者“在其他地方派生道具”(我们以前就这么做过)

再说一次——你必须理解你的组件中的“边界”，并且*分离*这些*关注点*——否则魔法将不起作用。

下面是一些背后的理论:

[![thekashey](img/638fcfb14e5c451b9d2dc31d89e5b034.png)](/thekashey) [## 我如何编写世界上最快的记忆库

### 安东·科尔祖诺夫 4 月 16 日 19 时 11 分阅读

#memoization #javascript #proxy #weakmap](/thekashey/how-i-wrote-the-world-s-fastest-memoization-library-34io)

如今，依靠自动使用跟踪比依靠你自己的技能更好。或者用 MobX。我是认真的。

> ![unknown tweet media content](img/4e7ef08a2c2cd0f0654ff0d316962181.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/EAGflD6WsAYWpGk.mp4" type="video/mp4"></video>![Kitze profile image](img/2a7c68f8967f91cc03bc37e09f5a1e60.png)Kitze[@ the Kitze](https://dev.to/thekitze)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ mweststrate](https://twitter.com/mweststrate)For the gazzilion 第一次我很生气，人们都忽略了 mobx-state-tree 是多么的神奇。这是棵他妈的表演树。*MADE*用于存储您的全局状态。没有重选，没有道具训练，没有保姆上下文。用了就好，早点回家。我操。2019 年 7 月 22 日下午 18:57[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1153378446099255298)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1153378446099255298)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1153378446099255298)

-

# 不是的翻版？

另一个要回答的大问题是，Redux 用在哪里，哪里的**钩子，上下文**，好友应该是**首选**。

同样，这不是一个正确的问题。同样，正确的问题应该是关于界限的。

> 当一个状态管理系统应该结束时，另一个状态管理系统开始。

这不是 Redux 和本地状态之间的边界点——它可能是`One Redux`和`Another Redux`之间的一个点。这更多的是关于[分形状态](https://blog.cloudboost.io/the-state-of-the-state-of-the-state-f93c8bdc6b1b)和**微前端**而不是 Redux 本身。而不是**隔离**。

考虑一下您应用程序是如何构建的。

*   应用程序的一部分是由按钮(原子)、按钮形式形式(分子)和页面(有机体)组成的。通常下层棋子没有任何状态，由它们控制`owner`。业主也在这么做。这是你正在构建你的“可重用”元素，它只是*服从*，没有任何“自己的”意见。
*   还有你的应用程序的“想法”，以及实现它的主要数据——“状态”。这种状态存在于顶部，并向下流动。It 和与“it”一起工作的组件确实有他们“自己”的观点。
*   中间有复杂的情况，在 saga 或其他中间件的后面可能需要“本地”状态变量。有时你可能只是将它作为你的“动作”的一部分发送，但有时更容易“同步”`local`和`global`变量。

> 真的，如果可以控制`input`的`value`并更新本地状态`onChange`——为什么不能在 redux 方面做同样的事情呢？许多表单管理库也在做同样的事情。
> PS:反向同步也不是大问题。
> PPS:真理的单一来源？单向同步没有问题。

# 所以？！

所以我会用更多的例子来修改这篇文章，到时候它会变得越来越好。

我可以给你一些建议:

*   试着“看到”复杂性，然后试着避免它。如果某件事对你来说不是很复杂，对另一个人来说可能会很复杂，包括明天的你。
*   尽量使用 POD(普通旧数据)类型——不要让复杂的结构渗透到你的应用程序中。如果一些数据(一个东西)应该是复杂，那么“复杂”对象仍然有一些位置，但是尝试从目标 UI 或逻辑要使用的数据中*导出*更简单(和更合理)的值。
*   “直呼其名”。如果你需要显示你有多少 todos 那是`todoCount`，不是`todos.length`。
*   使用物联网模式消除组件组合的复杂性。其实就是成分构成。
*   尝试将 UI、状态管理和数据访问分开。
*   分裂和征服者。每当问题变得太大而无法解决时——打破它，分解它。
*   同时——避免引入无意义的小片段，这可能会导致意大利面条式的代码，或者只是在你的头脑中不适合所有的部分。
*   试着思考事情。在域中。在层次和需求中(嗯[这里有更多关于它的内容](https://dev.to/thekashey/prefer-integration-tests-think-twice-1l6d))。
*   尽量让一切都在掌控之中。

但是你应该已经明白吃你的熊的唯一方法是🐻**分份吃节拍**🐻。**