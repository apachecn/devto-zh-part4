# 学习 NextJs -第 3 天

> 原文：<https://dev.to/eperedo/learning-next-day-3-10jh>

因为 Next 可以在服务器中呈现 React 组件，所以所有组件都有一个特殊的生命周期事件，称为 **getInitialProps** 。

该函数接受异步代码，因此您可以轻松地从远程资源获取数据，比如 http 请求。

让我们扩展我们的主页，从 [jsonplaceholder](https://jsonplaceholder.typicode.com/users) API 获取用户列表。

该组件将接收一个带有对象数组的 **users** prop，每个对象带有用户信息。您可以使用 map 方法遍历用户并显示每个用户的名字。

```
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}

export default Home; 
```

现在你需要通过用户道具，此时 **getInitialProps** 进入舞台。

```
function Home({ users }) {
  return (
    <div>
      <h1>Users</h1>
      {users.map(user => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}

async function getInitialProps() {
  const response = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await response.json();
  return { users };
}

Home.getInitialProps = getInitialProps;

export default Home; 
```

您必须返回一个 javascript 普通对象，然后将作为道具注入到组件中。该方法只允许在**页面的**组件中使用，不允许在普通组件中使用。如果你尝试这样做，它将抛出一个错误。

```
ReferenceError: fetch is not defined 
```

这是因为 **getInitialProps** 是在服务器上执行的，而 node 不像浏览器那样有本地的 **fetch** 方法。一种替代方法是使用 axios、supertest、superagent 等库。但是在这种情况下，我将使用[节点获取](https://www.npmjs.com/package/node-fetch)，因为我不想改变上面的代码。

```
npm install node-fetch 
```

现在您需要添加一行代码到组件导入节点-fetch

```
import fetch from 'node-fetch'; 
```

如果您在 [http://localhost:3000](http://localhost:3000) 打开浏览器，您会看到一个通过 http 从外部 API 加载的用户列表。