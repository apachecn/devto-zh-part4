# redux——尽可能简单

> 原文：<https://dev.to/clintdev/redux-as-simple-as-it-can-get-3h1g>

[*Redux*](https://www.gistia.com/beginners-guide-redux/) *顾名思义，是一个状态管理框架，可以用于不同的 web 技术，包括 react 和 ruby。 *Redux 不是 react 的一部分！**

 *当您在处理一个需要改变不同组件状态的大型应用程序时，这是非常有效的。假设您正在处理一个有超过 10 个组件的应用程序，并且每个组件都有自己的本地状态，应用程序的整个状态可以在 Redux [store](https://redux.js.org/basics/store#store) 中的单个对象上进行管理。

我将分享一些我用来设置 redux 的基本步骤。

**第一步:创建初始状态**

简单地说，状态是一个 JavaScript 对象，它存储将在组件中使用的动态数据。这些数据决定了组件的行为。假设 Student 代表应用程序的当前状态。让我们定义一下我们学生的当前状态:-

```
const initialState = {
  name: 'Valerie',
  grade: 4,
  age: 8
}; 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 2:定义您的行动和行动创建者**

一个[动作](https://redux.js.org/basics/actions#actions)只是一个 JavaScript 对象，它包含将数据从应用程序发送到存储的信息。他们是商店唯一的信息来源。
在我们的例子中，我们的行动可以是招收学生，支付他们的费用或给他们分配宿舍。让我们定义一个动作。定义 Redux 动作就像用 type 属性声明一个对象一样简单。我们首先通过将动作类型赋给一个常量变量来声明它。这只是最佳实践。

```
const ENROLL = 'ENROLL'; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们定义我们的动作创建者，它将用于将动作发送到商店。动作创建器只是一个返回动作的 JavaScript 函数。

```
const enrollStudentAction = (status) => {
  return {
    type: ENROLL,
    status: status
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

**步骤 3:创建您的减速器**

一个[缩减器](https://redux.js.org/basics/reducers#reducers)描述了我们的状态如何相对于我们已经描述过的[动作](https://redux.js.org/basics/actions#actions)而变化。缩减器是一个将状态作为第一个参数和动作的函数。Redux 中的 Reducers 负责状态修改。减速器返回新状态。

```
const studentReducer = (state = initialState, action) => {
  switch (action.type) {
    case ENROLL:
      let newInitialState = Object.assign({},state);
       newInitialState.status= action.message;
        return newInitialState;
    default:
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**N/B**
在 Redux 中，状态是只读的，也就是说，reducer 函数必须总是返回状态的一个新副本，决不能直接修改状态。Redux 不强制执行状态不变性，但是，您有责任在 reducer 函数的代码中强制执行它。

在我们的例子中，我们的状态是一个对象。为了在对象中实施状态不变性，我们使用了`Object.assign()`实用程序。`Object.assign()`获取目标对象和源对象，并将属性从源对象映射到目标对象。任何匹配的特性都将被源对象中的特性覆盖。

因此，我们的`Object.assign({},state)`实用程序将一个空对象作为目标对象，将我们的状态(即我们的`initialState`对象)作为源对象。这只是创建了我们国家的一个克隆。既然我们有了状态对象克隆，那么我们可以在其中添加一个新的属性。我们的新属性是 status，并被设置为值“已注册”。

**第四步:创建我们的商店**

正如我上面提到的，[商店](https://redux.js.org/basics/store#store)存放我们的状态，所有更新它的逻辑都传递给它。为了创建我们的商店，我们使用了来自`redux`的`createStore()`函数。它采用缩减器，状态——或者如果你喜欢称之为`preloaded state`,在某些情况下还采用增强器，例如 applymiddleware()。但我不会深入探讨这个问题。我想尽可能的减少。

```
const store = Redux.createStore(studentReducer, initialState) 
```

Enter fullscreen mode Exit fullscreen mode

我们的商店已经准备好了！

**状态 6:订阅**

[订阅](https://redux.js.org/api/store#subscribelistener)只是让你知道每次店铺的变化。如果你正在编写一个 [react-redux](https://react-redux.js.org/5.x/introduction/basic-tutorial#basic-tutorial) 应用程序，那么你永远不需要它，因为 react-redux 为你做了这些。(也就是说，本质上它会观察变化，然后告诉 react 进行重绘)。例如，在我们的例子中，如果我们想知道我们的学生状态是否正在改变，我们可以添加一个事件监听器。每当调度一个动作时都会调用它，并且状态树的某些部分可能已经改变。然后可以调用 getState()来读取回调中的当前状态树。例如，在我们的例子中，如果我们想检查一个新生是否注册，我们可以创建一个名为`handleChange`的事件监听器，如下所示:-

```
function handleChange() {
  const currentStudent = store.getState();
  if (currentStudent.status === 'enrolled') {
    console.log('A new Student is enrolled!');
  } else {
    console.log('Student not yet enrolled!');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们`subscribe`这个事件到商店是这样的:-

```
store.subcribe(handleChange) 
```

Enter fullscreen mode Exit fullscreen mode

**最终调度我们的行动**

分派仅仅意味着将我们创建的逻辑(动作)传递给 redux 存储。现在是关键时刻了。我们现在可以测试我们的更新逻辑了。
分派方法是您用来将动作分派到 Redux 存储的方法。调用`store.dispatch()`并传递从动作创建者返回的值会将一个动作发送回商店。

```
store.dispatch(enrollStudentAction('Enrolled')); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过调用 store 上的`getState()`方法来检查我们的状态变化:-

```
// Log the initial state
console.log(store.getState() 
```

Enter fullscreen mode Exit fullscreen mode

一旦你这样做了，你会看到我们的州现在在底部有了`status: 'enrolled'``财产。我们杀了它！

干杯！*