# 用 22 行代码创建 React Modal

> 原文：<https://dev.to/elaziziyoussouf/create-react-modal-with-22-lines-of-code-4652>

如今，模态是 React 应用程序中最常用的组件之一，有一个简单的方法来创建 React 模态将有助于您在即将到来的 React 应用程序中快速学习模态的相关特性。在本教程中，我们将只用 22 行代码构建一个简单的 React 模型。

下面的 gif 将帮助你理解我们正在努力构建的东西

[![](img/55aabca5b8548410301470b97cd577d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V4HY9BJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qkllsnpuzmoj4sn6cxx.gif)

> 我们开始吧

### 创建 React 项目

首先，使用 create-react-app cli
创建一个简单的 react 项目

```
npx  Create-react-app  SimpleModal 
```

```
import React from "react";
import ReactDOM from "react-dom";

import "./styles.css";

function App() {
  return (
    <div className="App">
      <h1>Create React Modal in X line of code </h1>
      <button>Click Me</button>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

在本教程中，我们将使用 [react-popup](https://react-popup.elazizi.com/) 包，它是一个简单而强大的 react 组件，有很多好处:

*   模态，工具提示，菜单:都在一个🏋️
*   全风格定制👌
*   函数作为子模式来控制代码中任何地方的弹出窗口。💪
*   IE 支持。🚀
*   类型脚本支持👌
*   所有这些时钟都压缩了大约 3 kB。⚡️

此软件包在 npm 存储库中以 reactjs-popup 的形式提供。它可以在所有流行的捆扎机上正常工作。

```
yarn add reactjs-popup 
```

### 创建反应模态

从 reactjs-popup 导入`Popup`组件，并像下面这样开始使用它。
添加`trigger`属性作为简单的 React 按钮元素，并将`modal`属性设置为 true。

```
import React from "react";
import ReactDOM from "react-dom";
import Popup from "reactjs-popup";

import "./styles.css";

function App() {
  return (
    <div className="App">
      <h1>Create React Modal with 22 line of code </h1>
      <Popup modal trigger={<button>Click Me</button>}>
        Modal Content
      </Popup>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

[![](img/caa3f0a6564fc63341474450cb3b3b80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FhWRfNm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2c15yz37y11softwga34.gif)

### 定制和样式化反应模态

我们需要为模态内容组件创建一个`Content.js`文件，并添加一些样式

```
//content.js
import React from "react";

export default ({ close }) => (
  <div className="modal">
    <a className="close" onClick={close}>
      &times;
    </a>
    <div className="header"> Modal Title </div>
    <div className="content">
      {"  "}
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Atque, a nostrum.
      Dolorem, repellat quidem ut, minima sint vel eveniet quibusdam voluptates
      delectus doloremque, explicabo tempore dicta adipisci fugit amet
      dignissimos?
      <br />
      Lorem ipsum dolor sit amet, consectetur adipisicing elit. Consequatur sit
      commodi beatae optio voluptatum sed eius cumque, delectus saepe
      repudiandae explicabo nemo nam libero ad, doloribus, voluptas rem alias.
      Vitae?
    </div>
  </div>
); 
```

```
/* index.css */ 
.modal {
  font-size: 12px;
}
.modal > .header {
  width: 100%;
  border-bottom: 1px solid gray;
  font-size: 18px;
  text-align: center;
  padding: 5px;
}
.modal > .content {
  width: 100%;
  padding: 10px 5px;
}
.modal > .actions {
  margin: auto;
}
.modal > .actions {
  width: 100%;
  padding: 10px 5px;
  text-align: center;
}
.modal > .close {
  cursor: pointer;
  position: absolute;
  display: block;
  padding: 2px 5px;
  line-height: 20px;
  right: -10px;
  top: -10px;
  font-size: 24px;
  background: #ffffff;
  border-radius: 18px;
  border: 1px solid #cfcece;
} 
```

> 现在是施展魔法的时候了

因为`reactjs-popup`提供了一个子函数模式，你可以完全控制弹出状态

我们将更新我们的示例，使用一个函数作为子元素，而不是 react 元素来实现`close`按钮。

```
//index.js
import React from "react";
import ReactDOM from "react-dom";
import Popup from "reactjs-popup";

import Content from "./Content.js";
import "./styles.css";

function App() {
  return (
    <div className="App">
      <h1>Create React Modal with 22 line of code </h1>
      <Popup modal trigger={<button>Click Me</button>}>
        {close => <Content close={close} />}
      </Popup>
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

### 最终结果

react js-popup:[https://react-popup.elazizi.com/](https://react-popup.elazizi.com/)
repo:[https://github . com/yjose/create-react-modal-with-22-line-of-code](https://github.com/yjose/create-react-modal-with-22-line-of-code)T5】code sandbox:[https://code sandbox . io/s/create-react-modal-with-22-line-of-code-v2u7t](https://codesandbox.io/s/create-react-modal-with-22-lines-of-code-v2u7t)

[![](img/55aabca5b8548410301470b97cd577d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V4HY9BJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qkllsnpuzmoj4sn6cxx.gif)

请务必访问[反应-弹出主页](https://react-popup.elazizi.com/)

感谢阅读！如果你认为其他人应该阅读这篇文章并使用这个项目，发微博并分享这篇文章。

记得关注我，这样你就能收到我未来帖子的通知。