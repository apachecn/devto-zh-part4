# 我们如何使用 Firebase 代替 Redux(带 React)

> 原文：<https://dev.to/pragli/how-we-use-firebase-instead-of-redux-with-react-1abi>

[![How we use Firebase instead of Redux (with React)](img/b1e6ef5345190dc279bceafa3a62ee7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NhPon9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pragli.com/conteimg/2019/09/fb-react-redux.png)

本文解释了 Pragli 如何使用 Firebase 实时数据库作为 React 前端的 Redux 存储。

## 背景

Vivek 和我使用 Firebase 和 React 来操作 Pragli。

对于那些不熟悉的人来说， [Firebase 实时数据库](https://firebase.google.com/products/realtime-database/) (RTDB)提供浏览器内(或应用内)数据读取、写入和订阅。一个客户机可以简单地写入一个 JSON 文档，该文档会立即传播到所有其他客户机。这极大地消除了对服务器代码的需求。

数据被表示为一个大的 JSON 文档，子数据由“routes”引用。例如，下面 JSON 文档中的用户在路线`users/dsafreno`上。

```
{
  "teams": {
    "Pragli": { ... },
    ...
  },
  "users": {
    "dsafreno": { ... },
    "vnair611": { ... },
    ...
  }
} 
```

对于生产应用程序，客户端不能做所有的事情，主要是出于安全原因。例如，发送电子邮件或使用集成进行身份验证需要不应与客户端共享的令牌。我们使用 Firebase 的[云功能](https://firebase.google.com/products/functions/)来填补空白。

## 布线 Firebase RTDB 和 React 吸合(默认)

Firebase RTDB 的问题是，它不是为 React 设计的，所以将两者连接在一起很糟糕。我们最终一遍又一遍地做着同样的事情:

*   在`componentDidMount`订阅一堆数据
*   在`componentWillUnmount`取消订阅
*   在`componentDidUpdate`中执行我们的“数据装载”逻辑

```
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = { user: null, team: null };
  }
  componentDidMount() {
    let {userId, teamId} = this.props;
    // subscribe to user data
    let userRef = firebase.database().ref(`users/${userId}`);
    let userOff = userRef.on('value', (snap) => {
      this.setState({user: snap.val()});
    }
    this.userOff = () => ref.off('value', userOff);
    // subscribe to team data
    let teamRef = firebase.database().ref(`teams/${teamId}`);
    let teamOff = teamRef.on('value', (snap) => {
      this.setState({team: snap.val()});
    }
    this.teamOff = () => ref.off('value', teamOff);
  }
  componentDidUpdate(prevProps, prevState) {
    if (!prevState.user && this.state.user) {
        // first time we got user data!
    }
    if (!prevState.team && this.state.team) {
        // first time we got team data!
    }
  }
  componentWillUnmount() {
    this.userOff();
    this.teamOff();
  }
  render() {
    let { user, team } = this.state;
    if (!user || !team) {
      return null;
    }
    // ...
  }
}

export default Example 
```

很丑吧？对于一个 React 组件来说，在 Firebase 中以两条路线订阅数据是一大堆样板文件。需要更多数据的组件甚至更糟。

所以我们集思广益，考虑了一些解决方案。

## 思想

**从高层组件传递更多数据作为道具**

我们考虑订阅高级组件中的数据，并将其传递给子组件。我们开始在一些地方实现这一点，但我们最终感到沮丧，因为它导致了太多的子组件/中间组件重新呈现，减慢了应用程序的速度。

**从 Firebase RTDB 加载数据→ Redux → React**

Redux 是一个 JS 应用程序的状态容器，通常与 React 一起使用。

我们考虑将我们的数据同步到 RTDB Firebase 的 Redux 中，然后订阅 Redux 数据存储。甚至还有一个[库](https://github.com/prescottprue/react-redux-firebase)可以让 React、Redux 和 Firebase RTDB 很好地配合使用。

但是，Firebase RTDB 的全部意义不就是拥有一个易于使用的状态源吗？为什么用 Redux 复制？

我们决定想出一个不涉及通过 Redux 传输状态的解决方案。

这让我们找到了最终的解决方案...

## 自动加载规格数据

最终，我们决定编写自己的包装函数，使访问 Firebase RTDB 更加方便。

关键思想是通过静态模板静态地指定组件需要哪些数据。一旦数据可用，Firebase RTDB 就会获取这些数据，并将其作为道具直接传递给组件。

我们使用以下模式:

```
const MY_DATA_SPEC = {
  name: 'myData',
  template: 'data/{myUid}',
  await: true
}; 
```

这个模式指定路由`data/{myUid}`上的数据作为`myData`属性传递到组件中(`myUid`被假定为从父节点作为属性传递进来)。

`await: true`阻止组件安装，直到它在该路径接收到一些数据(因此`componentDidMount`总是有数据)。

## 一起接线-带数据

我们编写了`withDbData`来方便地用这个规范中的数据加载组件。

上面的组件现在看起来是这样的:

```
class Example extends React.Component {
  componentDidMount() {
    // first time we got data!
  }
  render() {
    let {user, team} = this.props;
    // don't need to null check since we await the data!
  }
}

const USER_SPEC = {
  name: 'user',
  template: 'users/{userId}',
  await: true
};

const TEAM_SPEC = {
  name: 'team',
  template: 'teams/{teamId}',
  await: true
};

export default withDbData([USER_SPEC, TEAM_SPEC])(Example) 
```

下面是源代码(MIT 授权，放心使用)。在 Github [这里](https://gist.github.com/dsafreno/43850a4333b4a56a57f4e64d730ccc8b)也有。

```
import React from 'react';
import firebase from 'firebase/app';
import equal from 'deep-equal';

export function withDbData(specs) {
  let propToSpecs = {};
  for (let spec of specs) {
    let {propIds} = parseSpec(spec);
    for (let propId of propIds) {
      if (!propToSpecs[propId]) {
        propToSpecs[propId] = [];
      }
      propToSpecs[propId].push(spec);
    }
  }

  return (Child) => {
    let Wrapper = class extends React.PureComponent {
      constructor(props) {
        super(props);
        this.unmounting = false;
        this.offs = {};
        this.state = {};
      }
      subscribeToSpec(spec) {
        let { name, keys } = spec;
        let { propIds, formatPath } = parseSpec(spec);
        let path = formatPath(this.props);
        if (!path) {
          return;
        }
        let ref = firebase.database().ref(path);
        let offFunc = ref.on('value', (snap) => {
          let dat = keys ? filterKeys(snap.val(), keys) : snap.val();
          if (equal(dat, this.state[name])) {
            return;
          }
          this.setState({
            [name]: dat,
          });
        });
        let hasBeenOffed = false;
        let off = () => {
          if (hasBeenOffed) {
            return;
          }
          hasBeenOffed = true;
          if (!this.unmounting) {
            this.setState({
              [name]: null,
            });
          }
          ref.off('value', offFunc);
        };
        for (let propId of propIds) {
          if (!this.offs[propId]) {
            this.offs[propId] = [];
          }
          this.offs[propId].push(off)
        }
      }
      componentDidMount() {
        for (let spec of specs) {
          this.subscribeToSpec(spec)
        }
      }
      componentDidUpdate(prevProps) {
        let resubs = new Set();
        for (let prop of Object.keys(propToSpecs)) {
          if (prevProps[prop] !== this.props[prop]) {
            if (this.offs[prop]) {
              for (let off of this.offs[prop]) {
                off();
              }
            }
            this.offs[prop] = [];
            for (let spec of propToSpecs[prop]) {
              if (resubs.has(spec.name)) {
                continue;
              }
              resubs.add(spec.name);
              this.subscribeToSpec(spec);
            }
          }
        }
      }
      componentWillUnmount() {
        this.unmounting = true;
        for (let offList of Object.values(this.offs)) {
          for (let off of offList) {
            off();
          }
        }
        this.offs = {};
      }
      render() {
        for (let spec of specs) {
          if (spec.await && !this.state[spec.name]) {
            return null;
          }
        }
        let childProps = Object.assign({}, this.props, this.state);
        return (<Child {... childProps} />);
      }
    }
    return Wrapper;
  }
} 
```

## 结论

这对你学习如何更好地使用 Firebase 和 React 有帮助吗？你还有什么后续问题吗？在 doug@pragli.com 给我发一封电子邮件，或者在推特上关注我。