# 用 React-Async 获取 API 数据

> 原文：<https://dev.to/aviatorifeanyi/fetching-api-data-with-react-async-32cl>

作为开发人员，当提出 api 请求时，尤其是使用 react 时，我们总是面临使用 javascript fetch API 或 axios 库的选择。根据开发人员的需求和使用情况，从上面选择一个总是很有效的。然而，我很好奇寻找其他的做事方式，这时候我发现了一个包:react-async。

这篇文章是我试图理解并自学这个包的用法

## 介绍 React-Async

用于声明性承诺解析和数据获取的 React 组件和钩子[文档](https://www.npmjs.com/package/react-async)

使用该软件包的好处之一是:

*   它还可以与 promises、async/await 和 Fetch API 一起工作

这篇文章的其余部分将是代码。下定决心😁😁

我们将使用 react cli

首先安装 react CLI:create-react-app(CRA)

```
npx install create-react-app async-react

cd async-react

npm start 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我使用 npx 来安装 CRA 和引导我的项目。这节省了我首先在我的系统中全局安装 CRA，然后启动项目的时间

而是为了你的需要

```
npm install -g create-react-app
create-react-app async-react

cd async-react
npm start 
```

Enter fullscreen mode Exit fullscreen mode

厉害！！！我们的开发环境已经开始

在您喜欢的代码编辑器中打开我们的项目文件夹，然后进入 src 文件夹

这是我们做大部分工作的地方

将 App.js 重命名为 Posts.js。我们将呈现一个帖子列表

打开 Posts.js 文件，输入代码

```
//import react and react-async

import './App.css'
import React from "react"
import Async from "react-async"

const loadPosts = () =>
 fetch('https://jsonplaceholder.typicode.com/posts')
    .then(res => (res.ok ? res : Promise.reject(res)))
    .then(res => res.json())

const Posts = () => (
  <Async promiseFn={loadPosts}>
    {({ data, error, isLoading }) => {
      if (isLoading) return <div>Fetching data... Please Wait</div> 
      if (error) return <div>Something went wrong: ${error.message}</div> 
      if (data){
          const responseData = data.map((post)=>{
            return(
              <div>
               <p> {post.title} </p>
               <hr />
               <h1> {post.body} </h1>
              </div>
            )
          })
        return(
          <div>
           {responseData}
          </div>
        )
       }
      return null
    }}
  </Async> 
)

export default Posts 
```

Enter fullscreen mode Exit fullscreen mode

在顶部，我们导入了 react 和 react-async

但是我们仍然没有 react-async 包
让我们安装它

`npm install --save react-async`

使用 react-async 有三种方法

*   作为一个钩子
*   作为一个组件
*   作为一个工厂

我们使用**作为组件**的功能

### 代码说明

我们创建一个 loadPosts 函数，它使用 fetch api
发出一个 api 请求，这个请求返回一个承诺，这个承诺被解决或拒绝。

如果这个问题得到解决，它会为我们的组件提供一些支持，包括-

*   数据:api 请求成功，我们已经将数据注入到组件中

*   错误:不成功的 api 请求。这包含了出错的细节

*   isLoading:来自我们的 api 的数据响应还没有到达，所以我们等待直到它被解决

在 Posts 组件中，我们利用提供给我们的道具并返回数据

注意这一行

```
 <Async promiseFn={loadPosts}> 
```

Enter fullscreen mode Exit fullscreen mode

这就是奇迹发生的地方

我们传入 loadPosts 函数，该函数将 api 请求作为一个道具传递给异步组件，当它被解析时，我们将获得数据、错误和 isLoading 道具，然后我们可以在 Posts 组件中使用它们

回想一下，我们从 react-async 导入了 Async

### 更新

打开 index.js 文件并进行适当的修改

```
//remove 
import App from './App'

//Add
import Posts from './Posts'

ReactDOM.render(<Posts />, document.getElementById('root') ) 
```

Enter fullscreen mode Exit fullscreen mode

有了这些方法，我们的应用程序应该可以工作了