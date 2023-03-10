# 仅使用 React 挂钩的状态管理

> 原文：<https://dev.to/bnevilleoneill/state-management-using-only-react-hooks-5871>

**由[绵羊奥凯](https://blog.logrocket.com/author/ovieokeh/)T3]撰写**

Hooks API 带来了一种全新的编写和思考 React 应用的方式。到目前为止，我最喜欢的钩子之一是`useReducer`，它允许您处理复杂的状态更新，这也是我们将在本文中看到的。

在大型 React 应用程序中管理共享状态通常需要引入第三方库，如 Redux 和 MobX。这些第三方库使得以更加可预测和细粒度的方式更新应用程序的状态变得更加容易，但是它们通常会带来额外的开销和学习曲线。

好消息是，由于`useReducer`，你现在可以在没有额外的包装和学习曲线的情况下获得同样的好处——好吧，也许是一个微小的曲线。到本文结束时，您应该能够以可预测的方式管理应用程序的状态，而不需要任何第三方包。

> **注意:**这不是一篇抨击 Redux、MobX 或任何其他状态管理包的文章。他们都有闪光的案例。本文旨在让您熟悉`useReducer`以及如何使用它来管理应用程序的状态。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 什么是`useReducer`？

在我们进入如何使用`useReducer`来管理共享状态之前，我们必须解构它，这样我们才能更好地理解它。

这是从 v16.8 开始，现在与 [React 一起发布的新的定制钩子之一。它允许您在分派某些动作时更新组件的部分状态，这与 Redux 的工作方式非常相似。](https://reactjs.org/docs/hooks-intro.html)

它接受一个 reducer 函数和一个初始状态作为参数，然后为您提供一个状态变量和一个调度函数，使您能够更新状态。如果你熟悉 Redux 如何通过 reducers 和 actions 更新存储，那么你已经知道`useReducer`是如何工作的了。

## `useReducer`如何工作？

一个`useReducer`需要两件事来工作:一个初始状态和一个缩减函数。我们将在下面看到它们的样子，然后详细解释它们的用途。

考虑下面的代码片段:

```
// we have to define the initial state of the component's state
const initialState = { count: 0 }

// this function will determine how the state is updated
function reducer(state, action) {
  switch(action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 }
    case 'DECREMENT':
      return { count: state.count - 1 }
    case 'REPLACE':
      return { count: action.newCount }
    case 'RESET':
      return { count: 0 }
    default:
      return state
  }
}

// inside your component, initialize your state like so
const [state, dispatch] = useReducer(reducer, initialState); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们为组件定义了一个初始状态——一个根据调度的动作更新状态的 reducer 函数——并且在第 21 行初始化了组件的状态。

对于没有和 Redux 合作过的人，我们来分解一下。

### `initialState`变数

这是组件第一次挂载时的状态默认值。

### 减速器功能

我们希望在某些动作发生时更新组件的状态。该函数负责根据动作指定状态应该包含的内容。它返回一个对象，然后用该对象替换状态。

它接受两个参数:`state`和`action`。

`state`是应用程序的当前状态，`action`是一个包含当前发生的动作细节的对象。它通常包含一个表示动作的`type:`。`action`还可以包含更多的数据，通常是状态中要更新的新值。

一个动作可能是这样的:

```
const replaceAction = {
  type: 'REPLACE',
  newCount: 42,
} 
```

Enter fullscreen mode Exit fullscreen mode

回头看看我们的 reducer 函数，可以看到一个检查`action.type`值的 switch 语句。如果我们已经将`replaceAction`作为当前动作传递给了我们的 reducer，那么 reducer 将返回一个对象`{ count: 42 }`，这个对象将被用来替换组件的状态。

### 调度一个动作

我们现在知道什么是 reducer，以及它如何通过被调度的动作来确定组件的下一个状态。然而，我们如何派遣这样的行动？

回到代码片段，检查第 21 行。您会注意到`useReducer`在一个数组中返回两个值。第一个是状态对象，第二个是一个叫做`dispatch`的函数。这是我们用来分派动作的。

例如，如果我们想要分派上面定义的`replaceAction`，我们应该这样做:

```
dispatch(replaceAction)

// or

dispatch({
  type: 'REPLACE',
  newCount: 42,
}) 
```

Enter fullscreen mode Exit fullscreen mode

Dispatch 只不过是一个函数，由于 JavaScript 中的函数是一等公民，我们可以通过 props 将它们传递给其他组件。这个简单的事实就是你可以在应用中使用`useReducer`来代替 Redux 的原因。

## 用`useReducer`代替 Redux

因为你正在阅读这篇文章。你是怎么利用这些来摆脱 Redux 的？

我们知道如何调度一个动作来更新组件的状态，现在我们将看一个场景，其中根组件的状态将作为 Redux 存储的替换。

让我们定义一下我们商店的初始状态:

```
const initialState = {
  user: null,
  permissions: [],
  isAuthenticating: false,
  authError: null,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的减速器功能:

```
function reducer(state, action) {
  switch(action.type) {
    case 'AUTH_BEGIN':
      return {
        ...state,
        isAuthenticating: true,
      }

    case 'AUTH_SUCCESS':
      return {
        isAuthenticating: false,
        user: action.user,
        permissions: action.permissions
        authError: null,
      }

    case 'AUTH_FAILURE':
      return {
        isAuthenticating: false,
        user: null,
        permissions: []
        authError: action.error,
      }

    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们的根组件。这将保存存储并将所需的数据和分派功能传递给需要它们的组件。这将允许子组件根据需要读取和更新存储。

让我们看看它在代码中的样子:

```
function App() {
  const [store, dispatch] = useReducer(initialState)

  return (
    <React.Fragment>
      <Navbar user={store.user} />
      <LoginPage store={store} dispatch={dispatch} />
      <Dashboard user={store.user} />
      <SettingsPage permissions={store.permissions} />
    </React.Fragment>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们设置了`App`来处理存储，这是我们将存储值传递给子组件的地方。如果我们使用 Redux，我们必须使用`Provider`包装所有的组件，创建一个单独的商店，然后对于每个需要连接到商店的组件，使用`connect`将它们包装在一个特设中。

但是，使用这种方法，我们可以绕过使用所有样板文件，直接将存储的值作为道具传递给组件。我们可以有尽可能多的储备，减少，初始状态等。而不必引入 Redux。

好了，让我们写一个登录函数，从`<LoginPage />`组件调用它，观察商店是如何更新的。

```
async function loginRequest(userDetails, dispatch) {
  try {
    dispatch({ type: 'AUTH_BEGIN' })
    const { data: { user, permissions } } = await axios.post(url, userDetails)
    dispatch({ type: 'AUTH_SUCCESS', user, permissions }) 
  } catch(error) {
    dispatch({ type: 'AUTH_FAILURE', error: error.response.data.message })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们会像这样在`LoginPage`组件中使用它:

```
function LoginPage(props) {
  // ...omitted for brevity
  const handleFormSubmit = async (event) => {
    event.preventDefault()

    await loginRequest(userDetails, props.dispatch)
    const { authError } = props.store
    authError
      ? handleErrors(authError)
      : handleSuccess()
  }
  // ...omitted for brevity
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经能够更新从其他几个组件中读取的存储变量。一旦减速器返回到由动作确定的新状态，这些组件就获得新的值`user`和`permissions`。

这是在不同组件之间共享动态数据的一种非常模块化的方式，同时仍然保持代码相对简单，没有样板文件。您可以通过使用 [`useContext`钩子](https://reactjs.org/docs/hooks-reference.html#usecontext)进一步改进这一点，使所有组件都可以使用存储和分派功能，而不必手工传递它。

## 告诫

如果我们是客观的，我们需要讨论一下`useReducer`的一些相当重要的限制。这些限制可能会阻碍您使用`useReducer`管理应用程序的所有状态。

### 店铺限制

你的商店不是真正的全球性的。Redux 的全局存储实现意味着存储本身不依赖于任何组件；它与你的应用程序是分开的。

您从`useReducer`获得的状态是依赖于组件的，以及它的分派功能。这使得不可能在不同的 reducer 上使用来自一个`useReducer`调用的分派。例如，以这两个独立的商店及其配送功能为例:

```
const [notificationStore, dispatch1] = useReducer(initialState, notificationReducer)
const [authStore, dispatch2] = useReducer(initialState, authReducer) 
```

Enter fullscreen mode Exit fullscreen mode

因为调度函数依赖于返回它的`useReducer`调用，所以不能在`authStore`中使用`dispatch1`触发状态更新，也不能在`notificationStore`中使用`dispatch2`触发状态更新。

这个限制意味着您必须手动跟踪哪个分派函数属于哪个缩减器，这可能最终会导致更多的膨胀。在撰写本文时，还没有已知的方法来组合调度函数或 reducers。

### 扩展性

Redux 的一个我最喜欢的特性是它的可扩展性。例如，您可以添加一个日志记录中间件来记录所有调度的操作，并且您可以使用 Chrome 扩展来查看您的存储，甚至区分调度之间的变化。

如果你决定用`useReducer`代替 Redux，这些都是你必须放弃的。或者您可以自己实现这些，但是您将重新引入 Redux 带来的样板文件。

## 结论

挂钩是 React 库的一个非常好的补充。它允许以更可预测和更有组织的方式更新组件的状态，并且在某种程度上(当与 useContext 结合使用时)，使组件之间共享数据变得更容易。

它也有它的缺点，我们在上面已经讨论过了，如果你能找到一种有效的方法来克服它们，请在下面的评论区告诉我。

查看 [React 文档，了解关于这个](https://reactjs.org/docs/hooks-reference.html#usereducer)和其他可用挂钩的更多信息。编码快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[状态管理只使用 React 钩子](https://blog.logrocket.com/state-management-using-only-react-hooks/)首先出现在[日志博客](https://blog.logrocket.com)上。