# 如何用 React 和 Firebase 数据库构建 todo 应用程序

> 原文：<https://dev.to/rossanodan/how-to-build-a-todo-app-with-react-and-firebase-database-coh>

## 第 1 天

📅2019 年 6 月 11 日
🕐1h
🏁初始设置和准备

# 初始设置

我将使用`create-react-app`工具来搭建项目文件夹。这是一个由脸书提供的工具，允许简单地搭建一个预先配置的启动项目。

```
npx create-react-app todo-app 
```

初始项目包括

*   `node_modules`:包含所有必要的依赖项。它是用`create-react-app`工具搭建应用程序生成的(其中有一个`npm install`)
*   `public`:包含一些文件，如`index.html`、`application favicon`和一个`manifest`，其中包含一些基本的应用程序设置
*   `src`:包含代码
*   `.gitignore`
*   这里有所有的项目信息，比如版本、作者，主要是应用程序正常工作所需的依赖项
*   `yarn.lock`:包含了 Yarn 需要的所有相关版本的依赖关系

[![](img/95ab5de45ce60ad8c8dd5942820fe64b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8RpZVUt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ac9cpb4bpprx03cm8euv.png)

要运行 starter basic 应用程序，只需做以下工作

```
cd todo-app
npm start 
```

[![](img/17af1d08ff36cfbb2022ed230f159390.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAZJSz6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r7rniixpbddwys3hx554.png)

`npm start`是我将用来开发这个应用程序的几个预配置命令之一。其他命令包括:

*   `npm test`
*   `npm build`
*   `npm eject`(暂时远离它)

# 准备组件

为了使用可持续和可扩展的结构，我喜欢将事物分开。我将为组件创建两个文件夹。

[![](img/1e098de190af0e75d2aa417558db3b72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TvKQxUZr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ty6xjeagmabliffuwtvi.png)

这两个文件夹将包含(惊喜)组件！它们之间唯一的区别是，**容器**是一个由**管理应用程序状态**的组件，所以它是一个**有状态组件**。其他组件是**无状态组件**。

# 主要成分`<App />`

让我们创建第一个组件。
我要去把`App.js`、`App.test.js`和`App.css`移动到他们自己的文件夹`./containers/App/`:

[![](img/6f06cb0945032b8df92d0e5814ed85d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HAJq00-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hm7jg5fkc8x2vifhr40h.png)

```
// App.js
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        Placeholder
      </div>
    );
  }
}

export default App; 
```

```
/* App.css */
.App {
  text-align: center;
} 
```

目前`App.test.js`没有变化。

更新`index.js` -导入 App 组件-因为文件位置改变，删除`logo.svg`等无用文件。

# `<Todo />`分量

让我们在`./components`文件夹中创建`<Todo />`组件。创建`Todo.js`、`Todo.test.js`和`Todo.css`。

[![](img/a8698d1dcf9d72e8dac14ad5b39bed9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cJb-pF1e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1z32qe9hv618920cfdwx.png)

```
// Todo.js
import React from 'react';
import './Todo.css';

const todo = () => (
    <div className="Todo">
        <p>Placeholder</p>
    </div> )

export default todo; 
```

```
/* Todo.css */
.Todo {} /* Empty for now */ 
```

`Todo.test.js`类似于`App.test.js`:

```
import React from 'react';
import ReactDOM from 'react-dom';
import Todo from './Todo';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<Todo />, div);
  ReactDOM.unmountComponentAtNode(div);
}); 
```

现在我可以使用`<Todo />`组件到`<App />`组件中，这样做:

```
import React, { Component } from 'react';
import './App.css';

import Todo from '../../components/Todo/Todo';

class App extends Component {
  render() {
    return (
      <div className="App">
        <Todo />
      </div>
    );
  }
}

export default App; 
```

点击此处查看代码

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [桑松](https://github.com/rossanodan) / [ todo-app](https://github.com/rossanodan/todo-app)

### 用 React 构建的简单待办应用。

<article class="markdown-body entry-content p-5" itemprop="text">

这个项目是用 [Create React App](https://github.com/facebook/create-react-app) 引导的。

## 如何在本地运行

```
git clone https://github.com/RossanoDan/todo-app.git
cd todo-app
npm install
npm start 
```

</article>

[View on GitHub](https://github.com/rossanodan/todo-app)