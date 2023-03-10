# 优化具有错误边界的 React 组件

> 原文：<https://dev.to/ogwurujohnson/optimizing-react-components-with-error-boundaries-2doh>

> 免责声明:本文中讨论的概念是 React 高级概念的一部分，如官方文档中所述。但是我认为这是每个 react 开发人员都应该知道和使用的东西。在本教程结束时，你会发现我的观点是正确的。

#### 反应过来的我们知道:

按照其最初的设计，当 JavaScript 错误发生在一个组件中，小到一个`Button`组件，就会导致 react 应用程序完全崩溃和失败。当时 react 没有提供处理这种情况的方法，也不能在这些错误发生时恢复。

但是现在，随着 React 16 的出现，facebook 的 React 团队引入了一个名为`error boundaries`的错误处理新概念。

#### 引入误差边界:

随着`error boundaries`的引入，组件可以有错误并平静地崩溃，而不必影响整个应用程序状态，从而导致应用程序范围的崩溃

根据 [React 文档](https://reactjs.org/docs/error-boundaries.html)，

> 错误边界是 React 组件，它捕捉子组件树中任何地方的 JavaScript 错误，记录这些错误，并显示一个回退 UI，而不是崩溃的组件树。错误边界在渲染期间、在生命周期方法中以及在它们下面的整个树的构造函数中捕捉错误。

错误边界的工作方式类似于 JavaScript 中的`catch(){}`块，但这次是针对组件的。有人可能会说，既然它像 JavaScripts 的`catch(){}`块一样工作，为什么我们不使用`try/catch`来创建错误边界。所以我们可以有这样的东西:

```
 try { 
         <ShowButton /> 
      } catch(err) {
         console.log(err)
      } 
```

Enter fullscreen mode Exit fullscreen mode

这看起来不错，可以解决我们想要用`Error Boundaries`解决的问题，但问题是`try/catch`只对命令式代码有效，但因为我们使用的是声明性的 react，react 组件是声明性的，并指定应该呈现什么。但是有了`Error Boundaries`，React 的声明性得以保留。

> 只是补充一下这篇文章，要了解命令式代码和声明式代码的区别，看看这些文章，[这里](https://codeburst.io/declarative-vs-imperative-programming-a8a7c93d9ad2)和
> 这里

* * *

错误边界，只能是类组件，我怎么说呢，你不能用功能组件，去创建一个`error boundary`，而是一个类组件。

对于被认为是错误边界的类组件，它需要有生命周期方法 static `getDerivedStateFromError()`或`componentDidCatch()`中的一个(或两个)。其中,`getDerivedStateFromError()`用于呈现带有开发人员指定的错误响应的回退用户界面，而`componentDidCatch()`用于记录错误信息，因此这里您可以使用您选择的任何日志服务，或者我们最喜欢的`console.log`。

* * *

跳到代码中，这就是错误边界的样子；

```
 import React, {Component} from 'react';
 class ErrorBoundary extends Component {
    constructor(props) {
       super(props);
       this.state = { hasError: false };
    }

    static getDerivedStateFromError(error) {
       // Update state so the next render will show the fallback UI.
       return { hasError: true };
    }

    componentDidCatch(error, info) {
       // You can also log the error to an error reporting service
       logErrorToMyService(error, info);
    }

    render() {
      if (this.state.hasError) {
        // You can render any custom fallback UI
        return <h1>Something went wrong.</h1>;
      }

      return this.props.children; 
    }
 } 
```

Enter fullscreen mode Exit fullscreen mode

> 代码示例摘自 react 文档，对于进一步的阅读，我建议您看一看。

有了代码中的`Error boundaries`,我们就不会在按钮组件渲染中出现错误，杀死整个应用程序，我们将完全控制这些事情，应用程序的用户也不会疑惑发生了什么。 [![confused gif](img/188ee4a688bd83d49805c4d417798d9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUeKseru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/kaq6GnxDlJaBq/giphy.gif%3Fcid%3D790b76115d480c6d6e704676777dfb3f%26rid%3Dgiphy.gif)

要使用创建的`error boundary`，我们所要做的就是，将任何我们希望被错误边界覆盖的组件包装在其中；

最后，需要注意的是，错误边界不能在事件侦听器中使用。当处理事件监听器时，最好使用`try/catch`块。

> 注意:如何使用`error boundaries`，完全取决于你，你可以将整个应用程序封装在一个错误边界内，或者简单地将单个组件封装在一个错误边界内。但是，仍然强烈建议您只将放在几个重要的位置。

关于错误边界的进一步阅读，我不会推荐 react 文档[以外的其他来源](https://reactjs.org/docs/error-boundaries.html)

我希望你像我一样喜欢使用`error boundaries`。我就说到这里，如果你有任何问题或反馈，欢迎在[推特](https://twitter.com/devopsjay)上评论或留言