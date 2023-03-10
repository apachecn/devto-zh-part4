# 用 React 和 Blockstack 构建一个分散的待办事项列表

> 原文：<https://dev.to/dkb868/build-a-decentralized-todo-list-with-react-and-blockstack-59cm>

在本教程中，您将学习使用 Blockstack 和 React 构建一个分散的待办事项列表。Blockstack 是一个平台，可以非常容易地构建去中心化的应用程序。与传统的身份验证/存储方法相比，使用块堆栈身份验证和存储来构建简单的应用程序更快、更安全。

## Blockstack 的去中心化方法

像谷歌和脸书这样的大公司有集中的数据库，在那里他们控制你的数据，可以做任何他们想做的事情。

块堆栈应用允许用户完全控制他们的数据。没有他们的允许，任何人都不能访问用户的数据。用户数据被加密并存储在私人“数据柜”中，用户可以授予应用程序对其存储进行读/写数据的权限。

对于我们的待办事项列表应用程序，这意味着应用程序开发人员永远不会知道你的待办事项列表上有什么。

## App

我们的待办事项列表将非常简单，因此我们可以专注于学习 Blockstack 如何工作。

这是完成后的应用程序的样子:

[![](img/cf3b0222cfe69dfdac621524a148019b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WX4wLfqM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/99ec89mz7xqdxdz2dnq3.png)

这里是一个演示网站:[https://blockstack-todo-list.netlify.com/](https://blockstack-todo-list.netlify.com/)

Github 回购:[https://github.com/dkb868/secure-todo-list](https://github.com/dkb868/secure-todo-list)

## 设置

首先，我们将设置环境。您应该已经安装了 node.js 的最新版本。

### 做出反应

我们将使用`create-react-app`，所以在您的终端中键入`npx create-react-app secure-todo-list`来创建新项目

一分钟左右，应该就完成了。

[![](img/95af494362d2086447d4eb98da420bb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yEeuMfJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/juzs52dnavi314pqbxl8.png)

用`cd secure-todo-list`导航到你的新目录，然后输入`npm start`确保一切正常。

您应该会在浏览器中看到:

[![](img/7bdfc389e0c3ed4f59f32da2a8e7b694.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zVNuCl9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zwo0amtc5pyozaxteo3q.png)

然后在编码编辑器中打开项目文件夹，让我们做一些清理工作。删除以下文件:

*   `App.css`
*   `App.test.js`
*   `index.css`
*   `logo.svg`

然后打开`App.js`，将内容替换为:

```
import React from "react"

class App extends React.Component {
  render() {
    return <div>Nice Meme</div>
  }
}

export default App 
```

并更新`index.js`

```
import React from "react"
import ReactDOM from "react-dom"
import App from "./App"
import * as serviceWorker from "./serviceWorker"

ReactDOM.render(<App />, document.getElementById("root"))

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister() 
```

### 漂亮些

如果不用更漂亮的，我强烈推荐。它使你的代码更加整洁。你可以通过寻找更漂亮的插件把它添加到你的编辑器中。

向您的项目根目录(`secure-todo-list/`)添加一个`.prettierrc`文件，以一个空对象作为内容，这为您提供了默认设置。

```
{} 
```

### 语义 UI

我们将使用语义 UI，一个 CSS 库，给我们的应用程序一些样式。

通过将这一行添加到 html 文件的头部，将这个 url ( `https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css`)复制到您的`public/index.html`中。

```
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css"
/> 
```

现在你应该有一个非常漂亮，极简的网站完成。

[![](img/e5e7a8e0f6bb8feb531da6795966a496.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s_n7AQYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3h1h0bkaznnh1asyc6c7.png)

### 区块链账户

你需要一个 Blockstack 帐户，以便登录和使用你的应用程序。你可以去[https://blockstack.org/](https://blockstack.org/)并从菜单中选择`Create ID`得到一个。

[![](img/bc44a3f6c7d92ac85737822c72c53915.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jsvOZDLS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mm4kpkrvezci739y1h5e.png)

## 简单的待办事项列表

我们将首先在 React 中构建一个简单的 todo 列表，不包含任何块堆栈。每当页面刷新时，应用程序状态都会丢失，但这将更容易看到 blockstack 的位置。

### 初始状态

让我们从向应用程序添加一些状态开始。在`App.js`
中的渲染函数上面添加这个

```
state = {
  todos: [
    {
      id: 1,
      title: "Wash the dishes",
      done: false,
    },
    {
      id: 2,
      title: "Clean my room",
      done: false,
    },
  ],
} 
```

现在我们的应用程序跟踪待办事项，这些事项有三个属性:

*   id:此待办事项的唯一标识符
*   标题:该任务的名称
*   完成:该任务是否已完成

### 显示待办事项

现在我们有了一些待办事项，让我们在页面上显示它们。

改变你的渲染方法如下:

```
 render() {
    return (
      <div style={{ padding: "30px 0" }}
        className="ui text container center aligned">
        <h2>My Todos</h2>
        <div className="ui grid">
          <div className="row centered">
            <div className="column twelve wide">
              <div className="grouped fields">
                {this.state.todos
                  .filter(todo => !todo.done)
                  .map(todo => (
                    <div key={todo.id} className="field">
                      <div className="ui checkbox">
                        <input type="checkbox" />
                        <label>{todo.title}</label>
                      </div>
                    </div>
                  ))}
              </div>
            </div>
          </div>
        </div>
      </div>
    );
  } 
```

像`ui text container center aligned`这样的所有类名都来自语义 UI，有助于让我们的应用程序看起来更好。

第`this.state.todos.filter(todo => !todo.done).map(todo => ...`行过滤掉已经完成的待办事项，并将其从页面中隐藏。

现在你应该有一个看起来像待办事项列表的东西。

[![](img/54e1fd78e04d65fe935c16220d3721a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k0FcPDcD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nrvnde40tn24ta0s0d23.png) 如果你点击其中一个复选框，你会发现它什么也不做。理想情况下，我们希望检查时东西消失，所以让我们把它加进去。

### 完成待办事项

向复选框添加一个`onClick`处理程序。

```
<input
  type="checkbox"
  onClick={() => {
    this.handleCheckboxClick(todo.id)
  }}
/> 
```

我们使用了一个有点奇怪的语法，因为我们想要将所选 todo 的 id 传递给我们的处理函数。

应该将处理程序添加到呈现函数的上方。

```
 handleCheckboxClick(id) {
    let newTodos = [...this.state.todos];
    newTodos[newTodos.findIndex(todo => todo.id === id)].done = true;
    this.setState({
      todos: newTodos
    });
  } 
```

这是在 React 中修改数组状态的许多方法之一。首先，我们复制当前待办事项列表，然后将所选待办事项(由其 id 标识)标记为完成，并更新状态。

现在，当您选中该框时，todo 应该从页面中消失，因为我们过滤掉了任何标记为完成的项目。

### 添加待办事项

在现实生活中，人们可能有比洗碗和打扫房间更多的任务要做，所以让我们允许用户添加他们自己的待办事项。

首先，让我们向我们的呈现方法添加一个输入表单。

```
render() {
    return (
      <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <h2>My Todos</h2>
        <div className="ui grid">
          <div className="row centered">
            <div className="column twelve wide">
              <form className="ui form" onSubmit={this.handleAddTodoClick}>
                <div className="inline fields">
                  <div className="twelve wide field">
                    <input
                      type="text"
                      value={this.state.newTodo}
                      onChange={this.hanldeInputChange}
                    />
                  </div>
                  <button className="ui button primary" type="submit">
                    Add todo
                  </button>
                </div>
              </form>
            </div>
          </div>
          <div className="row centered">
            <div className="column twelve wide">
              <div className="grouped fields">
                {this.state.todos
                  .filter(todo => !todo.done)
                  .map(todo => (
                    <div key={todo.id} className="field">
                      <div className="ui checkbox">
                        <input
                          type="checkbox"
                          onClick={() => {
                            this.handleCheckboxClick(todo.id);
                          }}
                        />
                        <label>{todo.title}</label>
                      </div>
                    </div>
                  ))}
              </div>
            </div>
          </div>
        </div>
      </div>
    );
  } 
```

然后让我们实现所有这些处理函数。

更新初始状态以跟踪我们新的 todo 值，并清除那些默认的 todo

```
state = {
  todos: [],
  newTodo: "",
} 
```

实现`handleInputChange`函数，该函数将跟踪用户输入的内容。

```
hanldeInputChange = e => {
  this.setState({
    newTodo: e.target.value,
  })
} 
```

接下来我们实现`handleAddTodoClick`,当用户点击 enter 或者点击按钮来添加他们的新 todo 项目时，就会调用这个函数。

```
handleAddTodoClick = e => {
  e.preventDefault()
  const newTodo = {
    id: this.state.todos.length + 1,
    title: this.state.newTodo,
    done: false,
  }
  const todos = [...this.state.todos]
  todos.push(newTodo)
  this.setState({
    todos: todos,
    newTodo: "",
  })
} 
```

你的整个`App.js`应该是这样的:

```
import React from "react"

class App extends React.Component {
  state = {
    todos: [],
    newTodo: "",
  }

  handleCheckboxClick(id) {
    let newTodos = [...this.state.todos]
    newTodos[newTodos.findIndex(todo => todo.id === id)].done = true
    this.setState({
      todos: newTodos,
    })
  }

  handleAddTodoClick = e => {
    e.preventDefault()
    const newTodo = {
      id: this.state.todos.length + 1,
      title: this.state.newTodo,
      done: false,
    }
    const todos = [...this.state.todos]
    todos.push(newTodo)
    this.setState({
      todos: todos,
      newTodo: "",
    })
  }

  hanldeInputChange = e => {
    this.setState({
      newTodo: e.target.value,
    })
  }

  render() {
    return (
      <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <h2>My Todos</h2>
        <div className="ui grid">
          <div className="row centered">
            <div className="column twelve wide">
              <form className="ui form" onSubmit={this.handleAddTodoClick}>
                <div className="inline fields">
                  <div className="twelve wide field">
                    <input
                      type="text"
                      value={this.state.newTodo}
                      onChange={this.hanldeInputChange}
                    />
                  </div>
                  <button className="ui button primary" type="submit">
                    Add todo
                  </button>
                </div>
              </form>
            </div>
          </div>
          <div className="row centered">
            <div className="column twelve wide">
              <div className="grouped fields">
                {this.state.todos
                  .filter(todo => !todo.done)
                  .map(todo => (
                    <div key={todo.id} className="field">
                      <div className="ui checkbox">
                        <input
                          type="checkbox"
                          onClick={() => {
                            this.handleCheckboxClick(todo.id)
                          }}
                        />
                        <label>{todo.title}</label>
                      </div>
                    </div>
                  ))}
              </div>
            </div>
          </div>
        </div>
      </div>
    )
  }
}

export default App 
```

现在，您应该能够添加新的待办事项，并检查它们。唯一的问题是，当你刷新页面时，你会丢失所有珍贵的待办事项。现在是时候使用 Blockstack 实际保存我们的待办事项了。

## 我们来添加 Blockstack！

现在，我们将使用块堆栈添加用户身份验证和存储。首先用`ctrl-c`停止你的 app，用`npm install blockstack`安装 blockstack。然后我们可以用`npm start`再次启动应用程序，一切应该还是一样的。

### 认证

通过在类声明上方添加以下几行，在`App.js`中设置块堆栈。

```
import { UserSession, AppConfig } from "blockstack";

const appConfig = new AppConfig(["store_write"]);
const userSession = new UserSession({ appConfig: appConfig });

class App extends React.Component {
    ...
} 
```

行`const appConfig = new AppConfig(["store_write"]);`用于设置我们的 blockstack 应用程序的配置。您可以向用户请求所需的权限。在这种情况下，我们请求`store_write`权限，这允许我们将数据存储在用户的私有存储中。

如果我们想要构建一些更社会化的东西，我们会想要`publish_data`权限，这允许某些用户数据对其他用户可见。

`const userSession = new UserSession({ appConfig: appConfig });`建立用户会话，允许我们处理认证。

在页面顶部添加一个登录按钮。

```
<div style={{ padding: "30px 0" }} className="ui text container center aligned">
  <button className="ui button positive" onClick={this.handleSignIn}>
    Sign in with blockstack
  </button>
  <h2>My Todos</h2>
  ...
</div> 
```

并像这样实现我们的处理函数`this.handleSignIn`:

```
handleSignIn = () => {
  userSession.redirectToSignIn()
} 
```

是的，实现登录只需要一行代码。

您的页面现在应该看起来像这样:

[![](img/fae4f32a9472e3127b4ca975ac49002e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6WusUplB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ti0g8udvia482kjixtu0.png) 
让我们点击那个按钮，看看会发生什么！

嗯，我们被带到 blockstack 浏览器进行登录，但看起来有一个问题...

[![](img/2d11926bf0dfc1910b7fb046b1b4f5b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kbe4TAKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jbvaasat0go87bvt0bl8.png)

嗯，“无法获取有关请求身份验证的应用程序的信息。请联系应用维护人员来解决问题。”这不是很好的描述，但是我们的控制台说了一些更有用的东西。

```
Access to fetch at 'http://localhost:3000/manifest.json' from origin 'https://browser.blockstack.org' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled. 
```

这是怎么回事？这其实是你刚入门 Blockstack 时很常见的 bug，就来说说吧。

### 修复 CORS 错误

问题是 Blockstack 浏览器正试图从您的网站访问一个名为`manifest.json`的文件，其中包含有关您的应用程序的信息。但是，由于 CORS，默认情况下，网站不能向不同域上的其他网站发出请求。这样做是出于安全目的。因此，我们的网站现在拒绝 Blockstack 浏览器对我们的`manifest.json`的请求，但我们实际上希望 Blockstack 能够访问该文件。

为此，我们需要修改我们的 webpack 配置。因为我们使用了`create-react-app`，所以 webpack 配置是隐藏的。为了修改它，我们使用命令`npm run eject`。您可能会得到一个关于有未跟踪的文件和未提交的更改的警告。所以首先将所有的更改提交给 git。

```
git add -A
git commit -m "did things"
npm run eject 
```

[![](img/6019e05c54e298ea5af019d51172b7b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Kb3zzZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zxjgjxqpcyflboheeox8.png)

您将在您的目录中看到两个名为`scripts`和`config`的新文件夹。转到`config/webpackDevServer.config.js`，在模块导出函数的顶部添加下面一行。

```
module.exports = function(proxy, allowedHost) {
  return {
    headers: {
      "Access-Control-Allow-Origin": "*"
    },

    // WebpackDevServer 2.4.3 introduced a security fix that prevents remote
    // websites from potentially accessing local content through DNS rebinding:

    ...

  }
} 
```

现在用`npm start`再次启动项目，让我们再次尝试登录。

[![](img/c43396213c6270150698390a4a19f1ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GM36vFBy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1g424i5cr4fpl4xuhgs6.png)

我们的应用程序可能会使用比“创建 React 应用程序示例”更好的名称，所以让我们转到`public/manifest.json`来修改它。您可以在此将应用程序名称更改为您喜欢的任何名称。

```
{  "short_name":  "Todo List",  "name":  "Secure Todo List",  "icons":  [  {  "src":  "favicon.ico",  "sizes":  "64x64 32x32 24x24 16x16",  "type":  "image/x-icon"  }  ],  "start_url":  ".",  "display":  "standalone",  "theme_color":  "#000000",  "background_color":  "#ffffff"  } 
```

### 认证继续

现在让我们根据用户是否登录来修改视图。注销的用户可能不应该看到他们的待办事项列表，而登录的用户不需要看到登录按钮。

为了更清楚，我们将把这两个东西分成不同的部分。我们将有一个显示待办事项列表的`TodoList`组件和一个显示登录页面的`Login`组件。

将`App.js`的内容复制到一个名为`TodoList.js`的新文件中，并修改如下。

```
import React from "react"

class TodoList extends React.Component {
  state = {
    todos: [],
    newTodo: "",
  }

  handleCheckboxClick(id) {
    let newTodos = [...this.state.todos]
    newTodos[newTodos.findIndex(todo => todo.id === id)].done = true
    this.setState({
      todos: newTodos,
    })
  }

  handleAddTodoClick = e => {
    e.preventDefault()
    const newTodo = {
      id: this.state.todos.length + 1,
      title: this.state.newTodo,
      done: false,
    }
    const todos = [...this.state.todos]
    todos.push(newTodo)
    this.setState({
      todos: todos,
      newTodo: "",
    })
  }

  hanldeInputChange = e => {
    this.setState({
      newTodo: e.target.value,
    })
  }

  render() {
    return (
      <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <h2>My Todos</h2>
        <div className="ui grid">
          <div className="row centered">
            <div className="column twelve wide">
              <form className="ui form" onSubmit={this.handleAddTodoClick}>
                <div className="inline fields">
                  <div className="twelve wide field">
                    <input
                      type="text"
                      value={this.state.newTodo}
                      onChange={this.hanldeInputChange}
                    />
                  </div>
                  <button className="ui button primary" type="submit">
                    Add todo
                  </button>
                </div>
              </form>
            </div>
          </div>
          <div className="row centered">
            <div className="column twelve wide">
              <div className="grouped fields">
                {this.state.todos
                  .filter(todo => !todo.done)
                  .map(todo => (
                    <div key={todo.id} className="field">
                      <div className="ui checkbox">
                        <input
                          type="checkbox"
                          onClick={() => {
                            this.handleCheckboxClick(todo.id)
                          }}
                        />
                        <label>{todo.title}</label>
                      </div>
                    </div>
                  ))}
              </div>
            </div>
          </div>
        </div>
      </div>
    )
  }
}

export default TodoList 
```

然后像这样做一个`Login.js`组件。

```
import React from "react"

class Login extends React.Component {
  handleSignIn = () => {
    this.props.userSession.redirectToSignIn()
  }

  render() {
    return (
      <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <h1>Decentralized Todo List</h1>
        <p>This is the most secure todo list on the market.</p> 
        <button className="ui button positive" onClick={this.handleSignIn}>
          Sign in with blockstack
        </button>
      </div>
    )
  }
}

export default Login 
```

我们把`userSession`作为道具传进去。该对象包含与用户认证相关的有用函数。

最后，我们的`App.js`将被修改，当用户注销时显示`Login`组件，当用户登录时显示`TodoList`。

```
import React from "react"
import { UserSession, AppConfig } from "blockstack"
import Login from "./Login"
import TodoList from "./TodoList"
const appConfig = new AppConfig(["store_write"])
const userSession = new UserSession({ appConfig: appConfig })

class App extends React.Component {
  render() {
    return (
      <div>
        {userSession.isUserSignedIn() ? (
          <TodoList userSession={userSession} />
        ) : (
          <Login userSession={userSession} />
        )}
      </div>
    )
  }
}

export default App 
```

我们使用函数`userSession.isUserSignedIn()`来确定是否有登录的用户。

现在，默认情况下，您应该会看到登录页面。单击该按钮时，您会被重定向到 Blockstack，选择您的 id 后，您会被重定向到您的应用程序，然后...它仍然向您显示登录页面。那是怎么回事？

原来我们实际上处于中间登录阶段。至此，Blockstack 已经为应用程序提供了一个包含所有用户信息的令牌。我们需要再添加一个函数调用来从 toke 中提取信息并完成登录。

将这些行添加到您的`App`组件中的`render()`函数之上。

```
 componentWillMount() {
    if (userSession.isSignInPending()) {
      userSession
        .handlePendingSignIn()
        .then(() => {
          window.location = window.location.origin;
        })
        .catch(err => console.log(err));
    }
  } 
```

这将从令牌中提取用户信息，完成登录，然后刷新页面。

下面的图表解释了整个块堆栈身份验证过程。

[![](img/080a0b4f9974654adfe012a26b9b9e3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Le5eWlO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.blockstack.org/storaimg/app-sign-in.png)

设置好之后，再次尝试登录，您应该会被重定向到 todo 列表。

最后，让我们向 todo 列表页面添加一个注销按钮。转到`TodoList.js`，在`render`功能中的页面顶部添加一个按钮。

```
 <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <button className="ui button negative" onClick={this.handleSignout}>
          Sign out
        </button>

        <h2>My Todos</h2>
        <div className="ui grid">

            ...

     </div>
</div> 
```

在`render`函数的上方添加`handleSignout`函数。

```
handleSignout = () => {
  this.props.userSession.signUserOut(window.location.origin)
} 
```

现在，您可以使用 Blockstack 登录和注销应用程序。

### 存储待办事项

现在，用户可以登录我们的应用程序，我们可以用 Blockstack 存储他们的数据。

我们将使用`blockstack.js`库的两个核心函数:`putFile`和`getFile`。

他们做的和他们听起来一样。`putFile`允许您存储文件，`getFile`允许您检索文件。您可以存储任何类型的文件，如果您愿意，还可以对它们进行加密。

在我们的例子中，我们将以 JSON 格式存储我们的 todos，因为这样便于处理。

转到`TodoList.js`，修改`handleAddTodoClick`功能如下:

```
handleAddTodoClick = e => {
  e.preventDefault()
  const newTodo = {
    id: this.state.todos.length + 1,
    title: this.state.newTodo,
    done: false,
  }
  const todos = [...this.state.todos]
  todos.push(newTodo)
  const options = { encrypt: true }
  this.props.userSession
    .putFile("todos.json", JSON.stringify(todos), options)
    .then(() => {
      this.setState({
        todos,
        newTodo: "",
      })
    })
} 
```

这将所有用户的待办事项存储在一个名为`todos.json`的文件中

修改`handleCheckboxClick`以便当我们将 todos 标记为完成时，这也在用户存储器中更新。

```
 handleCheckboxClick(id) {
    let newTodos = [...this.state.todos];
    newTodos[newTodos.findIndex(todo => todo.id === id)].done = true;
    const options = { encrypt: true };
    this.props.userSession
      .putFile("todos.json", JSON.stringify(newTodos), options)
      .then(() => {
        this.setState({
          todos: newTodos
        });
      });
  } 
```

现在试着做一些 todos，你应该会在你的控制台上看到类似这样的东西，表明文件已经被存储了。

[![](img/c5feb192cf08239a5c57f7e0c2ba91af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0ZIdYbfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g3fy9iq2vmto2sird77d.png)

如果刷新页面，您将看不到任何内容，因为我们仍然需要检索 todos。

向您的类添加一个名为`fetchData`的新函数，它将从用户存储中获取 todo 列表。

```
 async fetchData() {
    const options = { decrypt: true };
    const file = await this.props.userSession.getFile("todos.json", options);
    let todos = JSON.parse(file || "[]");
    this.setState({
      todos
    });
  } 
```

我们将在`componentDidMount`中调用这个函数

```
componentDidMount() {
    this.fetchData();
} 
```

现在您可以添加一个 todo 项，刷新您的页面，它将仍然存在！

### 添加用户档案数据

现在我们的应用程序感觉不太个性化，但我们可以使用 Blockstack 来获取用户姓名等信息，以定制他们的体验。

向状态添加一个新字段来存储用户对象。

```
state = {
  newTodo: "",
  todos: [],
  user: null,
} 
```

然后修改`fetchData`函数，用用户信息更新状态。

```
 async fetchData() {
    const options = { decrypt: true };
    const file = await this.props.userSession.getFile("todos.json", options);
    let todos = JSON.parse(file || "[]");
    this.setState({
      todos,
      user: new Person(this.props.userSession.loadUserData().profile)
    });
  } 
```

并在文件的顶部添加一条 import 语句。

```
import { Person } from "blockstack" 
```

`Person`对象将用户数据放在一个容易访问的格式中。

修改`render`函数显示一些用户信息。我们将显示他们的姓名和个人资料图片。

```
render() {
    const { user } = this.state;

    return (
      <div
        style={{ padding: "30px 0" }}
        className="ui text container center aligned"
      >
        <button className="ui button negative" onClick={this.handleSignout}>
          Sign out
        </button>
        <h1>{user && user.name()}</h1>
        <img
          className="ui centered medium rounded image"
          src={user && user.avatarUrl()}
          alt="user profile image"
        />
        <h2>My Todos</h2> 
        ... 
```

现在，应用程序应该显示用户的姓名和个人资料图像。

[![](img/1cbd7c55cb02ba65bad02b4cd4971cf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--viAHxdGe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wetblamt8bv2rp3lb9no.png) 
我们的应用看起来不错，现在让我们部署它，让世界其他地方看到。

## 部署到网络生活

部署 React 应用程序的方法有很多，但 Netlify 是最好的方法之一。它允许您轻松设置连续部署。

首先让我们在 github 上创建一个新的存储库。

[![](img/7e89a29c7e719869babdb12459302395.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2ZbTH8uX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nys0g3vnp81uj9wna8os.png)

添加并提交所有文件。

```
git add -A
git commit -m "made everything" 
```

然后按照命令推送现有的存储库。对我来说就是:

```
git remote add origin https://github.com/dkb868/secure-todo-list.git
git push -u origin master 
```

现在你应该在 github 上有一个漂亮的新回购了。

[![](img/4e280ecff173af9e3972072ad8aa937e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2yTWQFyI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lg09jq1xrc6xsqfhdxut.png)

在 [Netlify](https://www.netlify.com/) 上创建一个账户，然后在你的仪表板中选择“从 Git 新建网站”。

[![](img/15a05892aff04cc8730298f56c9c4e37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWwktFyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kakkv39ngszqvbfe4gux.png)

选择 Github，并搜索您的回购。

[![](img/26a73898a49412f4392112f2fd19e537.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gnvMtbuu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t1gf4928j5ftscvgs70m.png)

使用以下构建设置，然后单击`Deploy Site`

[![](img/fb64f3d37b1d623f2a2f8a4f2726fbb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CXAlb2mK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wk3ijbdk77gim906k5td.png)

给它几分钟，然后你应该有你的网站在 something.netlify.com。如果需要，您可以修改此名称，或者添加自定义域。

如果我们打开新发布的应用程序，我们会看到一个熟悉的错误。

[![](img/9dae19bddabcc5b3ceb258c428f9aaee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ceFyexyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r597foo6qtvl3i2liufu.png)

我们知道这是一个 CORS 错误，我们已经在开发环境中修复了它，所以现在我们需要在生产环境中修复它。

使用 Netlify，这就像在根项目目录中添加一个`netlify.toml`文件一样简单。

```
[[headers]]
  for = "/*"
  [headers.values]
  Access-Control-Allow-Origin = "*" 
```

添加该文件并将其推送到 GitHub。启用连续部署后，它将在几分钟内自动部署。

现在一切都应该运行良好。

[![](img/4fd58bd68b3561513ae069cec47ddd89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3tN0pm3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/412o6qqie3ro8btasuoc.png)

## 结论

如果你做到了这一步，恭喜你完成了这个应用程序！

如果你在某个时候迷失了，你可以查看 github 回购或演示网站以供参考。

演示网站:[https://blockstack-todo-list.netlify.com/](https://blockstack-todo-list.netlify.com/)

Github 回购:[https://github.com/dkb868/secure-todo-list](https://github.com/dkb868/secure-todo-list)

这是我的第一个编码教程，所以如果你对我可以改进的地方有任何反馈，请告诉我。

另外，Blockstack 正在举办一个黑客马拉松，你可以参加！