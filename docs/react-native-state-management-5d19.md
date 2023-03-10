# react-本机状态管理

> 原文：<https://dev.to/thefinnomenon/react-native-state-management-5d19>

随着应用程序的增长，其复杂性也在增加。如果不使用适当的状态管理解决方案，在组件之间共享状态会很快变得复杂。这些年来出现了无数的库、模式和解决方案来解决 React 中的状态管理问题，本文将介绍最流行的四种；组件状态、上下文 API 和 Redux。

# 组件状态

对于简单的应用程序，组件状态是一个很好的解决方案。

对于基于类的组件，这意味着使用`setState`。

```
class Counter extends Component {
  state = { count: 0 }

  render() {
    return (
      <View>
        <Text>{this.state.count}</Text>
        <Button
          onPress={() => this.setState({ count: this.state.count + 1 })}
          title="+"
        />
        <Button
          onPress={() => this.setState({ count: this.state.count - 1 })}
          title="-"
        />
      </View>
    )
  }
} 
```

对于功能组件，使用`useState`。

```
const Counter = props => {
  const [count, setCount] = useState(0)

  return (
    <View>
      <Text>{count}</Text>
      <Button onPress={() => setCount(count + 1)} title="+" />
      <Button onPress={() => setCount(count - 1)} title="-" />
    </View>
  )
} 
```

> 对于功能组件中更复杂的本地状态管理，您可以使用`useReducer()`，这使得以可预测的方式处理大量变化的状态变得更加容易。

## 优点

*   封装状态简单
*   天然反应溶液

## 弊

*   在组件之间共享状态可能会很复杂
*   不太适合复杂的应用程序

# 上下文 API

上下文 API 有助于解决“道具钻取”的问题，通过多层组件向下传递道具，使其到达需要的地方。使用上下文 API，您可以在组件之间共享值，而不必传递属性。React 团队建议使用它来“共享可以被 React 组件树视为“全局”的数据，比如当前认证的用户、主题或首选语言”。

使用上下文 API 包括三个部分，

*   调用`React.createContext(OptionalInitialValue)`创建一个上下文。它返回一个带有`Provider`和`Consumer`的对象。
*   使用树中更高位置的`<Provider value={anything}>`组件来包装您想要访问`value`的任何组件树。
*   使用树中`Provider`下任意位置的`<Consumer>`组件，让组件访问`Provider`中的价值主张。有两种方法可以使用`Consumer`、【渲染道具】样式或者用钩子。

```
// Initialize a Context
const MyContext = React.createContext({name: ''});

const MyComponent = () => (
    // Wrap a part of the component tree in the Provider
    <MyContext.Provider value={{name: 'Alice'}}>
        <MySubComponent />
        <MyOtherSubComponent />
    </MyContext.Provider>
);

const MySubComponent = () => (
    // Wrap a component in the Consumer to access the value prop "render props" style
    <MyContext.Consumer>
        { value => (
            <Text>{value.name}</Text>
        )}
    </MyContext.Consumer>
);

const MyOtherSubComponent = () => {
    // Use the useContext hook to get the value
    const value = useContext(MyContext);

    return <Text>{value.name}</Text>;
}; 
```

> 当上下文值改变时，调用 useContext 的组件将总是重新呈现。如果重新呈现组件的开销很大，可以使用内存化来优化它。

## 优点

*   当你有一些全局状态想要提供给很多组件(例如主题、国际化、用户信息)时，这是一个很好的选择。
*   没有支柱钻孔
*   天然反应溶液
*   易于实现，只需将组件包装在提供者和消费者组件中。

## 弊

*   当你有很多全局状态时，这并不是最好的选择，并且最终会有很多上下文来包装你的应用程序。
*   消费者“渲染道具”风格会引入过多的嵌套，降低可读性。
*   当使用 useContext 和上下文值改变时，可能需要考虑不必要的重新呈现。

# Redux

Redux 可能是所有第三方状态管理解决方案中最受欢迎的。尽管 Redux 被认为对初学者来说很复杂并且被过度使用(在某些情况下确实如此)，但它实际上相当简单，在更大的应用程序中肯定有它的用途。受 Flux 和 Elm 的启发，Redux 使用了单向数据流的概念。

*   这个应用程序有一个中央状态
*   状态变化触发 UI 更新
*   特殊函数处理状态变化
*   用户交互触发这些特殊的状态改变功能
*   一次只发生一种变化

[![Redux Architecture](img/48aaf78121125d3c471992f393439028.png "Redux Architecture")](https://res.cloudinary.com/practicaldev/image/fetch/s--ce9S8Bn4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thefinnternet.com/static/657d3433e0e6b2b8a029aa7bc331a841/50336/redux_flow.png)

## 重要棋子

### 根店

应用程序的唯一真实来源。

```
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer); 
```

### 动作

描述应用程序中动作的普通 JavaScript 对象。通常有一个类型和一个有效负载。

```
const action = {
    type: 'LOGIN',
    payload: { email: scott@dunder.com, password: 'thatswhatshesaid' },
} 
```

制作一个包含所有动作类型的文件是一个很好的实践，因为它们将会在应用程序的多个地方使用&你不想冒混淆它们的风险。另一个好的实践是创建动作创建器，然后导入到组件中使用。

```
// ./actions.js

// Action type
export const LOGIN = 'LOGIN';

// Action creator
export const login = (email, password) => {
    return { type: LOGIN, payload: { email, password} };
}

import { login } from './actions';

<Button onPress(() => dispatch(login(email,password)))>
    Login
</Button> 
```

### 减速器

纯函数接受当前状态和动作，并返回新状态。为了保持 reducers 的大小易于管理，您可以将它们分成单独的文件。

```
import { LOGIN } from '../constants/actionTypes';

const initialState = {
  user: ''
};

const rootReducer = (state = initialState, action) => {
  switch (action.type) {
    case LOGIN:
        return { ...state, user: action.payload };
    default:
        return state;
  }
};

export default rootReducer; 
```

> 理解第`{ ...state, user: action.payload }`行很重要。它用整个旧状态创建了一个全新的状态对象，并被新的用户值覆盖。这符合不修改状态的 Redux 原则。

### 分派(动作)

Dispatch 是一个 Redux store 函数，用于将一个动作分派给存储。

```
// Dispatch an action object
dispatch({ type: INCREMENT, payload: 1 });

// Dispatch an action creator
dispatch(increment) 
```

### mapStateToProps(state, ownProps?)

该函数在想要访问 Redux 状态的组件中定义，用于从存储中选择组件需要的状态部分。

```
function mapStateToProps(state) {
  return {
    count: state.count
  };
} 
```

> 可选地接受名为 ownProps 的第二个参数，该参数保存组件的属性。

### mapDispatchToProps

这是一个可选函数，您可以为想要使用 Redux 存储的组件定义，它用于创建在被调用时调度的函数，并将这些函数作为道具传递给组件。

> 默认情况下，连接的组件具有作为道具的`dispatch`功能。

有几种方法可以定义这个映射，但是最简单的方法是将它定义为一个对象。

```
// Import the action creators
import { increment, decrement, reset } from './actions';

const mapDispatchToProps = {
    increment,
    decrement,
    reset
}; 
```

### 连接(mapStateToProps，mapDispatchToProps？，mergeProps？，选项？)(组件)

这个函数包装您的组件，并将其连接到 Redux 存储。

```
// Export and use this for your Component instead of normal Component export
export default connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?) 
```

> mergeProps 和 options 不常用。

### 使用选择器()

`useSelector`钩子允许您从 Redux 存储中提取状态。该功能实质上取代了功能组件的`mapStateToProps`。

```
export const Counter= () => {
  const count = useSelector(state => state.count);

  return <Text>{count}</Text>;
} 
```

### 使用 Dispatch()

`useDispatch`钩子从 Redux 存储中返回对`dispatch`函数的引用。这个钩子代替了使用`connect`注射`dispatch`的需要。

```
export const Counter= () => {
  const dispatch = useDispatch();

  return (
    <Button onPress=(() => dispatch(increment))>
        Increment
    </Button>
  );
} 
```

## 主要原则

### 真理的单一来源

整个应用程序的状态存储在单个存储中的一个对象中。这使得您的状态更容易处理，应用程序更容易推理，调试也不那么痛苦。

### 只读状态

状态不能直接修改，只能通过发出描述变化的动作来修改。这意味着视图不直接修改状态。相反，视图中的用户交互可以分派一个动作，告诉函数更新状态。这个函数**用更新的状态替换**旧的状态，并且 UI 相应地更新。

### 减速器是纯函数

Reducers 是简单而特殊的函数，接受当前状态和动作，并返回新的更新状态。

> **状态被替换而非突变**

Reducers 也是纯函数，这意味着它们总是为同一组输入返回相同的输出。

```
// Impure
const impure = function(n) {
  return Math.random() * n;
}

impure(3)   // 1.2
impure(3)   // 1.8
impure(3)   // 0.9

// Pure
const pure = function(a) {
  return 1 + a;
}

pure(3) // 4
pure(3) // 4
pure(3) // 4 
```

正如你所看到的，第一个函数是不纯的，如果你多次输入相同的值给`n`，你会得到不同的结果给`Math.random()`。另一方面，第二个函数是纯函数，因为它总是输入+ 1。

## 使用 Redux

`yarn add redux react-redux`

在幕后，Redux 利用了前面提到的上下文 API，因此它需要将您的应用程序包装在一个`Provider`中，并传入`root store`。

```
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import rootReducer from './reducers';

const store = createStore(rootReducer)

const App = () => (
  <Provider store={store}>
    <App />
  </Provider>
); 
```

然后，有两种方法可以从存储中访问状态。

```
import { connect } from 'react-redux';
import { increment } from '../actions';

class Counter extends Component {
  increment = () => {
     // Dispatch increment action
    this.props.increment();
  }

  render() {
    return (
      <View>
          // Access count variable that is available from props
          <Text>{this.props.count}</Text>
          <Button onPress={this.increment}>+</Button>
      </View>
    )
  }
}

// This function is used to pullout the state needed by this
// component and mapping it to the component's props
// (e.g. count -> this.props.count)
function mapStateToProps(state) {
  return {
    count: state.count
  };
}

// An object of action creators to add to the props
const mapDispatchToProps = {
    increment,
};

// Wrapping your component in connect puts the results of
// mapStateToProps, mapDispatchToProps and the dispatch 
// function into the components props
export default connect(mapStateToProps, mapDispatchToProps)(Counter); 
```

较新的方法利用了钩子，所以只能在功能组件中实现。

```
import { useSelector, useDispatch } from 'react-redux';
import { increment } from '../actions';

const App = props => {
    // Get counter from the state store
    const counter = useSelector(state => state.counter);
    // Get the dispatch function
    const dispatch = useDispatch();

  return (
    <View>
        { counter }
        <Button onPress={ () => dispatch(increment) }>
            increase
        </Button>
    </View>
  );
} 
```

## 优点

*   有条不紊且结构良好的状态管理
*   易于测试/
*   启用热重装
*   强大的调试；时间旅行调试

## 弊

*   轻微的学习曲线
*   大量样板代码
*   需要中间件来更好地处理异步等高级情况

# 结论

选择如何在应用程序中管理状态似乎是一项艰巨的任务，但我建议您从本地状态开始，并将其中一些切换到上下文 API，因为您会发现在许多地方都需要它。如果你发现自己有太多的上下文，那么你应该考虑将你的全局状态转移到 Redux。