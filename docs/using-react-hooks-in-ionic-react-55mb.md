# 在离子反应中使用反应挂钩

> 原文：<https://dev.to/ionic/using-react-hooks-in-ionic-react-55mb>

如果你一直在关注 React 生态系统中的新闻，你可能听说过 React v16.8 中新的 [React Hooks API](https://reactjs.org/docs/hooks-intro.html) 。

钩子将状态和上下文等 React 特性暴露给功能性或非类组件。它们还使得在组件之间共享“有状态逻辑”变得更加容易，比如访问存储中的数据，而不需要复杂的包装组件。

现在，离子支持反应(在撰写本文时处于测试阶段，[尝试一下吧！](https://blog.ionicframework.com/announcing-the-ionic-react-beta/))，我们很高兴看到 hooks 如何让常见的应用构建任务和访问本机 API 变得非常简单和干净，并希望在 Ionic React 应用的上下文中浏览新的 Hooks APIs，包括一个[真实演示应用](https://github.com/mlynch/ionic-react-hooks-demo)，我们将在本文的结尾深入探讨。

我们很快就会发现，钩子比看起来要复杂得多！

## 无状态功能组件

从历史上看，React 中的功能组件并不管理它们自己的状态，因为没有办法在扩展了`React.Component`的类之外访问这些特性。这是它们被称为“无状态功能组件”的部分原因，它们中使用的任何类似于状态的东西都被视为严重的代码味道(并且很可能被破坏)。

让我们以一个简单的无状态功能组件为例:

```
export const MyComponent = ({ name }) => <h1>My name is {name}</h1>;

// Using the component
<MyComponent name="Max" /> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`MyComponent`是一个功能组件(也就是说，它不是一个基于类的组件)，但是它也是*无状态的*，因为它不管理自己的内部状态，并且*是纯的*，因为它没有副作用(也就是说，它在自身之外进行修改，比如写一个文件或者更新一个全局变量)。更确切地说，数据是通过`props`提供给组件的，比如`name`，它们只是由组件以可预测的方式呈现出来。

这些限制使得无状态功能组件非常适合创建许多小型的、表示性的组件，这在许多情况下是可取的。然而，这仍然意味着做任何更复杂的事情都需要基于类的组件。

## 给功能组件添加状态

钩子完全改变了功能组件在 React 中可以做的事情，以一种安全的，甚至可能是*优越的*方式(相对于基于类的组件)将状态、异步操作(如 fetch)和 API(如 Context)带给功能组件。

为了说明这一点，让我们修改这个例子，使用钩子来管理一点点内部状态:

```
export const MyComponent = () => {
  const [ name, setName ] = useState('Max');

  return (
  <>
    <h1>My name is {name}</h1>
    <IonInput value={name} onChange={(e) => setName(e.target.value)} />
  </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`IonInput`用于输入一个名称，这个名称在组件的内部状态中被跟踪，并在`<h1>`标签中呈现。

在第一行，我们看到第一次使用带有`useState`的钩子。在这种情况下，`useState` *将*与 React 中的状态管理特性挂钩，并创建一个状态变量。`useState`接受状态变量默认值的一个参数，然后返回一个有两个值的数组，这两个值被*析构*成两个局部变量:`name`和`setName`。

第一个值，`name`是我们的状态变量，这是我们呈现给`<h1>`元素并设置为`IonInput`的`value`的值。第二个值，`setName`是一个函数，我们调用它来*设置*状态变量的值。这种情况下的`name`和`setName`都可以随便叫。

当然，大多数组件会有许多状态变量，谢天谢地我们可以任意多次调用`useState`，每个变量调用一次。

如果您认为这看起来很神奇，因为每次组件重新渲染时都会调用`MyComponent`函数，并且您不确定 React 如何跟踪所有不同的`useState`调用，那么您就有所发现了。为了实现这一点，React 跟踪发出`useState`调用的顺序，因此对于在哪里可以调用`useState`有严格的规则(例如，在条件语句中调用它是*不允许的*)。为了避免问题，林挺工具可以帮助您保持对`useState`的正确使用，但是一个好的经验法则是将`useState`调用保持在函数的顶级，而不是嵌套在任何条件或嵌套范围内。基本上，保持简单！

## 离子反应和反应钩例子

现在我们对钩子和管理状态变量有了基本的了解，让我们看一个更复杂的例子，使用 Ionic React 和 React 钩子构建一个登录表单:

```
import React, { useState } from 'react';

import {
  IonApp, 
  IonHeader,
  IonTitle,
  IonToolbar,
  IonContent,
  IonInput,
  IonList,
  IonItem,
  IonLabel,
  IonButton
} from '@ionic/react';

const LoginForm = () => {
  const [ email, setEmail ] = useState('');
  const [ password, setPassword ] = useState('');

  const [ formErrors, setFormErrors ] = useState({});

  const submit = async () => {
    try {
      await login({
        email,
        password
      });
    } catch (e) {
      setFormErrors(e);
    }
  }

  return (
    <>
      <IonHeader>
        <IonToolbar>
          <IonTitle>
            Login
          </IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent>
        <form onSubmit={(e) => { e.preventDefault(); submit();}}>
          <div>
            {formErrors ? (
              formErrors.message
            ): null}
          </div>
          <IonList>
            <IonItem>
              <IonLabel>Email</IonLabel>
              <IonInput name="email" type="email" value={email} onChange={(e) => setEmail(e.target.value)}/>
            </IonItem>
            <IonItem>
              <IonLabel>Password</IonLabel>
              <IonInput name="password" type="password" value={email} onChange={(e) => setPassword(e.target.value)}/>
            </IonItem>
          </IonList>

          <IonButton expand={true} type="submit">Log in</IonButton>
        </form>
      </IonContent>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## TypeScript 呢？

在我们继续之前，你可能想知道在上面的例子中(这是普通的 JS)，如何使用 TypeScript 的`useState`和其他钩子。幸运的是，`useState`是一个通用函数，如果类型参数不能被推断出来，它可以接受类型参数。

例如，如果我们有一个类型的`Car`想要设置为 state，我们可以这样调用它:

```
const [ car, setCar ] = useState<Car>({ color: 'red' }) 
```

Enter fullscreen mode Exit fullscreen mode

钩子在基于 TypeScript 的 React 应用中非常好用！

## 离子与基于类的反应成分反应

上面的例子很有趣，钩子当然是一个古怪的、令人好奇的新 API，使用起来非常愉快。然而，它们在 React 社区中引起轰动的原因之一是它们带来的代码简单性的好处。

为了说明这一点，让我们构建与上面相同的示例，但是使用传统的基于 React 类的组件方法:

```
import React, { useState, FormEvent } from 'react';

import {
  IonHeader,
  IonToolbar,
  IonTitle,
  IonContent,
  IonList,
  IonItem,
  IonLabel,
  IonInput,
  IonButton
} from "@ionic/react";

export class LoginPage extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      email: '',
      password: ''
    }
  }

  async handleSubmit(e: FormEvent) {
    e.preventDefault();

    try {
      const user = await login(email, password);

      // ...
    } catch (e) {
      console.error(e);
    }
  }

  handleInputChange(e) {
    this.setState({
      [e.target.name]: e.target.value
    });
  }

  render() {
    return (
    <>
      <IonHeader>
        <IonToolbar color="primary">
          <IonTitle>Login</IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent>
        <form onSubmit={e => this.handleSubmit(e)} action="post">
          <IonList>
            <IonItem>
              <IonLabel>Email</IonLabel>
              <IonInput  type="email" value={email} onInput={(e: any) => this.handleInputChange(e)} />
            </IonItem>
            <IonItem>
              <IonLabel>Password</IonLabel>
              <IonInput type="password" value={password} onInput={(e: any) => this.handleInputChange(e)} />
            </IonItem>
            <IonButton type="submit">Log in</IonButton>
          </IonList>
        </form>
      </IonContent>
    </>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，你会注意到基于类的组件的一些特征:一个构造函数，调用`this.state`，必须在回调处理程序中捕获`this`(在上面的例子中，我们使用了箭头函数方法来处理事件处理程序，但是许多使用了`this.eventHandler = this.eventHandler.bind(this)`，它有一些严重的缺陷)。

虽然这个例子并不复杂，但它足以表明，作为一个使用钩子的功能组件，这个组件比它的基于类的对应组件更简单(尽管有些人可能更喜欢基于类的方法的样板，也许是另一个时代的 Java 开发人员)。

## 有副作用的成分:useEffect

状态变量只是钩子的一个用例。在一个组件被渲染后，许多组件需要做一些被认为是“副作用”的事情(比如在挂载或更新时)。副作用是由于使用该组件的副作用而导致组件之外的内容被修改的任何操作。例如，发出 API 请求是许多组件需要执行的副作用。

这就是`useEffect`的用武之地。例如，假设我们需要在组件挂载时通过向我们的 API 发出请求来获取一些数据:

```
const MyComponent: = () => {
  const [data, setData] = useState({});

  useEffect(() => {
    async function loadData() {
      const loadedData = await getDataFromAPI();
      setData(loadedData);
    }

    loadData();
  }, []);

  const items = (data.items || []);

  return (
    <div>
      There are {items.length} entries
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

对于基于类的组件，数据获取通常是在生命周期方法中完成的，例如`componentDidMount`，相比之下，起初并不清楚上面的调用`useEffect`是如何工作的。

您可以将`useEffect`视为生命周期方法`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`的组合，假设它在组件被挂载和渲染后立即运行，将在每次组件更新时运行，并且可以在组件被卸载时运行清理。

然而，在上面的例子中，我们不想在每次更新后都获取数据！如果一个组件在短时间内连续更新多次，这可能意味着数千个冗余的 API 请求。相反，`useEffect`需要一个额外的参数*依赖* : `useEffect(effectCallack, dependencyList)`。在`dependencyList`中，你可以告诉效果只在某些状态变量改变后运行，或者传递一个空数组，只允许效果在组件第一次挂载时运行。

在上面的例子中，我们将`[]`作为依赖列表传递，这样我们的效果只在组件第一次挂载时运行。

一个注意事项:`useEffect`仅在你希望执行与组件渲染相关的副作用时才需要。相反，如果您希望在一个动作(比如在组件中单击一个按钮)之后发出一个 API 请求，只需正常地进行提取，并在数据返回时为您的状态变量调用相应的 setter 函数，并且您希望更新组件。从这个意义上来说，`useEffect`是一个容易混淆的名字，因为您可以在不使用它的情况下在组件中加入副作用。

## 使用 useContext 实现简单的状态管理

大多数 React 开发人员都知道在他们的应用程序中共享全局状态的困难。不管是好是坏，这场斗争已经让许多开发人员把目光投向了像 Redux 这样强大的解决方案，这些解决方案对于他们的需求来说是多余的，而更简单的解决方案就足够了。

好了，有了钩子、[上下文](https://reactjs.org/docs/context.html)和`useContext` API，这场斗争实际上已经结束了。使用`useContext`访问全局上下文实例可以进行基本的状态管理，或者轻松地[创建自己的迷你 Redux](https://medium.com/simply/state-management-with-react-hooks-and-context-api-at-10-lines-of-code-baf6be8302c) ，没有外部依赖性，架构更简单。

我们将在最后遍历示例应用程序中的`useContext`钩子。

## 自定义挂钩

React 提供了许多现成的挂钩，但是它们不仅仅用于状态管理或访问上下文！

在接下来的章节中，我们将看看一些定制钩子，以及它们如何为 React 和 Ionic 应用程序开发带来巨大的好处。

## 带有 Ionic 和 React 钩子的本地 API

因为钩子对于可重用的、有状态的逻辑是完美的，也许它们对于访问 iOS、Android、electronic 和浏览器上的本地 API 的插件也是完美的？事实证明确实如此，我们可以构建或使用定制的挂钩来实现这一点！

想象一下在设备上访问地理位置 API。一个名为`useGeolocation`的定制钩子可能会自动监听地理位置的变化，并更新一个状态变量:

```
const MyApp = () => {
  const pos = useGeolocation();

  return (
    <span>Lat: {pos.lat}, Lng: {pos.lng}</span>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子展示了钩子隐藏的力量。只用一行代码，我们就在组件挂载上设置了一个地理位置查询，它启动一个观察器，当我们的位置改变时，它将更新，然后更新一个状态变量，这将导致组件重新呈现并显示更新后的位置。

现在，想象一下对其他本机特性(如相机、存储、照片或条形码扫描)做同样的事情，您可以感受到钩子使与这些类型的 API 交互变得多么容易。

这和爱奥尼亚有什么关系？嗯，随着 Ionic React 的推出，我们正在探索为[电容器](http://capacitor.ionicframework.com/)中可用的 API 做一组挂钩，我们认为这将非常棒！

## 一个示例 app

在介绍完钩子之后，让我们来看看一个简单的 Ionic React 应用程序，它使用了上面的一些钩子，Puppers 应用程序(源代码[这里是](https://github.com/mlynch/ionic-react-hooks-demo)):

[![](img/0a7429be98139375f70d6942ff044df4.png)](https://blog.ionicframework.com/wp-content/uploads/2019/04/ss1.png)

这个应用程序从[狗 API](https://dog.ceo/) 中获取了一系列可爱且非常好的小狗的随机图像，有几个功能完全是多余的，但也恰到好处，包括将最后的图像保存到本地存储，以及一个 redux 的迷你实现，用于仅使用`useReducer`钩子通过上下文 API 管理状态(没有外部依赖性！).还有一个名为`useLocalStorage`的定制钩子，它自动加载一个键和值并保存到 localStorage(从 usehooks.com 的[移植而来)。](http://usehooks.com)

这个演示应用程序还展示了如何在没有 TypeScript 的普通 JS 应用程序中使用 Ionic React。

#### App.js

在 [App.js](https://github.com/mlynch/ionic-react-hooks-demo/blob/master/src/App.js) 中，我们在文件的底部有我们的主要应用组件:

```
const App = () => {
  return (
    <IonApp>
      <AppContextProvider>
        <Puppers />
      </AppContextProvider>
    </IonApp>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

该组件创建一个`IonApp`元素，然后用一个`AppContextProvider`包装应用程序的内容，这将是我们管理全局状态的主要`Context`。在该组件内部，呈现了`Puppers`页面。非常基础，如果你不熟悉上下文 API，请确保在继续之前[阅读更多关于它的内容](https://reactjs.org/docs/context.html)。

接下来，我们有`AppContextProvider` :

```
const AppContext = createContext();

const AppContextProvider = (props) => {
  const [data, setData] = useLocalStorage('data', initialState);

  let [state, dispatch] = useReducer(reducer, data);

  let value = { state, dispatch };

  useEffect(() => {
    setData(state);
  }, [state, setData]);

  return (
    <AppContext.Provider value={value}>{props.children}</AppContext.Provider>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这个要复杂得多，使用了许多钩子，甚至是一个定制钩子！让我们浏览一下其中的一些内容:

第一行调用我们的定制`useLocalStorage`钩子，它将加载值并自动持久存储到本地存储中的`data`项:

```
const [data, setData ] = useLocalStorage('data', initialState); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们使用`useReducer`创建一个缩减器，它接受一个缩减器函数和一个初始值，我们将从`useLocalStorage`传入`data`状态值。这将导致 reducer 使用从 localStorage 加载的任何数据作为它的初始值！如果你以前没有用过 redux，`useReducer`一开始很可能会很怪异。然而，对于复杂的状态逻辑来说，它是一个有用的工具，让我们可以管理单个全局状态对象，该对象可以被应用程序中的操作修改。我们的应用程序只有一个动作，但是你可以想象普通的应用程序有上百个动作。阅读更多关于 [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer) 的信息。

```
let [state, dispatch] = useReducer(reducer, data); 
```

Enter fullscreen mode Exit fullscreen mode

而我们的减速器功能很基本:

```
const reducer = (state, action) => {
  if (action.type === 'setPuppers') {
    return { ...state, puppers: action.puppers }
  }
  return state;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果这令人困惑，希望看到一个组件“使用”上面的上下文和 reducer 会使它变得更清楚:

#### Puppers.js

让我们来看一下 puppers 组件，它遍历 API 中的 Puppers 列表，并一个接一个地呈现它们:

```
export const Puppers = () => {
  const { state, dispatch } = useContext(AppContext);

  const fetchPuppers = useCallback(async () => {
    const ret = await fetch('https://dog.ceo/api/breeds/image/random/10');
    const json = await ret.json();
    dispatch({
      type: 'setPuppers',
      puppers: json.message
    })
  }, [dispatch]);

  useEffect(() => {
    fetchPuppers();
  }, [fetchPuppers]);

  return (
  <>
    <IonHeader>
      <IonToolbar>
        <IonTitle>Puppers</IonTitle>
        <IonButtons slot="end">
          <IonButton onClick={() => fetchPuppers()}>
            <IonIcon icon="refresh" />
          </IonButton>
        </IonButtons>
      </IonToolbar>
    </IonHeader>
    <IonContent>
      {state.puppers.map(pupper => {
        return (
          <IonCard key={pupper}>
            <IonCardContent>
              <img src={pupper} />
            </IonCardContent>
          </IonCard>
        )
      })}
    </IonContent>
  </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行来。第一行访问我们使用`App`组件中的`<AppContextProvider>`组件实例化的`AppContext`，特别是提供者
的`value`

```
const { state, dispatch } = useContext(AppContext); 
```

Enter fullscreen mode Exit fullscreen mode

`state`变量将包含我们在上下文中的全局状态，而`dispatch`变量是一个函数，我们可以调用它来向我们的 reducer 发送一个动作(例如，更新我们的状态)。

接下来，我们定义一个可以用来调用 API 的函数:

```
const fetchPuppers = useCallback(async() => {
  const ret = await fetch('https://dog.ceo/api/breeds/image/random/10');
  const json = await ret.json();
  dispatch({
    type: 'setPuppers',
    puppers: json.message
  })
}, [dispatch]); 
```

Enter fullscreen mode Exit fullscreen mode

因为我们要从组件中的几个不同地方调用`fetchPuppers`，所以我们使用`useCallback`钩子来确保钩子 API 正确理解这个函数的依赖关系。这是丹·阿布拉莫夫在他的[使用效果完全指南](https://overreacted.io/a-complete-guide-to-useeffect/)中提供的在几个钩子中共享一个功能的解决方案，尽管有其他方法可以实现这一点。我们提供了`dispatch`函数作为对`fetchPuppers`调用的依赖，因为一旦响应返回，就会用新的 puppers 调用它。

接下来，我们使用带有一个空依赖列表的`useEffect`(即`[]`作为最后一个参数)来获取这个组件:T2

```
useEffect(() => {
  fetchPuppers();
}, [fetchPuppers]); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们渲染我们的组件，并遍历每个 pupper，将它们渲染到屏幕上:

```
return (
<>
  <IonHeader>
    <IonToolbar>
      <IonTitle>Puppers</IonTitle>
      <IonButtons slot="end">
        <IonButton onClick={() => fetchPuppers()}>
          <IonIcon icon="refresh" />
        </IonButton>
      </IonButtons>
    </IonToolbar>
  </IonHeader>
  <IonContent>
    {state.puppers.map(pupper => {
      return (
        <IonCard key={pupper}>
          <IonCardContent>
            <img src={pupper} />
          </IonCardContent>
        </IonCard>
      )
    })}
  </IonContent>
</>
); 
```

Enter fullscreen mode Exit fullscreen mode

这里要看几样东西:首先，注意工具栏中按钮的`onClick`事件。这将对 API 进行一次新的获取，获得 10 个以上的随机 puppers，这将导致全局状态更新，并重新呈现我们的组件。

最后，假设我们使用的是全局状态而不是局部状态，当我们渲染每个 pupper 时，我们访问的是来自初始`useContext`调用的`state.puppers`字段。

就是这样！

## 何去何从

尽管 React 挂钩非常新，但社区已经创造了大量有趣的挂钩。一个这样的库， [react-use](https://streamich.github.io/react-use/) ，有一些简单而强大的挂钩，比如 [useVideo](https://streamich.github.io/react-use/?path=/story/ui-usevideo--docs) (用于轻松地与 HTML5 视频元素交互)。我个人很喜欢钩子使得与 HTML5 媒体元素等有状态控件和 localStorage 等 API 的交互变得简洁明了。

此外，请务必观看丹·阿布拉莫夫的 [React Conf Hooks 主题公告](https://youtu.be/dpw9EHDh2bM?t=698)，以及他更详细地挖掘 Hooks 的精彩博客帖子，如[使用效果的完整指南](https://overreacted.io/a-complete-guide-to-useeffect/)。

最后，请留意 Ionic 团队专门为 Ionic React 应用程序开发的一些非常棒的挂钩工具(使用电容器实现原生功能)。我们喜欢钩子，认为它们会使构建应用程序变得相当容易。而且，如果你还没有试过 [Ionic React beta](https://blog.ionicframework.com/announcing-the-ionic-react-beta/) 试试吧，让我们知道你的想法！

关于使用钩子和它们如何在 Ionic React 应用中特别有用有什么问题吗？请在下面留下您的评论，我们会尽力帮助您！