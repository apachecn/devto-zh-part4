# 像老板一样通过 React 的树传递数据😎

> 原文：<https://dev.to/bassemibrahim/pass-data-through-react-s-tree-like-a-boss-2fbi>

嘿👋，欢迎回到另一篇博文。这一次，让我们看看如何像 **BOSS** 一样通过 React 的组件树传递数据😎

# 正常(非 BOSS)的方式是什么？

我们先来看看最简单的方法。只需将数据作为道具通过整棵树。想象以下场景，我们有一个名为 *StatusComponent* 的组件，它呈现描述应用程序状态的简单文本。对于我们的示例应用程序，我们有下面的树:

### 我们的示例应用的组件树:

-> App
- > - >首页
- > - > - >侧边栏
- > - > - > - >状态组件

当然，在这里我将使用一个小的示例应用程序。现实生活中可能有几十个组件。这意味着非最优的方法可能会对可维护性和可读性产生严重的不良影响。

```
// App is calling the HomePage and passing those data as props
<HomePage
    successMsg="App initialized"
    loadingMsg="Loading app…"
    errorMsg="Error encountered"
    serverDownMsg="Server down, Try again later!"
/>

// HomePage is calling SideBar and passing its props along
<SideBar
    successMsg={this.props.successMsg}
    loadingMsg={this.props.loadingMsg}
    errorMsg={this.props.errorMsg}
    serverDownMsg={this.props.serverDownMsg}
/> 
// SideBar is calling StatusComponent and passing its props along
<StatusComponent    
    successMsg={this.props.successMsg}
    loadingMsg={this.props.loadingMsg}
    errorMsg={this.props.errorMsg}
    serverDownMsg={this.props.serverDownMsg}
/> 
// Finally, StatusComponent is using the data passed all the way from App
switch(state) {
    case normal : return <p{this.props.successMsg}/>
    case loading : return <p{this.props.loadingMsg}/>
    case error : return <p{this.props.errorMsg}/>
    case serverDown : return <p{this.props.serverDownMsg}/> } 
```

Enter fullscreen mode Exit fullscreen mode

这很简单明了。👍但是，如果在整个组件树中使用，可能会非常糟糕。👎您将继续在树中传递数据。你的组件会变得太大太快。代码将被重复。**(上帝禁止！😠)**

我们能做什么？我们如何优化这一点？我们先来分析一下上面的代码！🧐这里有两个因素在起作用:

1.  保存数据的变量数量(在我们的例子中是 4)
2.  数据必须通过的组件数量，这里只有 2 个。*应用程序*拥有数据，而*状态组件*正在使用这些数据。这样我们就剩下了*主页*和*侧边栏*。

我有两种策略可供选择🔥🔥。每种策略都将解决其中一个因素。

# 分组变量:

让我们处理第一个因素，保存数据的变量的数量。

**这是 Javascript！我们可以简单地把 4 分成 1 组。但是不要把你发现的任何变量分组。尽量只将逻辑上相关的分组。幸运的是，在我们的案例中，所有 4 个变量都是相关的。** 

```
// App is calling the HomePage and passing those grouped variables as props
<HomePage
    messages= {
        successMsg:"App initialized",
        loadingMsg:"Loading app…",
        errorMsg:"Error encountered",
        serverDownMsg:"Server down, Try again later!"
    }
/> 
// HomePage is calling SideBar and passing its props along
<SideBar
    messages={this.props.messages}
/> 
// SideBar is calling StatusComponent and passing its props along
<StatusComponent
    messages={this.props.messages}
/> 
// Finally, StatusComponent is dividing the grouped variable passed all the way from App
switch(state) {
    case(success)    : return <p>{this.props.messages.successMsg}</p>
    case(loading)    : return <p>{this.props.messages.loadingText}</p>
    case(error)      : return <p>{this.props.messages.errorText}</p>
    case(serverDown) : return <p>{this.props.messages.serverDownText}</p>  } 
```

Enter fullscreen mode Exit fullscreen mode

这看起来更好，不是吗？我们可以到此为止吗？

不要！尽管如此，这里还是有可以优化的地方！你已经知道了吗？主页和侧边栏实际上并不需要这些数据。他们只是充当数据传递的桥梁。我闻到了**代码重复** 🧐😠。但是我们不能就这样把它去掉。或者我们可以吗？😈

# 使用 React 上下文:

让我们来解决第二个因素，数据必须通过树的级数。这里我们需要的是上下文。

根据 React docs， **Context** 提供了一种通过组件树传递数据的方式，而不必在每一层手动传递属性。这对于像区域设置首选项和 UI 主题这样的全局数据来说是完美的。应用程序中的许多组件都需要数据。

想看看例子吗？当然可以！

```
// First we create a context
const MessageContext = React.createContext({
    successMsg:"App initialized",
    loadingMsg:"Loading app…",
    errorMsg:"Error encountered",
    serverDownMsg:"Server down, Try again later!"
});

// App render method
<MessageContext.Provider>
    <HomePage />
</MessageContext.Provider> 
// HomePage is calling SideBar without passing props
<SideBar/>

// SideBar is calling StatusComponent without passing props
<StatusComponent/>

// StatusComponent receiving its required data from the context instead of its parent
static contextType = MessageContext;
render() {
    switch(state) {
        case(success)    : return <p>{this.context.successMsg}</p>
        case(loading)    : return <p>{this.context.loadingText}</p>
        case(error)      : return <p>{this.context.errorText}</p>
        case(serverDown) : return <p>{this.context.serverDownText}</p>     }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面的代码中看到的。我们不会把数据当作道具来传递。使用上下文，我们不关心我们必须经历多少组件深度。可以从组件树中的任何位置访问上下文中的数据。

您可以在 [React 上下文文档页面](https://reactjs.org/docs/context.html)中找到更多详细信息。

你觉得这篇文章有用吗？你已经知道这一切了吗？请在下面的评论中告诉我。

以 always 为例，t0±t1±happe code & & & & & & &