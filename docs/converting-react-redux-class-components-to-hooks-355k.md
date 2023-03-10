# 将 React-Redux 类组件转换为挂钩

> 原文：<https://dev.to/vinodchauhan7/converting-react-redux-class-components-to-hooks-355k>

随着日常技术的进步， **React-Redux** 也发布了 7.1.0 版本的钩子。因此，这意味着我们也需要用最新的 react 实践来更新自己。

## 反应-挂钩

React Hooks 有助于我们为相同的功能编写更少的代码，这些代码是我们以前用类组件编写的。如果你是反应钩子的新手，请看看我最近写的关于钩子的文章。

[![vinodchauhan7 image](img/be17ce86844e07d6d48266e54b3a2d51.png)](/vinodchauhan7) [## React-hooks - useState，useEffect，useRef，useMemo

### vinodchauhan 7 Aug 11 ' 193 分钟读取

#react #reacthooks #javascript](/vinodchauhan7/react-hooks-usestate-useeffect-useref-usememo-2dj)

在这篇文章中，我们将把简单的 **react-redux** 应用程序转换成带有 redux 的钩子，该应用程序目前使用基于类的组件方法。

下面是基本代码沙盒[https://codesandbox.io/embed/fzkdv](https://codesandbox.io/embed/fzkdv)

#### 计数器类视图-组件

```
import React, { Component } from "react";
import { connect } from "react-redux";

class Counter extends Component {
  render() {
    return (
      <div>
        <h3>Count : {this.props.data.count}</h3>
        <p>
          <button
            onClick={() => this.props.dispatch({ type: "INC", value: 1 })}
          >
            INCREMENT
          </button> 
          <br />
          <br />

          <button
            onClick={() => this.props.dispatch({ type: "DEC", value: 1 })}
          >
            DECREMENT
          </button>
        </p>
      </div>
    ); //return ends here
  } //render ends here
}

const mapsStateToProps = state => {
  return {
    data: state
  };
};

export default connect(mapsStateToProps)(Counter); 
```

# 现在让我们一步一步来:

1)将“计数器”类组件转换为函数组件。

```
import React, { Component } from "react";
import { connect } from "react-redux";

const Counter = props => {
  return (
    <div>
      <h3>Count : {props.data.count}</h3>
      <p>
        <button onClick={() => props.dispatch({ type: "INC", value: 1 })}>
          INCREMENT
        </button> 
        <br />
        <br />

        <button onClick={() => props.dispatch({ type: "DEC", value: 1 })}>
          DECREMENT
        </button>
      </p>
    </div>
  ); //return ends here
};

const mapsStateToProps = state => {
  return {
    data: state
  };
};

export default connect(mapsStateToProps)(Counter); 
```

在上述转换中，我们只是做了功能组件，它的代码似乎很少。

2)目前，我们通过 mapStateToProps()和
从我们的商店获取数据，然后用 in connect HOC 包装我们的功能组件。

### 使用选择器

使用选择器挂钩帮助我们从存储中读取状态。我们需要从“react-redux”导入“useSelector”

### 导入

```
import { connect, useSelector } from "react-redux"; 
```

### readstatfromstoreviewook

```
let data = useSelector(state => state); 
```

### UseStateInComponent

```
<h3>Count : {data.count}</h3> 
```

### NoMapsStateToProps

```
export default connect()(Counter); 
```

3)到目前为止，我们成功地通过 useSelector 从我们的存储中读取了状态，并更新了我们的组件，现在我们将查看“useDispatch”挂钩，以向我们的存储分派操作。

### 使用 Dispatch:

useDispatch 帮助我们将动作从组件调度到存储。

### 导入

```
import { useSelector, useDispatch } from "react-redux"; 
```

### [制作对象](#makeobject)

```
 const dispatch = useDispatch(); 
```

### update button onclick 事件

```
<p>
        <button onClick={() => dispatch({ type: "INC", value: 1 })}>
          INCREMENT
        </button> 
        <br />
        <br />

        <button onClick={() => dispatch({ type: "DEC", value: 1 })}>
          DECREMENT
        </button>
      </p> 
```

### NoHOC_Connect

```
export default Counter; 
```

### **祝贺你**

您已经使用 react-redux 挂钩成功地转换为功能组件。

## 减速器

```
const initialState = {
  count: 0
};

const reducer = (state = initialState, action) => {
  let newState = { ...state };
  switch (action.type) {
    case "INC":
      newState.count = newState.count + action.value;
      state = { ...newState };
      return newState;
    case "DEC":
      newState.count = newState.count - action.value;
      state = { ...newState };
      return newState;
    default:
      return newState;
  }
};

export default reducer; 
```

## Index.js

```
import React from "react";
import ReactDOM from "react-dom";
import reducer from "./store/reducer";
import { Provider } from "react-redux";
import { createStore } from "redux";
import Counter from "./counter";

import "./styles.css";

const store = createStore(reducer);

function App() {
  return (
    <div className="App">
      <Counter />
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
); 
```

如果任何人在转换中有问题，请让我知道。我很乐意帮助你们。