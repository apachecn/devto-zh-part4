# iceCream:一个新的库，用于处理应用程序中的 Redux/Redux-Saga。

> 原文：<https://dev.to/rdhox/icecream-a-new-library-to-handle-redux-redux-saga-in-your-application-4ipb>

我终于有时间开源我个人使用 redux 和 redux-saga 的方式了。受 DVA 模型的启发，这个库帮助你组织你的代码，并尽可能简单地使用 redux 和 redux-saga。
您可以在这里找到您需要了解的一切:

[https://icecreamjs.netlify.com/](https://icecreamjs.netlify.com/)

该库刚刚发布，请不要犹豫，将您的反馈发送给我，并尝试提供新功能！

## 安装包

使用你最喜欢的包管理器:

```
yarn add icecream-please 
```

或者

```
npm i --save icecream-please 
```

## 基本示例

先写个模型吧。
模型是一个经典的 JavaScript 对象`key:value`,它包含了应用程序运行所需的所有逻辑。
你的整个应用程序只能有一个模型，但是通过把它分成不同的部分来组织你的应用程序通常是很有用的。

让我们创建一个模型来处理一个基本计数器:

```
// counterModel.js

export default {
  modelname: "counter",
  state: {
    number: 0
  },
  reducers: {
    add(state) {
      return {
        ...state,
        number: state.number + 1
      };
    },
    sub(state) {
      return {
        ...state,
        number: state.number - 1
      };
    },
    reset(state) {
      return {
        ...state,
        number: 0
      };
    }
  },
  effects: {},
  listeners: {}
}; 
```

冰淇淋是和 redux 和 redux-saga 合作，仅此而已。这意味着**你可以将它与任何种类的 JavaScript 库和框架**一起使用。我们在这里用 React】

```
// index.js
import React from "react";
import ReactDOM from "react-dom";
import { Provider } from "react-redux";
import iceCreamPlease from "icecream-please";
import counterModel from "./counterModel";
import App from "./components/App";

const store = iceCreamPlease({ models: [counterModel] });

const Container = () => (
  <Provider store={store}>
    <App />
  </Provider> );

ReactDOM.render(<Container />, document.getElementById("root")); 
```

下面是 App.js 文件的代码:

```
// App.js
import React from "react";
import { connect } from "react-redux";

const App = props => {
  const {
    counter: { number },
    dispatch
  } = props;

  return (
    <div style={{ textAlign: "center" }}>
      <h2>Use the counter:</h2>
      <h2>{number}</h2>
      <div>
        <button
          onClick={() => {
            dispatch({ type: "counter/sub" });
          }}
        >
          SUB
        </button>
        <button
          onClick={() => {
            dispatch({ type: "counter/reset" });
          }}
        >
          RESET
        </button>
        <button
          onClick={() => {
            dispatch({ type: "counter/add" });
          }}
        >
          ADD
        </button>
      </div>
    </div>
  );
};

export default connect(({ counter }) => ({ counter }))(App); 
```

瞧！你可以在这里找到这个例子[，在这里](https://github.com/icecreamjs/icecream/tree/master/examples/icp-basicexample)找到一个不太基本的[。](https://github.com/icecreamjs/icecream/tree/master/examples/icecream-example)