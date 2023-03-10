# 用 redux-thunk 处理异步操作

> 原文：<https://dev.to/delph/handling-asynchronous-actions-with-redux-thunk-1dpc>

**本文假设 redux* 中的一些基础知识

## Redux

Redux 是 React 应用程序中管理全局状态的常用库。Redux 非常适合同步动作的状态更新(例如，递增/递减计数器)，但通常情况下，大多数应用程序需要执行某种异步动作(例如，调用 API 从服务器获取数据)。

## redux-thunk

redux-thunk 是一个中间件，它允许您编写与存储交互的异步逻辑。顾名思义，redux 中间件位于一个动作被分派的时刻和它到达缩减器的时刻之间。

## 入门

首先，创建 react 应用程序并安装依赖项

`npm install redux react-redux redux-thunk axios --save`

或者

`yarn add redux react-redux redux-thunk axios`

### index.js

在您的根`index.js`文件中，按照常规从‘react-redux’导入提供者，并用它包装应用程序组件，以便整个应用程序可以访问 redux 存储。

我们还需要像往常一样从“redux”导入 createStore。唯一不同的是，我们还需要导入 applyMiddleware，一个来自' redux '的函数，以及来自' redux-thunk '的 thunk。当我们创建存储时，这将作为第二个参数传入，这样每当我们调度一个动作时，该动作将首先作为中间件发送给 redux thunk。

```
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

import App from './components/App';
import reducers from './reducers';

const store = createStore(reducers, applyMiddleware(thunk));

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.querySelector('#root')
); 
```

在这一点上，我们会得到一些错误，因为我们还没有创建我们的'应用'组件和减速器。但是首先，让我们做一些配置并创建一些动作创建器来获取我们的数据。

### API 配置

虽然这一步不是必需的，但对于创建 axios 实例和在 apis 文件夹中指定基本 URL 是很有用的。通过预配置 axios，我们不需要在每次发出请求时都指定基础。

对于这个例子，我们将从 jsonplaceholder 获取一个帖子列表。

```
// src/apis/jsonPlaceholder.js
import axios from 'axios';

export default axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com'
}) 
```

### 动作创建者

普通同步应用和 redux thunk 异步动作的主要区别就在于这一步。

一般来说，对于 redux，一个动作创建器只是一个函数，它返回一个普通的 javascript 对象，带有一个类型属性(偶尔还有一些其他属性，比如‘有效载荷’等)。)

使用 redux thunk，动作创建者还可以选择返回一个函数，而不是一个动作对象。我们获取帖子的动作创建器应该是这样的:

```
// src/actions/index.js
import jsonPlaceholder from '../apis/jsonPlaceholder';

// normal action creator
export const clearPosts = () => ({
    type: 'CLEAR_POSTS'
})

// thunk action creators
export const fetchPosts = () =>  async dispatch => {
  const response = await jsonPlaceholder.get('/posts')

  dispatch({type: 'FETCH_POSTS', payload: response.data})
 }

export const fetchUser = id =>  async dispatch => {
  const response = await jsonPlaceholder.get(`/users/${id}`)

  dispatch({type: 'FETCH_USER', payload: response.data})
 } 
```

除了 dispatch 参数之外，我们还可以选择传入第二个参数 getState，这将使我们能够完全控制从 redux 存储中更改或获取信息。

```
// src/actions/index.js

export const fetchPostsAndUsers = id =>  async (dispatch, getState) => {
  await dispatch(fetchPosts())

  const userIds = _.uniq(_.map(getState().posts, 'userId'))

  userIds.forEach(id => dispatch(fetchUser(id)))
 } 
```

### 减速器

这里没什么不同。

```
// src/reducers/index.js
import { combineReducers } from 'redux';
import postsReducer from './postsReducer';
import userReducer from './userReducer';

export default combineReducers({
  posts: postsReducer,
  users: userReducer
});

// src/reducers/postsReducer.js
export default (state = [], action) => {
  switch (action.type) {
    case 'FETCH_POSTS':
      return action.payload;
    default:
      return state;
  }
};

// src/reducers/userReducer.js
export default (state = [], action) => {
  switch (action.type) {
    case 'FETCH_USER':
      return [...state, action.payload];
    default:
      return state;
  }
}; 
```

## 最后，我们的 App.js

按照正常的 redux，我们需要从“react-redux”导入连接，以便访问 redux 存储中的状态。

```
// src/components/App.js

import React from 'react';
import { connect } from 'react-redux';
import { fetchPosts } from '../actions';

class App extends React.Component {
  componentDidMount() {
    this.props.fetchPosts();
  }

  renderList() {
    return this.props.posts.map(post => {
      return (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.body}</p>
        </div>
      );
    });
  }

  render() {
    return <div>{this.renderList()}</div>;
  }
}

const mapStateToProps = state => {
  return { posts: state.posts };
};

export default connect(
  mapStateToProps,
  { fetchPosts }
)(App); 
```